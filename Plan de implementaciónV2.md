# 📘 Plan de Implementación: Aplicación “Joyeria JB”
**Arquitectura | Flutter + Dart + Firebase + Provider | Estrategia Senior**

---

## 🧱 Fase 1 — Configuración Inicial

### 1.1 Entorno de Desarrollo
- **Flutter SDK:** Instalar la versión estable más reciente. Verificar con `flutter doctor` que no existan dependencias faltantes.
- **IDE:** 
  - **VS Code:** Extensiones oficiales `Flutter`, `Dart`, `Error Lens`, `Pubspec Assist`, `Firebase Explorer`.
  - **Android Studio:** Para emuladores Android, profiling de rendimiento y gestión de SDKs.
- **Control de versiones:** Git con estrategia `Git Flow` o `Trunk-Based Development`. Branches: `main`, `develop`, `feature/*`, `hotfix/*`.

### 1.2 Configuración de Firebase
- Crear proyecto en Firebase Console.
- Registrar apps `Android` e `iOS` con Bundle ID/Package Name correctos.
- Descargar `google-services.json` (Android) y `GoogleService-Info.plist` (iOS).
- Habilitar servicios:
  - **Authentication:** Email/Password, Google Sign-In (opcional), Apple Sign-In (iOS).
  - **Firestore Database:** Modo producción (no modo de prueba).
  - **Storage:** Para imágenes de productos y avatares.
  - **App Check (Recomendado):** Protección contra abuse de APIs.
- Configurar variables de entorno y permisos de red/almacenamiento en `AndroidManifest.xml` e `Info.plist`.

### 1.3 Estructura Inicial del Proyecto
- Ejecutar `flutter create joyeria_jb`.
- Limpiar boilerplate, configurar `pubspec.yaml` base.
- Activar lints estrictos (`analysis_options.yaml` con `package:flutter_lints/flutter.yaml` + reglas personalizadas).
- Configurar `flutter_native_splash` y `flutter_launcher_icons` para identidad visual inicial.

---

## 🏗️ Fase 2 — Arquitectura del Proyecto

### 2.1 Patrón Arquitectónico
Se adoptará una **Arquitectura por Características (Feature-First) + Capas Lógicas (Data/Domain/Presentation)**, adaptada a Flutter + Provider. Esta combinación garantiza escalabilidad, testabilidad y separación de responsabilidades.

### 2.2 Organización de Carpetas
```
lib/
├── core/
│   ├── constants/          # Strings, colores, rutas, keys
│   ├── theme/              # ThemeData Material 3, tipografía, espaciados
│   ├── utils/              # Validadores, formateadores, helpers
│   ├── network/            # Firebase initialization, interceptores, error handlers
│   ├── widgets/            # Componentes reutilizables globales (botones, inputs, loaders)
│   └── routes/             # Definición de rutas y guardias
├── features/
│   ├── auth/
│   ├── catalog/
│   ├── product_detail/
│   ├── cart/
│   ├── checkout/
│   ├── user_profile/
│   └── admin/              # Estructura vacía preparada para futuro panel
├── main.dart
└── injection.dart          # Registro de Providers y dependencias
```

### 2.3 Separación por Capas (dentro de cada feature)
- **Data:** Modelos de datos (`*.dart` con `fromJson/toJson`), repositorios concretos, servicios de Firebase.
- **Domain:** Entidades puras (sin dependencias de Firebase), casos de uso (lógica de negocio), interfaces de repositorios.
- **Presentation:** Pantallas, widgets específicos del feature, Providers que orquestan UI + estado.

### 2.4 Principios Clave
- **Inversión de Dependencias:** Los Providers dependen de interfaces, no de implementaciones concretas de Firebase.
- **Single Responsibility:** Cada Provider maneja un único dominio (Auth, Cart, Products, etc.).
- **Dependency Injection Manual:** Configurado en `main.dart` mediante `MultiProvider` para mantener control y facilitar testing.

---

## 🎨 Fase 3 — Diseño UI/UX

### 3.1 Identidad Visual Premium
- **Paleta de Colores (Material 3 adaptado):**
  - Fondo: `#FAF8F5` (Crema suave)
  - Primario: `#B89A5A` (Oro champagne)
  - Secundario/Texto: `#1A1A1A` (Negro carbón)
  - Acento/CTA: `#2E5D4A` (Verde esmeralda sutil) o `#C47A7A` (Rosa antiguo)
  - Estado/Feedback: Verde éxito, Rojo error, Amarillo advertencia (tonos pastel para mantener elegancia).
