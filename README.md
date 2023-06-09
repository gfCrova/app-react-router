# App <img width="200" src="https://github.com/gfCrova/app-react-router/assets/103906625/707fffa7-278b-4e49-a26d-413dc77f94af" />


<p> Aplicación pequeña pero rica en funciones que le permitirá realizar un seguimiento de sus contactos. </p><br>

Usaremos Vite para nuestro paquete y servidor de desarrollo para este tutorial. Necesitará Node.js instalado para la npmherramienta de línea de comandos.

👉️ Abre tu terminal y arranca una nueva aplicación React con Vite:

```shell
npm create vite@latest name-of-your-project -- --template react
# follow prompts
cd <your new project directory>
npm install react-router-dom localforage match-sorter sort-by
npm run dev
```

Debería poder visitar la URL impresa en la terminal:

``` console
VITE v3.0.7  ready in 175 ms

  ➜  Local:   http://127.0.0.1:5173/
  ➜  Network: use --host to expose
```

Tenemos algo de CSS preescrito para este tutorial para que podamos concentrarnos en React Router. Siéntete libre de juzgarlo severamente o escribir el tuyo propio 😅 (Hicimos cosas que normalmente no haríamos en CSS para que el marcado en este tutorial pudiera ser lo mínimo posible).

👉 Copie/Pegue el tutorial CSS que se encuentra <a href="https://gist.githubusercontent.com/ryanflorence/ba20d473ef59e1965543fa013ae4163f/raw/499707f25a5690d490c7b3d54c65c65eb895930c/react-router-6.4-tutorial-css.css"> Aquí </a> en ```src/index.css```

Este app creará, leerá, buscará, actualizará y eliminará datos. Una aplicación web típica probablemente estaría hablando con una API en su servidor web, pero vamos a usar el almacenamiento del navegador y falsificar la latencia de la red para mantener esto enfocado. Ninguno de estos códigos es relevante para React Router, así que adelante, cópielo y péguelo todo.

<!--La App se verá así: 
<img src="https://reactrouter.com/_docs/tutorial/15.webp" />-->

👉 Copie/pegue el módulo de datos del tutorial que se encuentra <a href="https://gist.githubusercontent.com/ryanflorence/1e7f5d3344c0db4a8394292c157cd305/raw/f7ff21e9ae7ffd55bfaaaf320e09c6a08a8a6611/contacts.js"> Aquí </a> en ```src/contacts.js```

Todo lo que necesita en la carpeta src es contacts.js, main.jsxy index.css. Puede eliminar cualquier otra cosa (como App.jsy assets, etc.).

👉 Elimina los archivos que no uses src/para que solo te queden estos:

```
src
├── contacts.js
├── index.css
└── main.jsx
```

Si tu aplicación se está ejecutando, podría explotar momentáneamente, solo continúa 😋. Y con eso, ¡estamos listos para comenzar!

Agregar un enrutador
Lo primero que debemos hacer es crear un enrutador de navegador y configurar nuestra primera ruta. Esto habilitará el enrutamiento del lado del cliente para nuestra aplicación web.

El ```main.jsx``` archivo es el punto de entrada. Ábrelo y pondremos React Router en la página.

👉 Cree y renderice un enrutador de navegador enmain.jsx

``` js
import * as React from "react";
import * as ReactDOM from "react-dom/client";
import {
  createBrowserRouter,
  RouterProvider,
} from "react-router-dom";
import "./index.css";

const router = createBrowserRouter([
  {
    path: "/",
    element: <div>Hello world!</div>,
  },
]);

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
);
```

Esta primera ruta es lo que a menudo llamamos la "ruta raíz", ya que el resto de nuestras rutas se renderizarán dentro de ella. Servirá como el diseño raíz de la interfaz de usuario, tendremos diseños anidados a medida que avancemos.

La Ruta Raíz
Agreguemos el diseño global para esta aplicación.

👉 Crear ```src/routesysrc/routes/root.jsx```

```console
mkdir src/routes
touch src/routes/root.jsx
```

(Si no quieres ser un nerd de la línea de comandos, usa tu editor en lugar de esos comandos 🤓)

👉 Crear el componente de diseño raíz

``` js
export default function Root() {
  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <form id="search-form" role="search">
            <input
              id="q"
              aria-label="Search contacts"
              placeholder="Search"
              type="search"
              name="q"
            />
            <div
              id="search-spinner"
              aria-hidden
              hidden={true}
            />
            <div
              className="sr-only"
              aria-live="polite"
            ></div>
          </form>
          <form method="post">
            <button type="submit">New</button>
          </form>
        </div>
        <nav>
          <ul>
            <li>
              <a href={`/contacts/1`}>Your Name</a>
            </li>
            <li>
              <a href={`/contacts/2`}>Your Friend</a>
            </li>
          </ul>
        </nav>
      </div>
      <div id="detail"></div>
    </>
  );
}
```

Nada específico de React Router todavía, así que siéntete libre de copiar y pegar todo eso.

👉 Establecer <Root>como ruta raízelement

``` js
/* existing imports */
import Root from "./routes/root";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
  },
]);

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
);
```
La aplicación debería verse algo como esto ahora. Seguro que es bueno tener un diseñador que también pueda escribir el CSS, ¿no? (Gracias Jim 🙏).


Manejo de errores no encontrados
Siempre es una buena idea saber cómo responde su aplicación a los errores al principio del proyecto porque todos escribimos muchos más errores que funciones cuando creamos una nueva aplicación. Sus usuarios no solo obtendrán una buena experiencia cuando esto suceda, sino que también lo ayudarán durante el desarrollo.

Agregamos algunos enlaces a esta aplicación, veamos qué sucede cuando hacemos clic en ellos.

👉 Haz clic en uno de los nombres de la barra lateral

captura de pantalla del elemento de error React Router predeterminado
¡Bruto! Esta es la pantalla de error predeterminada en React Router, empeorada por nuestros estilos de caja flexible en el elemento raíz de esta aplicación 😂.

Cada vez que su aplicación arroje un error al renderizar, cargar datos o realizar mutaciones de datos, React Router lo detectará y mostrará una pantalla de error. Hagamos nuestra propia página de error.

👉 Crear un componente de página de error

```  js
touch src/error-page.jsx
import { useRouteError } from "react-router-dom";

export default function ErrorPage() {
  const error = useRouteError();
  console.error(error);

  return (
    <div id="error-page">
      <h1>Oops!</h1>
      <p>Sorry, an unexpected error has occurred.</p>
      <p>
        <i>{error.statusText || error.message}</i>
      </p>
    </div>
  );
}
```
  
👉 Establecer <ErrorPage>como errorElementen la ruta raíz

