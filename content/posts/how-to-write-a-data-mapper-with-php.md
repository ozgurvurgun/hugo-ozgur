---
title: How to write a data mapper with php
description:
date: 2019-07-07 
draft: false
tags:  php #reflection #datamapper #annotation #annotationreader #doctrine #symfony
---


When you develop an API client with PHP, you would need data mapper for mapping data from API to an object. 
<!--more-->
For example, you have requested an API endpoint and you get a response like that:

```json
{
  "user": {
    "username": "delirehberi",
    "bio": "carpenter"
  }
}
```

And you have a User class 

```php
<?php
class User{ 
    protected $username; 
    protected $fullname; 
    protected $bio;
      /**
     * @return mixed
     */
    public function getUsername()
    {
        return $this->username;
    }

    /**
     * @param mixed $username
     *
     * @return User
     */
    public function setUsername($username)
    {
        $this->username = $username;

        return $this;
    }

    /**
     * @return mixed
     */
    public function getFullname()
    {
        return $this->fullname;
    }

    /**
     * @param mixed $fullname
     *
     * @return User
     */
    public function setFullname($fullname)
    {
        $this->fullname = $fullname;

        return $this;
    }

    /**
     * @return mixed
     */
    public function getBio()
    {
        return $this->bio;
    }

    /**
     * @param mixed $bio
     *
     * @return User
     */
    public function setBio($bio)
    {
        $this->bio = $bio;

        return $this;
    }
}
```

You need to map JSON response to the PHP object. But how?

You will need to know how to use [ReflectionClass](https://php.net/manual/en/class.reflectionclass.php), [PropertyAccess](https://symfony.com/doc/current/components/property_access.html), [AnnotationReader](https://www.doctrine-project.org/api/annotations/1.6/Doctrine/Common/Annotations/AnnotationReader.html).

### How do you map class properties to API response fields and returns an object?

At first, you need to decode JSON string to PHP array or object.

```php
$result = json_decode($response, true);
```

After that, create a Reflection class;

```php
$reflectionObj = new \ReflectionClass(User::class);
```

Now you can access all properties

```php
$properties = $reflectionObj->getProperties();
```

You don't need to know your User Class has which properties anymore. 

Now, we need PropertyAccessor for manipulating our User object. 

```php
$propertyAccessor = PropertyAccess::createPropertyAccessor();
```

### How can manipulate an object property with PropertyAccess object?

```php
$propertyAccessor->setValue(
                    $userObject,
                    $property->getName(), //eg: username
                    $value //eg: emre
                );
```

OK, we know how to access the property of an object and how to manipulate another object properties.

Now we can start writing our data mapper.

Create an annotation class to configure properties to json values.

```php
use Doctrine\Common\Annotations\Annotation;

/**
 * Class DataMapper.
 *
 * @Annotation
 * @Annotation\Target({"PROPERTY"})
 */
class DataMapper
{
    public $json_field;
}

```

We will use this object to define a JSON field equal to an object property. We can update our User class property annotations like that:

```php
 /**
     * @var
     * @DataMapper(json_field="username")
     */
    protected $username;
    /**
     * @var
     * @DataMapper(json_field="full_name")
     */
    protected $fullname;
    /**
     * @var
     * @DataMapper(json_field="bio")
     */
    protected $bio;
```

note: don't forget to add DataMapper class to the header.

We are ready for writing data mapping logic.

- Create an annotation reader object
- Create property accessor
- Create a new empty object (eg: User)
- Create a reflection object for reach properties
- Get object properties from reflection object
- Foreach to properties
  - read property annotation for DataMapper annotation
  - get json_field value from annotation
  - access data from JSON with json_field value
  - set property of a new object with data
- return new filled object :)

```php
$reader = new AnnotationReader();
$propertyAccessor = PropertyAccess::createPropertyAccessor();
$user = new User();
$reflectionObj = new \ReflectionClass(User::class);
$properties = $reflectionObj->getProperties();
foreach ($properties as $key => $property) {
  $propertyAnnotation = $reader->getPropertyAnnotation($property, DataMapper::class);
  $json_field = $propertyAnnotation->json_field;
  if(!$json_field) continue;
  $value = $result[$json_field];
  $propertyAccessor->setValue(
                    $user,
                    $property->getName(),
                    $value
                );
}

var_dump($user);
```

Congrats, you can write as a function and use in your project.