- **Tipografía:** 
  - Títulos: Serif moderna (ej. `Playfair Display` o similar) para transmitir lujo.
  - Cuerpo/UI: Sans-serif legible (ej. `Inter` o `Roboto`).
  - Escala tipográfica basada en 1.25 ratio.
- **Espaciados:** Sistema de grilla de `8px`. Márgenes y paddings consistentes. Radio de bordes: `8px` (tarjetas), `12px` (botones), `24px` (modales).

### 3.2 Experiencia Minimalista y Adaptable
- Uso generoso de espacio en blanco (`whitespace`) para resaltar productos.
- Sombras sutiles (`elevation` baja en Material 3).
- Imágenes de alta resolución con relación de aspecto consistente (`4:5` o `3:4` para joyería).
- **Responsive:** 
  - Móvil: 1 columna / scroll vertical.
  - Tablet/Web: Grids de 2-3 columnas, navegación lateral fija, menús expandidos.
  - Uso de `LayoutBuilder`, `Breakpoint` personalizados y `FractionallySizedBox`.

---

## 📦 Dependencias Requeridas (`pubspec.yaml`)

| Dependencia | Propósito | Cuándo Usarla | Problema que Resuelve |
|-------------|-----------|---------------|----------------------|
| `provider` | Gestión de estado declarativo | Orquestar UI, negocio y datos | Evita prop-drilling, desacopla lógica de vistas |
| `firebase_core` | Inicialización del ecosistema Firebase | `main()` antes de cualquier servicio | Conecta la app con Firebase de forma segura |
| `firebase_auth` | Autenticación y gestión de sesiones | Login, registro, recuperación, verificación email | Maneja tokens, refresh automático y persistencia segura |
| `cloud_firestore` | Base de datos NoSQL en tiempo real | Catálogo, inventario, pedidos, perfiles | Reemplaza joins complejos con documentos jerárquicos y sincronización automática |
| `firebase_storage` | Almacenamiento de archivos multimedia | Imágenes de productos, banners, avatares | CDN optimizada, compresión automática, URLs firmadas |
| `image_picker` | Selección de imágenes desde galería/cámara | Perfil de usuario, futuras subidas admin | Abstracción multiplataforma para acceso a medios |
| `cached_network_image` | Cache de imágenes remotas | Cards de productos, banners | Reduce consumo de datos, elimina parpadeos, mejora rendimiento |
| `flutter_svg` | Renderizado de vectores SVG | Iconos personalizados, logos, ilustraciones | Escalado perfecto, menor peso vs PNG, soporte dark mode |
| `go_router` | Enrutamiento declarativo y profundo | Navegación global, guardias de autenticación | Simplifica stacks, deep links, transiciones y protección de rutas |
| `intl` | Internacionalización y formateo | Fechas, monedas, números, regiones | Garantiza consistencia en precios y localización |
| `uuid` | Generación de identificadores únicos | Carrito local, transacciones pendientes, IDs offline | Evita colisiones, permite sync diferido con Firestore |
| `shimmer` | Efecto de carga placeholder | Mientras se obtienen datos de Firestore | Mejora percepción de velocidad, reduce bounce rate |
| `flutter_staggered_grid_view` | Grids adaptativos tipo masonry | Catálogo, categorías, galería de productos | Distribución óptima en pantallas de distintos tamaños |
| `shared_preferences` | Persistencia local clave-valor | Carrito de invitado, preferencias, última sesión | Permite funcionamiento offline y recuperación rápida |

---

## 📱 Diseño de Pantallas (Especificación UX/UI)

### 1. Pantalla de Inicio de Sesión
- **Layout:** Centrado vertical y horizontal. Logo superior con margen amplio.
- **Campos:** Email/Usuario y Contraseña con decoración Material 3 (`InputDecorator`), bordes redondeados, iconos de visibilidad.
- **Botones:** Primario “Ingresar” (full width), Secundario “Crear cuenta” (outline), Terciario “Continuar como invitado”.
- **UX Avanzado:** 
  - Validación en tiempo real (formato email, longitud contraseña).
  - Estados de carga: Botón deshabilitado con indicador circular.
  - Manejo de errores: SnackBar/Toast con icono descriptivo y mensaje claro.
  - “Olvidé mi contraseña”: Modal bottom sheet con flujo de recovery via email Firebase.
  - Guest flow: Navega a Home con carrito en `shared_preferences` hasta checkout o registro.