``` js
/* previous imports */
import ErrorPage from "./error-page";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
  },
]);
  
ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
);
```
  
La página de error ahora debería verse así:

nueva página de error, pero sigue siendo fea
(Bueno, eso no es mucho mejor. Tal vez alguien se olvidó de pedirle al diseñador que hiciera una página de error. Tal vez todos se olvidan de pedirle al diseñador que haga una página de error y luego culpan al diseñador por no pensar en ello 😆)

Tenga en cuenta que useRouteErrorproporciona el error que se arrojó. Cuando el usuario navega a rutas que no existen, obtendrá una respuesta de error con un "No encontrado" statusText. Veremos algunos otros errores más adelante en el tutorial y los discutiremos más.

Por ahora, es suficiente saber que casi todos sus errores ahora serán manejados por esta página en lugar de infinitos controles giratorios, páginas que no responden o pantallas en blanco 🙌

La interfaz de usuario de la ruta de contacto
En lugar de una página 404 "No encontrada", queremos mostrar algo en las URL a las que nos hemos vinculado. Para eso, necesitamos hacer una nueva ruta.

👉 Crear el módulo de ruta de contacto
  
``` console
touch src/routes/contact.jsx
```

👉 Agregue la interfaz de usuario del componente de contacto

Es solo un montón de elementos, siéntete libre de copiar/pegar.
  
``` js
import { Form } from "react-router-dom";

export default function Contact() {
  const contact = {
    first: "Your",
    last: "Name",
    avatar: "https://placekitten.com/g/200/200",
    twitter: "your_handle",
    notes: "Some notes",
    favorite: true,
  };

  return (
    <div id="contact">
      <div>
        <img
          key={contact.avatar}
          src={contact.avatar || null}
        />
      </div>

      <div>
        <h1>
          {contact.first || contact.last ? (
            <>
              {contact.first} {contact.last}
            </>
          ) : (
            <i>No Name</i>
          )}{" "}
          <Favorite contact={contact} />
        </h1>

        {contact.twitter && (
          <p>
            <a
              target="_blank"
              href={`https://twitter.com/${contact.twitter}`}
            >
              {contact.twitter}
            </a>
          </p>
        )}

        {contact.notes && <p>{contact.notes}</p>}

        <div>
          <Form action="edit">
            <button type="submit">Edit</button>
          </Form>
          <Form
            method="post"
            action="destroy"
            onSubmit={(event) => {
              if (
                !confirm(
                  "Please confirm you want to delete this record."
                )
              ) {
                event.preventDefault();
              }
            }}
          >
            <button type="submit">Delete</button>
          </Form>
        </div>
      </div>
    </div>
  );
}

function Favorite({ contact }) {
  // yes, this is a `let` for later
  let favorite = contact.favorite;
  return (
    <Form method="post">
      <button
        name="favorite"
        value={favorite ? "false" : "true"}
        aria-label={
          favorite
            ? "Remove from favorites"
            : "Add to favorites"
        }
      >
        {favorite ? "★" : "☆"}
      </button>
    </Form>
  );
}
```
  
Ahora que tenemos un componente, conectémoslo a una nueva ruta.

👉 Importa el componente de contacto y crea una nueva ruta

``` js
/* existing imports */
import Contact from "./routes/contact";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
  },
  {
    path: "contacts/:contactId",
    element: <Contact />,
  },
]);

/* existing code */
```
  
¡Ahora, si hacemos clic en uno de los enlaces o visitamos, /contacts/1 obtenemos nuestro nuevo componente!

representación de ruta de contacto sin el diseño principal
Sin embargo, no está dentro de nuestro diseño raíz 😠

Rutas anidadas
Queremos que el componente de contacto se muestre dentro del <Root>diseño de esta manera.


Lo hacemos haciendo que la ruta de contacto sea secundaria de la ruta raíz.

👉 Mueva la ruta de contactos para que sea un elemento secundario de la ruta raíz

``` js
const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
      },
    ],
  },
]);
```
  
Ahora volverá a ver el diseño raíz, pero una página en blanco a la derecha. Necesitamos decirle a la ruta raíz dónde queremos que represente sus rutas secundarias. Eso lo hacemos con <Outlet>.

Encuentra el ```<div id="detail">``` y pon una salida dentro.

👉 Renderizar un ```<Outlet>```

``` js
import { Outlet } from "react-router-dom";

export default function Root() {
  return (
    <>
      {/* all the other elements */}
      <div id="detail">
        <Outlet />
      </div>
    </>
  );
}
```
  
Enrutamiento del lado del cliente
Es posible que lo haya notado o no, pero cuando hacemos clic en los enlaces en la barra lateral, el navegador realiza una solicitud de documento completo para la siguiente URL en lugar de usar React Router.

El enrutamiento del lado del cliente permite que nuestra aplicación actualice la URL sin solicitar otro documento del servidor. En su lugar, la aplicación puede generar inmediatamente una nueva interfaz de usuario. Hagamos que suceda con <Link>.

👉 Cambiar la barra lateral ```<a href>a<Link to>```
  
``` js
import { Outlet, Link } from "react-router-dom";

export default function Root() {
  return (
    <>
      <div id="sidebar">
        {/* other elements */}

        <nav>
          <ul>
            <li>
              <Link to={`contacts/1`}>Your Name</Link>
            </li>
            <li>
              <Link to={`contacts/2`}>Your Friend</Link>
            </li>
          </ul>
        </nav>

        {/* other elements */}
      </div>
    </>
  );
}
```
  
Puede abrir la pestaña de red en las herramientas de desarrollo del navegador para ver que ya no solicita documentos.

Cargando datos
La mayoría de las veces, los segmentos de URL, los diseños y los datos se acoplan (¿se triplican?) juntos. Ya podemos verlo en esta aplicación:

``` console
URL Segment	Component	Data
/	          <Root>    	  list of contacts
contacts/:id	<Contact>	  individual contact
```
  
Debido a este acoplamiento natural, React Router tiene convenciones de datos para obtener datos fácilmente en los componentes de su ruta.

Hay dos API que usaremos para cargar datos loadery useLoaderData. Primero crearemos y exportaremos una función de cargador en el módulo raíz, luego la conectaremos a la ruta. Finalmente, accederemos y renderizaremos los datos.

👉 Exportar un cargador desde ```root.jsx```
  
``` js
import { Outlet, Link } from "react-router-dom";
import { getContacts } from "../contacts";

export async function loader() {
  const contacts = await getContacts();
  return { contacts };
}
```
  
👉 Configurar el cargador en la ruta

``` js
/* other imports */
import Root, { loader as rootLoader } from "./routes/root";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
      },
    ],
  },
]);
```
  
👉 Acceder y renderizar los datos

``` js
import {
  Outlet,
  Link,
  useLoaderData,
} from "react-router-dom";
import { getContacts } from "../contacts";

