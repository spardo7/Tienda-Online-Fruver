# Propuesta Final 

## El Problema

Muchos negocios locales de venta y reparto a domicilio operan de forma completamente manual y desorganizada:

- Los pedidos se reciben por teléfono y se anotan en un cuaderno.
- La asignación de domicilios se coordina mediante grupos de WhatsApp.
- El estado de los envíos se actualiza en hojas de cálculo al final del día.
- Los clientes no tienen forma de saber dónde está su pedido, lo que genera un alto volumen de llamadas innecesarias al negocio.

Este sistema es ineficiente, propenso a errores humanos y dificulta el crecimiento y la escalabilidad del negocio.

---

# La Solución — La Canasta Familiar 🧺

**La Canasta Familiar** es una plataforma web desarrollada para un fruver bogotano real, diseñada para digitalizar y centralizar la operación de ventas, domicilios y seguimiento de pedidos.

La solución se compone de dos módulos principales integrados:

## 1. Panel de Administración (Interfaz Privada)

Permite a los empleados gestionar toda la operación desde el navegador:

- Registrar productos con nombre, categoría, precio, stock, peso y dimensiones.
- Administrar inventario en tiempo real mediante Firestore.
- Asignar pedidos automáticamente a repartidores disponibles.
- Gestionar el flujo completo de entregas mediante un sistema de estados:
  
```text
Pedido recibido → Buscando repartidor → Asignado → En camino → Entregado
```

## 2. Seguimiento en Tiempo Real (Interfaz Pública)

Los clientes pueden consultar el estado de su pedido sin necesidad de llamar al negocio:

- Estado actualizado automáticamente paso a paso.
- Mapa interactivo con Google Maps.
- Ubicación animada del domiciliario en tiempo real.
- ETA calculado dinámicamente con `requestAnimationFrame`.
- Geocodificación automática de direcciones mediante Google Geocoding API.

Además, el sistema incluye:

- Tienda online con carrito persistente.
- Autenticación de usuarios con Firebase.
- Minijuego interactivo con Canvas API.
- Sistema de gamificación con descuento secreto del 30%.

---

# 🧺 La Canasta Familiar — Tienda Online

> Plataforma web completa para un fruver bogotano con tienda online, autenticación, carrito persistente, panel administrativo, seguimiento de domicilios en tiempo real y elementos de gamificación.

---

# 📋 Tabla de Contenidos

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

# 🛠 Tecnologías Utilizadas

| Tecnología | Versión / Fuente | Uso |
|---|---|---|
| HTML5 + CSS3 | Nativo | Estructura y estilos |
| JavaScript ES6+ | Vanilla JS | Lógica del frontend |
| Firebase Auth | SDK 12.11.0 | Autenticación |
| Cloud Firestore | SDK 12.11.0 | Base de datos |
| Google Maps JS API | Google Maps | Seguimiento en tiempo real |
| Google Geocoding API | REST API | Conversión dirección → coordenadas |
| WhatsApp API | `wa.me` | Confirmación de pedidos |
| Canvas API | Navegador | Minijuego interactivo |
| MutationObserver | Navegador | Sistema secreto |
| requestAnimationFrame | Navegador | Animaciones del mapa |

---

# 📁 Estructura del Proyecto

```text
la-canasta-familiar/
│
├── index.html
├── css/
│   └── visual_III.css
├── images/
│   ├── Favicon Canasta.jpg
│   ├── Fondo de pantalla.png
│   ├── Maps Canasta.jpg
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
│   └── default.jpg
├── video/
│   └── videoplayback (1).mp4
└── audio/
    └── videoplayback.m4a
```

---

# 🏗 Arquitectura General del HTML

La aplicación funciona como una SPA (Single Page Application) desarrollada completamente en Vanilla JavaScript.

Todas las secciones existen simultáneamente en el DOM y se muestran u ocultan dinámicamente mediante clases CSS:

```css
.seccion-activa
.seccion-oculta
```

## Estructura principal

```html
<header>
<nav>
<section id="tienda">
<section id="info">
<section id="info1">
<section id="info2">
<section id="juegos">
<section id="admin">
<section id="repartidores">
<div id="carritoFlotante">
<div id="modalOverlay">
<footer>
```

---

# 📑 Secciones de la Página

| Sección | Contenido |
|---|---|
| `#tienda` | Catálogo de productos |
| `#info` | Quiénes somos |
| `#info1` | Servicios |
| `#info2` | Contacto |
| `#juegos` | Minijuego y multimedia |
| `#admin` | Panel administrativo |
| `#repartidores` | Seguimiento de pedidos |

---

# 🛒 Tienda Online

El catálogo combina productos hardcodeados y productos almacenados en Firestore.

## Productos Base

```javascript
const productosBase = [
  {
    id:1,
    nombre:'🍅 Tomates Frescos (1kg)',
    precio:12000
  }
];
```

## Productos Dinámicos desde Firestore

```javascript
async function cargarProductosFirestore() {
  const snap = await getDocs(collection(db, 'productos'));
}
```

## Funcionalidades

- Búsqueda en tiempo real.
- Ordenamiento por precio, nombre y popularidad.
- Renderizado dinámico mediante template literals.
- Carrito persistente por usuario autenticado.

