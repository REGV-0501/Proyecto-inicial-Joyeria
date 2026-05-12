Actúa como un arquitecto de software senior y experto en desarrollo móvil multiplataforma. Quiero crear una aplicación de joyería llamada **“Joyeria JB”** utilizando **Flutter + Dart + Firebase**, desarrollada en **VS Code o Android Studio**.

Tu tarea NO es generar código todavía.
Primero debes crear un **plan completo de implementación en formato Markdown**, detallado y profesional, explicando paso a paso cómo desarrollar la aplicación desde cero.

La respuesta debe centrarse en:

* Arquitectura del proyecto
* Organización de carpetas
* UI/UX
* Flujo de navegación
* Firebase
* Firestore
* Authentication
* State Management con Provider
* Dependencias necesarias
* Diseño responsive
* Buenas prácticas
* Seguridad
* Escalabilidad
* Flujo de desarrollo

NO generes código fuente.
NO generes widgets completos.
SOLO quiero planificación, estructura y procedimiento técnico detallado.

---

# Objetivo de la Aplicación

La aplicación será una tienda de joyería moderna con:

* Catálogo de productos
* Categorías
* Carrito de compras
* Inicio de sesión
* Registro de usuarios
* Gestión de inventario
* Pagos
* Perfil de usuario
* Panel administrativo futuro
* Experiencia visual elegante y minimalista

---

# Tecnologías Obligatorias

Debes incluir:

* Flutter
* Dart
* Firebase
* Firebase Authentication
* Cloud Firestore
* Firebase Storage
* Provider (state management)
* Responsive UI
* Material Design 3
* Arquitectura escalable

---

# Dependencias Requeridas

Debes explicar:

* Qué dependencias irán en `pubspec.yaml`
* Para qué sirve cada una
* Cuándo utilizarla
* Qué problemas resuelve

Incluye dependencias como:

* provider
* firebase_core
* firebase_auth
* cloud_firestore
* firebase_storage
* image_picker
* cached_network_image
* flutter_svg
* go_router o auto_route
* intl
* uuid
* shimmer
* flutter_staggered_grid_view
* shared_preferences

---

# Plan de Implementación

Genera un roadmap profesional dividido en fases:

## Fase 1 — Configuración Inicial

* Instalación de Flutter
* Configuración de Firebase
* Configuración Android/iOS
* Configuración VS Code
* Estructura inicial del proyecto

## Fase 2 — Arquitectura

* Clean Architecture
* Separación por capas
* Services
* Providers
* Models
* Repositories
* Screens
* Widgets reutilizables

## Fase 3 — Diseño UI/UX

Explica:

* Paleta de colores elegante
* Tipografía
* Espaciados
* Diseño minimalista
* Experiencia premium
* Diseño adaptable

---

# Diseño de Pantallas

Debes describir detalladamente cada pantalla:

## 1. Pantalla de Inicio de Sesión

Debe incluir:

* Logo de la joyería centrado
* Campos:

  * Usuario o correo
  * Contraseña
* Botón “Ingresar”
* Botón “Crear cuenta”
* Opción “Continuar como invitado”
* Recuperar contraseña
* Validaciones UX
* Estados de carga
* Manejo de errores
* Diseño elegante y moderno

---

## 2. Barra de Navegación Global

Todas las pantallas (excepto login) deben tener una barra superior personalizada:

### Elementos:

* Izquierda:

  * Ícono de bolsa/carrito de compras
* Centro:

  * Logo de la joyería
* Derecha:

  * Menú hamburguesa

### El menú hamburguesa desplegable debe contener:

* Inicio
* Categorías
* Collares
* Anillos
* Pulseras
* Aretes
* Promociones
* Mi cuenta
* Mis pedidos
* Cerrar sesión

Explica:

* UX del menú
* Navegación
* Animaciones
* Comportamiento responsive

---

## 3. Pantalla Principal (Home)

Debe incluir:

* Banner principal
* Productos destacados
* Categorías
* Carrusel de promociones
* Productos nuevos
* Sección “Nosotros”
* Botón agregar al carrito
* Cards elegantes de productos

---

## 4. Pantalla de Categorías

Debe incluir:

* Grid de categorías
* Filtros
* Búsqueda
* Ordenamiento

---

## 5. Pantalla Detalle de Producto

Debe incluir:

* Galería de imágenes
* Nombre
* Precio
* Descripción
* Material
* Stock
* Botón agregar al carrito
* Productos relacionados

---

## 6. Carrito de Compras

Debe incluir:

* Lista de productos
* Cantidades
* Subtotal
* Total
* Eliminar producto
* Checkout

---

## 7. Perfil de Usuario

Debe incluir:

* Información personal
* Historial de pedidos
* Dirección
* Configuración

---

# Firebase y Firestore

Explica detalladamente:

* Cómo estructurar Firestore
* Colecciones
* Subcolecciones
* Seguridad
* Reglas de Firestore
* Manejo de usuarios
* Manejo de inventario
* Manejo de pedidos

---

# Base de Datos

Basado en el siguiente modelo relacional, explica cómo migrarlo o adaptarlo a Firestore:

Tablas:

* clientes
* empleados
* ventas
* detalle_venta
* pagos
* productos
* categorías
* proveedores
* inventario
* promociones

Debes explicar:

* Relaciones
* Estructura recomendada en Firestore
* Optimización de consultas
* Escalabilidad

---

# Flujo de Navegación

Explica:

* Flujo de autenticación
* Navegación entre pantallas
* Manejo de sesiones
* Protección de rutas
* Persistencia de login

---

# Provider State Management

Explica:

* Cómo organizar Providers
* Providers por módulo
* Manejo del carrito
* Manejo del usuario
* Manejo de productos
* Buenas prácticas

---

# UX/UI Profesional

Incluye recomendaciones sobre:

* Animaciones suaves
* Skeleton loading
* Shimmer effects
* Empty states
* Estados de error
* Responsive design
* Accesibilidad
* Optimización visual

---

# Seguridad

Explica:

* Seguridad Firebase
* Validaciones
* Protección de datos
* Manejo seguro de sesiones
* Prevención de accesos no autorizados

---

# Escalabilidad

Explica:

* Cómo preparar la app para crecer
* Panel administrativo futuro
* API futura
* Modularización
* Separación frontend/backend

---

# Resultado Esperado

La respuesta debe entregarse:

* En formato Markdown
* Bien estructurada
* Profesional
* Paso a paso
* Fácil de seguir
* Sin generar código
* Sin omitir detalles técnicos importantes

El objetivo es tener una guía completa de planificación y arquitectura antes de comenzar el desarrollo real de la aplicación.
