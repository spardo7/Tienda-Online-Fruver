---

## 🎓 Contexto Académico — Propuesta Final Ingeniería Web

### El Problema

Muchos negocios locales de venta y reparto a domicilio operan de forma completamente manual y desorganizada:

- Los pedidos se reciben por teléfono y se anotan en un cuaderno.
- La asignación de domicilios se coordina por grupos de WhatsApp.
- El estado de los envíos se actualiza en hojas de cálculo al final del día.
- Los clientes no tienen forma de saber dónde está su pedido, lo que genera un alto volumen de llamadas innecesarias al negocio.

Este sistema es ineficiente, propenso a errores humanos y no permite que el negocio crezca ni escale.

---

### La Solución — La Canasta Familiar 🧺

**La Canasta Familiar** es una plataforma web completa desarrollada para un fruver bogotano real, que resuelve todos los problemas anteriores mediante dos componentes integrados:

**1. Panel de Administración (Interfaz Privada)**

Permite a los empleados del negocio gestionar toda la operación desde el navegador:

- Registrar nuevos productos con datos completos: nombre, categoría, precio, stock, peso y dimensiones.
- Visualizar y administrar el inventario en tiempo real desde Firestore.
- Asignar pedidos a los repartidores disponibles de forma automática, priorizando por zona de cobertura.
- Hacer seguimiento del estado de cada envío a través de un pipeline de 5 fases: *Pedido recibido → Buscando repartidor → Asignado → En camino → Entregado*.

**2. Seguimiento en Tiempo Real (Interfaz Pública)**

El cliente puede ver exactamente qué pasa con su pedido sin necesidad de llamar:

- Estado actual del pedido actualizado automáticamente paso a paso.
- Mapa interactivo con Google Maps que muestra la ubicación animada del domiciliario en ruta hacia su dirección.
- ETA actualizado en tiempo real mediante `requestAnimationFrame`, calculado proporcionalmente a la distancia real.
- Dirección del cliente geocodificada automáticamente (texto → coordenadas) vía Google Geocoding API.

Adicionalmente, el sitio cuenta con tienda online con carrito persistente por usuario, autenticación con Firebase, minijuego interactivo y un modo secreto con descuento del 30% como sistema de gamificación.

---

### Arquitectura de la Solución

```
Frontend — SPA Vanilla JS (sin framework)
        │
        ├── Firebase Auth          → Registro e inicio de sesión de usuarios
        ├── Cloud Firestore        → Catálogo de productos y carritos persistentes
        ├── Google Maps JS API     → Mapa interactivo de seguimiento del domiciliario
        ├── Google Geocoding API   → Conversión de dirección de texto a coordenadas
        └── WhatsApp API (wa.me)   → Confirmación de pedido con resumen pre-armado
```

> Toda la lógica vive en un único archivo `index.html` organizado en 8 bloques `<script>`. Los módulos se comunican entre sí a través de `window.*`: Firebase expone `window._db`, `window._carrito` y `window._uid`; el minijuego expone `window.frutas`; el sistema de mapas expone `window.mostrarMapaSeguimiento()`.


# 🧺 La Canasta Familiar — Tienda Online

> Sitio web completo para un fruver bogotano con tienda online, carrito persistente, autenticación de usuarios, panel de administración, sistema de repartidores con mapa en tiempo real, minijuego interactivo y modo secreto con descuento.

---

## 📋 Tabla de Contenidos

