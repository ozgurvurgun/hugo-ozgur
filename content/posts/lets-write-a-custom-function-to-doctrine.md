---
title: Lets write a custom function to doctrine
description:
date: 2022-01-27 
draft: false
tags:  if you think i tell something wrong,do not hesitate to write me an email to z[œt]emre.xyz
---


Doctrine is well-known ORM for php applications, especially apps which uses #symfony framework. I like to use symfony when i need to write a php application in all scale. 

Doctrine is fittest ORM for sql operation in #PHP ecosystem in my opinion. I want to talk about writing custom #dql functions for doctrine.
<!--more-->
Often i have need to use native #postgresql functions in my apps, like `json_equality`function that i created. I use that function like this `json_equality(table.jsoncolumn,'fieldname_injsoncolumn','subfield_injsoncolumn')` and it will generate that sql `table.column -> 'fielname_injsoncolumn' ->> 'subfield_injsoncolumn'` and the postgres will look json column for correct equation. 

First, let's understand how it works. But, i will not go deep, if you interested you can learn about what is AST.

Doctrine have a parser for queries, it will parse what you write as query and then it will convert everything to sql queries. So, its not same what you write in your php code with sql queries. For example;

```php
$qb = $em->createQuery('SELECT u FROM App\Entity\User WHERE u.id = 3');
```
 
```sql
SELECT u FROM user u WHERE u.id=3
```

Look at the above. These two thing contains similar terms, `SELECT` , `FROM`,`WHERE`, maybe you are thinking these are same, but NOT. First ones are just like placeholders, they are DQL statements, orm will change everything inside the query to SQL statements. 

Like this, when you write `SELECT u FROM App\Entity\User WHERE json_equality(u.metadata,'social','allow_to_follow') = 1` query, doctrine will parse it and convert to that `SELECT u FROM user u WHERE CAST (u.metadata -> 'social' ->> 'allow_to_follow' AS INTEGER) = 1` query. 

	i assume you know json operators on postgres, if not, you can read documentation
	
Ok. Let's write some code. 

We have to create `JsonEquality` class which extends from `Doctrine\ORM\Query\AST\Functions\FunctionNode` class and that class will be placed in `src/Doctrine/ORM/Query/AST/Functions/JsonEquality.php` file. 

It will have two methods. First method name is `parse` and it will take one argument, its `Doctrine\ORM\Query\Parser`. We will use that argument to parse the part of the query. Second method name is `getSql` and it will take one argument, its `Doctrine\ORM\Query\SqlWalker`. We will use that argument to start parsing to part of query and will return part of the SQL query. 

Also we need two properties on that class to store parsed values in `parse` method and then use those properties in `getSql` method. It will be private:

``` php
private $field;
private $json_fields=[];
```

In the `parse` method, we will parse the part of the DQL query. Variable name is `$parser` for argument of method. And this parser have a method named `match` it will wait for a `token` to match, and it will walk through on the query until next token match or node match (wtf is the node? you can read about AST, seriously!). Also there is a ton of methods to generate predefined tokens, like `StringPrimary` method for string matches, `AritmeticExpression` for more complex expressions and subqueries. There is also another method exists in `Parser` object.

We can write first section. 

```php
public function parse(Parser $parser)
{
	$parser->match(Lexer::T_IDENTIFIER); //its our identifier json_equality
	$parser->match(Lexer::T_OPEN_PARENTHESIS);
	$this->field = $parser->StringPrimary(); //we are storing first argument of json_equality function as property. This is not the calue, its node of the first value. We will use that in getSql method.
	$parser->match(Lexer::T_COMMA); //this is the comma after first argument
	...
}
```

Let's stop here and think a little. Our function requires how many arguments? We saw three in example `json_equality(table.userdata,'user','firstname')` but it may be less; `json_equality(table.userdata,'username')` or it may be more; `json_equality(table.userdata,'comments','counts','favorite')`, then how we can handle dynamic lenght in arguments? There is no argument spreading feature in DQL. So? Maybe we can try another approach;

```php
public function parse(Parser $parser)
{
//...
	$this->field = $parser->StringPrimary();
	$lexer = $parser->getLexer(); //we will use it to look to the type of the current token
	while(count($this->json_fields)<1 || $lexer->lookahead['type'] != Lexer::T_CLOSE_PARENTHESIS) { //loop until reach the close paranthesis
     $parser->match(Lexer::T_COMMA); //walk over comma
     $this->json_fields[]= $parser->ArithmeticExpression(); //add new node to array property
   }
	$parser->match(Lexer::T_CLOSE_PARENTHESIS);//walk over close parenthesis and complete the parsing
}
```

Thats it! Our `parse` method is done and also have dynamic sized argument support.

We can contunie to generate SQL code.

In the `getSql` method, we need just `dispatch` method of nodes. It will take value of the node and we will assign to variables, and we will use to generate SQL code. 

```php
//lets assume our dql is json_equality(table.userdata,'user','firstname')
public function getSql(SqlWalker $sqlWalker)
{
	$field = $this->field->dispatch($sqlWalker); //get value from node, it will be table.userdata
	$values = []; // it will store json fields

	for($i = 0; $i<count($this->json_fields); $i++){
	 $values[]=$this->json_fields[$i]->dispatch($sqlWalker); // we have to dispatch all fields and push values to $values array
	}
	$lastValue= array_pop($values); //we are cutting the last element of the values array. We will use that just after ->> oparator, others will be use -> operator. 
	if(count($values)>1){
	 $json = join(' -> ',$values).' ->> '.$lastValue;
	}else{
	 $json = array_pop($values).' ->> '.$lastValue;
	}

	//and return generated SQL. In here i create for integer values, you can make it works for your needs. 
	return sprintf(
	 'CAST (%s -> %s AS INTEGER)', $field, $json
	);
}
```

Cool, right? But wait! How can use it? 

It must be registered in `config/packages/doctrine.yaml` file, or you can register via php for non-symfony apps. [Check the documentation](https://www.doctrine-project.org/projects/doctrine-orm/en/2.9/cookbook/dql-user-defined-functions.html#dql-user-defined-functions).

```yaml
doctrine:
  orm:
    string_functions:
	  json_equality: 'App\Doctrine\ORM\Query\AST\Functions\JsonEquality'
```

Now you can use it in your code. 

```php
$qb = $this->createQueryBuilder('u');
$qb->andWhere("json_equality(u.metadata,'social','follow_count') = 100");
echo $qb->getQuery()->getSQL();
```

And the result;

```sql
SELECT * FROM user WHERE CAST( u.metadata -> 'social' ->> 'follow_count' AS INTEGER) = 100;
```

Happy coding!

#php #symfony #english #web #doctrine 

