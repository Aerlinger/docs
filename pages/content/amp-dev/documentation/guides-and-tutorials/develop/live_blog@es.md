---
$title: Crear un blog en directo
toc: true
---





Los blogs en directo son páginas web que se actualizan frecuentemente durante el desarrollo de eventos como competiciones deportivas o días de elecciones. En AMP, puedes implementar un blog en directo usando el componente [`amp-live-list`](/es/docs/reference/components/amp-live-list.html).

Este tutorial ofrece información general sobre el componente `amp-live-list` y se centra en algunos detalles de la implementación para blogs en directo, como la [paginación](#pagination) y los [enlaces profundos](#deeplinking). Usaremos el [blog en directo de ejemplo] de AMP By Example (https://www.ampbyexample.com/samples_templates/live_blog/) para mostrar cómo implementar blogs en AMP.

[tip type="success"]

Usa la etiqueta de metadatos [LiveBlogPosting](http://schema.org/LiveBlogPosting) para que tu blog pueda integrarse con funciones de plataforma de terceros.

[/tip]

{{ image('/static/img/docs/tutorials/amp-live-list-ampbyexample.png', 700, 1441, align='right third') }} 

## Descripción general de `amp-live-list`

El componente [`amp-live-list`](/es/docs/reference/components/amp-live-list.html) busca contenido nuevo en el documento del host periódicamente y actualiza el navegador del usuario si hay elementos nuevos disponibles. Esto significa que cada vez que sea necesario añadir una entrada al blog, el CMS debe actualizar el documento del host para incluir la actualización en el cuerpo y en la sección [metadata](https://ampbyexample.com/samples_templates/live_blog/#metadata) de la página.


El código inicial del blog podría tener el siguiente aspecto:

```html
<amp-live-list id="my-live-list"
    data-poll-interval="15000"
    data-max-items-per-page="5">
  <button update on="tap:my-live-list.update">You have updates</button>
  <div items></div>
</amp-live-list>
```

Echemos un vistazo al código:

Cada componente `amp-live-list` necesita un ID único porque podría haber más de un componente en la página. En este ejemplo, hemos establecido `my-live-list` como ID único.

El atributo `data-poll-interval` especifica cada cuánto tiempo debe realizarse la búsqueda. Si el documento del host se actualiza, la nueva versión debería estar disponible para el usuario después del siguiente intervalo.

Cuando se añade un elemento nuevo al documento del host, el elemento `<button update on="tap:my-live-list.update">` muestra el botón "You have updates" (Tienes actualizaciones), que abre la página con las últimas entradas al pulsarlo.

Los blogs en directo pueden aumentar de tamaño y hacer que la página se alargue demasiado. El atributo `data-max-items-per-page` te permite especificar cuántos elementos se pueden añadir. Si el número de elementos es mayor que `data-max-items-per-page` después de la actualización, se quitarán los elementos más antiguos que superen este número. Por ejemplo, si el valor de `data-max-items-per-page` es 10, la página tiene 9 elementos y la última actualización tiene 3 nuevos, los 2 más antiguos se quitarán de la página al actualizar.

Todas las entradas de blog en `amp-live-list` deben ser secundarias de `<div items></div>`. Si nos referimos a las entradas como elementos, todos ellos deben tener un `id` y un `data-sort-time` únicos.

## Detalles de la implementación

Ahora que te has familiarizado con el componente `amp-live-list`, vamos a descubrir cómo podemos implementar un blog en directo más complejo. Sigue leyendo para obtener más información sobre cómo implementar la paginación y cómo funcionan los enlaces profundos.

### Paginación

Los blogs con páginas largas pueden usar la paginación para limitar el número de elementos que se muestran en una página y aumentar así el rendimiento. Para implementar la paginación, añade `<div pagination></div>` al componente `amp-live-list`. Después, inserta cualquier etiqueta que necesites para la paginación (por ejemplo, un número de página o un enlace a la página siguiente y a la anterior).

Con la paginación, el código de antes se convierte en lo siguiente:

```html
<amp-live-list id="my-live-list"
    data-poll-interval="15000"
    data-max-items-per-page="5">
  <button update on="tap:my-live-list.update">You have updates</button>
  <div items></div>
  <div pagination>
    <nav>
      <ul>
        <li>1</li>
        <li>Next</li>
      </ul>
     </nav>
   </div>
</amp-live-list>
```

{{ image('/static/img/docs/tutorials/amp-live-list-ampbyexample_pg2.png', 700, 1441, align='right third') }}  

Eres tú quien tiene que rellenar los elementos de navegación correctamente actualizando la página alojada. Por ejemplo, en el [blog en directo de ejemplo](https://www.ampbyexample.com/samples_templates/live_blog/) nosotros procesamos la página mediante una plantilla de servidor y usamos un parámetro de consulta para especificar cuál debe ser el primer elemento del blog en la página. Limitamos el tamaño de la página a 5 elementos. Así, si el servidor genera más de 5 elementos cuando un usuario llega a la primera página, la página muestra el elemento "Next" (Siguiente) en el área de navegación. Para obtener más información, consulta [amp-live-list.go](https://github.com/ampproject/amp-by-example/blob/master/backend/amp-live-list.go#L182) y [Live_Blog.html](https://github.com/ampproject/amp-by-example/blob/master/src/60_Samples_%2526_Templates/Live_Blog.html).

Cuando la cantidad de entradas del blog sea superior al número máximo de elementos establecidos en `data-max-items-per-page`, los elementos más antiguos del blog se mostrarán en las páginas siguientes; por ejemplo, en la página 2. El componente `amp-live-list` busca en el servidor a intervalos para ver si hay algún cambio en los elementos, pero no es necesario buscar en el servidor si el usuario no está en la primera página.

Puedes añadir a la página el atributo inhabilitado para desactivar el mecanismo de búsqueda. En el blog en directo de ejemplo, hemos generado este comportamiento en una plantilla de servidor: cuando la página solicitada no es la primera, el atributo inhabilitado se añade al componente `amp-live-list`.

### Enlaces profundos

Cuando publicas una entrada en el blog, es importante poder enlazarla de forma profunda para habilitar funciones (por ejemplo, para poder compartirla). Con `amp-live-list`, solo hay que usar el atributo `id` del elemento del blog para realizar enlaces profundos. Por ejemplo, [https://ampbyexample.com/samples_templates/live_blog/preview/#post3](https://ampbyexample.com/samples_templates/live_blog/preview/#post3) te permite navegar directamente a la entrada del blog con el ID `post3`.

AMP By Example usa una cookie en el [blog en directo de ejemplo](https://www.ampbyexample.com/samples_templates/live_blog/) para generar contenido actualizado, así que si es la primera vez que llegas a la página, la entrada con el ID `post3` quizá no esté disponible. En ese caso, serás redirigido a la primera entrada.


## Recursos

Consulta los siguientes recursos para obtener más información:

- Documentación de referencia de [amp-live-list](/es/docs/reference/components/amp-live-list.html)
- [Ejemplo amp-live-list de AMP By Example](https://ampbyexample.com/components/amp-live-list/)
- [Ejemplo de blog en directo de AMP By Example](https://www.ampbyexample.com/samples_templates/live_blog/)
 
 
 
