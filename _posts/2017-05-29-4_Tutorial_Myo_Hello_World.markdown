---
title: "Hola Mundo utilizando MYO ARMBAND"
layout: post
date: 2017-05-29 20:00
image: /assets/images/markdown.jpg
headerImage: false
tag:
- Dispositivos Móviles
- Android
- Android Studio
- Tutorial
- Myo
star: false
projects: true
category: project
author: matiassalinas
description: Desarrollando una aplicación básica con Myo Armband.
---

El siguiente tutorial fue trabajado en Android Studio 2.3.2, utilizando el Sistema Operativo OS X El Capitán, versión 10.11.6. SDK Mínimo: 5.0 Lollipop. Myo Android SDK: 0.10.0

---

Hoy vengo a cumplir lo que les he prometido anteriormente, una primera aplicación básica utilizando Myo Armband. ¿No conoces Myo? Te invito a revisar el siguiente enlace: [Qué es Myo]({{ site.url }}/Myo/).

Para este proyecto, utilizaremos las mismas configuraciones que se vieron en el primer tutorial de este blog: [Creado Proyectos en Android Studio]({{ site.url }}/1_Tutorial_Android_Studio/).

La aplicacion que haremos para este tutorial, es la típica que se hace al comenzar a desarrollar en algo nuevo, el famoso "Hello World" o "Hola Mundo".

Si bien Myo tiene un tutorial de esta aplicación, he decidido hacer uno propio ya que tuve una serie de problemas que lo más probable más de uno los tendrá al comenzar a desarrollar con Myo. Además, nunca está de más ayudar a los que tienen problemas con el Inglés.

