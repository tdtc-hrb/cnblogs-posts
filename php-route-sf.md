---
title: "Routing - Symfony"
date: 2026-01-08T10:08:08+08:00
---
- Symfony v8.0

## Creating Routes
- routes
- attributes
### routes
/config/routes.yaml:
```
# yaml-language-server: $schema=../vendor/symfony/routing/Loader/schema/routing.schema.json

# This file is the entry point to configure the routes of your app.
# Methods with the #[Route] attribute are automatically imported.
# See also https://symfony.com/doc/current/routing.html

# To list all registered routes, run the following command:
#   bin/console debug:router

controllers:
    resource: routing.controllers
    type: attribute  ## only

```
Add attributes only!

### Creating Routes as Attributes
/config/routes/attributes.yaml:
```
controllers:
    resource: '../../src/Controller/'
    type: attribute
    prefix:
        ja: '/ja-jp'
        en: '/en-us'
        zh: '/zh-cn'
```


## Matching HTTP Methods
By default, routes match any HTTP verb (GET, POST, PUT, etc.) Use the methods option to restrict the verbs each route should respond to:
```
// src/Controller/BlogApiController.php
namespace App\Controller;

use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Attribute\Route;

class BlogApiController extends AbstractController
{
    #[Route('/api/posts/{id}', methods: ['GET', 'HEAD'])]
    public function show(int $id): Response
    {
        // ... return a JSON response with the post
    }

    #[Route('/api/posts/{id}', methods: ['PUT'])]
    public function edit(int $id): Response
    {
        // ... edit a post
    }
}
```

## Ref
- [Creating Routes](https://symfony.com/doc/current/routing.html#creating-routes)
- [Matching HTTP Methods](https://symfony.com/doc/current/routing.html#matching-http-methods)
