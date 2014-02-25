---
layout: post
title: Usando el componente HttpFoundation
---
El componente HttpFoundation de Symfony permite encapsular y manejar la información de una petición de manera orientada a objetos, y ofrece una capa robusta que nos abstrae de tareas que pueden resultar tediosas de hacer a mano.

Basicamente el componente ofrece una serie de clases que permiten acceder a la información de la petición (Request) y clases que facilitan la creación de respuestas http a devolver (Response).

## ¿Porque usar este componente?

En mi opinión este componente facilita realizar tareas que de otra forma llegan a ser engorrosas y tediosas, ejemplo de ellos son:

*   Subida de archivos al servidor.
*   Manejo de cookies (no se maneja igual que la sesión por ejemplo, [cookies](http://es.php.net/manual/es/function.setcookie.php)).
*   Gestión de Sessión (Sesión por defecto, en Base de datos, en archivos, etc...).
*   Saber si una petición es asincrona.
*   Verificar que un parametro venga o exista en alguna de las variables super globales.
*   Obtener información de la url, como el puerto, el queryString, pathinfo, host, etc...
*   Seteo de los headers para la respuesta (algo que muchas veces no hacemos).
*   Manejo de Códigos de estado para la respuesta.
*   Creación de headers para descarga de archivos.
*   Entre otras cosas que aun no he usado...

## Instalando el componente

Para descargar el componente lo recomendable es hacerlo mediante composer:
{% highlight json %}
{
    "require": {
        "symfony/http-foundation": "*"
    }
}
{% endhighlight %}

## Creando el objeto Request

Crear la instancia del Request es tan sencillo como ejecutar el siguiente código:
{% highlight php %}
<?php //proyecto/index.php

require_once __DIR__ . '/vendor/autoload.php';

use Symfony\Component\HttpFoundation\Request;

$request = Request::createFromGlobals(); //acá creamos el objeto request
{% endhighlight %}

El método **createFromGlobals()** crea una instancia con la información de la petición a partir de las variables globales de php $_GET, $_POST, $_REQUEST, $_SERVER, $_FILES y $_COOKIE.

Ahora podemos obtener la info del request desde los atributos del objeto:

##### El atributo query:

Este atributo es una instancia de **(\Symfony\Component\HttpFoundation\ParameterBag)** y contiene la información contenida en la variable global $_GET, para usarlo desde el objeto request lo hacemos de la siguiente manera:

{% highlight php %}
<?php

$request->query->get('nombre'); 
//devuelve el valor contenido en $_GET['nombre'] si existe, si no devuelve null

$request->query->get('nombre', 'Manuel'); 
//si no se encuentra el indice nombre en $_GET el método devuelve 'Manuel' por defecto.

$request->query->get('usuario.nombre', 'Manuel', true); 
//acá hay algo interesante, si el indice usuario contiene un array, el componente
//intentará devolver el valor de la siguiente manera: $_GET['usuario']['nombre']
//recordar que para que funcione, se debe pasar true en el tercer argumento.
{% endhighlight %}

##### El atributo request:

Este atributo es una instancia de **(\Symfony\Component\HttpFoundation\ParameterBag)** y contiene la información contenida en la variable global $_POST, para usarlo desde el objeto request lo hacemos de la siguiente manera:

{% highlight php %}
<?php

$request->request->get('nombre'); 
//devuelve el valor contenido en $_POST['nombre'] si existe, si no devuelve null

$request->request->get('nombre', 'Manuel'); 
//si no se encuentra el indice nombre en $_POST el método devuelve 'Manuel' por defecto.

$request->request->get('usuario.nombre', 'Manuel', true); 
//acá hay algo interesante, si el indice usuario contiene un array, el componente
//intentará devolver el valor de la siguiente manera: $_POST['usuario']['nombre']
//recordar que para que funcione, se debe pasar true en el tercer argumento.
{% endhighlight %}

## Ventajas de usar el objeto Request

Un ejemplo de ello puede ser conocer si la petición es mediante ajax o no, en php seria algo como:

{% highlight ruby  %}
<?php

if (isset($_SERVER['HTTP_X_REQUESTED_WITH'])
    AND strtolower($_SERVER['HTTP_X_REQUESTED_WITH']) === 'xmlhttprequest') {
    // AJAX
}
{% endhighlight %}

Mientras que con el componente seria algo como:

{% highlight php %}
<?php

if ($request->isXmlHttpRequest()) {
    // AJAX
}
{% endhighlight %}