- [Tecnologías Utilizadas](#-tecnologías-utilizadas)
- [Estructura del Proyecto](#-estructura-del-proyecto)
- [Arquitectura General del HTML](#-arquitectura-general-del-html)
- [Secciones de la Página](#-secciones-de-la-página)
- [Tienda Online](#-tienda-online)
- [Autenticación Firebase](#-autenticación-firebase)
- [Carrito de Compras](#-carrito-de-compras)
- [Panel de Administración](#️-panel-de-administración)
- [Sistema de Repartidores](#-sistema-de-repartidores)
- [Mapa de Seguimiento](#️-mapa-de-seguimiento-google-maps)
- [Minijuego — Llena la Canasta](#-minijuego--llena-la-canasta)
- [Agente Secreto — 30% OFF](#️-misión-agente-dom--modo-secreto)
- [Firebase — Configuración y Colecciones](#-firebase--configuración-y-colecciones)
- [APIs de Google](#-apis-de-google)
- [DOM — Elementos Clave](#-dom--elementos-clave-y-su-función)
- [Variables y Estado Global](#-variables-y-estado-global)
- [Flujo Completo de un Pedido](#-flujo-completo-de-un-pedido)
- [Cómo Ejecutar](#-cómo-ejecutar)
- [Contacto](#-contacto)

---

## 🛠 Tecnologías Utilizadas

| Tecnología | Versión / Fuente | Uso |
|---|---|---|
| HTML5 + CSS3 | Nativo | Estructura y estilos del sitio |
| JavaScript ES6+ | Nativo (Vanilla) | Lógica completa del frontend |
| Firebase Auth | SDK 12.11.0 (ESM) | Registro e inicio de sesión de usuarios |
| Cloud Firestore | SDK 12.11.0 (ESM) | Base de datos de productos y carritos |
| Google Maps JS API | Cargada dinámicamente | Mapa interactivo de seguimiento del domiciliario |
| Google Geocoding API | REST via `fetch()` | Convertir dirección de texto a coordenadas lat/lng |
| WhatsApp API | `wa.me` link | Envío de pedidos por mensaje con texto pre-armado |
| Canvas API | Nativo del navegador | Minijuego de arrastrar frutas a la canasta |
| MutationObserver | Nativo del navegador | Detección de cambios en el DOM para el Agente Secreto |
| requestAnimationFrame | Nativo del navegador | Animación suave del marcador de moto en el mapa |

---

## 📁 Estructura del Proyecto

```
la-canasta-familiar/
│
├── index.html                  # Archivo principal — todo el sitio en un solo HTML
├── css/
│   └── visual_III.css          # Estilos generales del sitio (nav, cards, carrito, modal, etc.)
├── images/
│   ├── Favicon Canasta.jpg
│   ├── Fondo de pantalla.png   # Background fijo en <body>
│   ├── Maps Canasta.jpg        # Foto del local / mapa estático
│   ├── Tomates.jpg
│   ├── Brocoli_Fresco.jpg
│   ├── Platano_Maduro.jpg
│   ├── Zanahoria.jpg
│   ├── Naranja_Valencia.jpg
│   ├── Lechuca_Crespa.jpg
│   ├── Fresas.jpg
│   ├── Canasta_familiar.jpg
│   ├── Pina.jpg
│   ├── Papa_Pastusa.jpg
│   └── default.jpg             # Imagen fallback si no carga la del producto
├── video/
│   └── videoplayback (1).mp4  # Video de productos del campo colombiano
└── audio/
    └── videoplayback.m4a      # Música de fondo (autoplay + loop)
```

---

## 🏗 Arquitectura General del HTML

El sitio es una **Single Page Application (SPA) sin framework**: todas las secciones existen en el DOM simultáneamente y se muestran u ocultan mediante las clases CSS `seccion-activa` / `seccion-oculta`. La navegación no recarga la página.

```
<header>              ← Logo, saludo de usuario, botones auth, contador carrito
<div.search-bar>      ← Buscador principal de productos
<nav.nav-principal>   ← Menú de pestañas (Tienda, Quiénes Somos, Servicios, Contacto, Juegos, Repartidores, Admin)
<section#tienda>      ← Catálogo de productos con filtros y ordenamiento
<section#info>        ← Quiénes Somos
<section#info1>       ← Servicios (tarjetas)
<section#info2>       ← Contacto (mapa embed, formulario, iframe Google Maps)
<section#juegos>      ← Canvas minijuego + video + audio
<section#admin>       ← Panel de administración con login propio
<section#repartidores>← Sistema de seguimiento de pedidos + mapa Google Maps
<div#carritoFlotante> ← Carrito flotante superpuesto en toda la página
<section.contacto-rapido> ← Info de domicilios (zonas y precios)
<footer>
<div#modalOverlay>    ← Modal de login / registro Firebase (overlay)
```

Los scripts están organizados en **6 bloques `<script>`** al final del `<body>`:

| Bloque | Tecnología | Responsabilidad |
|---|---|---|
| 1 | `type="module"` + Firebase | Auth, Firestore, estado del carrito |
| 2 | Vanilla JS | Catálogo, búsqueda, ordenamiento, navegación |
| 3 | Vanilla JS | Panel Admin — login, CRUD productos, stats |
| 4 | Vanilla JS | Canvas — minijuego arrastrar frutas |
| 5 | Vanilla JS | Modal — abrir, cerrar, cambiar tab |
| 6 | Vanilla JS | Agente Secreto — condiciones y descuento |
| 7 | Vanilla JS | Sistema de Repartidores — asignación y progreso |
| 8 | IIFE Vanilla JS | Mapa Google Maps + Geocoding + animación moto |

---

## 📑 Secciones de la Página

La función `cambiarSeccion(id)` controla la visibilidad:

```javascript
window.cambiarSeccion = function(seccion) {
  // Cierra el modal si está abierto
  document.querySelectorAll('section').forEach(s => {
    s.classList.remove('seccion-activa');
    s.classList.add('seccion-oculta');
  });
  document.getElementById(seccion).classList.add('seccion-activa');
  // Actualiza el nav (clase .active)
};
```

| ID Sección | Ruta Nav | Contenido |
|---|---|---|
| `#tienda` | 🛒 Tienda Online | Catálogo, filtros, grid de productos |
| `#info` | 🏠 Quiénes Somos | Texto + imagen flotante del fruver |
| `#info1` | 📋 Servicios | 3 tarjetas: Punto Físico, Domicilios, Canasta Fresca |
| `#info2` | 📞 Contacto | Mapa estático, formulario, iframe Google Maps |
| `#juegos` | 🎮 Juegos & Más | Canvas juego + `<video>` + `<audio>` |
| `#admin` | ⚙️ Admin | Login empleado + panel CRUD productos |
| `#repartidores` | 🚚 Repartidores | Seguimiento de pedido + mapa + lista repartidores |

---

## 🛒 Tienda Online

### Fuentes de Productos

El catálogo combina dos orígenes de datos que se fusionan en el array `productos[]`:

**1. `productosBase[]` — Hardcodeados en el código:**

```javascript
const productosBase = [
  { id:1, nombre:'🍅 Tomates Frescos (1kg)', precio:12000, categoria:'frutas',
    popularidad:95, oferta:true, imagen:'images/Tomates.jpg' },
  // ... 9 productos más
];
```

**2. Firestore — Cargados dinámicamente con `cargarProductosFirestore()`:**

```javascript
async function cargarProductosFirestore() {
  if (!window._db) { setTimeout(cargarProductosFirestore, 400); return; }
  const snap = await window._getDocs(window._collection(window._db, 'productos'));
  snap.forEach(docSnap => {
    // Solo agrega si no existe ya en el array (evita duplicados)
    if (!productos.find(p => p.id === docSnap.id)) {
      productos.push({ id: docSnap.id, ...datos });
    }
  });
  renderProductos(productosFiltrados);
}
```

> El retry con `setTimeout` es necesario porque Firebase se inicializa en un módulo ES (`type="module"`) y puede no estar disponible inmediatamente al cargar el script clásico.

### Renderizado del Grid

`renderProductos(lista)` genera el HTML de cada tarjeta con template literals:

```javascript
function renderProductos(productosMostrar) {
  document.getElementById('gridProductos').innerHTML = productosMostrar.map(p => `
    <div class="producto-card">
      <img src="${p.imagen}" onerror="this.src='images/default.jpg'">
      ${p.oferta ? '<span class="oferta-badge">🔥 Oferta</span>' : ''}
      <h3>${p.nombre}</h3>
      <span>$${p.precio.toLocaleString()}</span>
      <button onclick="agregarAlCarrito('${p.id}')">🛒 Agregar</button>
    </div>
  `).join('');
}
```

### Búsqueda y Ordenamiento

```javascript
// Búsqueda por nombre o categoría (case-insensitive)
window.buscarProductos = function() {
  const termino = document.getElementById('buscadorPrincipal').value.toLowerCase();
  productosFiltrados = productos.filter(p =>
    p.nombre.toLowerCase().includes(termino) ||
    p.categoria.toLowerCase().includes(termino)
  );
  renderProductos(productosFiltrados);
};

// Ordenamiento (actúa sobre productosFiltrados, no sobre productos)
window.ordenarProductos = function() {
  const criterio = document.getElementById('ordenarPor').value;
  // 'precio-asc' | 'precio-desc' | 'nombre' | 'popular'
};
```

### Agregar al Carrito

```javascript
window.agregarAlCarrito = function(id) {
  const producto = productos.find(p => String(p.id) === String(id));
  window.agregarAlCarritoFirebase({ id, nombre, precio });
  // Feedback visual: botón cambia a "✅ Agregado" por 1.5s
};
```

---

## 🔐 Autenticación Firebase

El módulo Firebase se importa como **ES Module** (`type="module"`), que es el único scope donde `import` funciona. Las funciones que necesita el HTML global se exponen en `window.*`.

### Flujo de Registro

```javascript
window.registrarUsuario = async function(e) {
  e.preventDefault();
  const cred = await createUserWithEmailAndPassword(auth, email, pass);
  // Guarda perfil extendido en Firestore
  await setDoc(doc(db, 'usuarios', cred.user.uid), {
    nombre, email, telefono: tel,
    carrito: [], lista: [],
    creadoEn: new Date().toISOString()
  });
  cerrarModal();
};
```

### Flujo de Login

```javascript
window.loginUsuario = async function(e) {
  e.preventDefault();
  await signInWithEmailAndPassword(auth, email, pass);
  cerrarModal(); // onAuthStateChanged se encarga del resto
};
```

### Listener de Estado (`onAuthStateChanged`)

Este listener reacciona a cualquier cambio de autenticación (login, logout, recarga de página):

```javascript
onAuthStateChanged(auth, async (user) => {
  if (user) {
    // Muestra botón logout, nombre en nav, saludo en header
    // Carga el carrito guardado en Firestore del usuario
    const snap = await getDoc(doc(db, 'usuarios', user.uid));
    window._uid     = user.uid;
    window._carrito = snap.data().carrito || [];
    renderCarrito();
  } else {
    // Muestra botones login/registro, limpia carrito
    window._uid     = null;
    window._carrito = [];
    renderCarrito();
  }
});
```

---

## 🛒 Carrito de Compras

El carrito es un **div flotante** (`#carritoFlotante`) siempre presente en el DOM, visible u oculto con la clase `.oculto`.

### Estado

```javascript
window._carrito = [];  // Array de objetos { id, nombre, precio, cantidad }
window._uid     = null; // UID del usuario autenticado (null si no hay sesión)
```

### Operaciones Principales

```javascript
// Agregar (incrementa cantidad si ya existe)
window.agregarAlCarritoFirebase = async function(producto) {
  const yaExiste = window._carrito.find(p => p.id === producto.id);
  if (yaExiste) yaExiste.cantidad++;
  else window._carrito.push({ ...producto, cantidad: 1 });
  await guardarCarrito();
  renderCarrito();
};

// Eliminar por índice
window.eliminarItem = async function(index) {
  window._carrito.splice(index, 1);
  await guardarCarrito();
  renderCarrito();
};

// Vaciar todo
window.limpiarCarrito = async function() {
  window._carrito = [];
  await guardarCarrito();
  renderCarrito();
};

// Persistir en Firestore (solo si hay sesión)
async function guardarCarrito() {
  if (window._uid) {
    await updateDoc(doc(db, 'usuarios', window._uid), { carrito: window._carrito });
  }
}
```

### Renderizado del Carrito Flotante

`renderCarrito()` actualiza simultáneamente:
- `#contadorCarrito` — número en el header
- `#contadorCarritoMini` — número en el carrito flotante
- `.total-carrito` — suma total en COP
- `#listaCarritoMini` — lista de items con botón ✕ por ítem

### Campo de Dirección y Pedido

El carrito incluye un `<input>` para la dirección de domicilio (`#direccion-domicilio`). Al hacer clic en **"📱 Pedir por WhatsApp"** se llama `pedirWhatsapp()`, que usa ese valor para geocodificar y construir el mensaje.

---

## ⚙️ Panel de Administración

Accesible desde **⚙️ Admin** en el nav. Tiene su propio sistema de autenticación independiente de Firebase.

### Login Interno

```javascript
const EMPLEADOS = [
  { usuario: 'admin',    password: 'canasta2026', nombre: 'Administrador' },
  { usuario: 'empleado', password: 'fruver123',   nombre: 'Empleado'      }
];

window.loginAdmin = function() {
  const empleado = EMPLEADOS.find(e => e.usuario === usuario && e.password === password);
  if (empleado) {
    document.getElementById('admin-login-wrap').style.display    = 'none';
    document.getElementById('admin-panel-content').style.display = 'block';
    actualizarStats();
    cargarTablaInventario();
  }
};
```

> Los inputs del login responden a `Enter` mediante un listener `keydown`.

### Tabs del Panel

```javascript
window.adminCambiarTab = function(tab, boton) {
  // Activa/desactiva la clase 'activo' en los botones
  // Muestra 'admin-tab-registrar' o 'admin-tab-inventario'
  if (tab === 'inventario') cargarTablaInventario();
};
```

### Registrar Producto

El formulario recoge:

| Campo DOM | Tipo | Descripción |
|---|---|---|
| `#prod-nombre` | text | Nombre obligatorio |
| `#prod-emoji` | text | Emoji representativo (máx 4 chars) |
| `#prod-categoria` | select | frutas / verduras / canastas / otros |
| `#prod-precio` | number | Precio en COP |
| `#prod-popularidad` | number | 0–100, default 80 |
| `#prod-imagen` | text | Ruta relativa a `images/` |
| `#prod-descripcion` | textarea | Descripción del producto |
| `#prod-cantidad` | number | Stock disponible |
| `#prod-unidad` | select | kg / und / 500g / docena / lb / caja / canasta |
| `#prod-peso` | number | Peso en kg |
| `#prod-largo/ancho/alto` | number | Dimensiones en cm |
| `#prod-oferta` | checkbox | Marcar como oferta |

Al registrar:

```javascript
window.registrarProductoAdmin = async function() {
  // Validaciones: nombre, categoría, precio > 0, cantidad > 0
  const docRef = await window._addDoc(
    window._collection(window._db, 'productos'),
    nuevoProducto
  );
  // Agrega inmediatamente al array productos[] y re-renderiza el grid
  // Actualiza las estadísticas del panel
};
```

### Tabla de Inventario

`cargarTablaInventario()` hace un `getDocs` a la colección `/productos` en Firestore y construye una `<table>` con todos los campos. Cada fila tiene un botón **🗑️ Eliminar**:

```javascript
window.eliminarProductoAdmin = async function(firestoreId, boton) {
  if (!confirm('¿Estás seguro?')) return;
  await window._deleteDoc(window._doc(window._db, 'productos', firestoreId));
  // Quita del array productos[] y re-renderiza el grid en tiempo real
  boton.closest('tr').remove();
  actualizarStats();
};
```

### Estadísticas en Tiempo Real

```javascript
async function actualizarStats() {
  const snap = await getDocs(collection(db, 'productos'));
  // Cuenta total, frutas, verduras, ofertas
  // Actualiza #stat-total, #stat-frutas, #stat-verduras, #stat-ofertas
}
```

---

## 🚚 Sistema de Repartidores

### Pool de Repartidores

```javascript
let repartidores = [
  { id:1, nombre:'Juan Pérez 🛵',    telefono:'320-123-4567', zona:'Zona 1-2', disponible:true,  lat:4.6403, lng:-74.1448 },
  { id:2, nombre:'María Gómez 🚲',   telefono:'320-234-5678', zona:'Zona 3-5', disponible:true,  lat:4.6420, lng:-74.1460 },
  { id:3, nombre:'Carlos López 🛴',  telefono:'320-345-6789', zona:'Zona 1-2', disponible:false, lat:4.6380, lng:-74.1420 },
  { id:4, nombre:'Ana Rodríguez 🛵', telefono:'320-456-7890', zona:'Zona 3-5', disponible:true,  lat:4.6410, lng:-74.1450 }
];
```

### Flujo de Asignación

Al llamar `pedirWhatsapp()`:

```
[1] Validar carrito no vacío
[2] Marcar condicion3 del Agente Secreto (si carrito >= 2 items)
[3] Construir mensaje WhatsApp con lista de productos y total
[4] Abrir wa.me en nueva pestaña
[5] Navegar a sección #repartidores
[6] actualizarPaso(1) → "Pedido Recibido"
[7] setTimeout 2s → actualizarPaso(2) + buscarRepartidorReal()
```

### `buscarRepartidorReal()`

```javascript
function buscarRepartidorReal() {
  const disponibles = repartidores.filter(r => r.disponible);
  // Prioriza Zona 1-2 (peso = 1 vs 2)
  const mejorRepartidor = disponibles.reduce((mejor, actual) => {
    const pActual = actual.zona.includes('1-2') ? 1 : 2;
    return pActual < pMejor ? actual : mejor;
  });

  // Anima el card del repartidor seleccionado
  repartidores.forEach(r => { r.animando = r.id === mejorRepartidor.id; });
  renderRepartidores();

  setTimeout(() => {
    mejorRepartidor.disponible     = false;
    mejorRepartidor.pedidoAsignado = pedidoActual.id;
    actualizarPaso(3, `✅ ${mejorRepartidor.nombre}...`);
    // setTimeout 3s → paso 4 (En Camino)
    // setTimeout 8s → paso 5 (Entregado) + finalizarPedido()
  }, 2500);
}
```

### Progreso del Pedido (5 pasos)

```javascript
function actualizarPaso(paso, mensaje) {
  document.querySelectorAll('.step').forEach((step, index) => {
    step.classList.remove('activo', 'completado');
    if (index < paso - 1)       step.classList.add('completado');
    else if (index === paso - 1) step.classList.add('activo');
  });
  document.getElementById('mensaje-pedido').innerHTML = mensaje;
}
```

| Paso | Estado | Clase CSS |
|---|---|---|
| 1 | 🛒 Pedido Recibido | `activo` |
| 2 | 🔍 Buscando Repartidor | `activo` (anteriores: `completado`) |
| 3 | ✅ Repartidor Asignado | `activo` |
| 4 | 🚀 En Camino | `activo` |
| 5 | ✅ Entregado | `activo` + clase `progreso-completo` en el contenedor |

### Simulación de Actividad Aleatoria

```javascript
setInterval(() => {
  if (!pedidoActual && Math.random() > 0.7) {
    // Aleatoriamente pone un repartidor como "ocupado" por 5-15s
    // Simula actividad real en la lista
  }
}, ~10-20 segundos aleatorios);
```

---

## 🗺️ Mapa de Seguimiento (Google Maps)

Todo el código del mapa está encapsulado en una **IIFE** `(function(){ ... })()` para no contaminar el scope global (excepto `window.mostrarMapaSeguimiento`).

### Constantes

```javascript
const TIENDA_LAT = 4.6403229;
const TIENDA_LNG = -74.1448216;
const MAPS_KEY   = 'TU_API_KEY';
```

### Carga Diferida de Google Maps

```javascript
function cargarGoogleMaps(callback) {
  if (window.google && window.google.maps) { callback(); return; }
  const script = document.createElement('script');
  script.src   = `https://maps.googleapis.com/maps/api/js?key=${MAPS_KEY}&libraries=geometry`;
  script.onload = callback;
  document.head.appendChild(script);
}
```

El script de Maps solo se inyecta cuando el usuario hace un pedido con dirección — no se carga innecesariamente.

### Geocodificación de la Dirección

```javascript
async function geocodificarDireccion(direccion) {
  const url  = `https://maps.googleapis.com/maps/api/geocode/json?address=${query}&key=${MAPS_KEY}`;
  const data = await fetch(url).then(r => r.json());
  if (data.status === 'OK') return { lat, lng, formateada };
  // Fallback: coordenadas aleatorias cercanas a la tienda
  return null;
}
```

### Inicialización del Mapa (`initMapa`)

Crea o reutiliza la instancia del mapa y coloca 3 marcadores SVG personalizados:

| Marcador | Emoji | Color | Representa |
|---|---|---|---|
| `markerTienda` | 🧺 | Verde `#2e7d32` | La tienda (fijo) |
| `markerCliente` | 📍 | Rojo `#e53935` | Dirección del cliente |
| `markerMoto` | 🛵 | Naranja `#FF6F00` | Domiciliario (animado) |

### Ruta Bezier Cuadrática

En lugar de usar la Directions API (que requiere permisos adicionales), la ruta se calcula localmente con interpolación cuadrática:

```javascript
function interpolarRuta(lat1, lng1, lat2, lng2) {
  const puntos = 80;
  // Punto de control desviado lateralmente para crear curva natural
  const midLat = (lat1+lat2)/2 + (lng2-lng1)*0.15;
  const midLng = (lng1+lng2)/2 - (lat2-lat1)*0.15;
  // Fórmula Bezier: B(t) = (1-t)²P0 + 2(1-t)tP1 + t²P2
  for (let i = 0; i <= puntos; i++) {
    const t = i / puntos;
    coords.push({ lat: (1-t)*(1-t)*lat1 + 2*(1-t)*t*midLat + t*t*lat2, ... });
  }
  return coords; // 81 puntos interpolados
}
```

### Animación de la Moto

```javascript
function animarMoto(duracionMs) {
  // duracionMs es proporcional a la distancia real (~30 km/h en ciudad)
  // Easing: ease-in-out cuadrático para movimiento suave
  function step(now) {
    const progreso = Math.min((now - inicio) / duracionMs, 1);
    const t = progreso < 0.5
      ? 2 * progreso * progreso
      : 1 - Math.pow(-2 * progreso + 2, 2) / 2;
    const pos = routeCoords[Math.floor(t * total)];
    markerMoto.setPosition(pos);
    // Actualiza ETA en tiempo real
    document.getElementById('mapa-eta').textContent = `⏱️ ~${minRestantes} min`;
    if (progreso < 1) animFrame = requestAnimationFrame(step);
  }
  animFrame = requestAnimationFrame(step);
}
```

---

## 🎮 Minijuego — Llena la Canasta

Implementado con la **Canvas API** en un `<canvas id="miCanvas" width="500" height="320">`.

### Frutas Disponibles

```javascript
const frutas = [
  { x:60,  y:80,  emoji:'🍅', nombre:'Tomate',  enCanasta:false },
  { x:160, y:80,  emoji:'🍏', nombre:'Manzana', enCanasta:false },
  // ... 6 más: Naranja, Limón, Uvas, Banano, Fresa, Kiwi
];
```

### Zona de la Canasta

```javascript
const canasta = { x:170, y:230, w:160, h:80 };
function enCanasta(x, y) {
  return x > canasta.x && x < canasta.x + canasta.w
      && y > canasta.y && y < canasta.y + canasta.h;
}
```

### Eventos de Drag & Drop (Mouse)

```javascript
canvas.addEventListener('mousedown', e => {
  // Detecta si el click está dentro del radio de 25px de alguna fruta
  // Guarda referencia a la fruta arrastrada y el offset del click
});
canvas.addEventListener('mousemove', e => {
  // Mueve la fruta arrastrada siguiendo el cursor
  // Redibuja el canvas en cada frame
});
canvas.addEventListener('mouseup', () => {
  // Verifica si la fruta cayó dentro de canasta{}
  // Si sí: enCanasta = true, puntaje++, mensaje de éxito
  // Si no: mensaje de error
});
```

### Renderizado del Canvas

`dibujarCanvas()` redibuja todo en cada evento:
- Fondo con gradiente `#e8f5e9 → #fff8e1`
- Línea divisoria punteada entre la zona de frutas y la canasta
- Canasta como polígono trapezoidal marrón (`#8B5E3C`) con líneas verticales internas
- Frutas no entregadas como emoji de 36px con su nombre en 11px debajo
- Contador `puntaje/total` arriba a la derecha
- Mensaje de resultado abajo al centro

> `window.frutas` se expone en el scope global para que el **Agente Secreto** pueda monitorear si todas están `enCanasta: true`.

---

## 🕵️ Misión Agente Dom — Modo Secreto

Sistema de gamificación que otorga **30% de descuento** al cumplir 3 condiciones.

### Variables de Estado

```javascript
let condicion1_cumplida = false; // Login
let condicion2_cumplida = false; // Minijuego completo
let condicion3_cumplida = false; // Pedido WhatsApp con ≥2 productos
let misionCompletada    = false;
let pedidoWhatsappHecho = false;
```

### Detección de Condición 1 — Login

```javascript
function monitorearAutenticacion() {
  const observer = new MutationObserver(() => {
    const botonLogout   = document.getElementById('botonLogout');
    const saludoUsuario = document.getElementById('saludoUsuario');
    if (botonLogout.style.display !== 'none' || saludoUsuario.style.display !== 'none') {
      condicion1_cumplida = true;
    }
  });
  // Observa cambios de atributo 'style' en los 3 elementos de auth
  observer.observe(document.getElementById('botonesAuth'),   { attributes: true });
  observer.observe(document.getElementById('botonLogout'),   { attributes: true });
  observer.observe(document.getElementById('saludoUsuario'), { attributes: true });
}
```

### Detección de Condición 2 — Minijuego

```javascript
setInterval(() => {
  if (window.frutas) {
    const todasEnCanasta = window.frutas.every(f => f.enCanasta);
    if (todasEnCanasta && !condicion2_cumplida) {
      condicion2_cumplida = true;
    }
  }
}, 1000); // Poll cada segundo
```

### Detección de Condición 3 — Pedido WhatsApp

Marcada directamente dentro de `pedirWhatsapp()` antes de abrir el link:

```javascript
if (!condicion3_cumplida && carrito.length >= 2) {
  condicion3_cumplida = true;
  pedidoWhatsappHecho = true;
}
```

### Activación del Descuento

```javascript
function verificarMisionCompletada() {
  if (misionCompletada) return;
  if (condicion1_cumplida && condicion2_cumplida && condicion3_cumplida) {
    misionCompletada = true;

    // 1. Resaltar título en dorado
    document.querySelector('header h1').style.cssText = 'background:#FFD700; color:#000; ...';

    // 2. Banner azul con breakdown del descuento
    const total    = window._carrito.reduce(...);
    const descuento = total * 0.30;
    // Banner insertado después del <header>

    // 3. Botón WhatsApp aplica 30% OFF automáticamente
    // pedirWhatsapp() ya chequea misionCompletada para construir el mensaje
  }
}
setInterval(verificarMisionCompletada, 500);
```

---

## 🔥 Firebase — Configuración y Colecciones

### Configuración

```javascript
const firebaseConfig = {
  apiKey:            "...",
  authDomain:        "la-canasta-familiar.firebaseapp.com",
  projectId:         "la-canasta-familiar",
  storageBucket:     "la-canasta-familiar.firebasestorage.app",
  messagingSenderId: "...",
  appId:             "..."
};
const app  = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db   = getFirestore(app);
```

### Exposición al Scope Global

Como Firebase se importa en un módulo (`type="module"`), sus funciones se exponen mediante `window`:

```javascript
window._db         = db;
window._collection = collection;
window._addDoc     = addDoc;
window._getDocs    = getDocs;
window._deleteDoc  = deleteDoc;
window._doc        = doc;
window._query      = query;
window._orderBy    = orderBy;
```

### Colecciones en Firestore

**`/usuarios/{uid}`**

```json
{
  "nombre":    "Juan Ejemplo",
  "email":     "juan@ejemplo.com",
  "telefono":  "3001234567",
  "carrito":   [ { "id": 1, "nombre": "🍅 Tomates", "precio": 12000, "cantidad": 2 } ],
  "lista":     [],
  "creadoEn":  "2026-05-20T10:00:00.000Z"
}
```

**`/productos/{auto-id}`**

```json
{
  "nombre":      "Mango Tommy",
  "emoji":       "🥭",
  "categoria":   "frutas",
  "precio":      9500,
  "popularidad": 88,
  "imagen":      "images/mango.jpg",
  "descripcion": "Mango maduro del Tolima...",
  "oferta":      true,
  "dimensiones": {
    "cantidad": 30,
    "unidad":   "kg",
    "peso":     0.4,
    "largo":    12,
    "ancho":    8,
    "alto":     7
  },
  "fechaRegistro": "2026-05-20T10:00:00.000Z"
}
```

---

## 🗺 APIs de Google

### 1. Maps JavaScript API

- Se carga dinámicamente solo cuando el usuario hace un pedido con dirección
- Parámetro `libraries=geometry` para acceso a `google.maps.geometry`
- Estilos personalizados: sin POIs, sin tránsito, colores verdes naturales
- Controles: solo zoom (`disableDefaultUI: true, zoomControl: true`)

### 2. Geocoding API

Llamada directa REST desde el frontend:

```
GET https://maps.googleapis.com/maps/api/geocode/json
  ?address=Cll+45+%2312-30%2C+Bogotá%2C+Colombia
  &key=TU_API_KEY
```

Respuesta usada:
- `data.results[0].geometry.location` → `{ lat, lng }`
- `data.results[0].formatted_address` → dirección formateada para mostrar

> ⚠️ **La API Key debe tener habilitadas** Maps JavaScript API y Geocoding API en [Google Cloud Console](https://console.cloud.google.com). Se recomienda restringir la key por dominio en producción.

---

## 🧩 DOM — Elementos Clave y su Función

| ID / Selector | Tipo | Función |
|---|---|---|
| `#gridProductos` | `div` | Contenedor donde se inyecta el HTML de las tarjetas de productos |
| `#buscadorPrincipal` | `input[text]` | Input de búsqueda, dispara `buscarProductos()` en `keyup` |
| `#ordenarPor` | `select` | Criterio de ordenamiento, dispara `ordenarProductos()` en `change` |
| `#resultadoBusqueda` | `span` | Muestra "N resultado(s) para X" |
| `#carritoFlotante` | `div` | Carrito flotante, toggle con clase `.oculto` |
| `#listaCarritoMini` | `div` | Lista de items del carrito con botones ✕ |
| `#contadorCarrito` | `span` | Número de items en el header |
| `#contadorCarritoMini` | `span` | Número de items en el carrito flotante |
| `.total-carrito` | `span` | Total en COP en el carrito flotante |
| `#direccion-domicilio` | `input[text]` | Dirección de entrega para WhatsApp y geocodificación |
| `#modalOverlay` | `div` | Overlay del modal login/registro, toggle con clase `.activo` |
| `#formLogin` | `form` | Formulario de login Firebase |
| `#formRegistro` | `form` | Formulario de registro Firebase |
| `#botonesAuth` | `div` | Botones Login/Registro (visible sin sesión) |
| `#botonLogout` | `div` | Botón cerrar sesión + nombre (visible con sesión) |
| `#saludoUsuario` | `p` | Saludo personalizado en el header |
| `#nombreNav` | `span` | Nombre del usuario en la barra de navegación |
| `#admin-login-wrap` | `div` | Formulario de login del panel admin |
| `#admin-panel-content` | `div` | Panel completo (oculto hasta login admin) |
| `#admin-tab-registrar` | `div` | Tab de registro de productos |
| `#admin-tab-inventario` | `div` | Tab de tabla de inventario |
| `#tabla-productos-body` | `tbody` | Cuerpo de la tabla de inventario |
| `#admin-form-msg` | `p` | Mensaje de éxito/error al registrar producto |
| `#stat-total/frutas/verduras/ofertas` | `div` | Tarjetas de estadísticas del panel |
| `#pedido-proceso` | `div` | Sección de seguimiento del pedido (oculta por defecto) |
| `.step[data-step]` | `div×5` | Pasos del progreso del pedido |
| `#mensaje-pedido` | `p` | Mensaje de estado del pedido actual |
| `#mapa-seguimiento` | `div` | Contenedor del mapa (oculto por defecto) |
| `#mapa-div` | `div` | Div donde se instancia `google.maps.Map` |
| `#mapa-eta` | `span` | ETA actualizado por `requestAnimationFrame` |
| `#mapa-distancia` | `span` | Distancia calculada entre tienda y cliente |
| `#mapa-destino-label` | `span` | Dirección formateada del cliente |
| `#lista-repartidores` | `div` | Cards de los 4 repartidores con su estado |
| `#miCanvas` | `canvas` | Canvas del minijuego (500×320px) |
| `musicaFondo` | `audio` | Música de fondo con `autoplay` + `loop` |

---

## 📦 Variables y Estado Global

| Variable | Scope | Tipo | Descripción |
|---|---|---|---|
| `productosBase` | Local script | `Array` | 10 productos hardcodeados base |
| `productos` | Local script | `Array` | Fusión de base + Firestore |
| `productosFiltrados` | Local script | `Array` | Subconjunto filtrado/buscado |
| `window._carrito` | Global | `Array` | Items del carrito activo |
| `window._uid` | Global | `string\|null` | UID Firebase del usuario |
| `window._db` | Global | Firestore instance | Expuesta desde módulo ES |
| `repartidores` | Local script | `Array` | Pool de 4 repartidores |
| `pedidoActual` | Local script | `Object\|null` | Pedido en proceso |
| `pasoActual` | Local script | `number` | Paso actual (1–5) |
| `adminSesionActiva` | Local script | `boolean` | Estado del login admin |
| `condicion1/2/3_cumplida` | Local script | `boolean` | Condiciones Agente Secreto |
| `misionCompletada` | Local script | `boolean` | Flag de misión completa |
| `mapInstance` | IIFE | `google.maps.Map\|null` | Instancia reutilizable del mapa |
| `markerMoto` | IIFE | `google.maps.Marker\|null` | Marcador animado de la moto |
| `routeCoords` | IIFE | `Array<{lat,lng}>` | 81 puntos de la ruta Bezier |
| `animFrame` | IIFE | `number\|null` | ID del requestAnimationFrame activo |

---

## 🔄 Flujo Completo de un Pedido

```
Usuario agrega productos al carrito
        ↓
Escribe dirección en #direccion-domicilio
        ↓
Clic en "📱 Pedir por WhatsApp"
        ↓
pedirWhatsapp() {
  ├── Valida carrito no vacío
  ├── Marca condicion3 (Agente Secreto)
  ├── Construye mensaje WhatsApp con productos + total (+ 30% OFF si mision)
  ├── Abre wa.me en nueva pestaña
  ├── Navega a sección #repartidores
  ├── Llama mostrarMapaSeguimiento(direccion) {
  │     ├── cargarGoogleMaps() → inyecta script si no existe
  │     ├── geocodificarDireccion() → lat/lng via Geocoding API
  │     └── initMapa() → marcadores + ruta Bezier + animarMoto()
  │           └── requestAnimationFrame loop → actualiza ETA
  └── actualizarPaso(1) + setTimeout(2s) → buscarRepartidorReal() {
        ├── Filtra disponibles, prioriza Zona 1-2
        ├── Anima card del repartidor elegido
        ├── setTimeout(2.5s) → paso 3 (Asignado)
        ├── setTimeout(3s)   → paso 4 (En Camino)
        └── setTimeout(8s)   → paso 5 (Entregado) + finalizarPedido()
      }
}
```

---

## 🚀 Cómo Ejecutar

1. **Clona o descarga** el repositorio.
2. Asegúrate de tener todos los archivos de `images/`, `audio/` y `video/`.
3. Reemplaza `MAPS_KEY` con tu API Key de Google Maps (en el bloque IIFE del mapa).
4. El proyecto usa las credenciales Firebase ya incluidas en el código.

> ⚠️ **Firebase requiere HTTP**, no funciona desde `file://`. Opciones:
> - Extensión **Live Server** de VS Code
> - `npx serve .` en la terminal
> - Cualquier servidor local (Apache, Nginx, Python `http.server`)

### Credenciales del Panel Admin

| Usuario | Contraseña |
|---|---|
| `admin` | `canasta2026` |
| `empleado` | `fruver123` |

### Requisitos de la API Key de Google

En [Google Cloud Console](https://console.cloud.google.com) → APIs y servicios → Biblioteca, habilitar:
- ✅ **Maps JavaScript API**
- ✅ **Geocoding API**

---

## 📞 Contacto

**La Canasta Familiar**
📍 Cl. 8 #79c-16, Bogotá, Colombia
📞 320 8968237
🌐 [fruverhome.co](https://fruverhome.co/)