/* other code */

export default function Root() {
  const { contacts } = useLoaderData();
  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        {/* other code */}

        <nav>
          {contacts.length ? (
            <ul>
              {contacts.map((contact) => (
                <li key={contact.id}>
                  <Link to={`contacts/${contact.id}`}>
                    {contact.first || contact.last ? (
                      <>
                        {contact.first} {contact.last}
                      </>
                    ) : (
                      <i>No Name</i>
                    )}{" "}
                    {contact.favorite && <span>★</span>}
                  </Link>
                </li>
              ))}
            </ul>
          ) : (
            <p>
              <i>No contacts</i>
            </p>
          )}
        </nav>

        {/* other code */}
      </div>
    </>
  );
}
```
 

Escritura de datos + formularios HTML
Crearemos nuestro primer contacto en un segundo, pero primero hablemos de HTML.

React Router emula la navegación de formularios HTML como la primitiva de mutación de datos, de acuerdo con el desarrollo web antes de la explosión cámbrica de JavaScript. Le brinda las capacidades de UX de las aplicaciones renderizadas por el cliente con la simplicidad del modelo web de la "vieja escuela".

Si bien algunos desarrolladores web no están familiarizados con ellos, los formularios HTML en realidad provocan una navegación en el navegador, como hacer clic en un enlace. La única diferencia está en la solicitud: los enlaces solo pueden cambiar la URL, mientras que los formularios también pueden cambiar el método de solicitud (GET vs POST) y el cuerpo de la solicitud (datos del formulario POST).

Sin el enrutamiento del lado del cliente, el navegador serializará los datos del formulario automáticamente y los enviará al servidor como el cuerpo de la solicitud para POST y como URLSearchParams para GET. React Router hace lo mismo, excepto que en lugar de enviar la solicitud al servidor, utiliza el enrutamiento del lado del cliente y la envía a una ruta action.

Podemos probar esto haciendo clic en el botón "Nuevo" en nuestra aplicación. La aplicación debería explotar porque el servidor Vite no está configurado para manejar una solicitud POST (envía un 404, aunque probablemente debería ser un 405 🤷).


En lugar de enviar ese POST al servidor de Vite para crear un nuevo contacto, utilicemos en su lugar el enrutamiento del lado del cliente.

Creación de contactos
Crearemos nuevos contactos exportando un actionen nuestra ruta raíz, conectándolo a la configuración de la ruta y cambiando nuestro <form>a un React Router <Form>.

👉 Crea la acción y cambia <form>a<Form>

``` js
import {
  Outlet,
  Link,
  useLoaderData,
  Form,
} from "react-router-dom";
import { getContacts, createContact } from "../contacts";

export async function action() {
  const contact = await createContact();
  return { contact };
}

/* other code */

export default function Root() {
  const { contacts } = useLoaderData();
  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          {/* other code */}
          <Form method="post">
            <button type="submit">New</button>
          </Form>
        </div>

        {/* other code */}
      </div>
    </>
  );
}
```
  
👉 Importar y configurar la acción en la ruta

``` js
/* other imports */

import Root, {
  loader as rootLoader,
  action as rootAction,
} from "./routes/root";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    action: rootAction,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
      },
    ],
  },
]);
```
  
¡Eso es todo! Continúe y haga clic en el botón "Nuevo" y debería ver aparecer un nuevo registro en la lista 🥳


El createContactmétodo simplemente crea un contacto vacío sin nombre ni datos ni nada. ¡Pero aún crea un registro, lo prometo!

🧐 Espera un segundo... ¿Cómo se actualizó la barra lateral? ¿Dónde llamamos al action? ¿Dónde está el código para recuperar los datos? ¿Dónde están useState, onSubmity useEffect?!

Aquí es donde aparece el modelo de programación de la "web de la vieja escuela". Como discutimos anteriormente, <Form>evita que el navegador envíe la solicitud al servidor y la envía a su ruta actionen su lugar. En la semántica web, un POST generalmente significa que algunos datos están cambiando. Por convención, React Router usa esto como una sugerencia para revalidar automáticamente los datos en la página después de que finaliza la acción. ¡Eso significa que todos sus useLoaderDataganchos se actualizan y la interfaz de usuario permanece sincronizada con sus datos automáticamente! Muy genial.

Parámetros de URL en cargadores
👉 Haga clic en el registro Sin nombre

Deberíamos volver a ver nuestra antigua página de contacto estática, con una diferencia: la URL ahora tiene una identificación real para el registro.


Al revisar la configuración de la ruta, la ruta se ve así:
  
``` js
[
  {
    path: "contacts/:contactId",
    element: <Contact />,
  },
];
```
  
Tenga en cuenta el :contactIdsegmento de URL. Los dos puntos ( :) tienen un significado especial, convirtiéndolo en un "segmento dinámico". Los segmentos dinámicos coincidirán con los valores dinámicos (cambiantes) en esa posición de la URL, como la identificación del contacto. Llamamos a estos valores en la URL "URL Params", o simplemente "params" para abreviar.

Estos paramsse pasan al cargador con claves que coinciden con el segmento dinámico. Por ejemplo, nuestro segmento tiene un nombre, :contactIdpor lo que el valor se pasará como params.contactId.

Estos parámetros se utilizan con mayor frecuencia para buscar un registro por ID. Probémoslo.

👉 Agregue un cargador a la página de contacto y acceda a los datos conuseLoaderData

``` js
import { Form, useLoaderData } from "react-router-dom";
import { getContact } from "../contacts";

export async function loader({ params }) {
  const contact = await getContact(params.contactId);
  return { contact };
}

export default function Contact() {
  const { contact } = useLoaderData();
  // existing code
}
```
  
👉 Configurar el cargador en la ruta

``` js
/* existing code */
import Contact, {
  loader as contactLoader,
} from "./routes/contact";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    action: rootAction,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
        loader: contactLoader,
      },
    ],
  },
]);

/* existing code */
```
  
Actualización de datos
Al igual que la creación de datos, actualiza los datos con <Form>. Hagamos una nueva ruta en contacts/:contactId/edit. Nuevamente, comenzaremos con el componente y luego lo conectaremos a la configuración de la ruta.

👉 Crear el componente de edición

``` console
touch src/routes/edit.jsx
```
  
👉 Agregue la interfaz de usuario de la página de edición

Nada que no hayamos visto antes, siéntete libre de ```copiar/pegar```:
  
``` js
import { Form, useLoaderData } from "react-router-dom";

