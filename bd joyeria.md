Actúa como un administrador de una base de datos: proyecto tienda joyería, que entidades se necesitan para su gestión
<img width="790" height="443" alt="image" src="https://github.com/user-attachments/assets/95d37882-e334-4185-8703-d428ca1c0951" />
Para un sistema de gestión de joyería, estas son las entidades principales que necesitarías:Aquí tienes el resumen de las **11 entidades** del modelo:

**Núcleo comercial**
- `CLIENTE` — datos de contacto y registro del comprador
- `VENTA` — encabezado de cada transacción (fecha, total, estado)
- `DETALLE_VENTA` — líneas de la venta con cantidad, precio y descuento
- `PAGO` — método y monto con que se liquidó cada venta
- `EMPLEADO` — quién gestionó la venta

**Catálogo y stock**
- `PRODUCTO` — pieza de joyería con talla, peso y precio
- `CATEGORIA` — anillos, aretes, collares, pulseras, etc.
- `INVENTARIO` — stock actual y mínimo por producto
- `MATERIAL` — tipo de metal/piedra, quilates, descripción

**Abastecimiento**
- `PROVEEDOR` — casa de materiales o colecciones externas

---

**Consideraciones adicionales** que podrías necesitar según el alcance:

- `DEVOLUCION` — para rastrear devoluciones y cambios
- `ORDEN_COMPRA` — si quieres gestionar pedidos a proveedores
- `CLIENTE_VIP / MEMBRESIA` — para programas de fidelidad
- `FOTO_PRODUCTO` — si manejas catálogo digital con imágenes
- `REPARACION` — si ofrecen servicio de mantenimiento de joyas

¿Quieres que desarrolle el SQL para crear estas tablas, o profundizamos en alguna entidad específica?



Las entidades con sus atributos y tipo de tabla para cada una de las entidades

El archivo Excel contiene **13 hojas** organizadas así:

**Hoja "Resumen"** — vista ejecutiva con las 10 entidades, su tipo de tabla, número de atributos, PK y FKs de cada una.

**Hoja "Diccionario completo"** — todos los atributos de todas las entidades en una sola tabla, agrupados por entidad con encabezado de color según el tipo.

**10 hojas individuales** (una por entidad) con columnas: atributo, tipo de dato, permite nulo, PK, FK → tabla, descripción y ejemplo de valor.

Los tipos de tabla usados son:

| Tipo | Entidades |
|---|---|
| Maestra | CLIENTE, EMPLEADO, PROVEEDOR, PRODUCTO |
| Catálogo | CATEGORIA, MATERIAL |
| Operativa | INVENTARIO |
| Transaccional | VENTA, DETALLE_VENTA, PAGO |

¿Quieres que genere también el script SQL `CREATE TABLE` para cada entidad?

## Tablas
<img width="1009" height="298" alt="image" src="https://github.com/user-attachments/assets/ab49281a-2ddf-49ce-8fc3-fe1cefb517cd" />


El script `bdjoyeria.sql` está listo. Aquí un resumen de lo que incluye:

**Estructura del script**

| Sección | Contenido |
|---|---|
| §0 | `CREATE DATABASE bdjoyeria` con charset UTF-8 |
| §1–5 | Las 10 tablas en orden correcto de dependencia |
| §6 | 12 índices para consultas frecuentes |
| §7 | 2 triggers automáticos |
| §8 | Datos iniciales de catálogos |

**Relaciones implementadas con FK**

- `producto` → `categoria`, `proveedor`
- `material` → `producto` (CASCADE delete)
- `inventario` → `producto` (1 a 1)
- `venta` → `cliente`, `empleado`
- `detalle_venta` → `venta` (CASCADE delete), `producto`
- `pago` → `venta`

**Triggers incluidos**

- `trg_actualizar_total_venta` — recalcula el total de la venta automáticamente cada vez que se inserta un renglón en `detalle_venta`
- `trg_reducir_inventario` — descuenta el stock del producto al agregar una línea de venta

Compatible con **MySQL 8.x** y **MariaDB 10.x**. Para ejecutarlo: `mysql -u root -p < bdjoyeria.sql`


##Documento SQL
-- ============================================================
--  BASE DE DATOS: TIENDA JOYERÍA
--  Archivo  : bdjoyeria.sql
--  Motor    : MySQL 8.x / MariaDB 10.x
--  Creado   : 2025-05-11
--  Versión  : 1.0
-- ============================================================

-- ------------------------------------------------------------
-- 0. CREAR Y SELECCIONAR BASE DE DATOS
-- ------------------------------------------------------------
CREATE DATABASE IF NOT EXISTS bdjoyeria
    CHARACTER SET utf8mb4
    COLLATE utf8mb4_unicode_ci;

