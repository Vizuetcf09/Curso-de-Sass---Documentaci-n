# Que son las variables de Sass

- Las variables de Sass son sencillas: asignas un valor a un nombre.
- Empieza por el símbolo: ``$``.
- Puedes referirte a ese nombre en lugar de al propio valor. 
- Las variables permiten reducir las repeticiones, realizar operaciones matemáticas complejas, configurar bibliotecas y mucho más.
- Una declaración de variable se parece mucho a una [declaración de propiedad](https://sass-lang.com/documentation/style-rules/declarations/).
- Las variables pueden declararse donde quieras. 
- Para utilizar una variable, basta con incluirla en un valor.

## Sintaxis
  
Se escribe:

```SCSS
variable: expresion. 
```

***Ejemplos*:**

***SCSS:***
```SCSS
$base-color: #c6538c;
$border-dark: rgba($base-color, 0.88);

.alert {
	border: 1px solid $border-dark;
}
```
***Sass:***

```SCSS
$base-color: #c6538c
$border-dark: rgba($base-color, 0.88)

.alert 
	border: 1px solid $border-dark
```

***CSS:***
```CSS
.alert {
	border: 1px solid rgba(198, 83, 140, 0.88);
}
```

***⚠️ ¡Atención!***

CSS tiene sus [propias variables](https://sass-lang.com/documentation/style-rules/declarations#custom-properties), son totalmente diferentes a las variables de CSS.

- Las variables Sass son compiladas por Sass. Las variables CSS se incluyen en la salida CSS.
- Las variables CSS pueden tener diferentes valores para diferentes elementos, pero las variables Sass sólo tienen un valor a la vez.
- Las variables Sass son imperativas, lo que significa que si usas una variable y luego cambias su valor, el uso anterior permanecerá igual. Las variables CSS son declarativas, lo que significa que si cambias el valor, afectará tanto a los usos anteriores como a los posteriores.

***Ejemplos***:

***SCSS***:

```SCSS
$variable: value 1;
.rule-1 {
	value: $variable;
} 

$variable: value 2;
.rule-2 {
	value: $variable;
}
```

***Sass***:

```SCSS
$variable: value 1

.rule-1
	value: $variable

$variable: value 2
.rule-2 
   value: $variable
```

***CSS***:

```CSS
.rule-1 {
	value: value 1;
}

.rule-2 {
	value: value 2;
}
```


---
***💡 Curiosidades***:

Las variables Sass y todos los sus identificadores, tratan los guiones y guiones bajos como idénticos. Esto significa que ``$font-size`` o ``$font_size`` se refieren a la misma variable. 

*Esto es un remanente histórico de los primeros días de Sass, cuando sólo permitía guiones bajos en los nombres de identificadores. Una vez que Sass añadió soporte para guiones para coincidir con la sintaxis de CSS, los dos se hicieron equivalentes para facilitar la migración.*

---
# Valores predeterminados

- Normalmente, cuando asignas un valor a una variable, si esa variable ya tenía un valor, su valor antiguo se sobrescribe.
- Si estás escribiendo una librería Sass, puede que quieras permitir a tus usuarios configurar las variables de tu librería antes de usarlas para generar CSS.

## Bandera ``!default`` 

Para lograr lo anterior, Sass proporciona la bandera ``!default``. 
### Características de la bandera ``!default``

- La bandera ``!default`` asigna un valor a una variable sólo si esa variable no está definida o su valor es [nulo](https://sass-lang.com/documentation/values/null). 
- En caso contrario, se utilizará el valor existente.
### Configuración

- Las variables definidas con ``!default`` pueden configurarse al cargar un módulo con la [regla ``@use``](https://sass-lang.com/documentation/at-rules/use). 
- Las bibliotecas Sass a menudo utilizan variables ``!default`` para permitir a sus usuarios configurar el CSS de la biblioteca.
### Como cargar un módulo

Para cargar un módulo con configuración, escriba: 

```SCSS
@use url with (variable: valor, variable: valor)
``` 

- Los valores configurados anularán los valores por defecto de las variables. 
- Sólo se pueden configurar las variables escritas en el nivel superior de la hoja de estilos con un indicador ``!default``.

***Ejemplos***:

***SCSS***:

```SCSS
// _library.scss
$black: #000 !default;
$border-radius: 0.25rem !default;
$box-shadow: 0 0.5rem 1rem rgba($black, 0.15) !default;

code {
	border-radius: $border-radius;
	box-shadow: $box-shadow;
}
```

```SCSS
// style.scss
@use 'library' with (
	$black: #222,
	$border-radius: 0.1rem
);
```
***Sass***:
```SCSS
// _library.scss
$black: #000 !default
$border-radius: 0.25rem !default
$box-shadow: 0 0.5rem 1rem rgba($black, 0.15) !default

code
	border-radius: $border-radius;
	box-shadow: $box-shadow;
```

```SCSS
@use 'library' with ($black: #222, $border-radius: 0.1rem)
```
***CSS***:

```CSS
code {
	border-radius: 0.1rem;
	box-shadow: 0 0.5rem 1rem rgba(34, 34, 34, 0.15);
}
```

## Variables incorporadas

- Las variables definidas por un **[módulo incorporado](https://sass-lang.com/documentation/modules)** no pueden ser modificadas.

***Ejemplo:***

***SCSS:***

```scss
@use "sass:math" as math;

// This assignment will fail.
math.$pi: 0;
```

***Sass:***

```scss
@use "sass:math" as math

// This assignment will fail.
math.$pi: 0
```

# Variables locales y globales
## Scope

Las variables declaradas en el nivel superior de una hoja de estilo son *globales*. 
- Se puede acceder a ellas en cualquier parte de su módulo después de haber sido declaradas. 
Eso no es cierto para todas las variables. 
- Las variables declaradas en bloques (llaves en SCSS o código sangrado en Sass) son normalmente *locales*, y sólo se puede acceder a ellas dentro del bloque en el que fueron declaradas.

***Ejemplos:***

***CSSS:***

```scss
$global-variable: global value;

.content {
	$local-variable: local value;
	global: $global-variable;
	local: $local-variable;
}

.sidebar {
	global: $global-variable;

	// This would fail, because $local-variable isn't in scope:
		// local: $local-variable;
}
```

***Sass:***

```scss
$global-variable: global value

.content
	$local-variable: local value
	global: $global-variable
	local: $local-variable

.sidebar 
	global: $global-variable

	// This would fail, because $local-variable isn't in scope:
		// local: $local-variable;
```

***CSS:***

```css
.content {
  global: global value;
  local: local value;
}

.sidebar {
  global: global value;
}
```
### Sombra 

- Las variables locales pueden incluso declararse con el mismo nombre que una variable global. 
- Si esto sucede, en realidad hay dos variables diferentes con el mismo nombre: una local y otra global. 
- Esto ayuda a asegurar que un autor que escribe una variable local no cambie accidentalmente el valor de una variable global de la que ni siquiera es consciente.

***Ejemplos:***

***CSSS:***

```scss
$variable: global value;

.content {
	$variable: local value;
	value: $variable;
}

.sidebar {
	value: $variable;
}
```

***Sass:***

```scss
$variable: global value

.content 
	$variable: local value
	value: $variable

.sidebar 
	value: $variable
```

***CSS:***

```css
.content {
	value: local value;
}

.sidebar {
	value: global value;
}
```

### Indicador ``!global``

- Si necesitas establecer el valor de una variable global desde un ámbito local (como en un mixin), puedes utilizar el indicador ``!global``. 
- Una declaración de variable marcada como ``!global`` *siempre se asignará* al ámbito global.

***Ejemplos:***

***SCSS:***

```scss
$variable: first global value;

.content {
	$variable: second global value !global;
	value: $variable;
}

.sidebar {
	value: $variable;
}
```

***Sass:***

```scss
$variable: first global value

.content 
	$variable: second global value !global
	value: $variable

.sidebar 
	value: $variable
```

***CSS:***

```css
.content {
	value: second global value;
}

.sidebar {
	value: second global value;
}
```

---
**⚠️ ¡Atención!**

La bandera ``!global`` sólo puede utilizarse para establecer una variable que ya ha sido declarada en el nivel superior de un archivo. No puede utilizarse para declarar una nueva variable.

---
### Scope de control de flujo

Las variables declaradas en [reglas de control de flujo](https://sass-lang.com/documentation/at-rules/control) tienen reglas de alcance especiales:

- No sombrean variables al mismo nivel que la regla de control de flujo. En su lugar, simplemente asignan a esas variables. 
- Esto hace mucho más fácil asignar condicionalmente un valor a una variable, o construir un valor como parte de un bucle.

***Ejemplos:***

***SCSS:***

```scss
$dark-theme: true !default;
$primary-color: #f8bbd0 !default;
$accent-color: #6a1b9a !default;

@if $dark-theme {
	$primary-color: darken($primary-color, 60%);
	$accent-color: lighten($accent-color, 60%);
}

.button {
	background-color: $primary-color;
	border: 1px solid $accent-color;
	border-radius: 3px;
}
```

***Sass:***

```scss
$dark-theme: true !default
$primary-color: #f8bbd0 !default
$accent-color: #6a1b9a !default

@if $dark-theme 
	$primary-color: darken($primary-color, 60%)
	$accent-color: lighten($accent-color, 60%)

.button 
	background-color: $primary-color
	border: 1px solid $accent-color
	border-radius: 3px
```

***CSS:***

```css
.button {
  background-color: #750c30;
  border: 1px solid #f5ebfc;
  border-radius: 3px;
}
```
  
---
**⚠️ ¡Atención!**

Las variables en el Scope de control de flujo pueden asignarse a variables existentes en el ámbito externo, pero las nuevas variables declaradas en el Scope de control de flujo no serán accesibles en el Scope externo. Asegúrate de que la variable ya está declarada antes de asignarla, incluso si necesitas declararla como ``null``.

---
## Funciones variables avanzadas

La biblioteca central de Sass proporciona un par de funciones avanzadas para trabajar con variables. 
1. La función ``meta.variable-exists()`` devuelve si una variable con el nombre dado existe en el ámbito actual.
2. La función ``meta.global-variable-exists()`` hace lo mismo pero sólo para el ámbito global.

---
**⚠️ ¡Atención!**

Los usuarios ocasionalmente quieren usar interpolación para definir un nombre de variable basado en otra variable. Sass no permite esto, porque hace mucho más difícil saber de un vistazo qué variables están definidas dónde. 

- Lo que *puedes* hacer, sin embargo, es definir un **[map](https://sass-lang.com/documentation/values/maps)** de nombres a valores a los que puedes acceder usando variables. 

***Ejemplos:***

***SCSS:***

```scss
@use "sass:map";

$theme-colors: (
	"success": #28a745,
	"info": #17a2b8,
	"warning": #ffc107,
);

.alert {
	// Instead of $theme-color-#{warning}
	background-color: map.get($theme-colors, "warning");
}
```

***Sass:***

```scss
@use "sass:map"

$theme-colors: ("success": #28a745, "info": #17a2b8, "warning": #ffc107)

.alert
	// Instead of $theme-color-#{warning}
	background-color: map.get($theme-colors, "warning")
```

***CSS:***

```css
.alert {
	background-color: #ffc107;
}
```

---


---
# Bibliografía

1. [Sass - Variables](https://sass-lang.com/documentation/variables/)