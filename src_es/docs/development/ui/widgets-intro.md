---
title: Introducción a los Widgets
---

{% comment %} This page needs more cleanup. API references should be accumulated
at the end of the page to avoid repeating links. {% endcomment -%}

{% assign api = site.api | append: '/flutter' -%}

Los widgets de Flutter son construidos usando un moderno framework que toma
inspiración de [React](https://reactjs.org). La idea central es
que construyas tu UI de widgets. Los Widgets describen cómo debería ser su vista,
dada su configuración y estado actuales. Cuando el estado de un widget cambia,
el widget reconstruye su descripción, que el framework difiere de la descripción
anterior para determinar los cambios mínimos necesarios en el árbol de renderizado
subyacente para la transición de un estado al siguiente.

{{site.alert.note}}
  Si quieres conocer mejor a Flutter sumergiéndote en algo de código,
  echa un vistazo a [Codelab Layout 
  Básico](/docs/codelabs/layout-basics), [Construyendo Layouts en
  Flutter](/docs/development/ui/layout), y [Añadir Interactividad
  a tu App Flutter](/docs/development/ui/interactive).
{{site.alert.end}}

## Hola Mundo

La aplicación mínima de Flutter simplemente llama a la función [runApp()][] con un widget

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(
    Center(
      child: Text(
        'Hello, world!',
        textDirection: TextDirection.ltr,
      ),
    ),
  );
}
```

La función [runApp()][] toma el
[Widget]({{api}}/widgets/Widget-class.html) dado y lo convierte en la raíz del 
árbol de widgets. En este ejemplo, el árbol de widgets consiste de dos widgets, el widget
[Center]({{api}}/widgets/Center-class.html) y su hijo, el widget
[Text]({{api}}/widgets/Text-class.html).El framework obliga al widget raíz a 
cubrir la pantalla, lo que significa que el texto "Hello, world!" termina centrado 
en la pantalla. La dirección del texto debe especificarse en esta instancia; cuando se 
usa el widget MaterialApp, este lo hace por ti, como se demuestra más 
adelante.

Al escribir una aplicación, comúnmente creará nuevos widgets que son subclases de,
ya sea [StatelessWidget]({{api}}/widgets/StatelessWidget-class.html) o
[StatefulWidget]({{api}}/widgets/StatefulWidget-class.html), dependiendo de 
si su widget gestiona algún estado. El trabajo principal de un widget es implementar una 
función de construcción [build]({{api}}/widgets/StatelessWidget/build.html), que describe 
el widget en términos de otros widgets de nivel inferior. El framework construye estos 
widgets en orden, hasta que el proceso toca fondo en widgets que representan 
el [RenderObject]({{api}}/rendering/RenderObject-class.html) subyacente, el cual 
calcula y describe la geometría del widget.

## Widgets básicos

Flutter viene con un conjunto de potentes widgets básicos, de los cuales los siguientes
son de uso muy común:

 * [Text]({{api}}/widgets/Text-class.html): El widget
   [Text]({{api}}/widgets/Text-class.html) te permite crear una cadena de texto 
   con estilo, dentro de tu aplicación.

 * [Row]({{api}}/widgets/Row-class.html),
   [Column]({{api}}/widgets/Column-class.html): Estos widgets flexibles te 
   permiten crear layouts flexibles tanto en la dirección horizontal
   ([Row]({{api}}/widgets/Row-class.html)) como en la vertical 
   ([Column]({{api}}/widgets/Column-class.html)). Su diseño se basa 
   en el modelo de layout flexbox de la web.

 * [Stack]({{api}}/widgets/Stack-class.html): En lugar de estar orientados linealmente 
   (ya sea horizontal o verticalmente), un widget
   [Stack]({{api}}/widgets/Stack-class.html) te permite apilar los widgets uno 
   encima del otro en el orden como se pintan. Puedes utilizar el widget
   [Positioned]({{api}}/widgets/Positioned-class.html) en los hijos de un
   [Stack]({{api}}/widgets/Stack-class.html) para posicionarlos en relación con 
   el borde superior, derecho, inferior o izquierdo del stack. Los Stacks se basan en 
   el modelo de layout de posicionamiento absoluto en la web.

 * [Container]({{api}}/widgets/Container-class.html): El widget 
   [Container]({{api}}/widgets/Container-class.html) te permite crear 
   un elemento visuales rectangular. Un contenedor puede ser decorado con un
   [BoxDecoration]({{api}}/painting/BoxDecoration-class.html), como un fondo, 
   un borde o una sombra. Un
   [Container]({{api}}/widgets/Container-class.html) también puede tener márgenes, 
   relleno interno y restricciones aplicadas a su tamaño. Además, un
   [Container]({{api}}/widgets/Container-class.html) puede transformarse 
   en un espacio tridimensional utilizando una matriz.

A continuación, se presentan algunos widgets simples que combinan estos y otros widgets:

```dart
import 'package:flutter/material.dart';

