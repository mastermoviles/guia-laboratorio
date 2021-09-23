# Serialización de datos

## Introducción

Los programas muy a menudo necesitan enviar datos a un determinado destino, o bien leerlos de una determinada fuente externa, como por ejemplo puede ser un fichero para almacenar datos de forma permanente, o bien enviar datos a través de la red, a memoria, o a otros programas. Esta entrada/salida de datos en Java la realizaremos por medio de _flujos \(streams\)_ de datos, a través de los cuales un programa podrá recibir o enviar datos en serie. Si queremos transferir estructuras de datos complejas, deberemos convertir estas estructuras en secuencias de bytes que puedan ser enviadas a través de un flujo. Esto es lo que se conoce como serialización. Comenzaremos viendo los fundamentos de los flujos de entrada y salida en Java, para a continuación pasar a estudiar los flujos que nos permitirán serializar diferentes tipos de datos Java de forma sencilla.

## Flujos de datos de entrada/salida

Existen varios objetos que hacen de flujos de datos, y que se distinguen por la finalidad del flujo de datos y por el tipo de datos que viajen a través de ellos. Según el tipo de datos  que transporten podemos distinguir:

* Flujos de caracteres
* Flujos de _bytes_

Dentro de cada uno de estos grupos tenemos varios pares de objetos, de los cuales uno nos servirá para leer del flujo y el otro para escribir en él. Cada par de objetos será utilizado para comunicarse con distintos elementos \(memoria, ficheros, red u otros programas\). Estas clases, según sean de entrada o salida y según sean de caracteres o de _bytes_ llevarán distintos sufijos, según se muestra en la siguiente tabla:

|   | **Flujo de entrada / lector** | **Flujo de salida / escritor** |
| :--- | :--- | :--- |
| **Caractéres** | `_Reader` | `_Writer` |
| **Bytes** | `_InputStream` | `_OutputStream` |

Donde el prefijo se referirá a la fuente o sumidero de los datos que puede tomar valores como los que se muestran a continuación:

| **Prefijo** | **Fuente** |
| :--- | :--- |
| `File_` | Acceso a ficheros |
| `Piped_` | Comunicación entre programas mediante tuberías \(_pipes_\) |
| `String_` | Acceso a una cadena en memoria \(solo caracteres\) |
| `CharArray_` | Acceso a un _array_ de caracteres en memoria \(solo caracteres\) |
| `ByteArray_` | Acceso a un _array_ de _bytes_ en memoria \(solo _bytes_\) |

Además podemos distinguir los flujos de datos según su propósito, pudiendo ser:

* Canales de datos, simplemente para leer o escribir datos directamente en

    una fuente o sumidero externo.

* Flujos de procesamiento, que además de enviar o recibir datos realizan algún

    procesamiento con ellos. Tenemos por ejemplo flujos que realizan un filtrado

    de los datos que viajan a través de ellos \(con prefijo `Filter`\),

    conversores datos \(con prefijo `Data`\), _bufferes_ de datos

    \(con prefijo `Buffered`\), preparados para la impresión de elementos

    \(con prefijo `Print`\), etc.

Un tipo de filtros de procesamiento a destacar son aquellos que nos permiten convertir un flujo de _bytes_ a flujo de caracteres. Estos objetos son `InputStreamReader` y `OutputStreamWriter`. Como podemos ver en su sufijo, son flujos de caracteres, pero se construyen a partir de flujos de _bytes_, permitiendo de esta manera acceder a nuestro flujo de _bytes_ como si fuese un flujo de caracteres.

Para cada uno de los tipos básicos de flujo que hemos visto existe una superclase, de la que heredaran todos sus subtipos, y que contienen una serie de métodos que serán comunes a todos ellos. Entre estos métodos encontramos los métodos básicos para leer o escribir caracteres o _bytes_ en el flujo a bajo nivel. En la siguiente tabla se muestran los métodos más importantes de cada objeto:

| Clase | Métodos |
| :--- | :--- |
| `InputStream` | `read(),reset(),available(),close()` |
| `OutputStream` | `write(int b),flush(),close()` |
| `Reader` | `read(),reset(),close()` |
| `Writer` | `write(int c),flush(),close()` |

A parte de estos métodos podemos encontrar variantes de los métodos de lectura y escritura, otros métodos, y además cada tipo específico de flujo contendrá sus propios métodos. Todas estas clases se encuentran en el paquete `java.io`. Para más detalles sobre ellas se puede consultar la especificación de la API de Java.

