- Author: Santiago Guerrero <santiago@lifespikes.com>
- Original Date: 12/26/2022

# Ecosystem or integration layer based file structure
<sub>Primero que todo una disculpa por escribir este RFC en español, entiendo lo que se habla en los RFC, pero escribir en inglés no me va muy bien.</sub>


Este RFC tiene como enfoque proponer alternativas a nuestra estructura de archivos para nuestro **monorepo**, a la vez usar una herramienta como lo es [Turbo](https://turbo.build/) para el ecosistema de Javascript.

## Motivation

En cuanto a la estructura de archivos, me baso en la idea de nuestro stack, ya que usamos dos ecosistemas, los cuales son **PHP** y **JavaScript**, uno muy distinto del otro; **PHP** lo usamos primariamente para nuestro **backend** y **JavaScript** para nuestro **frontend** o ciertas herramientas escritas en **Nodejs**. Allí es donde viene la primera alternativa; usar una estructura de archivos basada en ecosistema, donde podemos tener en un directorio todo lo relacionado con **PHP** y otro relacionado con **JavaScript**.

Otra alternativa sería usar el ejemplo de muchos **monorepo** los cuales dividen sus módulos en dos categorías, **apps** y **packages**, es igualmente una arquitectura desacoplada, donde los módulos de integración están en el directorio **apps** y usan los módulos del directorio **packages**. Tomando como ejemplo nuestro monorepo, algunos repositorios que irían en el directorio **apps**, serían: **backend** y **frontend**. 
Lo anterior daría un mayor orden, ya que sería mucho mejor ubicar aquellos módulos que pertenecen a un ecosistema o a la capa de integración.

Por último, enfocándome más en el ecosistema de **JavaScript**, definitivamente estaría muy bien usar una herramienta llamada [Turbo](https://turbo.build/). Esta nos puede proveer todas las habilidades necesarias para un monorepo, como lo son:
- **Almacenamiento caché local:**
Esto puede ser muy útil para correr tests, y diferentes procesos, haciendo que vaya mucho más rápido nuestro flujo de trabajo.

- **Ejecución de tareas locales:**
La habilidad de que se corran tareas en el orden correcto o en paralelo. Un ejemplo de esto, es que podemos configurar el comando `dev` a nuestros módulos, en un mundo sin **Turbo** repo debemos abrir n consolas y ejecutar el `dev` command, pero con **Turbo** podemos ejecutarlo una única vez, y este se ocupara de ejecutarlos en paralelo si es así necesario.

Hay muchos más beneficios, puedes leer más al respecto en [Monorepo features](https://monorepo.tools/#monorepo-features).


## Design Detail
Nuestra estructura de archivos luce así actualmente, (tomaré como ejemplo a **Janus Phoenix**):

<sub>Remarcaré cuáles módulos son un módulo de JavaScript o pertenecen a la capa de integración
</sub>
```
.
├── bootstrap
├── database
│   ├── factories
│   └── seeders
├── docs
│   ├── modules
│   ├── request-docs
│   └── system-design
├── packages
│   ├── auth
│   ├── authorize
│   ├── backend    >>> It belongs to the backend integration layer
│   ├── calendar
│   ├── contracts
│   ├── dialpad
│   ├── frontend   >>> It belongs to the frontend integration layer
│   ├── gmail-retrieval
│   ├── notifications
│   ├── quoting
│   ├── react-pdf-form  >>> js package
│   ├── sunbiz
│   ├── utility
│   ├── virtual-docs
│   └── virtual-docs-js   >>> js package
├── public
├── resources
│   └── js
├── stubs
└── tests
    ├── Architecture
    ├── Feature
    └── Fixtures
```
<sub>Puedes imprimir este árbol de archivos usando el paquete [tree](https://formulae.brew.sh/formula/tree), de esta forma:
``tree -L 2 -o myfile.txt  -d -I 'node_modules|vendor|docker|storage'``
</sub>

### Primera alternativa 
<sub>Estructura de archivos basada en ecosistema
</sub>

La estructura de archivos puede lucir de la siguiente forma:

```
.
├── bootstrap
├── database
│   ├── factories
│   └── seeders
├── docs
│   ├── modules
│   ├── request-docs
│   └── system-design
├── javascript
│   ├── frontend   >>> It belongs to the frontend integration layer
│   ├── react-pdf-form 
│   └── virtual-docs-js 
├── php
│   ├── auth
│   ├── authorize
│   ├── backend    >>> It belongs to the backend integration layer
│   ├── calendar
│   ├── contracts
│   ├── dialpad
│   ├── gmail-retrieval
│   ├── notifications
│   ├── quoting
│   ├── sunbiz
│   ├── utility
│   ├── virtual-docs
├── public
├── resources
│   └── js
├── stubs
└── tests
    ├── Architecture
    ├── Feature
    └── Fixtures
```
El nombre, en vez de ser explícito al ecosistema, puede cambiar, pero mi prioridad es transmitir más que cualquier otra cosa, es su orden.

### Segunda alternativa
<sub>Estructura de archivos basada en la capa de integración
</sub>

La estructura de archivos, sería la siguiente:

```
.
├── bootstrap
├── database
│   ├── factories
│   └── seeders
├── docs
│   ├── modules
│   ├── request-docs
│   └── system-design
├── apps
│   ├── frontend   >>> It belongs to the frontend integration layer
│   ├── backend    >>> It belongs to the backend integration layer
├── packages
│   ├── auth
│   ├── authorize
│   ├── calendar
│   ├── contracts
│   ├── dialpad
│   ├── gmail-retrieval
│   ├── notifications
│   ├── quoting
│   ├── sunbiz
│   ├── utility
│   ├── virtual-docs
│   ├── react-pdf-form 
│   └── virtual-docs-js 
├── public
├── resources
│   └── js
├── stubs
└── tests
    ├── Architecture
    ├── Feature
    └── Fixtures
```

### Turbo
Ya expuestos sus beneficios anteriormente, realmente es muy sencillo integrar **Turbo** con un **monorepo** existente, ya que este se puede adaptar al manejador de paquetes que usemos, como **pnpm**, **yarn** o **npm**.
Una guia super útil sería esta: [Add to project](https://turbo.build/repo/docs/getting-started/add-to-project)


### Cost and Alternatives
Realmente no veo un costo muy grande a esta implementación, quizá sería adaptar el **cli-tool** de nuestro **monorepo** para que pueda tener en cuenta la nueva estructura de archivos, eso por el lado técnico, como tal no tengo más puntos en cuenta. Si tienen algún punto en cuenta que yo no, por favor dejarlo saber en los comentarios.