---

# 🔐 Autenticación Firebase

El sistema usa Firebase Authentication con:

- Registro de usuarios.
- Inicio de sesión.
- Persistencia de sesión.
- Sincronización automática del carrito.

## Registro

```javascript
createUserWithEmailAndPassword(auth, email, password)
```

## Login

```javascript
signInWithEmailAndPassword(auth, email, password)
```

## Listener de Estado

```javascript
onAuthStateChanged(auth, async(user) => {
  // Manejo de sesión
});
```

---

# 🛒 Carrito de Compras

El carrito se maneja mediante una variable global:

```javascript
window._carrito = [];
```

## Funciones principales

```javascript
agregarAlCarritoFirebase()
eliminarItem()
limpiarCarrito()
guardarCarrito()
renderCarrito()
```

## Características

- Persistencia en Firestore.
- Actualización automática de totales.
- Contador dinámico de productos.
- Integración con WhatsApp.

---

# ⚙️ Panel de Administración

Incluye un sistema de autenticación independiente.

## Credenciales

| Usuario | Contraseña |
|---|---|
| `admin` | `canasta2026` |
| `empleado` | `fruver123` |

## Funcionalidades

- CRUD de productos.
- Inventario en tiempo real.
- Estadísticas dinámicas.
- Gestión de ofertas.
- Eliminación de productos.

---

# 🚚 Sistema de Repartidores

El sistema administra automáticamente los pedidos y repartidores disponibles.

## Flujo

```text
Pedido recibido
↓
Buscando repartidor
↓
Asignado
↓
En camino
↓
Entregado
```

## Características

- Priorización por zonas.
- Simulación de actividad en tiempo real.
- Estados visuales dinámicos.
- ETA automático.

---

# 🗺️ Mapa de Seguimiento (Google Maps)

El mapa utiliza:

- Google Maps JavaScript API
- Google Geocoding API
- requestAnimationFrame
- Interpolación Bezier

## Funciones principales

```javascript
mostrarMapaSeguimiento()
geocodificarDireccion()
initMapa()
animarMoto()
```

## Marcadores personalizados

| Elemento | Emoji |
|---|---|
| Tienda | 🧺 |
| Cliente | 📍 |
| Repartidor | 🛵 |

---

# 🎮 Minijuego — Llena la Canasta

Minijuego desarrollado con Canvas API.

## Mecánica

El usuario debe arrastrar frutas hacia una canasta.

## Características

- Drag & Drop.
- Sistema de puntaje.
- Detección de colisiones.
- Renderizado dinámico.
- Integración con el modo secreto.

---

# 🕵️ Misión Agente Dom — Modo Secreto

Sistema de gamificación que desbloquea un descuento del 30%.

## Condiciones

1. Iniciar sesión.
2. Completar el minijuego.
3. Realizar un pedido con mínimo 2 productos.

## Resultado

- Banner secreto.
- Descuento automático.
- Mensaje especial en WhatsApp.

---

# 🔥 Firebase — Configuración y Colecciones

## Colección Usuarios

```json
{
  "nombre": "Juan",
  "email": "juan@email.com",
  "carrito": []
}
```

## Colección Productos

```json
{
  "nombre": "Mango",
  "categoria": "frutas",
  "precio": 9500
}
```

---

# 🗺 APIs de Google

## APIs utilizadas

- Maps JavaScript API
- Geocoding API

## Requisitos

La API Key debe tener habilitadas ambas APIs en Google Cloud Console.

---

# 🧩 DOM — Elementos Clave y su Función

| Selector | Función |
|---|---|
| `#gridProductos` | Grid de productos |
| `#carritoFlotante` | Carrito flotante |
| `#modalOverlay` | Modal login/registro |
| `#pedido-proceso` | Seguimiento del pedido |
| `#mapa-div` | Contenedor Google Maps |
| `#miCanvas` | Minijuego |

---

# 📦 Variables y Estado Global

| Variable | Descripción |
|---|---|
| `productos` | Catálogo total |
| `productosFiltrados` | Productos filtrados |
| `window._carrito` | Carrito activo |
| `window._uid` | Usuario autenticado |
| `repartidores` | Pool de repartidores |
| `pedidoActual` | Pedido en curso |

---

# 🔄 Flujo Completo de un Pedido

```text
Usuario agrega productos
        ↓
Ingresa dirección
        ↓
Presiona "Pedir por WhatsApp"
        ↓
Se genera mensaje automático
        ↓
Se abre WhatsApp
        ↓
Se inicia seguimiento
        ↓
Mapa en tiempo real
        ↓
Asignación de repartidor
        ↓
Entrega finalizada
```

---

# 🚀 Cómo Ejecutar

## Pasos

1. Clonar el repositorio.
2. Configurar la API Key de Google Maps.
3. Ejecutar mediante servidor local.

## Opciones recomendadas

```bash
npx serve .
```

o usar:

- Live Server de VS Code
- Apache
- Nginx
- Python HTTP Server

---

# 📞 Contacto

**La Canasta Familiar**

📍 Cl. 8 #79c-16, Bogotá, Colombia  
📞 320 8968237  
🌐 https://fruverhome.co/
