## Manual de Creación de Triggers en MySQL con MySQL Workbench

#### 1. Introducción a los Triggers

##### 1.1 Definición de Trigger
Un *trigger* es un procedimiento almacenado en una base de datos que se ejecuta automáticamente cuando ocurren eventos específicos en una tabla o una vista. Los eventos que pueden iniciar un *trigger* incluyen `INSERT`, `UPDATE`, o `DELETE`.

##### 1.2 Propósito y Utilidad de los Triggers
Los *triggers* son esenciales para:
- **Automatizar procesos** relacionados con la integridad y el mantenimiento de los datos.
- **Validar modificaciones** en la base de datos para garantizar la coherencia de los datos.
- **Auditar cambios** en las tablas mediante el registro de actividades.

#### 2. Sintaxis y Componentes de un Trigger

##### 2.1 Sintaxis General
La sintaxis básica para crear un *trigger* en MySQL es la siguiente:

```sql
CREATE TRIGGER [IF NOT EXISTS] nombre_del_trigger
  tiempo_del_trigger evento_del_trigger
  ON nombre_tabla FOR EACH ROW
  [orden_del_trigger]
  cuerpo_del_trigger;
```

##### 2.2 Descripción de Componentes
- **`IF NOT EXISTS`**: Opción para prevenir errores de duplicación al crear un *trigger* que ya existe.
- **`nombre_del_trigger`**: Identificador único dentro de la base de datos para el *trigger*.
- **`tiempo_del_trigger`**: Indica si el *trigger* actúa `BEFORE` (antes) o `AFTER` (después) del evento.
- **`evento_del_trigger`**: Define el evento que activará el *trigger* (`INSERT`, `UPDATE`, `DELETE`).
- **`nombre_tabla`**: La tabla sobre la que el *trigger* tiene efecto.
- **`FOR EACH ROW`**: Especifica que el *trigger* se ejecutará para cada fila afectada por el evento.
- **`orden_del_trigger`**: Especifica el orden de ejecución en relación con otros *triggers* (`FOLLOWS` o `PRECEDES` otro *trigger*).
- **`cuerpo_del_trigger`**: Contiene las declaraciones SQL que se ejecutarán cuando se active el *trigger*.

#### 3. Implementación Práctica de un Trigger en la Base de Datos "Sakila"

##### 3.1 Preparación de la Base de Datos
Asegúrese de que la tabla sobre la que se creará el *trigger* tiene las columnas necesarias. Por ejemplo, para un *trigger* que contabilice los alquileres, la tabla `film` debe tener una columna `rental_count`.

```sql
USE sakila;

ALTER TABLE film
ADD COLUMN rental_count INT DEFAULT 0 NOT NULL;
```

##### 3.2 Creación del Trigger
Ejemplo de *trigger* para incrementar un contador cada vez que se registra un nuevo alquiler:

```sql
DELIMITER $$

CREATE TRIGGER increment_rental_count
AFTER INSERT ON rental
FOR EACH ROW
BEGIN
    UPDATE film
    SET rental_count = rental_count + 1
    WHERE film_id = (SELECT film_id FROM inventory WHERE inventory_id = NEW.inventory_id);
END$$

DELIMITER ;
```

##### 3.3 Validación y Pruebas
Después de implementar el *trigger*, es fundamental probar su funcionamiento:

```sql
INSERT INTO rental (rental_date, inventory_id, customer_id, staff_id)
VALUES (NOW(), 1, 1, 1);

SELECT film_id, rental_count FROM film WHERE film_id = 
    (SELECT film_id FROM inventory WHERE inventory_id = 1);
```

#### 4. Gestión de Triggers

##### 4.1 Modificación de Triggers
Para modificar un *trigger*, primero debe ser eliminado y luego redefinido con las nuevas especificaciones.

##### 4.2 Eliminación de Triggers
Para eliminar un *trigger* cuando ya no es necesario o para reemplazarlo:

```sql
DROP TRIGGER IF EXISTS schema_name.nombre_del_trigger;
```