## Entrada, salida y salida de error estándar

Al igual que en C, en Java también existen los conceptos de entrada, salida, y salida de error estándar. La entrada estándar normalmente se refiere a lo que el usuario escribe en la consola, aunque el sistema operativo puede hacer que se tome de otra fuente. De la misma forma la salida y la salida de error estándar lo que hacen normalmente es mostrar los mensajes y los errores del programa respectivamente en la consola, aunque el sistema operativo también podrá redirigirlas a otro destino. En Java esta entrada, salida y salida de error estándar se tratan de la misma forma que cualquier otro flujo de datos, estando estos tres elementos encapsulados en tres objetos de flujo de datos que se encuentran como propiedades estáticas de la clase `System`:

|   | **Tipo** | **Objeto** |
| :--- | :--- | :--- |
| **Entrada estándar** | `InputStream` | `System.in` |
| **Salida estándar** | `PrintStream` | `System.out` |
| **Salida de error estándar** | `PrintStream` | `System.err` |

Para la entrada estándar vemos que se utiliza un objeto `InputStream` básico, sin embargo para la salida se utilizan objetos `PrintWriter` que facilitan la impresión de texto ofreciendo a parte del método común de bajo nivel `write` para escribir _bytes_, dos métodos más: `print` y `println`. Estas funciones nos permitirán escribir cualquier cadena, tipo básico, o bien cualquier objeto que defina el método `toString` que devuelva una representación del objeto en forma de cadena. La única diferencia entre los dos métodos es que el segundo añade automáticamente un salto de línea al final del texto impreso, mientras que en el primero deberemos especificar explícitamente este salto.

Para escribir texto en la consola normalmente utilizaremos:

```java
System.out.println("Hola mundo");
```

En el caso de la impresión de errores por la salida de error de estándar, deberemos utilizar: 

```java
System.err.println("Error: Se ha producido un error");
```

Además la clase `System` nos permite sustituir estos flujos por defecto por otros flujos, cambiando de esta forma la entrada, salida y salida de error estándar.

> Truco: Podemos ahorrar tiempo si en Eclipse en lugar de escribir `System.out.println` escribimos simplemente `sysout` y tras esto pulsamos _Ctrl + Espacio_.

## Acceso a ficheros

Podremos acceder a ficheros bien por caracteres, o bien de forma binaria \(por _bytes_\). Las clases que utilizaremos en cada caso son:

|   | Lectura | Escritura |
| :--- | :--- | :--- |
| Caracteres | `FileReader` | `FileWriter` |
| Binarios | `FileInputStream` | `FileOutputStream` |

Para crear un lector o escritor de ficheros deberemos proporcionar al constructor el fichero del que queremos leer o en el que queramos escribir. Podremos proporcionar esta información bien como una cadena de texto con el nombre del fichero, o bien construyendo un objeto `File` representando al fichero al que queremos acceder. Este objeto nos permitirá obtener información adicional sobre el fichero, a parte de permitirnos realizar operaciones sobre el sistema de ficheros. A continuación vemos un ejemplo simple de la copia de un fichero carácter a carácter:

```java
public void copia_fichero() {
  int c;
  try {
    FileReader in = new FileReader("fuente.txt");
    FileWriter out = new FileWriter("destino.txt");

    while( (c = in.read()) != -1) {
      out.write(c);
    }

    in.close();
    out.close();

  } catch(FileNotFoundException e1) {
    System.err.println("Error: No se encuentra el fichero");
  } catch(IOException e2) {
    System.err.println("Error leyendo/escribiendo fichero");
  }
}
```

En el ejemplo podemos ver que para el acceso a un fichero es necesario capturar dos excepciones, para el caso de que no exista el fichero al que queramos acceder y por si se produce un error en la E/S. Para la escritura podemos utilizar el método anterior, aunque muchas veces nos resultará mucho más cómodo utilizar un objeto `PrintWriter`con el que podamos escribir directamente líneas de texto:

```java
public void escribe_fichero() {
  FileWriter out = null;
  PrintWriter p_out = null;

  try {
    out = new FileWriter("result.txt");
    p_out = new PrintWriter(out);
    p_out.println(
      "Este texto será escrito en el fichero de salida");

  } catch(IOException e) {
    System.err.println("Error al escribir en el fichero");
  } finally {
    p_out.close();
  }
}
```

