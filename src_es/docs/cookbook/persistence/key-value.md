---
title: Almacenando datos clave-valor en disco
prev:
  title: Leyendo y Escribiendo Archivos
  path: /docs/cookbook/persistence/reading-writing-files
next:
  title: Reproducir y pausar un video
  path: /docs/cookbook/plugins/play-video
---

Si tienes una colección relativamente pequeña de pares clave-valor que te gustaría guardar,
podemos usar el plugin 
[shared_preferences]({{site.pub}}/packages/shared_preferences).

Normalmente tendrías que escribir integraciones nativas de plataforma para almacenar 
datos en ambas plataformas. Afortunadamente, el plugin 
[shared_preferences]({{site.pub}}/packages/shared_preferences)
se puede usar para conservar los datos de clave-valor en el disco. El plugin de preferencias 
compartidas ajusta `NSUserDefaults` en iOS y `SharedPreferences` en Android, proporcionando un
almacenamiento persistente para datos simples.

## Instrucciones

  1. Añadir la dependencia
  2. Guardar Datos
  3. Leer Datos
  4. Remover Datos

## 1. Añadir la dependencia

Antes de empezar, necesitas añadir el plugin 
[shared_preferences]({{site.pub}}/packages/shared_preferences) 
al archivo `pubspec.yaml`:

```yaml
dependencies:
  flutter:
    sdk: flutter
  shared_preferences: "<la versión más reciente>"
```

## 2. Guardar Datos

Para persistir los datos, usa los métodos setter proporcionados por la clase 
`SharedPreferences`. Los métodos Setter están disponibles para varios tipos primitivos, 
como `setInt`, `setBool`, y `setString`.

Los métodos Setter hacen dos cosas: primero, actualizan de forma sincrónica el par 
clave-valor en la memoria. Luego, persisten los datos en el disco.

<!-- skip -->
```dart
// obtener preferencias compartidas
final prefs = await SharedPreferences.getInstance();

// fijar valor
prefs.setInt('counter', counter);
```

## 3. Leer datos

Para leer datos, usa el método Getter apropiado provisto por el
`SharedPreferences` clase. Para cada setter hay un getter correspondiente.
Por ejemplo, puedes usar los métodos `getInt`, `getBool`, y `getString`.

<!-- skip -->
```dart
final prefs = await SharedPreferences.getInstance();

// Intenta leer datos de la clave del contador. Si no existe, retorna 0.
final counter = prefs.getInt('counter') ?? 0;
```

## 4. Remover datos

Para eliminar datos, usa el método `remove`.

<!-- skip -->
```dart
final prefs = await SharedPreferences.getInstance();

prefs.remove('counter');
```

## Tipos soportados

Si bien es fácil y conveniente usar el almacenamiento de clave-valor, tiene limitaciones:

- Solo se pueden usar tipos primitivos: `int`, `double`, `bool`, `string` y 
`stringList`
- No está diseñado para almacenar una gran cantidad de datos.

Para obtener más información acerca de Preferencias Compartidas en Android, visite
[Cómo utilizar preferencias 
compartidas]({{site.android-dev}}/guide/topics/data/data-storage#pref)
en el sitio web de desarrolladores de Android.

## Prueba de soporte

Puede ser una buena idea probar el código que persiste datos usando 
`shared_preferences`. Para hacerlo, necesitas simular el 
`MethodChannel` utilizado por la biblioteca `shared_preferences`.

Puedes llenar `SharedPreferences` con valores iniciales en tus pruebas ejecutando 
el siguiente código en un método `setupAll` en tus archivos de prueba:

<!-- skip -->
```dart
const MethodChannel('plugins.flutter.io/shared_preferences')
  .setMockMethodCallHandler((MethodCall methodCall) async {
    if (methodCall.method == 'getAll') {
      return <String, dynamic>{}; // establecer valores iniciales aquí si lo desea
    }
    return null;
  });
```

## Ejemplo

```dart
import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  // Este widget es la raíz de la aplicación.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Shared preferences demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: MyHomePage(title: 'Shared preferences demo'),
    );
  }
}

class MyHomePage extends StatefulWidget {
  MyHomePage({Key key, this.title}) : super(key: key);
  final String title;

  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;

  @override
  void initState() {
    super.initState();
    _loadCounter();
  }

  //Cargando el valor del contador en el inicio
  _loadCounter() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    setState(() {
      _counter = (prefs.getInt('counter') ?? 0);
    });
  }

  //Incrementando el contador después del clic
  _incrementCounter() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    setState(() {
      _counter = (prefs.getInt('counter') ?? 0) + 1;
      prefs.setInt('counter', _counter);
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.title),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text(
              'You have pushed the button this many times:',
            ),
            Text(
              '$_counter',
              style: Theme.of(context).textTheme.display1,
            ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        tooltip: 'Increment',
        child: Icon(Icons.add),
      ), // Esta coma final hace que el formateo automático sea más agradable para los métodos de compilación.
    );
  }
}
```
