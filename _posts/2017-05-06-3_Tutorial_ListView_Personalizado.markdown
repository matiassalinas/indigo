---
title: "Personalizando tu propio ListView"
layout: post
date: 2017-05-06 15:20
image: /assets/images/markdown.jpg
headerImage: false
tag:
- Dispositivos Móviles
- Android
- Android Studio
- Tutorial
- ListView
- Adapter
star: false
projects: true
category: project
author: matiassalinas
description: Personalizando tu propio ListView
---

El siguiente tutorial fue trabajado en Android Studio 2.3, utilizando el Sistema Operativo OS X El Capitán, versión 10.11.6. SDK Mínimo: 5.0 Lollipop.

---

Hola! Uno de los elementos que más se utiliza en un proyecto para aplicaciones móviles son los ListView.

En este tutorial les enseñaré a personalizar sus propios ListView, y cómo poder rescatar información de los items al hacerles click.

El resultado al que queremos llegar es el siguiente.

![Markdowm Image][3]{: class="" }

Lo primero que se debe hacer es crear un nuevo proyecto en Android Studio. En éste tutorial partimos como pantalla inicial una <i>"Empty Activity"</i>.

Luego, con nuestro proyecto ya creado, procedemos a crear un ListView en nuestro layout, y asignarle un ID, en este caso le he puesto <i>"tutorialListView"</i>. A continuación verán como quedaría nuestro layout.

![Markdowm Image][1]{: class="bigger-image" }

Ahora, debemos crear la clase Java de nuestro objeto que queremos que sea mostrado en el ListView. Para éste ejemplo, crearemos la clase <i>Persona</i>, que tendrá las cadenas <i>nombre</i> y <i>apellido</i>, y el entero <i>edad</i>. Creamos el constructor de la clase, y dos métodos: <i>getNombreCompleto()</i> y <i>getEdad()</i>.

{% highlight java %}
package io.github.matiassalinas.tutorial_listview;

public class Persona {
    private String nombre;
    private String apellido;
    private int edad;

    public Persona(String nombre, String apellido, int edad) {
        this.nombre = nombre;
        this.apellido = apellido;
        this.edad = edad;
    }

    public String getNombreCompleto(){
        return this.nombre + " " + this.apellido;
    }

    public int getEdad() {
        return edad;
    }

}
{% endhighlight %}

Ya teniendo esto, crearemos un nuevo Layout (archivo XML). Le llamaremos <i>item_listview.xml</i>. En este layout indicaremos como queremos que se vean cada uno de los elementos de nuestro ListView. Para este ejemplo mostraremos dos <i>TextView</i>, a los cuales se les ha modificado el ID, como se muestra a continuación.

![Markdowm Image][2]{: class="bigger-image" }

Ahora crearemos el <i>Adapter</i> de nuestra ListView. El Adapter, será una clase Java que extenderá a la clase ArrayAdapter para poder utilizarla en nuestra ListView. Existen otras clases a las cuales podemos extender esta clase: <i>BaseAdapter</i>, <i>ArrayAdapter</i> y <i>CursorAdapter</i>, pero para nuestro tutorial utilizaremos la segunda. Si desean saber más al respecto de estas clases, les recomiendo leer el primer comentario a esta pregunta en StackOverflow: http://stackoverflow.com/questions/16796182/whats-the-difference-between-baseadapter-and-arrayadapter

En terminos generales, la funcionalidad principal de esta clase será llenar nuestro ListView con cada uno de los elementos del ArrayList que queremos utilizar, en donde cada elemento del ListView se verá según nosotros hayamos diseñado nuestro layout <i>item_listView.xml</i>.

Crearemos dos variables en nuestra clase: El context, y el ArrayList de Personas.

{% highlight java %}
public class ListViewAdapter extends ArrayAdapter {
    private Context context;
    private ArrayList<Persona> personas;

    public ListViewAdapter(Context context, ArrayList<Persona> personas){
        super(context,0,personas);
        this.context = context;
        this.personas = personas;
    }
}
{% endhighlight %}

Ahora debemos crear la función <i>getView()</i>, que es la encargada de mostrar las vistas de cada uno de los elementos del ListView. Éste método ya está implementado en la clase ArrayAdapter, por lo que lo debemos sobreescribir con <i>"@Override"</i>.

{% highlight java %}
@Override
public View getView(int position, View convertView, ViewGroup parent){
    //Obtenemos la persona
    Persona persona = (Persona) getItem(position);
    //R.layout.item_listview es el layout de cada uno de los elementos del ListView
    if (convertView == null) {
        convertView = LayoutInflater.from(getContext()).inflate(R.layout.item_listview, parent, false);
    }
    //Obtenemos los TextView del layout
    TextView itemTextView_1 = (TextView) convertView.findViewById(R.id.itemTextView_1);
    TextView itemTextView_2 = (TextView) convertView.findViewById(R.id.itemTextView_2);
    //Asignamos valores a los TextView, con la información de Persona
    itemTextView_1.setText(persona.getNombreCompleto());
    itemTextView_2.setText(persona.getEdad()+" años");
    //Retornamos la View
    return convertView;
}
{% endhighlight %}

Ya tenemos todo preparado para comenzar a trabajar en la clase MainActivity.

Crearemos una variable de tipo <i>ListView</i>, a la cual definiremos la ListView que creamos al comenzar este tutorial, y un <i>ArrayList</i> de <i>Persona</i>.

{% highlight java %}
private ListView listPersonas;
private ArrayList<Persona> personas;
{% endhighlight %}

Luego, en la función <i>onCreate()</i> le asignamos la ListView a la variable creada.

{% highlight java %}
listPersonas = (ListView) findViewById(R.id.tutorialListView);
{% endhighlight %}

En éste mismo método, agregaremos manualmente elementos a nuestro ArrayList de Persona.

{% highlight java %}
personas = new ArrayList<>();
personas.add(new Persona("Matías","Salinas",22));
personas.add(new Persona("Juan","Perez",30));
personas.add(new Persona("Pedro","Rodríguez",14));
personas.add(new Persona("Javier","Soto",18));
personas.add(new Persona("Mario","Navarro",21));
{% endhighlight %}

Y ahora comenzaremos a trabajar con el Adapter que creamos anteriormente. Primero le creamos y le asignamos el Adapter a nuestra ListView declarada anteriormente.

{% highlight java %}
ListViewAdapter adapter = new ListViewAdapter(getBaseContext(),personas);
listPersonas.setAdapter(adapter);
{% endhighlight %}

Con lo que llevamos ahora, ya podemos visualizar el ListView de la manera en que quisimos mostrar la información de cada Persona.

Pero eso no es todo, necesitamos capturar el evento de hacer click a cada uno de los item de nuestro ListView, y poder rescatar la información del elemento seleccionado.

{% highlight java %}
listPersonas.setOnItemClickListener(new AdapterView.OnItemClickListener() {
    @Override
    public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
        String msg = "Se ha seleccionado a " + personas.get(position).getNombreCompleto();
        Toast.makeText(getApplicationContext(),msg, Toast.LENGTH_SHORT).show();
    }
});
{% endhighlight %}

Tras todo esto, el resultado es el que he mostrado al principio de este tutorial.

Espero que les sirva en los proyectos que estén realizando. Cualquier duda que tengan, no duden en comentar y preguntar, aquí estaré para ayudarles.

Nos vemos pronto!

---

[1]: {{ site.url }}/assets/images/Tutorial_3/image_1.png
[2]: {{ site.url }}/assets/images/Tutorial_3/image_2.png
[3]: {{ site.url }}/assets/images/Tutorial_3/image_3.png