## Acceso a ficheros en Android

Hemos visto cómo leer y escribir ficheros en Java, pero cuando ejecutamos una aplicación Android sólo tendremos permiso para acceder a ficheros en determinados directorios.

Cada aplicación tiene su propio directorio para guardar datos privados, al que sólo la aplicación podrá acceder. Para obtener la ruta de dicho directorio podemos utilizar el siguiente código:

```java
File dir = this.getFilesDir();
```

Una vez obtenido el directorio, podemos especificar un fichero dentro de él de la siguiente forma:

```java
File file = new File(dir, "datos.dat");
```

También podremos acceder a ficheros en el almacenamiento externo \(tarjeta SD\).

## Acceso a recursos de la aplicación

En el caso de que nos interese empaquetar ficheros con la aplicación, podemos utilizar la carpeta de _assets_. Todos los recursos que se guarden en dicha carpeta se empaquetarán sin ser alterados \(a diferencia de _res_\).

El directorio de _assets_ se puede añadir a la aplicación desde Android Studio pulsando con el botón derecho sobre _app_ en el explorador del proyecto y seleccionando _New &gt; Folder &gt; Assets Folder_.

Para leer un fichero de dicho directorio podemos utilizar el siguiente código:

```java
InputStream is = getAssets().open("fichero.txt");
```

## Acceso a la red

Podemos también obtener flujos para leer datos a través de la red a partir de una URL. De esta forma podremos obtener por ejemplo información ofrecida por una aplicación web. Lo primero que debemos hacer es crear un objeto `URL` especificando la dirección a la que queremos acceder:

```java
URL url = new URL("http://www.ua.es/es/index.html");
```

A partir de esta URL podemos obtener directamente un flujo de entrada mediante el método `openStream`:

```java
InputStream in = url.openStream();
```

Una vez obtenido este flujo de entrada podremos leer de él o bien transformarlo a otro tipo de flujo como por ejemplo a un flujo de caracteres o de procesamiento. La lectura se hará de la misma forma que cualquier otro tipo de flujo.

## Codificación de datos

Si queremos guardar datos en un fichero binario, enviarlos a través de la red, o en general transferirlos mediante cualquier flujo de E/S, deberemos codificar estos datos en forma de _array_ de _bytes_. Los flujos de procesamiento `DataInputStream` y `DataOutputStream` nos permitirán codificar y descodificar respectivamente los tipos de datos simples en forma de _array_ de _bytes_ para ser enviados a través de un flujo de datos.

Por ejemplo, podemos codificar datos en un _array_ en memoria \(`ByteArrayOutputStream`\) de la siguiente forma:

```java
String nombre = "Jose";
String edad = 25;
ByteArrayOutputStream baos = new ByteArrayOutputStream();
DataOutputStream dos = new DataOutputStream(baos);

dos.writeUTF(nombre);
dos.writeInt(edad);
dos.close();
baos.close();

byte [] datos = baos.toByteArray();
```

Podremos descodificar este _array_ de _bytes_ realizando el procedimiento inverso, con un flujo que lea un _array_ de _bytes_ de memoria \(`ByteArrayInputStream`\):

```java
ByteArrayInputStream bais = new ByteArrayInputStream(datos);
DataInputStream dis = new DataInputStream(bais);
String nombre = dis.readUTF();
int edad = dis.readInt();
```

Si en lugar de almacenar estos datos codificados en una _array_ en memoria queremos guardarlos codificados en un fichero, haremos lo mismo simplemente sustituyendo el flujo canal de datos `ByteArrayOutputStream` por un `FileOutputStream`. De esta forma podremos utilizar cualquier canal de datos para enviar estos datos codificados a través de él.

## Serialización de objetos

Si queremos enviar un objeto a través de un flujo de datos, deberemos convertirlo en una serie de _bytes_. Esto es lo que se conoce como serialización de objetos, que nos permitirá leer y escribir objetos directamente.

