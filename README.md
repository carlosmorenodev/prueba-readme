## Capítulo X: Routing en Aplicaciones React

### 10.1 Introducción al Routing en Aplicaciones de Una Sola Página (SPA)

Las aplicaciones web modernas a menudo se construyen como "Aplicaciones de Una Sola Página" (Single Page Applications - SPA). A diferencia de las aplicaciones web tradicionales que requieren una recarga completa de la página del navegador para cada navegación, una SPA carga una única página HTML y actualiza dinámicamente su contenido a medida que el usuario interactúa con ella. Sin embargo, para que una SPA funcione de manera efectiva y ofrezca una experiencia de usuario coherente con la navegación, necesita una forma de gestionar las diferentes "vistas" o "páginas" dentro de esa única página HTML. Aquí es donde entra en juego el **routing**.

El routing en una SPA permite simular la navegación entre diferentes URLs, como si el usuario estuviera visitando páginas completamente distintas, pero sin una recarga real del navegador. Esto se logra manipulando la URL en la barra de direcciones del navegador (generalmente usando la API de historial del navegador) y renderizando los componentes de React apropiados según la ruta actual.

### 10.2 React Router Dom: La Solución Estándar para el Routing en React

**React Router Dom** es la biblioteca de enrutamiento declarativa más popular y ampliamente utilizada para aplicaciones React. Proporciona un conjunto de componentes que permiten mapear rutas URL a componentes de React, gestionar la navegación, pasar datos entre rutas y mucho más.

#### 10.2.1 Conceptos Fundamentales de React Router Dom

* **Componentes del Router:** En el corazón de React Router Dom se encuentran los componentes del router que envuelven toda la aplicación o la parte de la aplicación que necesita enrutamiento. Los más comunes son:
    * `<BrowserRouter>`: Utiliza la API de historial de HTML5 para mantener la UI sincronizada con la URL. Es el más común para aplicaciones web modernas.
    * `<HashRouter>`: Utiliza el hash de la URL (por ejemplo, `misitio.com/#/sobremi`). Es útil para entornos que no permiten rutas limpias (como servidores de archivos estáticos).
* **`<Routes>` y `<Route>`:**
    * `<Routes>`: Es el contenedor principal para todas tus definiciones de rutas. Renderiza la primera `<Route>` que coincide con la URL actual.
    * `<Route>`: Define una ruta individual. Toma dos props clave:
        * `path`: La ruta URL a la que este componente debe coincidir (ej. `/`, `/sobre-mi`, `/proyectos`).
        * `element`: El componente de React que debe renderizarse cuando la ruta `path` coincide.
* **`<Link>`:** Un componente utilizado para la navegación declarativa. A diferencia de un elemento `<a>` HTML estándar que provoca una recarga completa de la página, `<Link>` de React Router Dom solo actualiza la parte de la UI correspondiente, sin recargar la página. Toma una prop `to` que especifica la ruta a la que se debe navegar.
* **`useNavigate()`:** Un hook de React Router Dom que proporciona una función para la navegación programática (imperativa). Es útil cuando necesitas navegar a una ruta después de una acción del usuario (ej. un envío de formulario, una operación de inicio de sesión exitosa).
* **`useParams()`:** Un hook que permite acceder a los parámetros dinámicos de la URL.
* **`useLocation()`:** Un hook que devuelve el objeto de ubicación actual, que contiene información sobre la URL (pathname, search, hash, state).

#### 10.2.2 Navegación entre Páginas

La navegación entre páginas en React Router Dom se logra principalmente de dos maneras:

1.  **Navegación Declarativa con `<Link>`:**
    Este es el método preferido para la mayoría de los enlaces dentro de tu aplicación. Simplemente envuelve el texto o el elemento clicable con `<Link to="/ruta-destino">...</Link>`.
    ```jsx
    import { Link } from 'react-router-dom';

    function Navegacion() {
        return (
            <nav>
                <Link to="/">Inicio</Link>
                <Link to="/sobre-mi">Sobre Mí</Link>
                <Link to="/proyectos">Proyectos</Link>
            </nav>
        );
    }
    ```

2.  **Navegación Programática con `useNavigate()`:**
    Cuando necesitas activar la navegación basada en algún evento o lógica (por ejemplo, después de un envío de formulario exitoso), puedes usar el hook `useNavigate()`.
    ```jsx
    import { useNavigate } from 'react-router-dom';

    function FormularioLogin() {
        const navigate = useNavigate();

        const handleSubmit = (event) => {
            event.preventDefault();
            // Lógica de autenticación
            if (autenticacionExitosa) {
                navigate('/dashboard'); // Redirige al dashboard
            }
        };

        return (
            <form onSubmit={handleSubmit}>
                {/* Campos del formulario */}
                <button type="submit">Iniciar Sesión</button>
            </form>
        );
    }
    ```

