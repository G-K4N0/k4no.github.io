---
layout: single
title: Consumo de API
excerpt: "Una API (Interfaz de Programación de Aplicaciones es un conjunto de definiciones y protocolos que se usan para diseñar e integrar el software de las aplicaciones )"
date: 2021-08-26
classes: wide
header:
  teaser: https://g-k4n0.github.io/k4no.io/assets/images/android_api/api_android.png
  teaser_home_page: true
  icon: https://g-k4n0.github.io/k4no.io/assets/images/android_logo.png
categories:
  - Android
  - Front end
tags:  
  - android
  - Java
  - volley
---

> Las API son utiles para poder integrar aplicaciones en distintos entornos, esto viene siendo, que una aplicación es hecha en cierto lenguaje de programación puede ofrecer los datos que este procesa a distintas aplicaciones hechas con otras tecnologias, esto hace simple y flexible tanto el diseño, la administración  y por consecuente el uso de las aplicaciones ([API-RedHat](https://www.redhat.com/es/topics/api/what-are-application-programming-interfaces)).

<br>
<p align="center">
<img src="../assets/images/android_api/api_functions.png">
</p>
> **Nota:** Voy a describir las clases y alguna configuración necesaria, las vistas las omitiré porque están en el repositorio.

# Volley
> Necesitamos incluir en las dependecias, la libreria necesaria para poder trabajar con peticiones, en este caso se usará volley, entonces en el **build.gradle** incluimos la siguiente linea `implementation 'com.android.volley:volley:1.2.1'`, posteriormente se sincroniza nuestro proyecto para que sea posible usar las funcionalidades de volley, se puede consultar acerca de la implementacion en la página de android developers acerca de [Volley](https://developer.android.com/training/volley?hl=es-419)  para ver la documentacón.

> Entonces el **build.gradle** quedaria de la siguiente manera.

## build.gradle
```
   dependencies {
   
       implementation 'com.android.volley:volley:1.2.1' //implementación de la libreria de volley, la versión puede variar
       implementation 'androidx.appcompat:appcompat:1.4.2'
       implementation 'com.google.android.material:material:1.6.1'
       implementation 'androidx.constraintlayout:constraintlayout:2.1.4'
       testImplementation 'junit:junit:4.13.2'
       androidTestImplementation 'androidx.test.ext:junit:1.1.3'
       androidTestImplementation 'androidx.test.espresso:espresso-core:3.4.0'
   }
```

## AndroidManifest.xml

> Tenemos que agregar permisos de acceso a internet para poder consumir poder conectarnos a la api en cuestion, por lo tanto en el manifest tenemos que agregar esta linea.

```
<uses-permission android:name="android.permission.INTERNET"/>
```

> Tambien se habilita el trafico de red ClearText en true, si está en **false** los comoponentes como las pilas FTP, HTTP, DownloadManager

```
android:usesCleartextTraffic="true"
```

> Quedando todo de la siguiente manera.

```
   <?xml version="1.0" encoding="utf-8"?>
   <manifest xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:tools="http://schemas.android.com/tools"
       package="com.example.peticiones">
       <uses-permission android:name="android.permission.INTERNET"/>
   
       <application
           android:usesCleartextTraffic="true" 
           android:allowBackup="true"
           android:dataExtractionRules="@xml/data_extraction_rules"
           android:fullBackupContent="@xml/backup_rules"
           android:icon="@mipmap/ic_launcher"
           android:label="@string/app_name"
           android:roundIcon="@mipmap/ic_launcher_round"
           android:supportsRtl="true"
           android:theme="@style/Theme.Peticiones"
           tools:targetApi="31">
           <activity
               android:name=".MainActivity"
               android:exported="true">
               <intent-filter>
                   <action android:name="android.intent.action.MAIN" />
   
                   <category android:name="android.intent.category.LAUNCHER" />
               </intent-filter>
           </activity>
       </application>
   
   </manifest>
```

## MainActivity.java

> Ahora es tiempo de describir el `MainActivity`:

>Primero declaramos un atributo de tipo **RequestQueue**, que se encargará de gestionar automáticamente el envío de las peticiones, la administración de los hilos y la publicación de resultados en la Interfaz Gráfica de Usuario.

```
private RequestQueue colaPeticion;
```
> Seguido de esto, declaramos una variable de tipo **String** el cual almacenará nuestra **url**, indicandole una dirección ip y el puerto al que estariamos conectandonos.

```
String url = "http://192.168.11.21:5000/api/empleados";
```
> Tambien necesitaremos un `adaptador` y un `ArrayList` con los que mostraremos los datos.

```
ArrayList<String> datos;
ArrayAdapter<String> adapter;
```
> En el **onCreate** instanciamos nuestro atributo **RequestQueue**, pasandole un contexto, que en este caso le decimos que seria en el **MainActivity** con la palabra reservada **this**

```
colaPeticiones = Volley.newRequestQueue(this);
```
> nuestro metodo **onCreate** queda de la siguiente manera (los botones y listas son parte de mi UI, varia según las ideas de cada quien).

```
  @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);
           txtId = findViewById(R.id.txtId);
           txtApellidoPaterno = findViewById(R.id.txtApellidoPaterno);
           txtApellidoMaterno = findViewById(R.id.txtApellidMaterno);
           txtNombre = findViewById(R.id.txtNombre);
           txtDireccion = findViewById(R.id.txtDireccion);
           txtTelefono = findViewById(R.id.txtTelefono);
           txtEdad = findViewById(R.id.txtEdad);
   
           btnPost = findViewById(R.id.btnPost);
           btnGet = findViewById(R.id.btnGet);
           btnPut = findViewById(R.id.btnPut);
           btnDelete = findViewById(R.id.btnDelete);
   
           lstLista = findViewById(R.id.lstLista);
   
           colaPeticion = Volley.newRequestQueue(this);
   
           eventosButtons();
           eventoLista();
           getAll();
       }
```

## Metodos

> A continuación describiré los metodos para las distintas operaciones para un **CRUD** usando los 4 principales verbos de **HTTP** (_GET,POST,PUT y DELETE_).

### getAll()
> Este metodo es el que hace uso del verbo **GET** para poder obtener los datos del servidor (Backend).Dentro del metodo declaramos un **JSONArrayRequest** en mi caso la variable lo llamé **getAll** instanciandolo en la misma línea. El constructor del JSONArrayRequest requiere el metodo a usar (GET,POST, PUT, DELETE...), la url, un JSONArray,un listener de respuesta y un listener de errores.

> Entonces al hacer la declaracion e instancia en un principio quedaria de esta manera.

`JSONArrayRequest getAll = new JSONArrayRequest()`

> El constructor estará esperando los argumentos que anteriormente se describieron.
**Nota:** Lo que está entre **< >** son las opciones de cada argumento

- **Metodo:** Indicaremos el metodo HTTP que estariamos ocupando de la siguiente manera. Request.Method.\<GET,POST,PUT,DELETE...>
- **URL:** En este campo indicamos a donde nos estariamos conectando para poder manipular la informacion según el verbo http que estemos utilizando (este ya lo definimos antes como variable global).
- **jsonrequest:** Recibe un JSONArray, si ponemos un `null` es porque no enviaremos ningun dato sin embargo si le pasamos un JSONArray es porque estariamos enviando datos.
- **listener:** Un escuchador que estará esperando la respuesta JSON del servidor.
- **errorListener:** Un escuchador enfocado en los errores, si pasamos como argumento `null` los errores serán ignorados.

> Con esta descripción llega el momento de mostrar como queda el metodo ya con estos campos llenados.

```
      public void getAll(){
          JsonArrayRequest request_getAll = new JsonArrayRequest(
          Request.Method.GET,                  //indicamos el verbo http
          url,                                 //indicamos la url
          null,                                // no enviamos ningun jsonrequest
          new Response.Listener<JSONArray>() { //Comienza el cuerpo del Response.Listener
              @Override
              public void onResponse(JSONArray response) {
                  datos = new ArrayList<>();
                  for (int i = 0; i< response.length(); i++)
                  {
                      try {
                          JSONObject datoJson = response.getJSONObject(i);
                          String dato = datoJson.getString("_id")+ " : "+
                                  datoJson.get("apellidoPaterno") +" : "+
                                  datoJson.get("apellidoMaterno") +" : "+
                                  datoJson.get("nombre") +" : "+
                                  datoJson.get("direccion") +" : "+
                                 datoJson.get("telefono")+ " : "+
                                  datoJson.get("edad");
                          datos.add(dato);
                      } catch (JSONException e) {
                          Toast.makeText(MainActivity.this, "Error en Try ->" + e.getMessage(), Toast.LENGTH_SHORT).show();
                      }
                  }
                  adapter = new ArrayAdapter<>(MainActivity.this, android.R.layout.simple_list_item_1,datos);
                  lstLista.setAdapter(adapter);
              }
          }, new Response.ErrorListener() {  //Comienza el cuerpo del Response.ErrorListener
              @Override
              public void onErrorResponse(VolleyError error) {
                  Toast.makeText(MainActivity.this, "onError GET -->" + error.getMessage(), Toast.LENGTH_SHORT).show();
              }
          });
      colaPeticion.add(request_getAll);
    }
```

> En los siguientes metodos la estructura es la misma, asi que vamos a ver que solo son unos parámetros que se agragan.

### postOne()

> En el metodo guardar estaremos enviando datos, por lo que en el tercer parametro ya no será `null`. Primero crearemos un objeto JSON para pasarlo despues como argumento en nuestra petición, es el único argumento que cambia, entonces la estructura seria de la siguiente manera.

```
      public void postOne() {
          JSONObject json = new JSONObject(); //creo un JSONObject y lo lleno con los datos necesarios dentro de un try-catch
          try {
              json.put("apellidoPaterno", txtApellidoPaterno.getText().toString());
              json.put("apellidoMaterno", txtApellidoMaterno.getText().toString());
              json.put("nombre",txtNombre.getText().toString());
              json.put("direccion",txtDireccion.getText().toString());
              json.put("telefono",txtTelefono.getText().toString());
              json.put("edad",Integer.parseInt(txtEdad.getText().toString()));
          } catch (JSONException e) {
              e.printStackTrace();
          }
  
          // De aqui en adelante la estructura es la misma
          JsonObjectRequest jsonPost = new JsonObjectRequest(
          Request.Method.POST,                                //indicamos el metodo
          url,                                                //pasamos la url
          json,                                               // es aqui donde ya no dejamos en null si no que pasamos el jsonObject que creamos
          new Response.Listener<JSONObject>() {               //Comienza el cuerpo del Response.Listener
              @Override
              public void onResponse(JSONObject response) {
                  if (response.has("status")) //esto del response.has("status") es de acuerdo al Backend del ejercicio
                  {                           // va a variar la lógica según el backend que al que se conecten.
                      Toast.makeText(MainActivity.this,  txtNombre.getText().toString()+" Agregado con exito", Toast.LENGTH_SHORT).show();
                      getAll();
                  }
              }
          }, new Response.ErrorListener() {                  //Comienza el cuerpo del Response.ErrorListener
              @Override
              public void onErrorResponse(VolleyError error) {
                  Toast.makeText(MainActivity.this, "On error POST-->" + error.getMessage(), Toast.LENGTH_SHORT).show();
              }
          });
          colaPeticion.add(jsonPost);   //agregamos la petición a la cola de peticiones.
          limpiar();                    //solo se encarga de limpiar nuestro formulario del UI.
      }
```
### putData()

> En el verbo **put** que seria la parte de update en un crud, necesitamos pasarle un id como parámetro para que en el lado del backend determine su hay o no datos asociados con dicho identificador, para posteriormente ser editado, entonces en la parte del **request** agregaremos una pequeña modificación en la **url**.

```
      public void putData(){
          if (!txtId.getText().equals(""))
          {
              String ide = txtId.getText().toString(); // obtenemos la id del objeto
  
              JSONObject json = new JSONObject();      //creamos un JSONObject a partir de los datos ingresados por parte del usuario
              try {
                  json.put("apellidoPaterno", txtApellidoPaterno.getText().toString());
                  json.put("apellidoMaterno", txtApellidoMaterno.getText().toString());
                  json.put("nombre",txtNombre.getText().toString());
                  json.put("direccion",txtDireccion.getText().toString());
                  json.put("telefono",txtTelefono.getText().toString());
                  json.put("edad",Integer.parseInt(txtEdad.getText().toString()));
              } catch (JSONException e) {
                  e.printStackTrace();
              } 
              JsonObjectRequest jsonObjectPut = new JsonObjectRequest(
              Request.Method.PUT,         //indicamos el tipo de metodo
              url + "/" + ide,            //pasamos la url y le concatenamos un slash  /  y despues le concatenamos el id
              json,                       //le pasamos el jsonObject que lleva los datos ingresados por el usuario
              new Response.Listener<JSONObject>() {
                  @Override
                  public void onResponse(JSONObject response) {
                      if (response.has("status"))
                      {
                          Toast.makeText(MainActivity.this,  "Acualizado con éxito", Toast.LENGTH_SHORT).show();
                          getAll();
                      }
                  }
              }, new Response.ErrorListener() {
                  @Override
                  public void onErrorResponse(VolleyError error) {
                      Toast.makeText(MainActivity.this, "On error PUT-->" + error.getMessage(), Toast.LENGTH_SHORT).show();
                  }
              });
              colaPeticion.add(jsonObjectPut); //añadimos la petición a la cola
              limpiar();
          }
      }
```

### deleteOne()

> Para la parte del delete es la misma situación que el método put en la que debemos pasar un `id` para poder eliminar.

```
public void deleteOne()
      {
          if (!txtId.getText().equals(""))
          {
              String ide = txtId.getText().toString();  //obtenemos el id
              String name_employed = txtNombre.getText().toString(); //el nombre solo lo uso para notificar a quien he eliminado
              JsonObjectRequest jsonObjectDelete = new JsonObjectRequest(
              Request.Method.DELETE,            //indicamos el metodo
              url + "/" + ide,                  // pasamos la url concatenando un / y luego el id
              null,                             // otra vez lo ponemos en null porque esta vez no estamos enviando datos
              new Response.Listener<JSONObject>() {
                  @Override
                  public void onResponse(JSONObject response) {
                      if (response.has("status"))
                      {
                          Toast.makeText(MainActivity.this,  " "+ name_employed + " Eliminado ", Toast.LENGTH_SHORT).show();
                          getAll();
                      }
                  }
              }, new Response.ErrorListener() {
                  @Override
                  public void onErrorResponse(VolleyError error) {
                      Toast.makeText(MainActivity.this, "On error PUT-->" + error.getMessage(), Toast.LENGTH_SHORT).show();
                  }
              });
              colaPeticion.add(jsonObjectDelete); //pasamos la petición a la cola para que sea tramitada
          }
          limpiar();
      }
```
  El ejemplo completo se encuentra en:
- [Peticion-Volley](https://github.com/G-K4N0/Peticion-Volley)

Fuentes:
- [Android Developer](https://developer.android.com/training/volley/request?hl=es-419)
- [JSONArrayRequest](https://javadoc.io/static/com.android.volley/volley/1.1.1/com/android/volley/toolbox/JsonArrayRequest.html)