Bien, para comenzar, debemos descargar el (Android SDK)[https://developer.thalmic.com/start/] de la página de Thalmic.

![Markdowm Image][1]{: class="bigger-image" }

<div class="side-by-side">
   <div class="toleft">
        <p>Se descargará la carpeta "myo-android-sdk-0.10.0", la cual contiene el Android SDK, y unos ejemplos de la utilización de MYO. Una vez dentro de myo-android-sdk-0.10.0, debemos copiar la carpeta "myorepository" y pegarla en la carpeta principal de nuestro proyecto. A continuación les muestro una imágen de como se debe ver.</p>
    </div>
    <div class="toright">
        <img class="image" src="{{ site.url }}/assets/images/Tutorial_4/image_2.png" alt="Image_2">
    </div>
</div>

Hasta aquí, el tutorial oficial de Myo dice que estaríamos bien para comenzar a desarrollar con este dispositivo. Sin embargo, tuve unos problemas con la funcion Hub.init (que revisaremos después), que provocaba que la aplicación no cargara en mi dispositivo movil. La solución fue copiar unos archivos que se encuentran en myo-android-sdk-0.10.0/Eclipse/MyoSdk/libs. Copia todos los archivos menos myosdk.jar. Ahora, hay que volver a la carpeta de nuestro proyecto, e ir a la carpeta: [TU_PROYECTO]/app/src/main/, crear la carpeta "jniLibs", y colocar los archivos que copiamos anteriormente. 

![Markdowm Image][3]{: class="bigger-image" }

Ahora, en Android Studio, debemos indicar en nuestro Gradle que utilizaremos el MyoSDK. En el archivo build.gradle (Module:app), en dependencies, agregamos las siguientes lineas:

{% highlight java %}
repositories {
    maven {
        url '../myorepository'
    }
}
compile('com.thalmic:myosdk:0.10.+@aar')
{% endhighlight %}

En url se indica donde se encuentra la carpeta del SDK. Te recuerdo que la colocamos en la carpeta principal del proyecto.

Sincronizamos el Gradle, esperando que finalice correctamente. Si tienes un error revisa las configuraciones anteriores, tal vez te saltaste un paso!

Ya teniendo configurado el Gradle, modificaremos el AndroidManifest.xml. En esta etapa hay diferencias con el tutorial oficial de Myo, ya que los permisos que se indicaban en su documentación me dieron errores a mi y a mucha gente que trabaja con este dispositivo. Por lo tanto, les recomiendo agregar los siguientes permisos:

{% highlight xml %}
<uses-feature
    android:name="android.hardware.bluetooth_le"
    android:required="true" />

<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
{% endhighlight %}

El permiso de bluetooth es para conectar nuestro móvil al Myo.

El permiso "ACCESS_COARSE_LOCATION" es requerido para enviar datos de uso a Thalmic Labs.

Con todo esto, ya tenemos configurado todo para comenzar a desarrollar con Myo.

Primero, modificamos el layout principal del proyecto a algo similar a esto:

![Markdowm Image][4]{: class="bigger-image" }

Este layout tiene dos textview, "lock_state" y "text". El primero contiene el texto que se muestra al estar bloqueado el dispositivo Myo, y el segundo muestra "Hola Mundo", el cual se moverá al mover el brazo con el Myo, y cambiará de texto al realizar alguna acción.

<div class="side-by-side">
   <div class="toleft">
        <p>Ahora en "res", crearemos la carpeta "menu", y dentro de esta, el archivo "sync.xml". Haremos esto para tener un menú para sincronizar nuestro Myo con el móvil. El menú solo será un botón que será mostrado en la parte superior derecha del dispositivo.</p>
    </div>
    <div class="toright">
        <img class="image" src="{{ site.url }}/assets/images/Tutorial_4/image_5.png" alt="Image_5">
    </div>
</div>

El archivo sync.xml contendrá las siguientes lineas:

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto" >
    <item android:id="@+id/sync"
        android:title="Sincronizar"
        app:showAsAction="always"/>
</menu>
{% endhighlight %}

En nuestro archivo strings.xml agregaremos unos textos, correspondientes a cada acción que se realiza con el Myo

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<string name="arm_left">Brazo izquierdo</string>
<string name="arm_right">Brazo derecho</string>
<string name="hello_world">Hola Mundo</string>
<string name="unlocked">Desbloqueado</string>
<string name="locked">Bloqueado</string>
<string name="pose_fist">Puño</string>
<string name="pose_wavein">Movimiento hacia adentro</string>
<string name="pose_waveout">Movimiento hacia afuera</string>
<string name="pose_fingersspread">Mano abierta</string>
{% endhighlight %}

Ahora comienza lo bueno, a modificar MainActivity.java !!

Primero crearemos dos variables en la clase.

{% highlight java %}
private TextView mTextView;
private TextView mLockStateView;
{% endhighlight %}

* mTextView para el texto "Hola Mundo".
* mLockStateView para el bloqueo y desbloqueo del Myo.

Luego, creamos un listener del dispositivo Myo, en donde estaran las funciones que entrega Thalmics para trabajar con este dispositivo. Comprender cada una de las funciones es vital para desarrollar con Myo.

{% highlight java %}
private DeviceListener mListener = new AbstractDeviceListener() {
    // onConnect() cuando el Myo se conecta.
    @Override
    public void onConnect(Myo myo, long timestamp) {
        mTextView.setTextColor(Color.CYAN);
    }
    // onDisconnect() cuando el Myo se desconecta
    @Override
    public void onDisconnect(Myo myo, long timestamp) {
        mTextView.setTextColor(Color.RED);
    }
    // onArmSync() cuando el Myo se sincroniza. Se mostrara en que brazo se tiene el dispositivo
    @Override
    public void onArmSync(Myo myo, long timestamp, Arm arm, XDirection xDirection) {
        mTextView.setText(myo.getArm() == Arm.LEFT ? R.string.arm_left : R.string.arm_right);
    }
    // onArmUnsync() cuando el Myo se desincroniza
    @Override
    public void onArmUnsync(Myo myo, long timestamp) {
        mTextView.setText(R.string.hello_world);
        //addHistorial("Desincronizado");
    }
    // onUnlock() cuando el Myo se desbloquea
    @Override
    public void onUnlock(Myo myo, long timestamp) {
        mLockStateView.setText(R.string.unlocked);
    }
    // onLock() cuando el Myo se bloquea
    @Override
    public void onLock(Myo myo, long timestamp) {
        mLockStateView.setText(R.string.locked);
    }
    // onOrientationData() cuando se mueve el brazo con el Myo
    @Override
    public void onOrientationData(Myo myo, long timestamp, Quaternion rotation) {
        // Calculate Euler angles (roll, pitch, and yaw) from the quaternion.
        float roll = (float) Math.toDegrees(Quaternion.roll(rotation));
        float pitch = (float) Math.toDegrees(Quaternion.pitch(rotation));
        float yaw = (float) Math.toDegrees(Quaternion.yaw(rotation));
        // Adjust roll and pitch for the orientation of the Myo on the arm.
        if (myo.getXDirection() == XDirection.TOWARD_ELBOW) {
            roll *= -1;
            pitch *= -1;
        }
        // Next, we apply a rotation to the text view using the roll, pitch, and yaw.
        mTextView.setRotation(roll);
        mTextView.setRotationX(pitch);
        mTextView.setRotationY(yaw);
    }
    // onPose() cuando se detecta una accion
    @Override
    public void onPose(Myo myo, long timestamp, Pose pose) {
        // Handle the cases of the Pose enumeration, and change the text of the text view
        // based on the pose we receive.
        switch (pose) {
            case UNKNOWN:
                mTextView.setText(getString(R.string.hello_world));
                break;
            case REST:
            case DOUBLE_TAP:
                int restTextId = R.string.hello_world;
                switch (myo.getArm()) {
                    case LEFT:
                        restTextId = R.string.arm_left;
                        break;
                    case RIGHT:
                        restTextId = R.string.arm_right;
                        break;
                }
                mTextView.setText(getString(restTextId));
                break;
            case FIST:
                mTextView.setText(getString(R.string.pose_fist));
                break;
            case WAVE_IN:
                mTextView.setText(getString(R.string.pose_wavein));
                break;
            case WAVE_OUT:
                mTextView.setText(getString(R.string.pose_waveout));
                break;
            case FINGERS_SPREAD:
                mTextView.setText(getString(R.string.pose_fingersspread));
                break;
        }
        if (pose != Pose.UNKNOWN && pose != Pose.REST) {
            // Tell the Myo to stay unlocked until told otherwise. We do that here so you can
            // hold the poses without the Myo becoming locked.
            myo.unlock(Myo.UnlockType.HOLD);
            // Notify the Myo that the pose has resulted in an action, in this case changing
            // the text on the screen. The Myo will vibrate.
            myo.notifyUserAction();
        } else {
            // Tell the Myo to stay unlocked only for a short period. This allows the Myo to
            // stay unlocked while poses are being performed, but lock after inactivity.
            myo.unlock(Myo.UnlockType.TIMED);
        }
    }
};
{% endhighlight %}

Ya teniendo el listener con las funciones de Myo, debemos obtener los textview, e inicializar el Hub de Myo en la función onCreate. Para esto, añadimos las siguientes lineas de código.

{% highlight java %}
mLockStateView = (TextView) findViewById(R.id.lock_state);
mTextView = (TextView) findViewById(R.id.text);
//Hub Singleton
Hub hub = Hub.getInstance();
if (!hub.init(this)) {
    Toast.makeText(this, "No se pudo inicializar el Hub", Toast.LENGTH_SHORT).show();
    finish();
    return;
}
// DeviceListener callbacks.
hub.addListener(mListener);
{% endhighlight %}

Con esto solo nos faltaria sincronizar el Myo con el dispositivo movil. Para esto, debemos hacer funcional el menú que creamos anteriormente.

{% highlight java %}
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    MenuInflater inflater = getMenuInflater();
    inflater.inflate(R.menu.sync, menu);
    return true;
}