#### 10.2.3 Uso de Parámetros de Ruta

Los parámetros de ruta permiten pasar valores dinámicos en la URL. Son ideales para identificar recursos específicos, como el ID de un producto o el nombre de un usuario. Se definen en la ruta con un prefijo de dos puntos (`:`).

**Definición de la ruta:**
```jsx
<Route path="/usuarios/:id" element={<DetalleUsuario />} />
```

**Acceso a los parámetros en el componente:**
```jsx
import { useParams } from 'react-router-dom';

function DetalleUsuario() {
    const { id } = useParams(); // 'id' coincidirá con ':id' en la ruta
    return (
        <div>
            <h2>Detalle del Usuario {id}</h2>
            {/* Cargar datos del usuario con este ID */}
        </div>
    );
}
```

**Navegación a una ruta con parámetros:**
```jsx
<Link to="/usuarios/123">Ver Usuario 123</Link>
```


[Ver más](#parametros-ruta)





#### 10.2.4 Uso de Query Strings (Cadenas de Consulta)

Las cadenas de consulta (query strings) son otra forma de pasar datos a través de la URL, generalmente para filtrar, ordenar o paginar datos. Se anexan a la URL después de un signo de interrogación (`?`) y consisten en pares `clave=valor` separados por ampersands (`&`).

**Ejemplo de URL con query string:**
`misitio.com/productos?categoria=electronica&orden=precio`

**Acceso a los query strings en el componente:**
React Router Dom no tiene un hook dedicado para query strings como `useParams`. En su lugar, se utiliza el hook `useLocation()` para obtener el objeto de ubicación, y luego se parsea la propiedad `search`. La forma más común de parsear query strings es con la clase `URLSearchParams`.

```jsx
import { useLocation } from 'react-router-dom';

function ListaProductos() {
    const location = useLocation();
    const queryParams = new URLSearchParams(location.search);

    const categoria = queryParams.get('categoria'); // 'electronica'
    const orden = queryParams.get('orden');       // 'precio'

    return (
        <div>
            <h2>Productos</h2>
            {categoria && <p>Categoría: {categoria}</p>}
            {orden && <p>Ordenado por: {orden}</p>}
            {/* Renderizar productos según los filtros */}
        </div>
    );
}
```

---

### 10.3 Proyecto Práctico: Creando un Sitio Web de Portafolio con React Router Dom

Para solidificar los conceptos, construiremos un sitio web de portafolio simple con varias páginas y navegaremos entre ellas utilizando React Router Dom.

#### 10.3.1 Estructura del Proyecto

```
mi-portafolio/
├── public/
├── src/
│   ├── App.js         // Componente principal de la aplicación
│   ├── index.js       // Punto de entrada de React
│   ├── components/
│   │   ├── Navbar.js  // Barra de navegación
│   ├── pages/
│   │   ├── HomePage.js    // Página de Inicio
│   │   ├── AboutPage.js   // Página Sobre Mí
│   │   ├── ProjectsPage.js// Página de Proyectos
│   ├── index.css      // Estilos CSS (opcional)
│   └── App.css        // Estilos CSS (opcional)
├── package.json
└── README.md
```

#### 10.3.2 Pasos de Implementación

**Paso 1: Inicializar el Proyecto React**

Si aún no tienes un proyecto React, créalo usando `create-react-app`:
```bash
npx create-react-app mi-portafolio
cd mi-portafolio
```

**Paso 2: Instalar React Router Dom**
```bash
npm install react-router-dom
# o
yarn add react-router-dom
```

**Paso 3: Crear los Componentes de las Páginas**

Crea los siguientes archivos dentro de `src/pages/`:

* **`HomePage.js`**
    ```jsx
    // src/pages/HomePage.js
    import React from 'react';

    function HomePage() {
        return (
            <div>
                <h1>Bienvenido a mi Portafolio</h1>
                <p>Soy un desarrollador apasionado por crear soluciones web.</p>
            </div>
        );
    }

    export default HomePage;
    ```

* **`AboutPage.js`**
    ```jsx
    // src/pages/AboutPage.js
    import React from 'react';

    function AboutPage() {
        return (
            <div>
                <h1>Sobre Mí</h1>
                <p>Aquí puedes encontrar información sobre mi experiencia, habilidades y formación.</p>
                <p>Me especializo en desarrollo frontend con React.</p>
            </div>
        );
    }

    export default AboutPage;
    ```

* **`ProjectsPage.js`**
    ```jsx
    // src/pages/ProjectsPage.js
    import React from 'react';

    function ProjectsPage() {
        return (
            <div>
                <h1>Mis Proyectos</h1>
                <ul>
                    <li>Proyecto A: Descripción breve del proyecto A.</li>
                    <li>Proyecto B: Descripción breve del proyecto B.</li>
                    <li>Proyecto C: Descripción breve del proyecto C.</li>
                </ul>
            </div>
        );
    }

    export default ProjectsPage;
    ```

**Paso 4: Crear el Componente de la Barra de Navegación**

Crea el archivo `src/components/Navbar.js`:

```jsx
// src/components/Navbar.js
import React from 'react';
import { Link } from 'react-router-dom';
import './Navbar.css'; // Opcional: para estilos CSS

function Navbar() {
    return (
        <nav className="navbar">
            <ul className="navbar-nav">
                <li className="nav-item">
                    <Link to="/" className="nav-link">Inicio</Link>
                </li>
                <li className="nav-item">
                    <Link to="/sobre-mi" className="nav-link">Sobre Mí</Link>
                </li>
                <li className="nav-item">
                    <Link to="/proyectos" className="nav-link">Proyectos</Link>
                </li>
            </ul>
        </nav>
    );
}

export default Navbar;
```

Opcional: Añade algunos estilos básicos a `src/components/Navbar.css` (o donde manejes tus estilos):

```css
/* src/components/Navbar.css */
.navbar {
    background-color: #333;
    padding: 1rem;
}

.navbar-nav {
    list-style: none;
    margin: 0;
    padding: 0;
    display: flex;
    justify-content: center;
}

.nav-item {
    margin: 0 15px;
}

.nav-link {
    color: white;
    text-decoration: none;
    font-weight: bold;
    padding: 0.5rem 1rem;
    transition: background-color 0.3s ease;
}

.nav-link:hover {
    background-color: #555;
    border-radius: 4px;
}
```

**Paso 5: Configurar el Enrutamiento en `App.js`**

Modifica `src/App.js` para incluir el router, la barra de navegación y las rutas.

```jsx
// src/App.js
import React from 'react';
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';
import Navbar from './components/Navbar';
import HomePage from './pages/HomePage';
import AboutPage from './pages/AboutPage';
import ProjectsPage from './pages/ProjectsPage';
import './App.css'; // Opcional: estilos generales de la aplicación

function App() {
    return (
        <Router>
            <div className="App">
                <Navbar /> {/* La barra de navegación siempre visible */}

                <div className="content">
                    <Routes>
                        {/* Define tus rutas aquí */}
                        <Route path="/" element={<HomePage />} />
                        <Route path="/sobre-mi" element={<AboutPage />} />
                        <Route path="/proyectos" element={<ProjectsPage />} />
                        {/* Puedes añadir una ruta para 404 Not Found si lo deseas */}
                        {/* <Route path="*" element={<h1>404: Página no encontrada</h1>} /> */}
                    </Routes>
                </div>
            </div>
        </Router>
    );
}

export default App;
```

Opcional: Añade algunos estilos básicos a `src/App.css`:

```css
/* src/App.css */
.App {
    font-family: Arial, sans-serif;
    text-align: center;
}

.content {
    padding: 20px;
    margin-top: 20px; /* Para separar del navbar */
}
```

**Paso 6: Ejecutar la Aplicación**

Guarda todos los cambios y ejecuta tu aplicación:
```bash
npm start
# o
yarn start
```

Abre tu navegador en `http://localhost:3000`. Deberías ver tu portafolio con la barra de navegación. Al hacer clic en los enlaces "Inicio", "Sobre Mí" y "Proyectos", la URL cambiará y el contenido de la página se actualizará sin recargar el navegador, demostrando el funcionamiento de React Router Dom.

#### 10.3.3 Mejoras y Consideraciones Adicionales

* **Rutas Anidadas:** Para aplicaciones más complejas, puedes definir rutas anidadas para organizar mejor tu estructura de URL y la renderización de componentes.
* **Guards de Ruta (Protección de Rutas):** Puedes implementar lógica para proteger rutas, por ejemplo, impidiendo el acceso a ciertas páginas si el usuario no está autenticado. Esto generalmente se hace creando componentes de "ruta privada" que comprueban el estado de autenticación antes de renderizar el componente deseado.
* **Manejo de Errores (404):** Es una buena práctica tener una ruta comodín (`<Route path="*" element={<NotFoundPage />} />`) que capture cualquier URL que no coincida con las rutas definidas, mostrando una página de "No Encontrado".
* **Estilos y Layouts:** En aplicaciones reales, a menudo querrás mantener un layout consistente (cabecera, pie de página, barra lateral) mientras solo cambia el contenido principal. Puedes lograr esto estructurando tus componentes y envolviendo el `<Routes>` dentro de tu componente de layout.

---

Este enfoque proporciona una comprensión sólida de cómo implementar el enrutamiento en aplicaciones React, permitiendo una navegación fluida y una experiencia de usuario mejorada en tus SPA.










# 10.2.3 Uso de Parámetros de Ruta: Identificando Recursos Dinámicos { #parametros-ruta }

En el desarrollo de aplicaciones web, es muy común la necesidad de mostrar información específica basada en un identificador único. Por ejemplo, si tienes una lista de productos y quieres ver los detalles de un producto en particular, no querrías crear una página separada para cada producto. Ahí es donde los **parámetros de ruta** se vuelven indispensables.

Los parámetros de ruta son segmentos de la URL que se utilizan para capturar valores dinámicos. Estos valores son pasados al componente que se renderiza para esa ruta, permitiendo que el componente recupere y muestre la información relevante.

#### ¿Cómo funcionan los Parámetros de Ruta?

1.  **Definición en la Ruta:** En tu configuración de `<Route>`, defines un parámetro de ruta colocando un signo de dos puntos (`:`) delante del nombre del parámetro. Por ejemplo, `path="/productos/:id"` indica que la parte de la URL después de `/productos/` será el valor del parámetro `id`.

    ```jsx
    <Route path="/productos/:id" element={<DetalleProducto />} />
    ```
    En esta ruta, si la URL es `/productos/123`, el valor de `id` será `123`.

2.  **Acceso en el Componente:** Dentro del componente que se renderiza para esa ruta (en el ejemplo anterior, `DetalleProducto`), puedes acceder a los valores de los parámetros de ruta utilizando el hook `useParams()` proporcionado por `react-router-dom`.

    ```jsx
    import { useParams } from 'react-router-dom';

    function DetalleProducto() {
        const { id } = useParams(); // Desestructura el parámetro 'id'
        // ... ahora puedes usar 'id' para obtener los datos del producto
    }
    ```

#### Casos de Uso Comunes:

* **Páginas de Detalles:** Mostrar los detalles de un artículo, usuario, producto, publicación de blog, etc., basándose en su ID.
* **Perfiles de Usuario:** `example.com/usuarios/juanperez`
* **Categorías con Subcategorías:** `example.com/tienda/:categoria/:subcategoria`

#### Ejemplo Práctico: Perfiles de Usuario

Vamos a extender nuestro proyecto de portafolio para incluir una sección donde podamos ver perfiles de "colaboradores" o "clientes" con un ID dinámico.

**Objetivo:**
Crear una página que muestre los detalles de un usuario, donde el ID del usuario se obtiene directamente de la URL.

**Paso 1: Añadir un componente para los detalles del usuario**

Crea un nuevo archivo `src/pages/UserProfilePage.js`:

```jsx
// src/pages/UserProfilePage.js
import React, { useEffect, useState } from 'react';
import { useParams } from 'react-router-dom';

function UserProfilePage() {
    const { userId } = useParams(); // Accede al parámetro 'userId' de la URL
    const [userData, setUserData] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);

    // Simulamos una llamada a una API para obtener los datos del usuario
    useEffect(() => {
        setLoading(true);
        setError(null);
        // Datos de ejemplo (en un proyecto real, esto sería una llamada fetch/axios)
        const mockUsers = {
            '1': { id: '1', name: 'Alicia Pérez', email: 'alicia.perez@example.com', role: 'Desarrollador Frontend' },
            '2': { id: '2', name: 'Carlos García', email: 'carlos.garcia@example.com', role: 'Diseñador UX/UI' },
            '3': { id: '3', name: 'Elena Ramírez', email: 'elena.ramirez@example.com', role: 'Project Manager' },
        };

        const foundUser = mockUsers[userId];

        if (foundUser) {
            setTimeout(() => { // Simula un retraso de red
                setUserData(foundUser);
                setLoading(false);
            }, 500);
        } else {
            setLoading(false);
            setError(`Usuario con ID '${userId}' no encontrado.`);
        }
    }, [userId]); // El efecto se vuelve a ejecutar si userId cambia

    if (loading) {
        return <div>Cargando perfil de usuario...</div>;
    }

    if (error) {
        return <div>Error: {error}</div>;
    }

    if (!userData) {
        return <div>Selecciona un usuario para ver su perfil.</div>;
    }

    return (
        <div>
            <h1>Perfil de Usuario: {userData.name}</h1>
            <p><strong>ID:</strong> {userData.id}</p>
            <p><strong>Email:</strong> {userData.email}</p>
            <p><strong>Rol:</strong> {userData.role}</p>
        </div>
    );
}

export default UserProfilePage;
```

**Paso 2: Añadir la ruta al `App.js`**

En tu `src/App.js`, importa el nuevo componente y añade una nueva `<Route>` con un parámetro.

```jsx
// src/App.js
import React from 'react';
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';
import Navbar from './components/Navbar';
import HomePage from './pages/HomePage';
import AboutPage from './pages/AboutPage';
import ProjectsPage from './pages/ProjectsPage';
import UserProfilePage from './pages/UserProfilePage'; // ¡Importa el nuevo componente!
import './App.css';

function App() {
    return (
        <Router>
            <div className="App">
                <Navbar />

                <div className="content">
                    <Routes>
                        <Route path="/" element={<HomePage />} />
                        <Route path="/sobre-mi" element={<AboutPage />} />
                        <Route path="/proyectos" element={<ProjectsPage />} />
                        {/* ¡Aquí definimos la ruta con el parámetro userId! */}
                        <Route path="/usuarios/:userId" element={<UserProfilePage />} />
                        {/* Opcional: Ruta para 404 Not Found */}
                        <Route path="*" element={<h1>404: Página no encontrada</h1>} />
                    </Routes>
                </div>
            </div>
        </Router>
    );
}

export default App;
```

**Paso 3: Añadir enlaces para navegar a los perfiles de usuario (ejemplo)**

Podemos añadir una pequeña lista de usuarios en nuestra `HomePage.js` o en cualquier otro lugar, para poder navegar a sus perfiles.

Modifica `src/pages/HomePage.js` para incluir enlaces:

```jsx
// src/pages/HomePage.js
import React from 'react';
import { Link } from 'react-router-dom'; // Importa Link

function HomePage() {
    return (
        <div>
            <h1>Bienvenido a mi Portafolio</h1>
            <p>Soy un desarrollador apasionado por crear soluciones web.</p>

            <h2>Conoce a nuestros colaboradores:</h2>
            <ul>
                <li>
                    <Link to="/usuarios/1">Ver perfil de Alicia Pérez (ID: 1)</Link>
                </li>
                <li>
                    <Link to="/usuarios/2">Ver perfil de Carlos García (ID: 2)</Link>
                </li>
                <li>
                    <Link to="/usuarios/3">Ver perfil de Elena Ramírez (ID: 3)</Link>
                </li>
                <li>
                    <Link to="/usuarios/99">Ver perfil de Usuario No Existente (ID: 99)</Link>
                </li>
            </ul>
        </div>
    );
}

export default HomePage;
```

**Paso 4: Ejecutar y Probar**

1.  Asegúrate de que tu aplicación esté corriendo: `npm start` o `yarn start`.
2.  Abre tu navegador y ve a `http://localhost:3000`.
3.  Haz clic en los enlaces "Ver perfil de..." en la página de inicio.
4.  Observa cómo la URL cambia a algo como `http://localhost:3000/usuarios/1` y el contenido de la página se actualiza para mostrar los detalles del usuario correspondiente, extrayendo el ID `1` de la URL gracias a `useParams()`.

**Explicación del Flujo:**

1.  Cuando haces clic en `<Link to="/usuarios/1">`, React Router Dom intercepta la navegación.
2.  Busca una `<Route>` que coincida con `/usuarios/1`.
3.  Encuentra `<Route path="/usuarios/:userId" element={<UserProfilePage />} />`.
4.  React Router Dom extrae `1` como el valor del parámetro `userId`.
5.  Renderiza el componente `UserProfilePage`.
6.  Dentro de `UserProfilePage`, `useParams()` te permite acceder a `{ userId: '1' }`.
7.  El `useEffect` en `UserProfilePage` usa este `userId` para "buscar" (simulado en este caso) los datos del usuario correspondiente y los muestra en la UI.

Este ejemplo demuestra claramente cómo los parámetros de ruta permiten crear vistas dinámicas y reutilizables, un pilar fundamental en las SPAs modernas.