USE bdjoyeria;

-- ------------------------------------------------------------
-- 1. TABLAS MAESTRAS (sin dependencias externas)
-- ------------------------------------------------------------

-- 1.1 CLIENTE
CREATE TABLE cliente (
    id_cliente      INT             NOT NULL AUTO_INCREMENT,
    nombre          VARCHAR(100)    NOT NULL,
    telefono        VARCHAR(20)         NULL,
    email           VARCHAR(100)        NULL,
    direccion       VARCHAR(200)        NULL,
    fecha_registro  DATE            NOT NULL DEFAULT (CURRENT_DATE),
    tipo_cliente    VARCHAR(20)     NOT NULL DEFAULT 'Regular'
                    COMMENT 'Regular | VIP | Mayorista',
    rfc             VARCHAR(20)         NULL,
    CONSTRAINT pk_cliente       PRIMARY KEY (id_cliente),
    CONSTRAINT uq_cliente_email UNIQUE      (email),
    CONSTRAINT ck_cliente_tipo  CHECK       (tipo_cliente IN ('Regular','VIP','Mayorista'))
) COMMENT = 'Personas o empresas que compran en la tienda';


-- 1.2 EMPLEADO
CREATE TABLE empleado (
    id_empleado         INT             NOT NULL AUTO_INCREMENT,
    nombre              VARCHAR(100)    NOT NULL,
    cargo               VARCHAR(50)     NOT NULL
                        COMMENT 'Vendedor | Joyero | Gerente | Admin',
    telefono            VARCHAR(20)         NULL,
    email               VARCHAR(100)        NULL,
    fecha_contratacion  DATE            NOT NULL,
    activo              BOOLEAN         NOT NULL DEFAULT TRUE,
    CONSTRAINT pk_empleado      PRIMARY KEY (id_empleado),
    CONSTRAINT uq_empleado_email UNIQUE     (email),
    CONSTRAINT ck_empleado_cargo CHECK      (cargo IN ('Vendedor','Joyero','Gerente','Admin'))
) COMMENT = 'Personal que opera y gestiona la tienda';


-- 1.3 PROVEEDOR
CREATE TABLE proveedor (
    id_proveedor    INT             NOT NULL AUTO_INCREMENT,
    nombre          VARCHAR(100)    NOT NULL,
    contacto        VARCHAR(100)        NULL,
    pais            VARCHAR(60)         NULL,
    telefono        VARCHAR(20)         NULL,
    email           VARCHAR(100)        NULL,
    tipo            VARCHAR(30)     NOT NULL DEFAULT 'Fabricante'
                    COMMENT 'Fabricante | Distribuidor | Importador',
    CONSTRAINT pk_proveedor     PRIMARY KEY (id_proveedor),
    CONSTRAINT ck_proveedor_tipo CHECK      (tipo IN ('Fabricante','Distribuidor','Importador'))
) COMMENT = 'Empresas o personas que suministran productos o materiales';


-- 1.4 CATEGORIA
CREATE TABLE categoria (
    id_categoria    INT             NOT NULL AUTO_INCREMENT,
    nombre          VARCHAR(60)     NOT NULL,
    descripcion     VARCHAR(200)        NULL,
    CONSTRAINT pk_categoria     PRIMARY KEY (id_categoria),
    CONSTRAINT uq_categoria_nom UNIQUE      (nombre)
) COMMENT = 'Clasificación de los productos de joyería';


-- ------------------------------------------------------------
-- 2. TABLAS MAESTRAS DEPENDIENTES
-- ------------------------------------------------------------

-- 2.1 PRODUCTO
CREATE TABLE producto (
    id_producto     INT             NOT NULL AUTO_INCREMENT,
    nombre          VARCHAR(100)    NOT NULL,
    descripcion     TEXT                NULL,
    precio_venta    DECIMAL(10,2)   NOT NULL,
    costo           DECIMAL(10,2)   NOT NULL,
    id_categoria    INT             NOT NULL,
    id_proveedor    INT             NOT NULL,
    talla           VARCHAR(10)         NULL,
    peso_g          DECIMAL(8,3)        NULL,
    sku             VARCHAR(30)     NOT NULL,
    activo          BOOLEAN         NOT NULL DEFAULT TRUE,
    CONSTRAINT pk_producto          PRIMARY KEY  (id_producto),
    CONSTRAINT uq_producto_sku      UNIQUE       (sku),
    CONSTRAINT fk_producto_cat      FOREIGN KEY  (id_categoria)
        REFERENCES categoria (id_categoria)
        ON UPDATE CASCADE ON DELETE RESTRICT,
    CONSTRAINT fk_producto_prov     FOREIGN KEY  (id_proveedor)
        REFERENCES proveedor (id_proveedor)
        ON UPDATE CASCADE ON DELETE RESTRICT,
    CONSTRAINT ck_producto_precio   CHECK        (precio_venta >= 0),
    CONSTRAINT ck_producto_costo    CHECK        (costo >= 0)
) COMMENT = 'Cada pieza de joyería disponible en el catálogo';