Para leer o escribir objetos podemos utilizar los objetos `ObjectInputStream` y `ObjectOutputStream` que incorporan los métodos `readObject` y `writeObject` respectivamente. Los objetos que escribamos en dicho flujo deben tener la capacidad de ser _serializables_. Serán _serializables_ aquellos objetos que implementan la interfaz `Serializable`. Cuando queramos hacer que una clase definida por nosotros sea _serializable_ deberemos implementar dicho interfaz, que no define ninguna función, sólo se utiliza para identificar las clases que son _serializables_. Para que nuestra clase pueda ser _serializable_, todas sus propiedades deberán ser de tipos de datos básicos o bien objetos que también sean _serializables_.

Un uso común de la serialización se realiza en los _Transfer Objetcs_. Este tipo de objetos deben ser serializables para así poderse intercambiar entre todas las capas de la aplicación, aunque se encuentren en máquinas diferentes.

Por ejemplo, si tenemos un objeto como el siguiente:

```java
public class Punto2D implements Serializable {
    private int x;
    private int y;

    public int getX() {
        return x;
    }
    public void setX(int x) {
        this.x = x;
    }
    public int getY() {
        return y;
    }
    public void setY(int y) {
        this.y = y;
    }
}
```

Podríamos enviarlo a través de un flujo, independientemente de su destino, de la siguiente forma:

```java
Punto2D p = crearPunto();
FileOutputStream fos = new FileOutputStream(FICHERO_DATOS);
ObjectOutputStream oos = new ObjectOutputStream(fos);
oos.writeObject(p);
oos.close();
```

En este caso hemos utilizado como canal de datos un flujo con destino a un fichero, pero se podría haber utilizado cualquier otro tipo de canal \(por ejemplo para enviar un objeto Java desde un servidor web hasta una máquina cliente\). En aplicaciones distribuidas los objetos _serializables_ nos permitirán mover estructuras de datos entre diferentes máquinas sin que el desarrollador tenga que preocuparse de la codificación y transmisión de los datos.

Muchas clases de la API de Java son _serializables_, como por ejemplo las colecciones. Si tenemos una serie de elementos en una lista, podríamos serializar la lista completa, y de esa forma guardar todos nuestros objetos, con una única llamada a `writeObject`.

Cuando una clase implemente la interfaz `Serializable` veremos que Eclipse nos da un _warning_ si no añadimos un campo `serialVersionUID`. Este es un código numérico que se utiliza para asegurarnos de que al recuperar un objeto serializado éste se asocie a la misma clase con la que se creó. Así evitamos el problema que puede surgir al tener dos clases que puedan tener el mismo nombre, pero que no sean iguales \(podría darse el caso que una de ellas esté en una máquina cliente, y la otra en el servidor\). Si no tuviésemos ningún código para identificarlas, se podría intentar recuperar un objeto en una clase incorrecta.

Eclipse nos ofrece dos formas de generar este código pulsando sobre el icono del _warning_: con un valor por defecto, o con un valor generado automáticamente. Será recomendable utiliar esta segunda forma, que nos asegura que dos clases distintas tendrán códigos distintos.

## Ejercicios de Serialización

### Lectura de ficheros \(0,25 puntos\)

_a\)_ Crea un nuevo proyecto llamado `EjemploLectura`, con los datos por defecto del asistente.

_b\)_ Sube el proyecto a un nuevo repositorio git en bitbucket. Para ello:

* Crea un nuevo repositorio en bitbucket llamado `mastermoviles-java-lectura`
* Desde el proyecto de Android Studio activa el control de versiones con _VCS &gt; Enable Version Control Integration_, seleccionando _git_ como sistema de control de versiones.
* Habrá aparecido una nueva pestaña en la barra inferior del entorno, llamada _Version Control_. Ábrela y selecciona todos los fichero sin versionar \(_Unversiones Files_\). Tras esto selecciona _VCS &gt; Git &gt; Add_ para añadirlos todos a _git_.
* Ya podemos hacer _commit_ con _VCS &gt; Commit Changes …_. Introduce como mensaje _Initial commit_ y en el botón de _Commit_ selecciona _Commit and Push …_. 
* En la pantalla para hacer _push_, deberás definir el servidor remoto. Para ello pulsa sobre _Define remote_ e introduce la URL de tipo HTTPS de tu repositorio que puedes obtener a partir de bitbucket \(desde la opción _Clone_\).
* Pulsa sobre el botón _Push_ y el proyecto se subirá a bitbucket.

_c\)_ Haz que el `TextView` que aparece en el _layout_ por defecto ocupe toda la pantalla \(puedes hacerlo desde el editor visual\), y dale como identificador `tvContent`.