class MyAppBar extends StatelessWidget {
  MyAppBar({this.title});

  // Los campos en una subclase de Widgets siempre están marcados como "final".

  final Widget title;

  @override
  Widget build(BuildContext context) {
    return Container(
      height: 56.0, // en píxeles lógicos
      padding: const EdgeInsets.symmetric(horizontal: 8.0),
      decoration: BoxDecoration(color: Colors.blue[500]),
      // Row es un diseño horizontal y lineal.
      child: Row(
        // <Widget> es el tipo de artículos en la lista.
        children: <Widget>[
          IconButton(
            icon: Icon(Icons.menu),
            tooltip: 'Navigation menu',
            onPressed: null, // null desactiva el botón
          ),
          // Expanded expande a su hijo para rellenar el espacio disponible.
          Expanded(
            child: title,
          ),
          IconButton(
            icon: Icon(Icons.search),
            tooltip: 'Search',
            onPressed: null,
          ),
        ],
      ),
    );
  }
}

class MyScaffold extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // Material es una hoja de papel conceptual en la que aparece la UI.
    return Material(
      // Column es un diseño vertical y lineal.
      child: Column(
        children: <Widget>[
          MyAppBar(
            title: Text(
              'Example title',
              style: Theme.of(context).primaryTextTheme.title,
            ),
          ),
          Expanded(
            child: Center(
              child: Text('Hello, world!'),
            ),
          ),
        ],
      ),
    );
  }
}

void main() {
  runApp(MaterialApp(
    title: 'My app', // utilizado por el conmutador de tareas del sistema operativo
    home: MyScaffold(),
  ));
}
```

Asegúrate de tener una línea `uses-material-design: true` en la sección `flutter`
de tu archivo `pubspec.yaml`. Permite utilizar el conjunto predefinido de
[Iconos de Material Design](https://design.google.com/icons/).

```yaml
name: my_app
flutter:
  uses-material-design: true
```

Muchos widgets Material Desgin necesitan estar dentro de un
[MaterialApp]({{api}}/material/MaterialApp-class.html) para mostrarse correctamente, con 
el fin de heredar los datos del Theme. Por lo tanto, ejecutamos la aplicación con un
[MaterialApp]({{api}}/material/MaterialApp-class.html).

El widget `MyAppBar` crea un
[Container]({{api}}/widgets/Container-class.html) con una altura de 56 
píxeles independientes del dispositivo con un padding de 8 píxeles, tanto a la izquierda 
como a la derecha. Dentro del contenedor, `MyAppBar` usa un layout 
[Row]({{api}}/widgets/Row-class.html) para organizar sus hijos. El hijo 
del medio, el widget `title`, se marca como
[Expanded]({{api}}/widgets/Expanded-class.html), lo que significa 
que se expande para rellenar cualquier espacio disponible
que no haya sido consumido por los otros widgets hijos. Puedes tener múltiples
[Expanded]({{api}}/widgets/Expanded-class.html) hijos y determinar la 
proporción en la que consumen el espacio disponible utilizando el argumento
[flex]({{api}}/widgets/Expanded-class.html#flex) para
[Expanded]({{api}}/widgets/Expanded-class.html).

El widget `MyScaffold` organiza a sus hijos en una columna vertical. En la
parte superior de la columna se coloca una instancia de `MyAppBar`, pasándole un
widget [Text]({{api}}/widgets/Text-class.html) para usarlo como su título. Pasar 
widgets como argumentos de widgets es una técnica muy poderosa que te 
permite crear widgets genéricos que pueden ser reutilizados en 
una amplia variedad de formas. Finalmente, `MyScaffold` 
utiliza un [Expanded]({{api}}/widgets/Expanded-class.html) 
para rellenar el espacio restante con su cuerpo, 
que consiste en un mensaje centrado.

Para más información, mira [Layouts](/docs/development/ui/widgets/layout).

## Utilizando Material Components

Flutter proporciona una serie de widgets que te ayudan a crear aplicaciones que
siguen el Material Design. Una aplicación Material comienza con el widget
[MaterialApp]({{api}}/material/MaterialApp-class.html)
que construye una serie de widgets en la raíz de tu aplicación, incluyendo un
[Navigator]({{api}}/widgets/Navigator-class.html), que gestiona una pila de 
widgets identificados por strings, también conocidas como "routes". El
[Navigator]({{api}}/widgets/Navigator-class.html) te permite una transición 
sin problemas entre pantallas de su aplicación. Utilizar el widget
[MaterialApp]({{api}}/material/MaterialApp-class.html)
es totalmente opcional, pero es una buena práctica.

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MaterialApp(
    title: 'Flutter Tutorial',
    home: TutorialHome(),
  ));
}

class TutorialHome extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // Scaffold es un layout para la mayoría de los Material Components.
    return Scaffold(
      appBar: AppBar(
        leading: IconButton(
          icon: Icon(Icons.menu),
          tooltip: 'Navigation menu',
          onPressed: null,
        ),
        title: Text('Título de ejemplo'),
        actions: <Widget>[
          IconButton(
            icon: Icon(Icons.search),
            tooltip: 'Search',
            onPressed: null,
          ),
        ],
      ),
      // el body es la mayor parte de la pantalla.
      body: Center(
        child: Text('Hello, world!'),
      ),
      floatingActionButton: FloatingActionButton(
        tooltip: 'Add', // utilizado por las tecnologías de accesibilidad para discapacitados
        child: Icon(Icons.add),
        onPressed: null,
      ),
    );
  }
}
```