export default function EditContact() {
  const { contact } = useLoaderData();

  return (
    <Form method="post" id="contact-form">
      <p>
        <span>Name</span>
        <input
          placeholder="First"
          aria-label="First name"
          type="text"
          name="first"
          defaultValue={contact.first}
        />
        <input
          placeholder="Last"
          aria-label="Last name"
          type="text"
          name="last"
          defaultValue={contact.last}
        />
      </p>
      <label>
        <span>Twitter</span>
        <input
          type="text"
          name="twitter"
          placeholder="@jack"
          defaultValue={contact.twitter}
        />
      </label>
      <label>
        <span>Avatar URL</span>
        <input
          placeholder="https://example.com/avatar.jpg"
          aria-label="Avatar URL"
          type="text"
          name="avatar"
          defaultValue={contact.avatar}
        />
      </label>
      <label>
        <span>Notes</span>
        <textarea
          name="notes"
          defaultValue={contact.notes}
          rows={6}
        />
      </label>
      <p>
        <button type="submit">Save</button>
        <button type="button">Cancel</button>
      </p>
    </Form>
  );
}
```
  
👉 Agrega la nueva ruta de edición

``` js
/* existing code */
import EditContact from "./routes/edit";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    action: rootAction,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
        loader: contactLoader,
      },
      {
        path: "contacts/:contactId/edit",
        element: <EditContact />,
        loader: contactLoader,
      },
    ],
  },
]);

/* existing code */
```
  
Queremos que se represente en la salida de la ruta raíz, por lo que la convertimos en hermana de la ruta secundaria existente.

(Puede notar que reutilizamos el contactLoaderpara esta ruta. Esto es solo porque estamos siendo vagos en el tutorial. No hay razón para intentar compartir cargadores entre rutas, generalmente tienen los suyos).

Muy bien, hacer clic en el botón "Editar" nos da esta nueva interfaz de usuario:


Actualización de contactos con FormData
La ruta de edición que acabamos de crear ya muestra un formulario. Todo lo que tenemos que hacer para actualizar el registro es conectar una acción a la ruta. El formulario se publicará en la acción y los datos se revalidarán automáticamente.

👉 Agregue una acción al módulo de edición
  
``` js
import {
  Form,
  useLoaderData,
  redirect,
} from "react-router-dom";
import { updateContact } from "../contacts";

export async function action({ request, params }) {
  const formData = await request.formData();
  const updates = Object.fromEntries(formData);
  await updateContact(params.contactId, updates);
  return redirect(`/contacts/${params.contactId}`);
}

/* existing code */
👉 Conecta la acción a la ruta

/* existing code */
import EditContact, {
  action as editAction,
} from "./routes/edit";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    action: rootAction,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
        loader: contactLoader,
      },
      {
        path: "contacts/:contactId/edit",
        element: <EditContact />,
        loader: contactLoader,
        action: editAction,
      },
    ],
  },
]);

/* existing code */
```
  
Complete el formulario, presione guardar, ¡y debería ver algo como esto! (Excepto que es más agradable a la vista y tal vez menos peludo).


Discusión sobre mutaciones
😑 Funcionó, pero no tengo idea de lo que está pasando aquí...

Profundicemos un poco...

Abre ```src/routes/edit.jsxy``` mira los elementos del formulario. Observe cómo cada uno tiene un nombre:

``` js
<input
  placeholder="First"
  aria-label="First name"
  type="text"
  name="first"
  defaultValue={contact.first}
/>
```
  
Sin JavaScript, cuando se envía un formulario, el navegador lo crea FormDatay lo configura como el cuerpo de la solicitud cuando lo envía al servidor. Como se mencionó anteriormente, React Router evita eso y envía la solicitud a su acción en su lugar, incluido el archivo FormData.

Se puede acceder a cada campo del formulario con formData.get(name). Por ejemplo, dado el campo de entrada de arriba, puede acceder al nombre y apellido de esta manera:

``` js
export async function action({ request, params }) {
  const formData = await request.formData();
  const firstName = formData.get("first");
  const lastName = formData.get("last");
  // ...
}
```
  
Dado que tenemos un puñado de campos de formulario, solíamos Object.fromEntriesrecopilarlos todos en un objeto, que es exactamente lo que updateContactquiere nuestra función.

``` js
const updates = Object.fromEntries(formData);
updates.first; // "Some"
updates.last; // "Name"
```
  
Aparte de action, ninguna de estas API que estamos discutiendo son proporcionadas por React Router: request, request.formData, Object.fromEntriesson todas proporcionadas por la plataforma web.

Después de que terminemos la acción, tenga en cuenta redirectal final:
  
``` js
export async function action({ request, params }) {
  const formData = await request.formData();
  const updates = Object.fromEntries(formData);
  await updateContact(params.contactId, updates);
  return redirect(`/contacts/${params.contactId}`);
}
```
  
Los cargadores y las acciones pueden devolver unResponse (¡tiene sentido, ya que recibieron un Request!). El redirectasistente simplemente facilita la devolución de una respuesta que le dice a la aplicación que cambie de ubicación.

Sin el enrutamiento del lado del cliente, si un servidor redirigiese después de una solicitud POST, la nueva página obtendría los últimos datos y se representaría. Como aprendimos antes, React Router emula este modelo y automáticamente revalida los datos en la página después de la acción. Es por eso que la barra lateral se actualiza automáticamente cuando guardamos el formulario. El código de revalidación adicional no existe sin el enrutamiento del lado del cliente, por lo que tampoco es necesario que exista con el enrutamiento del lado del cliente.

Redirigir nuevos registros a la página de edición
Ahora que sabemos cómo redirigir, actualicemos la acción que crea nuevos contactos para redirigir a la página de edición:

👉 Redirigir a la página de edición del nuevo registro

``` js
import {
  Outlet,
  Link,
  useLoaderData,
  Form,
  redirect,
} from "react-router-dom";
import { getContacts, createContact } from "../contacts";

export async function action() {
  const contact = await createContact();
  return redirect(`/contacts/${contact.id}/edit`);
}
```
  
Ahora, cuando hagamos clic en "Nuevo", deberíamos terminar en la página de edición:


👉 Agrega un puñado de registros

Voy a usar la alineación estelar de oradores de la primera Conferencia Remix 😁


Estilo de enlace activo
Ahora que tenemos un montón de registros, no está claro cuál estamos viendo en la barra lateral. Podemos usar ```NavLink``` para arreglar esto.

👉 Usa un NavLinken la barra lateral

``` js
import {
  Outlet,
  NavLink,
  useLoaderData,
  Form,
  redirect,
} from "react-router-dom";

