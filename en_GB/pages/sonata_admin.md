# Sonata Admin cheatsheet

## Generate admin URL

### Object URL

Inside a CRUD template, a route for the current Admin class can be generated via the admin variable's  generateUrl() command:

``` twig
<a href="{{ admin.generateUrl('list') }}">List</a>
<a href="{{ admin.generateUrl('list', {'page': 1}) }}">List</a>

<a href="{{ admin.generateUrl('edit', {'id': object.id}) }}">List</a>
```

## Create a Route
You can register new routes by defining them in your **Admin** class. Only Admin routes should be registered this way.

```php
<?php
use Sonata\AdminBundle\Route\RouteCollection;

class MediaAdmin extends Admin
{
    protected function configureRoutes(RouteCollection $collection)
    {
        $collection->add('myCustom'); // Action gets added automatically
        $collection->add('view', $this->getRouterIdParameter().'/view');
    }
}
```