### 2. Barra de Navegación Global
- **TopAppBar personalizado:** 
  - Izquierda: `IconButton` de carrito con badge numérico dinámico.
  - Centro: Logo tipográfico/vectorial de “Joyeria JB”.
  - Derecha: `IconButton` de menú hamburguesa.
- **Drawer/Navigation Rail:**
  - Lista vertical con iconos lineales + texto.
  - Separadores visuales por secciones.
  - Animación de entrada `slideRight` + `FadeTransition`.
  - Backdrop oscuro semitransparente al abrir.
- **Responsive:** En tablet > 600px, el drawer se convierte en `NavigationRail` fijo a la izquierda. El hamburger se oculta si el ancho lo permite.

### 3. Pantalla Principal (Home)
- **Hero Banner:** Carrusel automático + manual con indicadores. CTAs “Ver colección”.
- **Categorías Destacadas:** Grid horizontal circular o cards pequeñas con iconos SVG.
- **Productos Nuevos/Recomendados:** Scroll horizontal con `PageView` o `ListView`. Cards con imagen, nombre truncado, precio formateado y botón rápido “+”.
- **Promociones:** Banner secundario con countdown o etiqueta “Oferta limitada”.
- **Sección “Nosotros”:** Texto breve + imagen artesanal + botón “Conócenos”.
- **UX:** Pull-to-refresh, skeleton loading, empty states si catálogo vacío.

### 4. Pantalla de Categorías
- **Header:** Barra de búsqueda persistente, botón de filtros/orden.
- **Grid:** `StaggeredGridView` con imágenes de proporción variable según joyería (collares verticales, anillos cuadrados).
- **Filtros:** Bottom sheet con chips (material, precio, disponibilidad). Aplicación inmediata sin recargar pantalla.
- **Ordenamiento:** Dropdown (Precio ↑↓, Novedades, Más vendidos).

### 5. Pantalla Detalle de Producto
- **Galería:** Swipe horizontal con indicadores. Zoom on tap.
- **Info:** Nombre (H2), Precio (H1, color primario), Stock (badge “Disponible” / “Bajo stock”).
- **Atributos:** Material, peso, garantía, cuidados. Acordeón expandible.
- **Acción:** Selector de cantidad (+/-), botón “Agregar al carrito” fijo en bottom safe area.
- **Relacionados:** Grid horizontal al final.

### 6. Carrito de Compras
- **Lista:** Swipe to delete o botón eliminar. Cada item muestra imagen miniatura, nombre, precio unitario, cantidad editable.
- **Resumen:** Subtotal, envío (calculado por región), impuestos, total. Código de descuento input.
- **Checkout:** Botón principal, deshabilitado si stock insuficiente. Empty state con ilustración y CTA “Explorar catálogo”.

### 7. Perfil de Usuario
- **Header:** Avatar, nombre, email verificado (badge).
- **Menú:** Lista agrupada (Cuenta, Pedidos, Direcciones, Pagos, Soporte, Configuración).
- **Pedidos:** Historial con estado (Pendiente, Enviado, Entregado). Expandible para ver detalle.
- **Acciones:** Cerrar sesión, eliminar cuenta (con confirmación y política de privacidad).

---

## 🔥 Firebase y Firestore

### 4.1 Estructura de Colecciones
```
users/                { uid, name, email, role, phone, addresses[], createdAt }
products/             { id, name, description, price, categoryIds[], material, stock, images[], isActive }
categories/           { id, name, slug, icon, sortOrder }
carts/                { userId or sessionId, items:[{productId, qty, priceAtAdd}], updatedAt }
orders/               { userId, items:[{...}], subtotal, tax, shipping, total, status, paymentStatus, createdAt }
promotions/           { id, name, discountType, value, startDate, endDate, applicableCategoryIds }
inventory_logs/       { productId, changeQty, reason, timestamp, adminId }
```

### 4.2 Reglas de Seguridad (Firestore)
- `users/{userId}`: `read, write` solo si `request.auth.uid == userId`.
- `products`, `categories`, `promotions`: `read` permitido a todos (`true`). `write` restringido a `request.auth.token.role == 'admin'`.
- `carts`: Creación permitida sin auth (sessionId). Actualización/eliminación solo si `userId` coincide.
- `orders`: `create` por usuario autenticado. `read` por usuario dueño o admin.
- `inventory_logs`: Solo escritura por admin.
- **Validaciones:** `size()`, `matches()`, `isNumber`, límites de stock (`>=0`), prevención de overwrites accidentales.