export default function Root() {
  return (
    <>
      <div id="sidebar">
        {/* other code */}

        <nav>
          {contacts.length ? (
            <ul>
              {contacts.map((contact) => (
                <li key={contact.id}>
                  <NavLink
                    to={`contacts/${contact.id}`}
                    className={({ isActive, isPending }) =>
                      isActive
                        ? "active"
                        : isPending
                        ? "pending"
                        : ""
                    }
                  >
                    {/* other code */}
                  </NavLink>
                </li>
              ))}
            </ul>
          ) : (
            <p>{/* other code */}</p>
          )}
        </nav>
      </div>
    </>
  );
}
```
  
Tenga en cuenta que estamos pasando una función a ```className```. Cuando el usuario está en la URL en el ```NavLink```, entonces ```isActive``` será verdadero. Cuando esté a punto de estar activo (los datos aún se están cargando), ```isPending``` será verdadero. Esto nos permite indicar fácilmente dónde está el usuario, así como brindar comentarios inmediatos sobre los enlaces en los que se ha hecho clic, pero aún estamos esperando que se carguen los datos.


Interfaz de usuario pendiente global
A medida que el usuario navega por la aplicación, React Router dejará la página anterior mientras se cargan los datos para la página siguiente. Es posible que haya notado que la aplicación no responde un poco al hacer clic en la lista. Proporcionemos al usuario algunos comentarios para que la aplicación no parezca que no responde.

React Router administra todo el estado detrás de escena y revela las partes que necesita para crear aplicaciones web dinámicas. En este caso, usaremos el ```useNavigation``` gancho.

👉 ```useNavigation``` para agregar una interfaz de usuario pendiente global

``` js
import {
  // existing code
  useNavigation,
} from "react-router-dom";

// existing code

export default function Root() {
  const { contacts } = useLoaderData();
  const navigation = useNavigation();

  return (
    <>
      <div id="sidebar">{/* existing code */}</div>
      <div
        id="detail"
        className={
          navigation.state === "loading" ? "loading" : ""
        }
      >
        <Outlet />
      </div>
    </>
  );
}
```
  
```useNavigation``` devuelve el estado de navegación actual: puede ser uno de "idle" | "submitting" | "loading".

En nuestro caso, agregamos una "loading" clase a la parte principal de la aplicación si no estamos inactivos. Luego, el CSS agrega un buen desvanecimiento después de un breve retraso (para evitar el parpadeo de la interfaz de usuario para cargas rápidas). Sin embargo, podrías hacer lo que quieras, como mostrar una rueda giratoria o una barra de carga en la parte superior.


Tenga en cuenta que nuestro modelo de datos ( ```src/contacts.js``` ) tiene un caché del lado del cliente, por lo que navegar al mismo contacto es rápido la segunda vez. Este comportamiento no es React Router, volverá a cargar datos para cambiar rutas sin importar si ha estado allí antes o no. Sin embargo, evita llamar a los cargadores para rutas que no cambian (como la lista) durante una navegación.

Eliminación de registros
Si revisamos el código en la ruta de contacto, podemos encontrar que el botón de eliminar se ve así:

``` js
<Form
  method="post"
  action="destroy"
  onSubmit={(event) => {
    if (
      !confirm(
        "Please confirm you want to delete this record."
      )
    ) {
      event.preventDefault();
    }
  }}
>
  <button type="submit">Delete</button>
</Form>
```
  
Tenga en cuenta los actionpuntos a "destroy". Like ```<Link to>```, <Form action>puede tomar un valor relativo . Dado que el formulario se representa en contact/:contactId, una acción relativa con destroyenviará el formulario ```contact/:contactId/destroy``` cuando se haga clic en él.

En este punto, debe saber todo lo que necesita saber para que el botón Eliminar funcione. ¿Quizás darle una oportunidad antes de continuar? Necesitarás:

Una nueva ruta
Y ```action``` en esa ruta
```deleteContactdesrc/contacts.js```
  
👉 Crear el módulo de ruta "destruir"
  
``` console
touch src/routes/destroy.jsx
```
  
👉 Agrega la acción de destruir

``` js
import { redirect } from "react-router-dom";
import { deleteContact } from "../contacts";

export async function action({ params }) {
  await deleteContact(params.contactId);
  return redirect("/");
}
```
  
👉 Agregue la ruta de destrucción a la configuración de la ruta

``` js
/* existing code */
import { action as destroyAction } from "./routes/destroy";

const router = createBrowserRouter([
  {
    path: "/",
    /* existing root route props */
    children: [
      /* existing routes */
      {
        path: "contacts/:contactId/destroy",
        action: destroyAction,
      },
    ],
  },
]);

/* existing code */
```
  
Muy bien, navegue hasta un registro y haga clic en el botón "Eliminar". ¡Funciona!

😅 Todavía estoy confundido por qué todo esto funciona

Cuando el usuario hace clic en el botón enviar:

```<Form>``` evita el comportamiento predeterminado del navegador de enviar una nueva solicitud POST al servidor, pero en su lugar emula el navegador creando una solicitud POST con enrutamiento del lado del cliente
Coincide ```<Form action="destroy">``` con la nueva ruta "```contacts/:contactId/destroy```"y le envía la solicitud.
Después de que la acción redirige, React Router llama a todos los cargadores de datos en la página para obtener los valores más recientes (esto es "revalidación"). useLoaderDatadevuelve nuevos valores y hace que los componentes se actualicen.
Agrega un formulario, agrega una acción, React Router hace el resto.

Errores contextuales
Solo por diversión, arroja un error en la acción de destrucción:

``` js
export async function action({ params }) {
  throw new Error("oh dang!");
  await deleteContact(params.contactId);
  return redirect("/");
}
```
  
¿Reconoces esa pantalla? Es nuestro errorElementde antes. El usuario, sin embargo, realmente no puede hacer nada para recuperarse de esta pantalla, excepto actualizar.

Vamos a crear un mensaje de error contextual para la ruta de destrucción:

``` js
[
  /* other routes */
  {
    path: "contacts/:contactId/destroy",
    action: destroyAction,
    errorElement: <div>Oops! There was an error.</div>,
  },
];
```
  
Ahora inténtalo de nuevo:

Nuestro usuario ahora tiene más opciones que actualizar de golpe, puede continuar interactuando con las partes de la página que no tienen problemas 🙌

Debido a que la ruta de destrucción tiene la suya propia errorElementy es secundaria de la ruta raíz, el error aparecerá allí en lugar de la raíz. Como probablemente hayas notado, estos errores surgen al más cercano errorElement. Agregue tantos o tan pocos como desee, siempre que tenga uno en la raíz.

Índice Rutas
Cuando cargamos la aplicación, notará una gran página en blanco en el lado derecho de nuestra lista.


Cuando una ruta tiene elementos secundarios y se encuentra en la ruta de la ruta principal, no <Outlet>tiene nada que representar porque ningún elemento secundario coincide. Puede pensar en las rutas de índice como la ruta secundaria predeterminada para llenar ese espacio.

👉 Crear el módulo de ruta de índice
  
``` console
touch src/routes/index.jsx
```
  
👉 Complete los elementos del componente de índice

Siéntase libre de copiar y pegar, nada especial aquí.

``` js
export default function Index() {
  return (
    <p id="zero-state">
      This is a demo for React Router.
      <br />
      Check out{" "}
      <a href="https://reactrouter.com">
        the docs at reactrouter.com
      </a>
      .
    </p>
  );
}
```
  
👉 Configurar la ruta índice

``` js
// existing code
import Index from "./routes/index";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    action: rootAction,
    children: [
      { index: true, element: <Index /> },
      /* existing routes */
    ],
  },
]);
```
  
Tenga en cuenta el ```{ index:true }``` lugar de ```{ path: "" }```. Eso le dice al enrutador que haga coincidir y represente esta ruta cuando el usuario se encuentra en la ruta exacta de la ruta principal, por lo que no hay otras rutas secundarias para representar en el archivo ```<Outlet>```.


¡Voila! No más espacios en blanco. Es común colocar tableros, estadísticas, feeds, etc. en las rutas de índice. También pueden participar en la carga de datos.

Botón Cancelar
En la página de edición tenemos un botón de cancelar que todavía no hace nada. Nos gustaría que hiciera lo mismo que el botón Atrás del navegador.

Necesitaremos un controlador de clics en el botón, así como useNavigatede React Router.

👉 Agregue el controlador de clic del botón cancelar con ```useNavigate```

``` js
import {
  Form,
  useLoaderData,
  redirect,
  useNavigate,
} from "react-router-dom";