Ahora que el código ha cambiado de `MyAppBar` y `MyScaffold` a los widgets
[AppBar]({{api}}/material/AppBar-class.html) y
[Scaffold]({{api}}/material/Scaffold-class.html), 
de `material.dart`, 
la aplicación se está empezando a ver un poco más Material. 
Por ejemplo, la barra de aplicaciones tiene una sombra y el texto hereda automáticamente 
el estilo correcto. También se añade acción flotante.

Note que estamos pasando de nuevo los widgets como argumentos a otros widgets. El widget
[Scaffold]({{api}}/material/Scaffold-class.html) toma un número de widgets diferentes 
como argumentos con nombre, cada uno de los cuales se coloca en el layout 
del Scaffold en el lugar apropiado. Del mismo modo, el widget
[AppBar]({{api}}/material/AppBar-class.html) nos permite pasarle widgets 
para el [leading]({{api}}/material/AppBar-class.html#leading) y los
[actions]({{api}}/material/AppBar-class.html#actions) de su widget
[title]({{api}}/material/AppBar-class.html#title). Este patrón se repite a 
lo largo de todo el framework y es algo que podría considerar al diseñar 
sus propios widgets.

Para más información, mira [Componentes Material](/docs/development/ui/widgets/material).

{{site.alert.note}}
  Material es uno de los dos paquetes de diseño incluidos con Flutter. Para un  
  diseño más centrado en iOS, mira el paquete [Componentes Cupertino](/docs/development/ui/widgets/cupertino)
  los cuales tienen su propio set de [CupertinoApp]({{api}}/cupertino/CupertinoApp-class.html), y 
  [CupertinoNavigationBar]({{api}}/cupertino/CupertinoNavigationBar-class.html) etc.
{{site.alert.end}}


## Manejar gestos

La mayoría de las aplicaciones incluyen alguna forma de interacción del usuario
con el sistema. El primer paso para construir una aplicación interactiva es detectar
gestos de entrada. Veamos cómo funciona creando un simple botón:

```dart
class MyButton extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () {
        print('MyButton was tapped!');
      },
      child: Container(
        height: 36.0,
        padding: const EdgeInsets.all(8.0),
        margin: const EdgeInsets.symmetric(horizontal: 8.0),
        decoration: BoxDecoration(
          borderRadius: BorderRadius.circular(5.0),
          color: Colors.lightGreen[500],
        ),
        child: Center(
          child: Text('Engage'),
        ),
      ),
    );
  }
}
```

El widget [GestureDetector]({{api}}/widgets/GestureDetector-class.html)
no tiene una representación visual, sino que detecta los gestos realizados por el usuario.
Cuando el usuario pulsa en
[Container]({{api}}/widgets/Container-class.html), el
[GestureDetector]({{api}}/widgets/GestureDetector-class.html) llama a su callback
[onTap]({{api}}/widgets/GestureDetector-class.html#onTap)
en este caso imprimiendo un mensaje en la consola. Puede utilizar
[GestureDetector]({{api}}/widgets/GestureDetector-class.html) para detectar 
una variedad de gestos de entrada, incluidos taps, drags y scales.

Muchos widgets usan un
[GestureDetector]({{api}}/widgets/GestureDetector-class.html) para proporcionar 
callbacks opcionales para otros widgets. Por ejemplo, los widgets
[IconButton]({{api}}/material/IconButton-class.html),
[RaisedButton]({{api}}/material/RaisedButton-class.html), y
[FloatingActionButton]({{api}}/material/FloatingActionButton-class.html) 
tienen [onPressed()]({{api}}/material/RaisedButton-class.html#onPressed)
callbacks que se activan cuando el usuario toca el widget.

Para más información, mira: [Gestos en Flutter](/docs/development/ui/advanced/gestures).

## Cambiar los widgets en respuesta a las entradas

Hasta ahora, esta página solo ha usado widgets stateless. Los widgets sin estado
reciben argumentos de su widget padre, que son almacenados en propiedades
[final]({{site.dart-site}}/guides/language/language-tour#final-and-const).
Cuando en un widget es invocado el método
[build]({{api}}/widgets/StatelessWidget/build.html), usa los valores almacenados 
por aquél (el widget) para derivar nuevos argumentos para los widgets que crea.

Con el fin de crear experiencias más complejas, por ejemplo, para reaccionar de formas
más interesantes a las entradas del usuario, las aplicaciones generalmente almacenan un estado.
Flutter utiliza StatefulWidgets para capturar esta idea. StatefulWidgets son
widgets especiales que saben cómo generar objetos State, que luego se usan
para mantener el estado. Considera este ejemplo básico, usando el
[RaisedButton]({{api}}/material/RaisedButton-class.html) mencionado anteriormente:

```dart
class Counter extends StatefulWidget {
  // Esta clase es la configuración para el estado. Guarda
  // los valores (en este caso nada) proporcionados por el padre y usados por el método
  // build del State. Los campos en una subclase Widget siempre se marcan como "final".

  @override
  _CounterState createState() => _CounterState();
}

class _CounterState extends State<Counter> {
  int _counter = 0;

  void _increment() {
    setState(() {
      // Esta llamada a setState le dice al framework Flutter que
      // algo ha cambiado en este State, lo que hace que se vuelva a ejecutar
      // el método build a continuación para que la pantalla pueda reflejar los
      // valores actualizados. Si cambias _counter sin llamar
      // setState(), entonces el método build no será llamado de nuevo,
      // así que aparentará no haber ocurrido nada.
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    // Este método se vuelve a ejecutar cada vez que se llama setState, por ejemplo
    // como se hace con el método _increment anterior.
    // El framework Flutter ha sido optimizado para hacer que los métodos build
    // se vuelvan a ejecutar rápidamente, para que puedas reconstruir cualquier cosa que
    // necesite ser actualizada en lugar de tener que cambiar individualmente
    // las instancias de los widgets.
    return Row(
      children: <Widget>[
        RaisedButton(
          onPressed: _increment,
          child: Text('Increment'),
        ),
        Text('Count: $_counter'),
      ],
    );
  }
}
```

Puede que se pregunte por qué StatefulWidget y State son objetos separados. En
Flutter, estos dos tipos de objetos tienen diferentes ciclos de vida. Los Widgets son
objetos temporales, usados para construir una presentación de la aplicación en su
estado actual. Por otro lado, los objetos de estado son persistentes entre llamadas a
[build()]({{api}}/widgets/State/build.html),
permitiéndoles recordar información.

El ejemplo anterior acepta la entrada del usuario y utiliza directamente el resultado
en su método build. En aplicaciones más complejas, diferentes partes de la jerarquía
de widgets podrían ser responsables de diferentes aspectos; por ejemplo, un
widget puede presentar una interfaz de usuario compleja con el objetivo de recopilar
información específica, como una fecha o lugar, mientras que otro widget podría
usar esa información para cambiar la presentación general.

En Flutter, los cambios se notifican "hacia arriba" por la jerarquía de widgets
mediante el uso de callbacks, mientras que el estado actual fluye "hacia abajo" a los 
stateless widgets que hacen la presentación. El padre común que redirige este flujo es 
el State. Veamos cómo funciona en la práctica, con este ejemplo un poco más complejo:


```dart
class CounterDisplay extends StatelessWidget {
  CounterDisplay({this.count});

  final int count;

  @override
  Widget build(BuildContext context) {
    return Text('Count: $count');
  }
}

class CounterIncrementor extends StatelessWidget {
  CounterIncrementor({this.onPressed});

  final VoidCallback onPressed;

  @override
  Widget build(BuildContext context) {
    return RaisedButton(
      onPressed: onPressed,
      child: Text('Increment'),
    );
  }
}

class Counter extends StatefulWidget {
  @override
  _CounterState createState() => _CounterState();
}

class _CounterState extends State<Counter> {
  int _counter = 0;

  void _increment() {
    setState(() {
      ++_counter;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Row(children: <Widget>[
      CounterIncrementor(onPressed: _increment),
      CounterDisplay(count: _counter),
    ]);
  }
}
```

Nótese la creación de dos nuevos stateless widgets, separando limpiamente
las responsabilidades de _mostrar_ el contador (CounterDisplay) y _cambiar_
el contador (CounterIncrementor).
Aunque el resultado neto es el mismo que el ejemplo anterior, la separación de
responsabilidades permite encapsular una mayor complejidad en los widgets individuales
manteniendo al mismo tiempo la simplicidad en el padre.

Para más información, mira:

* [StatefulWidget]({{api}}/widgets/StatefulWidget-class.html)
* [State.setState]({{api}}/widgets/State/setState.html)

## Juntando todo

Consideremos un ejemplo más completo que reúne los conceptos
introducidos anteriormente. Trabajaremos con una hipotética aplicación de compras,
que muestra varios productos puestos a la venta y mantiene un carrito de compras para
las compras previstas. Comencemos por definir nuestra clase de presentación,
`ShoppingListItem`:

```dart
class Product {
  const Product({this.name});
  final String name;
}

typedef void CartChangedCallback(Product product, bool inCart);

class ShoppingListItem extends StatelessWidget {
  ShoppingListItem({Product product, this.inCart, this.onCartChanged})
      : product = product,
        super(key: ObjectKey(product));

  final Product product;
  final bool inCart;
  final CartChangedCallback onCartChanged;

  Color _getColor(BuildContext context) {
    // El tema depende del BuildContext porque diferentes partes del árbol
    // pueden tener diferentes temas. El BuildContext indica dónde se está llevando a cabo la
    // llamada al método build y por lo tanto qué tema usar.

    return inCart ? Colors.black54 : Theme.of(context).primaryColor;
  }

  TextStyle _getTextStyle(BuildContext context) {
    if (!inCart) return null;

    return TextStyle(
      color: Colors.black54,
      decoration: TextDecoration.lineThrough,
    );
  }

  @override
  Widget build(BuildContext context) {
    return ListTile(
      onTap: () {
        onCartChanged(product, !inCart);
      },
      leading: CircleAvatar(
        backgroundColor: _getColor(context),
        child: Text(product.name[0]),
      ),
      title: Text(product.name, style: _getTextStyle(context)),
    );
  }
}
```

El widget `ShoppingListItem` sigue un patrón común para los widgets stateless.
Almacena los valores que recibe en su constructor en propiedades
[final]({{site.dart-site}}/guides/language/language-tour#final-and-const),
que luego utiliza durante la ejecución del método
[build]({{api}}/widgets/StatelessWidget/build.html).
Por ejemplo, el booleano `inCart` para alternar entre dos apariencias visuales:
uno que usa el color primario del tema actual y otro que usa gris.

Cuando el usuario toca el elemento de la lista, el widget no modifica su valor `inCart`
directamente. En lugar de eso, el widget llama a la función `onCartChanged`
recibido de su widget padre. Este patrón le permite almacenar el estado más arriba
en la jerarquía de widgets, lo que hace que el estado persista por períodos más largos de
tiempo. En el caso extremo, el estado almacenado en el widget pasado a
[runApp()][] persiste durante toda la vida útil de la aplicación.

Cuando el padre recibe el callback `onCartChanged`, el padre actualiza
su estado interno, lo cual desencadena la re-llamada al método build y crea una nueva
instancia de `ShoppingListItem` con el nuevo valor `inCart`. Aunque el padre
crea una nueva instancia de `ShoppingListItem` cuando se reconstruye, esa operación no es
costosa porque el framework compara los widgets recién construídos con los widgets
construídos previamente y aplica solo las diferencias al 
[RenderObject]({{api}}/rendering/RenderObject-class.html) subyacente.

Veamos un ejemplo de un widget padre que almacena un estado mutable:

<!--
class Product {
  const Product({this.name});
  final String name;
}

class ShoppingListItem extends StatelessWidget {
  ShoppingListItem({Product product, bool inCart, Function onCartChanged});
  @override
  Widget build(BuildContext context) => null;
}
-->
```dart
class ShoppingList extends StatefulWidget {
  ShoppingList({Key key, this.products}) : super(key: key);

  final List<Product> products;

  // El framework llama createState la primera vez que un widget aparece en una ubicación
  // dada del árbol. Si el padre vuelve a ejecutar build y usa el mismo tipo de 
  // widget (con la misma llave),
  // el framework reutiliza el objeto State en lugar de crear un nuevo objeto State.

  @override
  _ShoppingListState createState() => _ShoppingListState();
}

class _ShoppingListState extends State<ShoppingList> {
  Set<Product> _shoppingCart = Set<Product>();

  void _handleCartChanged(Product product, bool inCart) {
    setState(() {
      // Cuando un usuario cambia lo que hay en el carrito, necesitas cambiar 
      //_shoppingCart dentro de una llamada setState para activar un rebuild.
      // El framework entonces llama a build, abajo, 
      // que actualiza la apariencia visual de la aplicación.

      if (inCart)
        _shoppingCart.add(product);
      else
        _shoppingCart.remove(product);
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Shopping List'),
      ),
      body: ListView(
        padding: EdgeInsets.symmetric(vertical: 8.0),
        children: widget.products.map((Product product) {
          return ShoppingListItem(
            product: product,
            inCart: _shoppingCart.contains(product),
            onCartChanged: _handleCartChanged,
          );
        }).toList(),
      ),
    );
  }
}

void main() {
  runApp(MaterialApp(
    title: 'Shopping App',
    home: ShoppingList(
      products: <Product>[
        Product(name: 'Eggs'),
        Product(name: 'Flour'),
        Product(name: 'Chocolate chips'),
      ],
    ),
  ));
}
```

La clase `ShoppingList` extiende de
[StatefulWidget]({{api}}/widgets/StatefulWidget-class.html),
lo que significa que este widget almacena estado mutable. Cuando el widget `ShoppingList`
se inserta por primera vez en el árbol, el framework llama a la función
[createState()]({{api}}/widgets/StatefulWidget-class.html#createState)
para crear una nueva instancia de `_ShoppingListState` para asociar
con esa ubicación en el árbol. (Note que típicamente nombramos subclases de
[State]({{api}}/widgets/State-class.html) con
guiones bajos para indicar que son detalles privados de implementación.)
Cuando el padre de este widget hace un rebuild, el padre crea una nueva instancia de
`ShoppingList`, pero el framework reutiliza la instancia `_ShoppingListState`
que ya está en el árbol en lugar de llamar a
[createState]({{api}}/widgets/StatefulWidget-class.html#createState) de nuevo.

Para acceder a las propiedades del `ShoppingList` actual, el `_ShoppingListState` puede
usar su propiedad [widget]({{api}}/widgets/State-class.html#widget). Si el padre 
reconstruye y crea una nueva `ShoppingList`, el `_ShoppingListState` se 
reconstruye con el nuevo valor del [widget]({{api}}/widgets/State-class.html#widget).
Si desea ser notificado cuando cambie la propiedad
[widget]({{api}}/widgets/State-class.html#widget)
puede anular la función 
[didUpdateWidget()]({{api}}/widgets/State-class.html#didUpdateWidget),
que se pasa como `oldWidget` para que puedas comparar el widget antiguo
con el actual [widget]({{api}}/widgets/State-class.html#widget).

Cuando se maneja el callback `onCartChanged`, el `_ShoppingListState` muta su
estado interno agregando o quitando un producto de `_shoppingCart`. Para
indicar al framework que cambia su estado interno, envuelve esas llamadas
en una llamada [setState()]({{api}}/widgets/State-class.html#setState).
Llamando setState marca este widget como sucio y lo programa para 
que sea reconstruido la próxima vez que tu aplicación necesite actualizar la pantalla. 
Si te olvidas de llamar setState cuando se modifica el estado interno 
de un widget, el framework no sabrá que tu widget está sucio y podría no 
llamar a la función [build()]({{api}}/widgets/StatelessWidget/build.html), 
lo que significa que es posible que la interfaz de usuario no se actualice 
para reflejar el estado cambiado.

Gestionando el estado de esta manera, no es necesario escribir código separado
para crear y actualizar widgets hijo. En su lugar, simplemente implementa la función
de construcción, que maneja ambas situaciones.

## Responder a los eventos del ciclo de vida de los widgets

Después de llamar a
[createState]({{api}}/widgets/StatefulWidget-class.html#createState) en el 
StatefulWidget, el framework inserta el nuevo objeto de estado en el árbol y 
luego llama [initState]({{api}}/widgets/State-class.html#initState) en el 
objeto de estado. Una subclase de [State]({{api}}/widgets/State-class.html) puedes
sobreescribir [initState]({{api}}/widgets/State-class.html#initState) para hacer 
el trabajo que debe suceder solo una vez. Por ejemplo, sobreescribir
[initState]({{api}}/widgets/State-class.html#initState)
para configurar animaciones o suscribirse a los servicios de la plataforma. Implementaciones de
[initState]({{api}}/widgets/State-class.html#initState)
deben comenzar llamando [super.initState]({{api}}/widgets/State-class.html#initState).

Cuando un objeto de estado ya no es necesario, el framework llama
[dispose]({{api}}/widgets/State-class.html#dispose) en el objeto de estado.  
Sobreescribir la función [dispose]({{api}}/widgets/State-class.html#dispose)
para realizar tareas de limpieza. Por ejemplo, puedes sobreescribir
[dispose]({{api}}/widgets/State-class.html#dispose) para cancelar timers o 
darse de baja de los servicios de la plataforma. Las implementaciones de
[dispose]({{api}}/widgets/State-class.html#dispose) típicamente terminan llamando a
[super.dispose]({{api}}/widgets/State-class.html#dispose).

Para más información, mira: [State]({{api}}/widgets/State-class.html).

## Keys

Usa keys para controlar que los widgets del framework coinciden con
otros widgets cuando un widget se reconstruye. Por defecto, el framework coincide
widgets en la construcción actual y anterior de acuerdo con su
[runtimeType]({{api}}/widgets/Widget-class.html#runtimeType) y el orden en que 
aparecen. Con keys, el framework requiere que los dos widgets tengan 
la misma [key]({{api}}/widgets/Widget-class.html#key) así como el mismo
[runtimeType]({{api}}/widgets/Widget-class.html#runtimeType).

Las llaves son más útiles en los widgets que construyen muchas instancias del mismo tipo de
widget. Por ejemplo, el widget `ShoppingList`, que construye solo las instancias
`ShoppingListItem` suficientes para llenar su región visible:

 * Sin las llaves, la primera entrada en la construcción actual siempre se sincronizaría con la 
   primera entrada en la construcción anterior, incluso si, semánticamente, la primera entrada en
   la lista se ha desplazado fuera de la pantalla y ya no es visible en la ventana de visualización.

 * Asignando a cada entrada de la lista una llave "semántica", la lista infinita puede
   ser más eficaz porque el framework sincroniza las entradas con las llaves semánticas
   coincidentes y, por lo tanto, con apariencias visuales similares (o identicas).
   Además, sincronizar las entradas semánticamente significa que el estado conservado en
   los widgets hijo stateful permanecen unidos a la misma entrada semántica en lugar
   de la entrada, en la misma posición numérica dentro del viewport.

Para más información, mira [Key API]({{api}}/foundation/Key-class.html)-

## Global Keys

Usa global keys para identificar de forma única los widgets hijos. Las global keys 
deben ser globalmente únicas en toda la jerarquía de widgets, a diferencia de las llaves 
locales que sólo necesitan ser únicas entre hermanos. Debido a que son globalmente únicos, se 
puede usar una llave global para recuperar el estado asociado con un widget.

Para más información, mira 
[GlobalKey API]({{api}}/widgets/GlobalKey-class.html).

[runApp()]: {{api}}/widgets/runApp.html