### 4.3 Manejo de Inventario
- Uso de **Firestore Transactions** al realizar checkout para decrementar `stock` atómicamente.
- Si `stock < 1`, marcar `isActive: false` o mostrar “Agotado”.
- Logs de inventario para auditoría y prevención de discrepancias.

---

## 🗃️ Adaptación Relacional → NoSQL (Firestore)

| Tabla Relacional | Equivalente en Firestore | Estrategia de Adaptación |
|------------------|--------------------------|--------------------------|
| `clientes`       | `users`                  | Documento por usuario. Roles diferenciados con custom claims. |
| `empleados`      | `users` (con `role`)     | Mismo documento, campo `role: staff/admin`. Claims JWT para rutas seguras. |
| `productos`      | `products`               | Documento autocontenido. `categoryIds` como array. Sin foreign keys. |
| `categorías`     | `categories`             | Colección independiente. Join simulado en cliente con IDs. |
| `ventas`         | `orders`                 | Documento de pedido. Estado de pago integrado. |
| `detalle_venta`  | Embebido en `orders.items[]` | Array de mapas. Elimina colección relacional, optimiza lectura de pedidos. |
| `pagos`          | `orders.paymentStatus` + `paymentId` | Integración futura con Stripe. No almacenar datos sensibles. |
| `proveedores`    | `suppliers`              | Colección separada. Referenciada en `products` solo si necesario para B2B. |
| `inventario`     | `products.stock` + `inventory_logs` (subcolección) | Stock en documento principal para lecturas rápidas. Logs para trazabilidad. |
| `promociones`    | `promotions`             | Documento con fechas y reglas aplicables. Filtro por `isActive && dates`. |

### 📈 Optimización y Escalabilidad en Firestore
- **Denormalización controlada:** Se prioriza velocidad de lectura sobre normalización estricta.
- **Índices compuestos:** Configurados en Firebase Console para consultas con filtros múltiples + ordenamiento.
- **Paginación:** Uso de `startAfter` + `limit(20)` para evitar carga masiva.
- **Lecturas agrupadas:** `get()` múltiples con `batch()` o `getAll()` cuando sea posible.
- **Evitar queries anidadas profundas:** Máximo 1-2 niveles de subcolecciones.

---

## 🧭 Flujo de Navegación

### 5.1 Autenticación y Sesiones
- `FirebaseAuth.instance.authStateChanges()` escucha estado en `main`.
- Si hay sesión válida → Navega a `HomeRoute`.
- Si no → `LoginRoute`.
- “Invitado”: Se genera `sessionId` (UUID). Carrito se guarda en `shared_preferences`. Al registrarse, se migra a `users/{uid}/cart`.

### 5.2 Protección de Rutas (`go_router`)
- `redirect` basado en: `isLoggedIn`, `hasCompletedProfile`, `isAdmin`.
- Guardias: `AuthRedirectGuard`, `RoleRedirectGuard`.
- Deep links: Soporte para `/product/{id}`, `/category/{slug}`.

### 5.3 Persistencia y Sync
- Carrito local → sincronización en segundo plano al detectar login.
- Offline-first: Operaciones de lectura cacheadas. Escrituras encoladas si sin red.
- Transiciones suaves: `FadeTransition`, `SlideTransition` entre pantallas.

---

## 🧠 State Management con Provider

### 6.1 Organización de Providers
```
main.dart
└─ MultiProvider
   ├─ AuthProvider        (usuario, rol, sesión, login/logout)
   ├─ ProductProvider     (catálogo, filtros, carga por categoría)
   ├─ CartProvider        (items locales, sync, cálculo de totales)
   ├─ UserProvider        (perfil, direcciones, pedidos)
   └─ ThemeProvider       (modo claro/oscuro, tipografía, locales)
```

### 6.2 Patrones de Uso
- **UI Bindings:** `context.watch<T>()` solo en widgets que requieren re-render.
- **Lógica/Disparadores:** `context.read<T>()` o `Provider.of<T>(context, listen: false)` para acciones.
- **Estado de Carga:** Enum `DataStatus { initial, loading, success, error }` en cada Provider.
- **Carrito:** `ChangeNotifierProvider` con `notifyListeners()` optimizado. Debounce en updates masivos. Validación de stock antes de agregar.
- **Evitar Rebuilds Innecesarios:** Uso de `SelectorProvider`, `ProxyProvider` si hay dependencias entre estados. Separación clara de estado UI vs estado de negocio.

---

## 🎯 UX/UI Profesional

