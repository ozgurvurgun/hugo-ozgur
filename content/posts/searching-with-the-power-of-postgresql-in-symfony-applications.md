---
title: Searching with the power of postgresql in symfony applications
description:
date: 2020-06-15 
draft: false
tags:  en #php #symfony #postgresql
---


For instance, you need to write a search page for your last project, and it is not big enough to use an external search engine. Still, you want to use some powerful text searching features. You are brilliant if you preferred PostgreSQL as your database. Because PostgreSQL has very, very powerful search capabilities. I want to share some of them with you. 
<!--more-->
You can run the search queries on columns even if you don't index it. You need a `document` and a `query` for running on it. There is an operator for running your search query over the document. It is `@@`!

For instance; you have a content table like this:
```sql
create table content( id INT, title TEXT, body TEXT);
```

And, it includes some data;

```sql
insert into content values(1,'Demo content', 'Demo content body ');
insert into content values(2,'Demo content 2', 'Demo content body2 ');
insert into content values(3,'Verification title', 'Virtualization on cloud');
```
Let's run a search query on this table. Like I said, "We need a document and a query." But we do not index anything yet, and we not have any query typed data. So, we can use the `to_tsvector` function for creating our document on the fly, and we can use the `to_tsquery` function for creating our query on the fly.

```sql
select * from content where to_tsvector(title) @@ to_tsquery('demo');
```

And the result is;

```
 id |     title      |        body
----+----------------+---------------------
  1 | Demo content   | Demo content body
  2 | Demo content 2 | Demo content body2
(2 rows)
```

Meh, it was so easy. Let's try another thing. Did you see our last data? It includes *Verification* text inside. Can we search for it with any variant of the words? Yes, of course. For example, "Verify".

```sql
select * from content where to_tsvector(title) @@ to_tsquery('verify');
```

Get it? We can search for variants. PostgreSQL knows which word is matching with which words. 

Also, there is a lot of special features here. You can read about it in the official documentation. This is not a documentation dude, this is a blog post. Just I've noted the amazing things not documenting. Anyway, this is the documentation URL: [postgresql/textsearch](https://www.postgresql.org/docs/9.1/datatype-textsearch.html)

OK. Let's continue. 

I said you need to have a document and a query. So, you can create a document using different fields if you want. You can use `||` operator for concatenation.

```sql
select * from content where to_tsvector(title || ' ' || body) @@ to_tsquery('cloud');
```

Also, you need a correct query for searching. So, you can't use this `to_tsquery('verification cloud')` because it is not valid. The query must not include spaces. It must be a logical operation. So? You can split your text and merge it via *and* or *or*. Like this: `to_tsquery('cloud&verification')` or `to_tsquery('cloud|demo')`

You like that, right?

--

OK. Let's jump the Symfony section. We have the Doctrine. Great ORM. But sometimes it can disable us. Like now...

We need to use a native query to make a profit about searching. I tired while writing. I'm adding the example here. This is a very basic snippet. 

Note:I'm using `$rsm->generateSelectClause()` for prevent collisions happened when working with multiple tables.

Attention: This is an example code. Please filter your `$query` to prevent your application from an SQL injection vulnerability.

```php
  public function search(string $query,?string $role=null)
  {
   $query = (function($q){
       $pieces = explode(' ',$q);
       return join('&',$pieces);
    })($query);
    $rsm = new ResultSetMappingBuilder($this->_em,ResultSetMappingBuilder::COLUMN_RENAMING_INCREMENT);
    $rsm->addRootEntityFromClassMetadata('App\\Entity\\User', 'm');
    $rsm->addJoinedEntityFromClassMetadata('App\\Entity\\Profile', 'p','m','profile');
    $sql ="select ".$rsm->generateSelectClause()." from member m 
      inner join profile p on m.profile_id=p.id
      where to_tsvector(m.email || ' ' || p.prename || ' ' || p.lastname || ' ' || p.company || ' ' || p.position) @@ to_tsquery('$query')";
    if($role) {
      $sql.=" AND '$role' = ANY (roles)";
    }
    $qb = $this->_em->createNativeQuery($sql,$rsm);
    return $qb->getResult();
  }
```

Keep safe...