_d\)_ Añade un directorio de _assets_ al proyecto, pulsando con el botón derecho sobre _app_ en el explorador del proyecto, y seleccionando _New &gt; Folder &gt; Assets Folder_.

_e\)_ Añade a dicho directorio un fichero de texto cualquiera creado por ti, de nombre `texto.txt`. No utilices acentos ni caracteres especiales.

_f\)_ Puedes abrir un flujo para leer el fichero con:

```java
InputStream is = getAssets().open("texto.txt");
```

¿Aparece algún error? ¿Por qué? Pasa el ratón por encima del código subrayado en rojo para ver los detalles el error. Pon el cursor en dicho código y pulsa _alt + enter_ para ver las soluciones rápidas al error, y selecciona la más adecuada.

_g\)_ Lee el fichero con el siguiente código en el método `onCreate` de tu actividad principal:

```java
TextView tvContent = (TextView)findViewById(R.id.tvContent);

String texto = "";
int c;

while((c = is.read()) != -1) {
    texto += (char)c;
}

tvContent.setText(texto);
```

Corrige los errores que aparezcan de forma adecuada, siguiendo el método indicado en el punto anterior.

_h\)_ Introduce ahora algún acento en el texto. ¿Qué ocurre? ¿Por qué? _i\)_ Puedes convertir el flujo de _bytes_ anterior en un flujo de caracteres con:

```java
InputStreamReader isr = null;
isr = new InputStreamReader(is);
```

Utilízalo para conseguir que la lectura soporte caracteres especiales.

_j\)_ Consulta la documentación la clase `BufferedReader` en la referencia de Android en developer.android.com. Modifica el código de lectura para leer el fichero línea a línea utilizando el método `readLine()`. ¿Qué ventajas tiene esta forma de lectura?

### Acceso a la red \(0,5 puntos\)

Para este ejercicio vamos a utilizar una plantilla disponible en bitbucket, dentro de los proyectos del grupo _Master Moviles_. Deberemos:

_a\)_ Entrar en nuestra cuenta de bitbucket, y hacer un _Fork_ del proyecto `mastermoviles-java-serializacion`.

_b\)_ Hacer un _checkout_ del proyecto en Android Studio desde el repositorio bitbucket. Es **importante** hacer el _checkout_ de nuestra copia, no del repositorio original del grupo _Master Moviles_, ya que sobre éste último no tenemos permiso de escritura.

_c\)_ En `MainActivity`, completa el método `getContent` para que lea el contenido de la URL que se le pasa como parámetro y devuelva como resultado una cadena \(`String`\) con el contenido leído. En caso de haber algún error de lectura, devolverá la excepción correspondiente \(la propagará hacia arriba\). Utiliza un objeto `BufferedReader` para realizar la lectura.

_d\)_ ¿Qué ocurre al ejecutar el proyecto e intentar cargar una URL? Comprueba el error que se produce en tiempo de ejecución, e introduce las modificaciones necesarias para que funcione correctamente. Una vez funcione correctamente, realiza un _commit_.

_e\)_ Crea una versión alternativa utilizando una `AsyncTask`. Tomará como entrada la URL \(`String`\), y producirá como salida el contenido \(también `String`\). No es necesario publicar progreso. Realiza un _commit_ de la nueva versión.

### Serialización de datos \(0,5 puntos\)

En el proyecto anterior, existe una pantalla de preferencias accesible desde el menú de opciones. Vamos a hacer que esta pantalla nos permita guardar las preferencias en un fichero.

_f\)_ Introduce en los métodos `savePreferences` y `loadPreferences` de la actividad `PreferencesActivity` el código para guardar y cargar respectivamente todos los campos de un objeto de tipo `Preferences`. Utiliza para ello flujos de tipo `DataOutputStream` y `DataInputStream`.

Vamos a guardar los datos en un fichero `datos.dat` dentro del directorio privado de la aplicación. Podemos obtener la ruta de dicho fichero con:

```java
File dir = this.getFilesDir();
File file = new File(dir, "datos.dat");
```

Realiza un _commit_ del proyecto una vez funcione correctamente.

_g\)_ Crea una versión alternativa de los métodos de guardado y carga utilizando serialización de objetos mediante la interfaz `Serializable`. Realiza de nuevo un _commit_ del proyecto.

