---
title: "Agregando Fonts Android Studio"
layout: post
date: 2017-03-28 18:00
image: /assets/images/markdown.jpg
headerImage: false
tag:
- Dispositivos Móviles
- Android
- Android Studio
- Tutorial
star: false
projects: true
category: project
author: matiassalinas
description: Agregando fonts custom a nuestro proyecto de Android Studio
---

El siguiente tutorial fue trabajado en Android Studio 2.3, utilizando el Sistema Operativo OS X El Capitán, versión 10.11.6. SDK Mínimo: 5.0 Lollipop.

---

<div class="side-by-side">
   <div class="toleft">
        <p>Hola! antes de comenzar a desarrollar mi proyecto, quería agregar una fuente de texto, para personalizar un poco más mi aplicación.</p>
        <p>Lo primero que se debe hacer, es crear unas carpetas dentro de 'app', y colocar nuestra font como se muestra en la siguiente imágen.</p>
    </div>
    <div class="toright">
        <img class="image" src="{{ site.url }}/assets/images/Tutorial_2/image_1.png" alt="Image_1">
    </div>
</div>

Luego nos vamos al archivo <i>activity_main.xml</i>, el cual mostrará la pantalla inicial de nuestra aplicación. En ella, viene por defecto un <i>textview</i>. Dale click, y modifica su <i>ID</i>. Yo le he puesto txtHello, para recordar que es un texto y no confundirme después con las <i>IDs</i> que vaya a crear en el futuro.

![Markdowm Image][2]{: class="bigger-image" }

Tras asignarle el <i>ID</i> al <i>TextView</i> nos vamos al <i>MainActiviy.java</i> y en la función <i>onCreate</i> agregamos las siguientes lineas de texto.

{% highlight java %}
TextView txtV=(TextView)findViewById(R.id.txtHello);
Typeface face=Typeface.createFromAsset(getAssets(),"fonts/Leaner_Regular.ttf");
txtV.setTypeface(face);
{% endhighlight %} 

La primera linea obtiene el <i>TextView</i> con id <i>txtHello</i> que definimos anteriormente.

La segunda, obtiene el font que agregamos en app>assets>fonts.

Finalmente, se le asigna al <i>TextView</i> la fuente que asignamos en la variable <i>face</i>.

<div class="side-by-side">
   <div class="toleft">
        <img class="image" src="{{ site.url }}/assets/images/Tutorial_2/image_3.png" alt="Image_3">
    </div>
    <div class="toright">
        <p>Probamos nuestra aplicación utilizando el emulador o nuestro celular, y nos mostrará la fuente que hemos agregado.</p>
        <p>Si tienes alguna duda respecto a este tutorial, no dudes en consultar en los comentarios. </p>
    </div>
</div>


---

[1]: {{ site.url }}/assets/images/Tutorial_2/image_1.png
[2]: {{ site.url }}/assets/images/Tutorial_2/image_2.png
[3]: {{ site.url }}/assets/images/Tutorial_2/image_3.png