-- ------------------------------------------------------------
-- 3. TABLAS CATÁLOGO / DETALLE DE PRODUCTO
-- ------------------------------------------------------------

-- 3.1 MATERIAL
CREATE TABLE material (
    id_material     INT             NOT NULL AUTO_INCREMENT,
    id_producto     INT             NOT NULL,
    tipo            VARCHAR(50)     NOT NULL
                    COMMENT 'Oro | Plata | Platino | Diamante | Rubí | etc.',
    quilates        DECIMAL(4,2)        NULL
                    COMMENT 'Pureza del metal: 9, 14, 18, 24 k',
    descripcion     VARCHAR(200)        NULL,
    CONSTRAINT pk_material          PRIMARY KEY (id_material),
    CONSTRAINT fk_material_producto FOREIGN KEY (id_producto)
        REFERENCES producto (id_producto)
        ON UPDATE CASCADE ON DELETE CASCADE
) COMMENT = 'Metales, piedras y materiales que componen cada producto';


-- ------------------------------------------------------------
-- 4. TABLA OPERATIVA
-- ------------------------------------------------------------

-- 4.1 INVENTARIO
CREATE TABLE inventario (
    id_inventario           INT     NOT NULL AUTO_INCREMENT,
    id_producto             INT     NOT NULL,
    stock_actual            INT     NOT NULL DEFAULT 0,
    stock_minimo            INT     NOT NULL DEFAULT 1,
    ultima_actualizacion    DATE    NOT NULL DEFAULT (CURRENT_DATE),
    CONSTRAINT pk_inventario            PRIMARY KEY (id_inventario),
    CONSTRAINT uq_inventario_producto   UNIQUE      (id_producto),
    CONSTRAINT fk_inventario_producto   FOREIGN KEY (id_producto)
        REFERENCES producto (id_producto)
        ON UPDATE CASCADE ON DELETE RESTRICT,
    CONSTRAINT ck_inventario_stock      CHECK (stock_actual >= 0),
    CONSTRAINT ck_inventario_minimo     CHECK (stock_minimo >= 0)
) COMMENT = 'Control de existencias físicas de cada producto';


-- ------------------------------------------------------------
-- 5. TABLAS TRANSACCIONALES
-- ------------------------------------------------------------

-- 5.1 VENTA
CREATE TABLE venta (
    id_venta        INT             NOT NULL AUTO_INCREMENT,
    fecha           DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    total           DECIMAL(12,2)   NOT NULL DEFAULT 0.00,
    id_cliente      INT             NOT NULL,
    id_empleado     INT             NOT NULL,
    estado          VARCHAR(20)     NOT NULL DEFAULT 'Pendiente'
                    COMMENT 'Pendiente | Completada | Cancelada',
    notas           TEXT                NULL,
    CONSTRAINT pk_venta             PRIMARY KEY (id_venta),
    CONSTRAINT fk_venta_cliente     FOREIGN KEY (id_cliente)
        REFERENCES cliente  (id_cliente)
        ON UPDATE CASCADE ON DELETE RESTRICT,
    CONSTRAINT fk_venta_empleado    FOREIGN KEY (id_empleado)
        REFERENCES empleado (id_empleado)
        ON UPDATE CASCADE ON DELETE RESTRICT,
    CONSTRAINT ck_venta_estado      CHECK (estado IN ('Pendiente','Completada','Cancelada')),
    CONSTRAINT ck_venta_total       CHECK (total >= 0)
) COMMENT = 'Cabecera de cada transacción de venta';


-- 5.2 DETALLE_VENTA
CREATE TABLE detalle_venta (
    id_detalle      INT             NOT NULL AUTO_INCREMENT,
    id_venta        INT             NOT NULL,
    id_producto     INT             NOT NULL,
    cantidad        INT             NOT NULL DEFAULT 1,
    precio_unitario DECIMAL(10,2)   NOT NULL,
    descuento       DECIMAL(5,2)    NOT NULL DEFAULT 0.00
                    COMMENT 'Porcentaje de descuento: 0.00 a 100.00',
    subtotal        DECIMAL(10,2)   NOT NULL
                    COMMENT 'Calculado: cantidad × precio_unitario × (1 - descuento/100)',
    CONSTRAINT pk_detalle_venta         PRIMARY KEY (id_detalle),
    CONSTRAINT fk_detalle_venta_venta   FOREIGN KEY (id_venta)
        REFERENCES venta    (id_venta)
        ON UPDATE CASCADE ON DELETE CASCADE,
    CONSTRAINT fk_detalle_venta_prod    FOREIGN KEY (id_producto)
        REFERENCES producto (id_producto)
        ON UPDATE CASCADE ON DELETE RESTRICT,
    CONSTRAINT ck_detalle_cantidad      CHECK (cantidad > 0),
    CONSTRAINT ck_detalle_precio        CHECK (precio_unitario >= 0),
    CONSTRAINT ck_detalle_descuento     CHECK (descuento BETWEEN 0 AND 100)
) COMMENT = 'Líneas de productos dentro de una venta';


