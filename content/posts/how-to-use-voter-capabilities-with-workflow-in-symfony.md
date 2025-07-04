---
title: How to use voter capabilities with workflow in symfony
description:
date: 2020-01-16 
draft: false
tags:  symfony #workflow #voter #en #php
---


You need to use a [Voter](https://symfony.com/doc/current/security/voters.html) for workflow entity for managing access policies when you use the [Workflow component](https://symfony.com/doc/current/components/workflow.html). Workflow component supports access policies with guard option, but the documentation won't point out how to get the subject variable for a voter class.
<!--more-->
I faced this situation when I was developing a feature for my last customer. I needed to dig into the workflow component`s code. I found out the class that getting guard config, parsing with expression language, and creating transaction blocker. 

This class is evaluating expression with some variables. 

```php
$variables = [
            'token' => $token,
            'user' => $token->getUser(),
            'subject' => $event->getSubject(),
            'roles' => $roles,
            'role_names' => $roleNames,
            // needed for the is_granted expression function
            'auth_checker' => $this->authorizationChecker,
            // needed for the is_* expression function
            'trust_resolver' => $this->trustResolver,
            // needed for the is_valid expression function
            'validator' => $this->validator,
        ];
```
This is our solution, you can use these variables for writing your expression to workflow guard. I needed subject variable and I used to this in transaction configuration:

```yaml
        to_rejected:
          guard: "is_granted('reject',subject)"
          from: draft
          to: rejected
```

Now I can use my voter class with full capabilities.

