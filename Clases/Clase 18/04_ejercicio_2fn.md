# Migración hacia la **Segunda Forma Normal (2FN)**

**Objetivo:** eliminar dependencias parciales de una ​**clave compuesta**​.

### Escenario base (violando 2FN)

```sql
CREATE TABLE detalle_venta (
  id_venta INT,
  id_producto INT,
  nombre_producto VARCHAR(100),
  precio DECIMAL(10,2),
  cantidad INT,
  total DECIMAL(10,2),
  PRIMARY KEY (id_venta, id_producto)
);

INSERT INTO detalle_venta VALUES
(1, 1, 'Televisor', 950, 1, 950),
(1, 2, 'Cafetera', 70, 1, 70),
(2, 3, 'Laptop', 950, 1, 950),
(3, 2, 'Cafetera', 70, 1, 70);
```

🔍 Aquí `nombre_producto` y `precio` dependen solo de `id_producto`, no del par `(id_venta, id_producto)` → violación de 2FN.

### 1. Crear nueva tabla para los productos

```sql
CREATE TABLE producto2 (
id_producto INT PRIMARY KEY,
nombre VARCHAR(100),
precio DECIMAL(10,2)
);
```

### 2. Migrar los productos desde `detalle_venta`

```sql
INSERT INTO producto2 (id_producto, nombre, precio)
SELECT DISTINCT id_producto, nombre_producto, precio FROM detalle_venta;
```

### 3. Actualizar `detalle_venta` para referenciar por clave foránea

```sql
ALTER TABLE detalle_venta
ADD FOREIGN KEY (id_producto) REFERENCES producto2(id_producto);
```

### 4. Eliminar columnas redundantes

```sql
ALTER TABLE detalle_venta
DROP COLUMN nombre_producto,
DROP COLUMN precio;
```

 Ahora cada tabla contiene atributos dependientes solo de su clave primaria → cumple ​**2FN**​.