- **Microinteracciones:** Hero animations en transición producto → detalle. Escala `0.95` al presionar botones. Feedback háptico en acciones críticas.
- **Loading States:** `Shimmer` con geometría idéntica al layout final. Nunca dejar pantalla blanca.
- **Empty/Error States:** Ilustraciones vectoriales, mensajes empáticos (“No encontramos joyas con este filtro”), botones de acción claros.
- **Accesibilidad (a11y):** Contraste mínimo 4.5:1, `Semantics` labels, navegación por teclado (web), escalabilidad de texto (`MediaQuery.textScaler`).
- **Performance:** `const` en widgets estáticos, `ListView.builder`, imágenes optimizadas antes de subir a Storage, evitar cálculos pesados en `build()`.

---

## 🔒 Seguridad

- **Firebase Security Rules:** Validación estricta en servidor. Nunca confiar en el cliente.
- **Custom Claims:** Asignar `admin/staff` vía Firebase Admin SDK para control granular.
- **Protección de Datos:** No almacenar contraseñas, tokens de pago ni PII sensible en local. Usar `flutter_secure_storage` si fuera necesario.
- **Manejo de Sesiones:** Tokens JWT rotados automáticamente. Logout revoca sesión en cliente y limpia providers/local.
- **Validaciones:** Cliente (UX) + Servidor (Rules). Límites de longitud, tipos, rangos numéricos.
- **App Check:** Proteger endpoints de Firestore/Storage de bots y scraping.

---

## 🚀 Escalabilidad

- **Preparación para Panel Administrativo:** Estructura `features/admin/` lista. Rutas protegidas por rol. Futura versión web con `ResponsiveLayoutBuilder`.
- **Modularización:** Cada feature es autocontenida. Permite extracción a paquetes internos si el equipo crece.
- **Abstracción de Datos:** Repositorios dependen de interfaces. Fácil migración a REST/GraphQL si Firestore no cubre necesidades futuras.
- **CI/CD:** GitHub Actions para `flutter analyze`, `flutter test`, `flutter build`. Distribución vía Firebase App Distribution o Play Store/TestFlight.
- **Monitoreo:** Firebase Crashlytics + Performance Monitoring. Analytics para funnel de compra.
- **Gestión de Imágenes:** Pipeline de compresión antes de subir a Storage. Uso de CDN nativo de Firebase.
- **Arquitectura de Pagos:** Integración vía SDKs oficiales (Stripe, MercadoPago, PayPal). Nunca manejar datos de tarjeta directamente.

---

## 🗺️ Flujo de Desarrollo (Roadmap)

| Fase | Duración Estimada | Entregables Clave |
|------|-------------------|-------------------|
| **1. Setup & Config** | 1 semana | Proyecto Flutter, Firebase vinculado, `pubspec.yaml`, estructura base, lints, splash/icon |
| **2. Arquitectura & Auth** | 1.5 semanas | Carpeta `core/`, `go_router` configurado, `AuthProvider`, pantallas Login/Registro, validaciones, guest flow |
| **3. UI Theme & Home** | 1.5 semanas | Material 3 custom, `Theme` global, `HomeScreen`, banner, categorías, products placeholder, shimmer |
| **4. Catálogo & Detalle** | 2 semanas | `ProductProvider`, Firestore sync, grids, filtros, `ProductDetail`, galería, stock, relacionados |
| **5. Carrito & Checkout** | 1.5 semanas | `CartProvider`, sync guest/auth, UI carrito, cálculo totales, preparación pagos |
| **6. Perfil & Pedidos** | 1 semana | `UserProvider`, historial, direcciones, configuración, logout |
| **7. Seguridad & Optimización** | 1 semana | Security rules, App Check, indexes, testing, performance audit, responsive fix |
| **8. Release Prep** | 0.5 semanas | Build release, signing, store assets, documentation, handoff |

> ✅ **Nota de Arquitectura:** Se recomienda desarrollo iterativo con **feature toggles** para el panel admin y pagos. Cada fase debe incluir unit/widget tests, revisión de código y actualización de documentación interna.

---

## 📝 Conclusión

Este plan establece una base **profesional, segura y escalable** para “Joyeria JB”. La combinación de **Feature-First + Provider + Firestore + go_router** garantiza un desarrollo ordenado, mantenible y preparado para crecimiento real (panel admin, web, múltiples administradores, integraciones de pago). La arquitectura prioriza la experiencia premium, la consistencia visual y la resiliencia técnica, cumpliendo estándares de la industria móvil moderna.

Una vez validada esta planificación, se puede proceder a la implementación fase por fase con total claridad técnica y sin re-trabajos estructurales.