-- 5.3 PAGO
CREATE TABLE pago (
    id_pago     INT             NOT NULL AUTO_INCREMENT,
    id_venta    INT             NOT NULL,
    metodo      VARCHAR(30)     NOT NULL
                COMMENT 'Efectivo | Tarjeta de crédito | Tarjeta de débito | Transferencia | Mixto',
    monto       DECIMAL(12,2)   NOT NULL,
    fecha_pago  DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    referencia  VARCHAR(60)         NULL
                COMMENT 'Número de autorización o folio bancario',
    CONSTRAINT pk_pago              PRIMARY KEY (id_pago),
    CONSTRAINT fk_pago_venta        FOREIGN KEY (id_venta)
        REFERENCES venta (id_venta)
        ON UPDATE CASCADE ON DELETE RESTRICT,
    CONSTRAINT ck_pago_metodo       CHECK (metodo IN (
        'Efectivo','Tarjeta de crédito','Tarjeta de débito','Transferencia','Mixto')),
    CONSTRAINT ck_pago_monto        CHECK (monto > 0)
) COMMENT = 'Registro del método y monto con que se liquidó una venta';


-- ============================================================
-- 6. ÍNDICES ADICIONALES (rendimiento en consultas frecuentes)
-- ============================================================
CREATE INDEX idx_producto_categoria    ON producto      (id_categoria);
CREATE INDEX idx_producto_proveedor    ON producto      (id_proveedor);
CREATE INDEX idx_producto_activo       ON producto      (activo);
CREATE INDEX idx_venta_fecha           ON venta         (fecha);
CREATE INDEX idx_venta_cliente         ON venta         (id_cliente);
CREATE INDEX idx_venta_empleado        ON venta         (id_empleado);
CREATE INDEX idx_venta_estado          ON venta         (estado);
CREATE INDEX idx_detalle_venta         ON detalle_venta (id_venta);
CREATE INDEX idx_detalle_producto      ON detalle_venta (id_producto);
CREATE INDEX idx_pago_venta            ON pago          (id_venta);
CREATE INDEX idx_inventario_stock      ON inventario    (stock_actual);
CREATE INDEX idx_material_producto     ON material      (id_producto);


-- ============================================================
-- 7. TRIGGER: recalcular total de venta al insertar detalle
-- ============================================================
DELIMITER $$

CREATE TRIGGER trg_actualizar_total_venta
AFTER INSERT ON detalle_venta
FOR EACH ROW
BEGIN
    UPDATE venta
    SET    total = (
        SELECT COALESCE(SUM(subtotal), 0)
        FROM   detalle_venta
        WHERE  id_venta = NEW.id_venta
    )
    WHERE id_venta = NEW.id_venta;
END$$

-- Trigger para actualizar inventario al completar venta
CREATE TRIGGER trg_reducir_inventario
AFTER INSERT ON detalle_venta
FOR EACH ROW
BEGIN
    UPDATE inventario
    SET    stock_actual          = stock_actual - NEW.cantidad,
           ultima_actualizacion  = CURRENT_DATE
    WHERE  id_producto = NEW.id_producto;
END$$

DELIMITER ;


-- ============================================================
-- 8. DATOS INICIALES (catálogos base)
-- ============================================================

-- Categorías
INSERT INTO categoria (nombre, descripcion) VALUES
    ('Anillos',    'Argollas y solitarios para dedo'),
    ('Aretes',     'Aretes, argollas y pendientes'),
    ('Collares',   'Collares, cadenas y gargantillas'),
    ('Pulseras',   'Pulseras, esclavas y brazaletes'),
    ('Broches',    'Broches y prendedores decorativos'),
    ('Relojes',    'Relojes con acabado en metales preciosos');

-- Empleado administrador inicial
INSERT INTO empleado (nombre, cargo, email, fecha_contratacion) VALUES
    ('Administrador Sistema', 'Admin', 'admin@joyeria.com', CURRENT_DATE);


-- ============================================================
-- FIN DEL SCRIPT — bdjoyeria.sql
-- ============================================================