@Override
public boolean onOptionsItemSelected(MenuItem item) {
    // Handle item selection
    switch (item.getItemId()) {
        case R.id.sync:
            Toast.makeText(this, "Sync", Toast.LENGTH_SHORT).show();
            Intent intent = new Intent(this, ScanActivity.class);
            startActivity(intent);
            return true;
        default:
            return super.onOptionsItemSelected(item);
    }
}
{% endhighlight %}

Si te fijas, al momento de hacer click en el item con id "sync" (item del menú), se abrirá una nueva actividad "ScanActivity.class". Esta actividad viene incluida en el SDK de Myo, y escanea a través del Bluetooth en busca de un dispositivo Myo.

Y con esto se concluye este cuarto tutorial. Espero que les sirva a la hora de comenzar a desarrollar con este dispositivo.

El código de éste tutorial lo puedes ver en mi github: [GITHUB](https://github.com/matiassalinas/tutorial_myo)



---


<i>Fuente: [DEVELOPER THALMIC ANDROID SDK](https://developer.thalmic.com/docs/api_reference/android/index.html); Última vez visitado: Lunes 29 de Mayo, 2017</i><BR>

[1]: {{ site.url }}/assets/images/Tutorial_4/image_1.png
[3]: {{ site.url }}/assets/images/Tutorial_4/image_3.png
[4]: {{ site.url }}/assets/images/Tutorial_4/image_4.png
