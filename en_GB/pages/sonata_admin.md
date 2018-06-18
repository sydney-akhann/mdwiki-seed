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


## Add Custom Object Action

### 1. Extending Admin Controller

```php
<?php
// src/Acme/DemoBundle/Controller/CRUDController.php

namespace Acme\DemoBundle\Controller;

use Sonata\AdminBundle\Controller\CRUDController as Controller;

class CRUDController extends Controller
{
    public function addPaymentAction($id)
    {
        $request = $this->getRequest();
        $id = $request->get($this->admin->getIdParameter());
        $so = $this->admin->getObject($id);

        if (!$so) {
            throw $this->createNotFoundException(sprintf('unable to find the object with id : %s', $id));
        }

        $form = $this->createFromBuilder()
            //...
            ->getForm();

        return $this->render('admin/sales_order/add_payment.html.twig', [
            'object' => $so,
            "form" => $form->createView(),
            "action" => "add-payment",
        ]);
    }

}
```

### 2. Register Controller

```yaml
    admin.sales_order:
        class: Hermes\Bundle\CoreBundle\Admin\SalesOrderAdmin
        arguments: [~, Hermes\Bundle\CoreBundle\Entity\SalesOrder, HermesCoreBundle:SalesOrderAdmin]
        calls:
            - ['setTemplate', ['edit', ':admin/sales_order:edit.html.twig']]
        tags:
            - { name: sonata.admin, manager_type: orm, label: Sales Orders, group: app.admin.group.content, label_catalogue: "HermesCoreBundle" }
```

### 3. Custom Edit Template

```twig
{% extends base_template %}

{% block title %}Sales Order #{{ object.number }} - Add Payment{% endblock %}

{% block navbar_title %}
    {{ block('title') }}
{% endblock %}

{%- block actions -%}
{% include 'SonataAdminBundle:CRUD:action_buttons.html.twig' with { action: 'edit' } %}
{%- endblock -%}

{% block form %}
<div class="box box-primary">
    <div class="box-header">
        <h3 class="box-title">Add Payment</h3>
    </div>
    <div class="box-body">
        {{ form(form) }}
    </div>
</div>
{% endblock %}
```
