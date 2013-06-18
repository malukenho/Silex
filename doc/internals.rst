Internamente
=========

Este capítulo irá contar um pouco sobre como funciona o Silex
internamente.

Silex
-----

Application
~~~~~~~~~~~

Application é a interface de comunicação principal para o Silex. Ela implementa a feature do Symfony2
`HttpKernelInterface
<http://api.symfony.com/master/Symfony/Component/HttpKernel/HttpKernelInterface.html>`_,
então você pode passar `Request
<http://api.symfony.com/master/Symfony/Component/HttpFoundation/Request.html>`_
para o metodo ``handle`` e assim ele retornará `Response
<http://api.symfony.com/master/Symfony/Component/HttpFoundation/Response.html>`_.

Ele herda do serviço ``Pimple``, permitindo assim flexibilidade dentro e fora de sua aplicação
. Você pode substituir qualquer serviço, e você também é
capaz de lê-los.

O Application faz uso de `EventDispatcher
<http://api.symfony.com/master/Symfony/Component/EventDispatcher/EventDispatcher.html>`_
para disparar eventos usa-se do Symfony2 `HttpKernel
<http://api.symfony.com/master/Symfony/Component/HttpKernel/HttpKernel.html>`_
. Isso permite buscar o ``Request``, convertendo respostas em
objetos ``Response`` e exceções de manipulação. Nós também podemos usá-lo para despachar alguns
eventos personalizados como antes/depois , middlewares e erros.

Controller
~~~~~~~~~~

The Symfony2 `Route
<http://api.symfony.com/master/Symfony/Component/Routing/Route.html>`_ is
actually quite powerful. Routes can be named, which allows for URL generation.
They can also have requirements for the variable parts. In order to allow
settings these through a nice interface the ``match`` method (which is used by
``get``, ``post``, etc.) returns an instance of the ``Controller``, which
wraps a route.

ControllerCollection
~~~~~~~~~~~~~~~~~~~~

One of the goals of exposing the `RouteCollection
<http://api.symfony.com/master/Symfony/Component/Routing/RouteCollection.html>`_
was to make it mutable, so providers could add stuff to it. The challenge here
is the fact that routes know nothing about their name. The name only has
meaning in context of the ``RouteCollection`` and cannot be changed.

To solve this challenge we came up with a staging area for routes. The
``ControllerCollection`` holds the controllers until ``flush`` is called, at
which point the routes are added to the ``RouteCollection``. Also, the
controllers are then frozen. This means that they can no longer be modified
and will throw an Exception if you try to do so.

Unfortunately no good way for flushing implicitly could be found, which is why
flushing is now always explicit. The Application will flush, but if you want
to read the ``ControllerCollection`` before the request takes place, you will
have to call flush yourself.

The ``Application`` provides a shortcut ``flush`` method for flushing the
``ControllerCollection``.

.. tip::

    Instead of creating an instance of ``RouteCollection`` yourself, use the
    ``$app['controllers_factory']`` factory instead.

Symfony2
--------

Following Symfony2 components are used by Silex:

* **HttpFoundation**: For ``Request`` and ``Response``.

* **HttpKernel**: Because we need a heart.

* **Routing**: For matching defined routes.

* **EventDispatcher**: For hooking into the HttpKernel.

For more information, `check out the Symfony website <http://symfony.com/>`_.