export default function EditContact() {
  const { contact } = useLoaderData();
  const navigate = useNavigate();

  return (
    <Form method="post" id="contact-form">
      {/* existing code */}

      <p>
        <button type="submit">Save</button>
        <button
          type="button"
          onClick={() => {
            navigate(-1);
          }}
        >
          Cancel
        </button>
      </p>
    </Form>
  );
}
```
  
Ahora, cuando el usuario haga clic en "Cancelar", se le devolverá una entrada en el historial del navegador.

🧐 ¿Por qué no hay event.preventDefaulten el botón?

A ```<button type="button">```, aunque aparentemente redundante, es la forma HTML de evitar que un botón envíe su formulario.

Dos características más para ir. ¡Estamos en la recta final!

Parámetros de búsqueda de URL y envíos GET
Toda nuestra interfaz de usuario interactiva hasta ahora ha sido enlaces que cambian la URL o formularios que publican datos en acciones. El campo de búsqueda es interesante porque es una mezcla de ambos: es un formulario pero solo cambia la URL, no cambia los datos.

En este momento es solo un HTML normal <form>, no un React Router <Form>. Veamos qué hace el navegador con él por defecto:

👉 Escriba un nombre en el campo de búsqueda y presione la tecla Intro

Tenga en cuenta que la URL del navegador ahora contiene su consulta en la URL como URLSearchParams :

```http://127.0.0.1:5173/?q=ryan```
  
Si revisamos el formulario de búsqueda, se ve así:

``` js
<form id="search-form" role="search">
  <input
    id="q"
    aria-label="Search contacts"
    placeholder="Search"
    type="search"
    name="q"
  />
  <div id="search-spinner" aria-hidden hidden={true} />
  <div className="sr-only" aria-live="polite"></div>
</form>
```
  
Como hemos visto antes, los navegadores pueden serializar formularios por el nameatributo de sus elementos de entrada. El nombre de esta entrada es q, por eso la URL tiene ```?q=```. Si le pusiéramos un nombre, ```search``` la URL sería ```?search=```.

Tenga en cuenta que este formulario es diferente de los demás que hemos usado, no tiene <form method="post">. El valor predeterminado methodes "get". Eso significa que cuando el navegador crea la solicitud para el siguiente documento, no coloca los datos del formulario en el cuerpo POST de la solicitud, sino en el URLSearchParamsde una solicitud GET.

GET Envíos con enrutamiento del lado del cliente
Usemos el enrutamiento del lado del cliente para enviar este formulario y filtrar la lista en nuestro cargador existente.

👉 Cambiar ```<form>``` a ```<Form>```

``` js
<Form id="search-form" role="search">
  <input
    id="q"
    aria-label="Search contacts"
    placeholder="Search"
    type="search"
    name="q"
  />
  <div id="search-spinner" aria-hidden hidden={true} />
  <div className="sr-only" aria-live="polite"></div>
</Form>
```
  
👉 Filtra la lista si hay URLSearchParams

``` js
export async function loader({ request }) {
  const url = new URL(request.url);
  const q = url.searchParams.get("q");
  const contacts = await getContacts(q);
  return { contacts };
}
```

Debido a que se trata de un GET, no de un POST, React Router no llama al action. Enviar un formulario GET es lo mismo que hacer clic en un enlace: solo cambia la URL. Es por eso que el código que agregamos para filtrar está en el loader, no en el actionde esta ruta.

Esto también significa que es una página de navegación normal. Puede hacer clic en el botón Atrás para volver a donde estaba.

Sincronización de URL con el estado del formulario
Aquí hay un par de problemas de UX que podemos solucionar rápidamente.

Si vuelve a hacer clic después de una búsqueda, el campo del formulario aún tiene el valor que ingresó aunque la lista ya no esté filtrada.
Si actualiza la página después de buscar, el campo del formulario ya no tiene el valor, aunque la lista esté filtrada
En otras palabras, la URL y el estado de nuestro formulario no están sincronizados.

👉 Regrese qde su cargador y configúrelo como el valor predeterminado del campo de búsqueda

``` js
// existing code

export async function loader({ request }) {
  const url = new URL(request.url);
  const q = url.searchParams.get("q");
  const contacts = await getContacts(q);
  return { contacts, q };
}

export default function Root() {
  const { contacts, q } = useLoaderData();
  const navigation = useNavigation();

  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <Form id="search-form" role="search">
            <input
              id="q"
              aria-label="Search contacts"
              placeholder="Search"
              type="search"
              name="q"
              defaultValue={q}
            />
            {/* existing code */}
          </Form>
          {/* existing code */}
        </div>
        {/* existing code */}
      </div>
      {/* existing code */}
    </>
  );
}
```
  
Eso resuelve el problema (2). Si actualiza la página ahora, el campo de entrada mostrará la consulta.


Ahora para el problema (1), haciendo clic en el botón Atrás y actualizando la entrada. Podemos traer useEffectdesde React para manipular el estado del formulario en el DOM directamente.

👉 Sincronice el valor de entrada con los parámetros de búsqueda de URL

``` js
import { useEffect } from "react";

// existing code

export default function Root() {
  const { contacts, q } = useLoaderData();
  const navigation = useNavigation();

  useEffect(() => {
    document.getElementById("q").value = q;
  }, [q]);

  // existing code
}
```
  
🤔 ¿No deberías usar un componente controlado y React State para esto?

Ciertamente podría hacer esto como un componente controlado, pero terminará con más complejidad para el mismo comportamiento. No controlas la URL, el usuario lo hace con los botones de avance/retroceso. Habría más puntos de sincronización con un componente controlado.

Si todavía está preocupado, amplíe esto para ver cómo se vería
Envío de formulariosonChange
Tenemos que tomar una decisión sobre el producto aquí. Para esta interfaz de usuario, probablemente preferimos que el filtrado se realice con cada pulsación de tecla en lugar de cuando el formulario se envía explícitamente.

Ya hemos visto useNavigate, usaremos su primo, useSubmit, para esto.

``` js
// existing code
import {
  // existing code
  useSubmit,
} from "react-router-dom";

export default function Root() {
  const { contacts, q } = useLoaderData();
  const navigation = useNavigation();
  const submit = useSubmit();

  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <Form id="search-form" role="search">
            <input
              id="q"
              aria-label="Search contacts"
              placeholder="Search"
              type="search"
              name="q"
              defaultValue={q}
              onChange={(event) => {
                submit(event.currentTarget.form);
              }}
            />
            {/* existing code */}
          </Form>
          {/* existing code */}
        </div>
        {/* existing code */}
      </div>
      {/* existing code */}
    </>
  );
}
```
  
Ahora, mientras escribe, el formulario se envía automáticamente.

Tenga en cuenta el argumento de submit. Estamos de paso event.currentTarget.form. es currentTargetel nodo DOM al que se adjunta el evento y currentTarget.formes el nodo de formulario principal de la entrada. La submitfunción serializará y enviará cualquier formulario que le pases.

Adición de Spinner de búsqueda
En una aplicación de producción, es probable que esta búsqueda busque registros en una base de datos que es demasiado grande para enviarlos todos a la vez y filtrar el lado del cliente. Es por eso que esta demostración tiene una latencia de red falsa.

Sin ningún indicador de carga, la búsqueda se siente un poco lenta. Incluso si pudiéramos hacer que nuestra base de datos fuera más rápida, siempre tendremos la latencia de la red del usuario en el camino y fuera de nuestro control. Para una mejor UX, agreguemos algunos comentarios inmediatos de UI para la búsqueda. Para esto lo usaremos useNavigationde nuevo.

👉 Agrega el selector de búsqueda

``` js
// existing code

export default function Root() {
  const { contacts, q } = useLoaderData();
  const navigation = useNavigation();
  const submit = useSubmit();

  const searching =
    navigation.location &&
    new URLSearchParams(navigation.location.search).has(
      "q"
    );

  useEffect(() => {
    document.getElementById("q").value = q;
  }, [q]);

  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <Form id="search-form" role="search">
            <input
              id="q"
              className={searching ? "loading" : ""}
              // existing code
            />
            <div
              id="search-spinner"
              aria-hidden
              hidden={!searching}
            />
            {/* existing code */}
          </Form>
          {/* existing code */}
        </div>
        {/* existing code */}
      </div>
      {/* existing code */}
    </>
  );
}
```

Aparecerá ```navigation.location``` cuando la aplicación navegue a una nueva URL y cargue los datos para ella. Luego desaparece cuando ya no hay navegación pendiente.

Administrar la pila de historial
Ahora que el formulario se envía para cada pulsación de tecla, si escribimos los caracteres "seba" y luego los borramos con retroceso, terminamos con 7 nuevas entradas en la pila 😂. Definitivamente no queremos esto


Podemos evitar esto reemplazando la entrada actual en la pila de historial con la página siguiente, en lugar de empujarla.

👉 Uso replaceensubmit

``` js
// existing code

export default function Root() {
  // existing code

  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <Form id="search-form" role="search">
            <input
              id="q"
              // existing code
              onChange={(event) => {
                const isFirstSearch = q == null;
                submit(event.currentTarget.form, {
                  replace: !isFirstSearch,
                });
              }}
            />
            {/* existing code */}
          </Form>
          {/* existing code */}
        </div>
        {/* existing code */}
      </div>
      {/* existing code */}
    </>
  );
}
```
  
Solo queremos reemplazar los resultados de la búsqueda, no la página antes de comenzar a buscar, por lo que hacemos una verificación rápida si esta es la primera búsqueda o no y luego decidimos reemplazar.

Cada pulsación de tecla ya no crea nuevas entradas, por lo que el usuario puede volver a hacer clic fuera de los resultados de búsqueda sin tener que hacer clic 7 veces 😅.

Mutaciones sin navegación
Hasta ahora, todas nuestras mutaciones (las veces que cambiamos datos) han utilizado formularios que navegan, creando nuevas entradas en la pila de historial. Si bien estos flujos de usuarios son comunes, es igualmente común querer cambiar los datos sin provocar una navegación.

Para estos casos, tenemos el useFetchergancho. Nos permite comunicarnos con cargadores y acciones sin provocar una navegación.

El botón ★ en la página de contacto tiene sentido para esto. No estamos creando o eliminando un nuevo registro, no queremos cambiar las páginas, simplemente queremos cambiar los datos en la página que estamos viendo.

👉 Cambiar el ```<Favorite>``` formulario a un formulario de búsqueda

``` js
import {
  useLoaderData,
  Form,
  useFetcher,
} from "react-router-dom";

// existing code

function Favorite({ contact }) {
  const fetcher = useFetcher();
  let favorite = contact.favorite;

  return (
    <fetcher.Form method="post">
      <button
        name="favorite"
        value={favorite ? "false" : "true"}
        aria-label={
          favorite
            ? "Remove from favorites"
            : "Add to favorites"
        }
      >
        {favorite ? "★" : "☆"}
      </button>
    </fetcher.Form>
  );
}
```
  
Quizá quiera echarle un vistazo a ese formulario mientras estamos aquí. Como siempre, nuestro formulario tiene campos con un nameaccesorio. Este formulario se enviará formDatacon una favoriteclave que es "true" | "false". Como lo tiene method="post"llamará a la acción. Como no hay <fetcher.Form action="...">accesorios, se publicará en la ruta donde se representa el formulario.

👉 Crea la acción

``` js
// existing code
import { getContact, updateContact } from "../contacts";

export async function action({ request, params }) {
  let formData = await request.formData();
  return updateContact(params.contactId, {
    favorite: formData.get("favorite") === "true",
  });
}

export default function Contact() {
  // existing code
}
```
  
Bastante simple. Extraiga los datos del formulario de la solicitud y envíelos al modelo de datos.

👉 Configurar la nueva acción de la ruta

``` js
// existing code
import Contact, {
  loader as contactLoader,
  action as contactAction,
} from "./routes/contact";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    action: rootAction,
    children: [
      { index: true, element: <Index /> },
      {
        path: "contacts/:contactId",
        element: <Contact />,
        loader: contactLoader,
        action: contactAction,
      },
      /* existing code */
    ],
  },
]);
```
  
Muy bien, estamos listos para hacer clic en la estrella junto al nombre del usuario.


Mira eso, ambas estrellas se actualizan automáticamente. Nuestro nuevo ```<fetcher.Form method="post">``` funciona casi exactamente como el ```<Form>``` que hemos estado usando: llama a la acción y luego todos los datos se revalidan automáticamente, incluso sus errores se detectarán de la misma manera.

Sin embargo, hay una diferencia clave, no es una navegación: la URL no cambia, la pila de historial no se ve afectada.

IU optimista
Probablemente notó que la aplicación no respondía cuando hicimos clic en el botón favorito de la última sección. Una vez más, agregamos algo de latencia de red porque la tendrás en el mundo real.

Para darle al usuario algunos comentarios, podríamos poner la estrella en un estado de carga con fetcher.state(muy parecido navigation.statea antes), pero podemos hacer algo aún mejor esta vez. Podemos usar una estrategia llamada "interfaz de usuario optimista"

El buscador conoce los datos del formulario que se envían a la acción, por lo que está disponible para usted en ```fetcher.formData```. Usaremos eso para actualizar inmediatamente el estado de la estrella, aunque la red no haya terminado. Si la actualización finalmente falla, la interfaz de usuario volverá a los datos reales.

👉 Lea el valor optimista ```defetcher.formData```

``` js
// existing code

function Favorite({ contact }) {
  const fetcher = useFetcher();

  let favorite = contact.favorite;
  if (fetcher.formData) {
    favorite = fetcher.formData.get("favorite") === "true";
  }

  return (
    <fetcher.Form method="post">
      <button
        name="favorite"
        value={favorite ? "false" : "true"}
        aria-label={
          favorite
            ? "Remove from favorites"
            : "Add to favorites"
        }
      >
        {favorite ? "★" : "☆"}
      </button>
    </fetcher.Form>
  );
}
```
  
Si hace clic en el botón ahora, debería ver que la estrella cambia inmediatamente al nuevo estado. En lugar de representar siempre los datos reales, verificamos si el buscador tiene alguno formDataenviado, si es así, lo usaremos en su lugar. Cuando se realiza la acción, ya ```fetcher.formData``` no existirá y volveremos a usar los datos reales. Entonces, incluso si escribe errores en su código de interfaz de usuario optimista, eventualmente volverá al estado correcto 🥹

Datos no encontrados
¿Qué sucede si el contacto que estamos tratando de cargar no existe?


Nuestra raíz errorElementdetecta este error inesperado cuando intentamos generar un ```null``` contacto. Es bueno que el error se manejó correctamente, ¡pero podemos hacerlo mejor!

Siempre que tenga un caso de error esperado en un cargador o acción, como que los datos no existan, puede throw. La pila de llamadas se romperá, React Router la detectará y, en su lugar, se representará la ruta de error. Ni siquiera intentaremos hacer un nullcontacto.

👉 Lanza una respuesta 404 en el cargador

``` js
export async function loader({ params }) {
  const contact = await getContact(params.contactId);
  if (!contact) {
    throw new Response("", {
      status: 404,
      statusText: "Not Found",
    });
  }
  return { contact };
}
```
  
En lugar de generar un error de representación con Cannot read properties of null, evitamos el componente por completo y, en su lugar, representamos la ruta del error, diciéndole al usuario algo más específico.

Esto mantiene tus caminos felices, felices. Sus elementos de ruta no necesitan preocuparse por errores y estados de carga.

Rutas sin caminos
Una última cosa. La última página de error que vimos sería mejor si se representara dentro de la salida raíz, en lugar de toda la página. De hecho, cada error en todas nuestras rutas secundarias sería mejor en la salida, entonces el usuario tiene más opciones que presionar actualizar.

Nos gustaría que se viera así:


Podríamos agregar el elemento de error a cada una de las rutas secundarias pero, dado que es la misma página de error, no se recomienda.

Hay una forma más limpia. Las rutas se pueden usar sin una ruta, lo que les permite participar en el diseño de la interfaz de usuario sin requerir nuevos segmentos de ruta en la URL. Échale un vistazo:

👉 Envuelva las rutas secundarias en una ruta sin ruta

``` js
createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    loader: rootLoader,
    action: rootAction,
    errorElement: <ErrorPage />,
    children: [
      {
        errorElement: <ErrorPage />,
        children: [
          { index: true, element: <Index /> },
          {
            path: "contacts/:contactId",
            element: <Contact />,
            loader: contactLoader,
            action: contactAction,
          },
          /* the rest of the routes */
        ],
      },
    ],
  },
]);
```
  
Cuando se arrojan errores en las rutas secundarias, nuestra nueva ruta sin ruta lo detectará y renderizará, conservando la interfaz de usuario de la ruta raíz.

Rutas JSX
Y como truco final, muchas personas prefieren configurar sus rutas con JSX. Puedes hacer eso con createRoutesFromElements. No hay diferencia funcional entre JSX u objetos al configurar tus rutas, es simplemente una preferencia estilística.

``` js
import {
  createRoutesFromElements,
  createBrowserRouter,
  Route,
} from "react-router-dom";

const router = createBrowserRouter(
  createRoutesFromElements(
    <Route
      path="/"
      element={<Root />}
      loader={rootLoader}
      action={rootAction}
      errorElement={<ErrorPage />}
    >
      <Route errorElement={<ErrorPage />}>
        <Route index element={<Index />} />
        <Route
          path="contacts/:contactId"
          element={<Contact />}
          loader={contactLoader}
          action={contactAction}
        />
        <Route
          path="contacts/:contactId/edit"
          element={<EditContact />}
          loader={contactLoader}
          action={editAction}
        />
        <Route
          path="contacts/:contactId/destroy"
          action={destroyAction}
        />
      </Route>
    </Route>
  )
);
```  

¡Eso es todo! Gracias por darle una oportunidad a React Router. Esperamos que este tutorial le brinde un comienzo sólido para crear excelentes experiencias de usuario. Hay mucho más que puedes hacer con React Router, así que asegúrate de revisar todas las API 😀
