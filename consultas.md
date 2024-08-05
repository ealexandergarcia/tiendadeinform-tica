### 1.Consultas sobre una tabla

1. Lista el nombre de todos los productos que hay en la tabla `producto`.

   ```javascript
   db.producto.find({}, { nombre: 1, _id: 0 });
   ```

   ```sql
   select nombre
   from producto;
   ```
2. Lista los nombres y los precios de todos los productos de la tabla `producto`.

   ```javascript
   db.producto.find({}, { id_fabricante: 0, _id: 0 });
   ```

   ```sql
   select nombre, precio
   from producto;
   ```
3. Lista todas las columnas de la tabla `producto`.

   ```
   db.producto.find();
   ```

   ```sql
   select *
   from producto;
   ```
4. Lista el nombre de los productos, el precio en euros y el precio en dólares estadounidenses (USD).

   ```
   db.producto.aggregate([
     {
       $project: {
         nombre: 1,
         precio_eur: "$precio",
         precio_usd: { $multiply: ["$precio", 1.10] }
       }
     }
   ]);

   ```

   ```sql
   SELECT nombre, 
          precio AS VDolar, 
          ROUND(precio * 0.93, 2) AS VEuro
   FROM producto;

   ```
5. Lista el nombre de los productos, el precio en euros y el precio en dólares estadounidenses (USD). Utiliza los siguientes alias para las columnas: `nombre de producto`, `euros`, `dólares`.

   ```
   db.producto.aggregate([
     {
       $project: {
         nombre_de_producto: "$nombre",
         euros: "$precio",
         dolares: { $multiply: ["$precio", 1.10] }
       }
     }
   ]);

   ```

   ```sql
   SELECT nombre as `nombre de producto`, 
          precio AS `dólares`, 
          ROUND(precio * 0.93, 2) AS `euros`
   FROM producto;

   ```
6. Lista los nombres y los precios de todos los productos de la tabla `producto`, convirtiendo los nombres a mayúscula.

   ```
   db.producto.aggregate([
     {
       $project: {
         nombre: { $toUpper: "$nombre" },
         precio: 1
       }
     }
   ]);

   ```

   ```sql
   SELECT UPPER(nombre), 
          precio 
   FROM producto;

   ```
7. Lista los nombres y los precios de todos los productos de la tabla `producto`, convirtiendo los nombres a minúscula.

   ```
   db.producto.aggregate([
     {
       $project: {
         nombre: { $toLower: "$nombre" },
         precio: 1
       }
     }
   ]);

   ```

   ```sql
   SELECT lower(nombre), 
          precio 
   FROM producto;

   ```
8. Lista el nombre de todos los fabricantes en una columna, y en otra columna obtenga en mayúsculas los dos primeros caracteres del nombre del fabricante.

   ```
   db.fabricante.aggregate([
     {
       $project: {
         nombre: 1, // Incluye el campo "nombre" en la salida
         iniciales_fabricante: {
           $toUpper: {
             $substr: ["$nombre", 0, 2]
           }
         }
       }
     }
   ]);

   ```

   ```sql
   SELECT 
   	nombre,
   	UPPER(SUBSTRING(nombre, 1, 2)) AS iniciales_fabricante
   FROM fabricante;

   ```
9. Lista los nombres y los precios de todos los productos de la tabla `producto`, redondeando el valor del precio.

   ```
   db.producto.aggregate([
     {
       $project: {
         nombre: 1,
         precio: { $round: ["$precio", 0] }
       }
     }
   ]);

   ```

   ```sql
   SELECT nombre, 
          round(precio) 
   FROM producto;
   ```
10. Lista los nombres y los precios de todos los productos de la tabla `producto`, truncando el valor del precio para mostrarlo sin ninguna cifra decimal.

    ```
    db.producto.aggregate([
      {
        $project: {
          nombre: 1,
          precio: { $floor: "$precio" }
        }
      }
    ]);

    ```

```sql
SELECT nombre, 
       FLOOR(precio) AS precio_truncado
FROM producto;
   
```

11. Lista el identificador de los fabricantes que tienen productos en la tabla `producto`.

    ```
    db.producto.find({}, { id_fabricante:1,_id:0 });
    ```

```sql
SELECT id_fabricante
FROM producto;
   
```

12. Lista el identificador de los fabricantes que tienen productos en la tabla `producto`, eliminando los identificadores que aparecen repetidos.
    ```javascript
    db.producto.distinct("id_fabricante");
    ```

```sql
SELECT DISTINCT id_fabricante
FROM producto;
   
   
```

13. Lista los nombres de los fabricantes ordenados de forma ascendente.

    ```javascript
    db.fabricante.find().sort({ nombre: 1 });
    ```

```sql
SELECT 
	nombre
FROM fabricante
order by nombre asc;
   
```

14. Lista los nombres de los fabricantes ordenados de forma descendente.

    ```
    db.fabricante.find().sort({ nombre: -1 });
    ```

```sql
SELECT 
	nombre
FROM fabricante
order by nombre desc;

```

15. Lista los nombres de los productos ordenados en primer lugar por el nombre de forma ascendente y en segundo lugar por el precio de forma descendente.

    ```javascript
    db.producto.find().sort({ nombre: 1, precio: -1 });
    ```

```sql
SELECT 
    f.nombre AS fabricante_nombre,
    p.precio
FROM producto p
JOIN fabricante f ON p.id_fabricante = f.id
ORDER BY f.nombre ASC, p.precio DESC;
   
```

16. Devuelve una lista con las 5 primeras filas de la tabla `fabricante`.

    ```javascript
    db.fabricante.find().limit(5);
    ```

```sql
SELECT *
FROM fabricante
limit 5;
   
```

17. Devuelve una lista con 2 filas a partir de la cuarta fila de la tabla `fabricante`. La cuarta fila también se debe incluir en la respuesta.

    ```javascript
    db.fabricante.find().skip(3).limit(2);
    ```

```sql
SELECT *
FROM fabricante
LIMIT 2 OFFSET 3;
   
```

18. Lista el nombre y el precio del producto más barato. (Utilice solamente las cláusulas `ORDER BY` y `LIMIT`)

    ```
    db.producto.find().sort({ precio: 1 }).limit(1);
    ```

```sql
select *
from producto
order by precio asc
limit 1;   
```

19. Lista el nombre y el precio del producto más caro. (Utilice solamente las cláusulas `ORDER BY` y `LIMIT`)

    ```javascript
    db.producto.find().sort({ precio: -1 }).limit(1);
    ```

```sql
select 
	nombre,
    precio
from producto
order by precio asc
limit 1;   
```

20. Lista el nombre de todos los productos del fabricante cuyo identificador de fabricante es igual a 2.

    ```
    db.producto.find({"id_fabricante": ObjectId("66b00d414a08e3a7ff313117")}, { nombre: 1, _id: 0 });
    ```

```sql
select 
	nombre
from producto
where id_fabricante =2;   
```

21. Lista el nombre de los productos que tienen un precio menor o igual a 120€.

    ```javascript
    db.producto.find({ precio: { $lte: 120 } }, { nombre: 1, _id: 0 });
    ```

```sql
select 
	nombre
from producto
where precio <=120   
```

22. Lista el nombre de los productos que tienen un precio mayor o igual a 400€.

    ```javascript
    db.producto.find({ precio: { $gte: 400 } }, { nombre: 1, _id: 0 });
    ```

```sql
select 
	nombre
from producto
where precio >= 400   
```

23. Lista el nombre de los productos que **no tienen** un precio mayor o igual a 400€.

    ```
    db.producto.find({ precio: { $lt: 400 } }, { nombre: 1, _id: 0 });
    ```

```sql
SELECT 
    nombre
FROM 
    producto
WHERE 
    precio < 400;   
```

24. Lista todos los productos que tengan un precio entre 80€ y 300€. Sin utilizar el operador `BETWEEN`.

    ```javascript
    db.producto.find({ precio: { $gte: 80, $lte: 300 } }, { nombre: 1, _id: 0 });
    ```

```sql
SELECT 
    nombre
FROM 
    producto
WHERE 
    precio >= 80 AND precio <= 300;
   
```

25. Lista todos los productos que tengan un precio entre 60€ y 200€. Utilizando el operador `BETWEEN`.

    ```
    db.producto.find({ precio: { $gte: 60, $lte: 200 } }, { nombre: 1, _id: 0 });

    ```

```sql
SELECT 
    nombre
FROM 
    producto
WHERE 
    precio BETWEEN 60 AND 200;
   
```

26. Lista todos los productos que tengan un precio mayor que 200€ y que el identificador de fabricante sea igual a 6.

    ```javascript
    db.producto.find({"id_fabricante": ObjectId("66b00d414a08e3a7ff31311b"), precio:{$gte:200}}, { nombre: 1, _id: 0 });
    ```

```sql
SELECT 
    nombre
FROM 
    producto
WHERE 
    precio > 200 AND id_fabricante = 6;
   
```

27. Lista todos los productos donde el identificador de fabricante sea 66b00d414a08e3a7ff313116, 66b00d414a08e3a7ff313118 o 66b00d414a08e3a7ff31311a. Sin utilizar el operador `IN`.

    ```javascript
    db.producto.find({ nombre: { $regex: /a$/ } }, { nombre: 1, _id: 0 });
    ```

```sql
SELECT 
    nombre
FROM 
    producto
WHERE 
    id_fabricante = 1 OR id_fabricante = 3 OR id_fabricante = 5;
   
```

28. Lista todos los productos donde el identificador de fabricante sea 1, 3 o 5. Utilizando el operador `IN`.

    ```javascript
    db.producto.find({
      id_fabricante: {
        $in: [
          ObjectId("66b00d414a08e3a7ff313116"),
          ObjectId("66b00d414a08e3a7ff313118"),
          ObjectId("66b00d414a08e3a7ff31311a")
        ]
      }
    }, {
      nombre: 1,
      _id: 0
    });

    ```

```sql
SELECT 
    nombre
FROM 
    producto
WHERE 
    id_fabricante IN (1, 3, 5);
   
```

29. Lista el nombre y el precio de los productos en céntimos (Habrá que multiplicar por 100 el valor del precio). Cree un alias para la columna que contiene el precio que se llame `céntimos`.

    ```javascript
    [
      {
        $project: {
          nombre: "$nombre",
          céntimos: { $multiply: ["$precio", 100] }
        }
      }
    ]
    ```

```sql
SELECT 
    nombre,
    precio * 100 AS céntimos
FROM 
    producto;
   
```

30. Lista los nombres de los fabricantes cuyo nombre empiece por la letra `S`.

    ```javascript
    [
      {
        $match: {
          nombre: {
            $regex: "^S",
            $options: "i"
          }
        }
      },
      {
        $project: {
          _id: 0,
          nombre: 1
        }
      }
    ]
    ```

```sql
  SELECT 
    nombre
FROM 
    fabricante
WHERE 
    nombre LIKE 's%';

```

31. Lista los nombres de los fabricantes cuyo nombre termine por la vocal `e`.

    ```javascript
    [
      {
        $match: {
          nombre: {
            $regex: "e$",
            $options: "i"
          }
        }
      },
      {
        $project: {
          _id: 0,
          nombre: 1
        }
      }
    ]
    ```

```sql
SELECT 
    nombre
FROM 
    fabricante
WHERE 
    nombre LIKE '%e';
   
```

32. Lista los nombres de los fabricantes cuyo nombre contenga el carácter `w`.
    ```
    [
      {
        $match: {
          nombre: { $regex: /w/, $options: "i" }
        }
      },
      {
        $project: {
          _id: 0,
          nombre: 1
        }
      }
    ]
    ```

```sql
SELECT 
    nombre
FROM 
    fabricante
WHERE 
    nombre LIKE '%w%';
   
```

33. Lista los nombres de los fabricantes cuyo nombre sea de 4 caracteres.
    ```javascript
    [
      {
        $match: {
          nombre: { $regex: "^.{4}$" }
        }
      },
      {
        $project: {
          _id: 0,
          nombre: 1
        }
      }
    ]
    ```

```sql
SELECT 
    nombre
FROM 
    fabricante
WHERE 
    LENGTH(nombre) = 4;
   
```

34. Devuelve una lista con el nombre de todos los productos que contienen la cadena `Portátil` en el nombre.

    ```javascript
    [
      {
        $match: {
          nombre: /Portátil/
        }
      },
      {
        $project: {
          _id: 0,
          nombre: 1
        }
      }
    ]
    ```

```sql
SELECT 
    nombre
FROM 
    producto
WHERE 
    nombre LIKE '%Portátil%';

   
```

35. Devuelve una lista con el nombre de todos los productos que contienen la cadena `Monitor` en el nombre y tienen un precio inferior a 215 €.
    ```javascript
    [
      {
        $match: {
          nombre: {
            $regex: "Monitor",
            $options: "i"
          },
          precio: {
            $lt: 215
          }
        }
      },
      {
        $project: {
          _id: 0,
          nombre: 1,
          precio:1
        }
      }
    ]
    ```

```sql
SELECT 
    nombre
FROM 
    producto
WHERE 
    nombre LIKE '%Monitor%' AND precio < 215;
   
```

36. Lista el nombre y el precio de todos los productos que tengan un precio mayor o igual a 180€. Ordene el resultado en primer lugar por el precio (en orden descendente) y en segundo lugar por el nombre (en orden ascendente).

    ```javascript
    [
      {
        $match: {
          precio: {
            $gte: 180
          }
        }
      },
      {
        $project: {
          _id: 0,
          nombre: 1,
          precio: 1
        }
      },
      {
        $sort: {
          precio: -1,
          nombre: 1
        }
      }
    ]
    ```

```sql
SELECT 
    nombre,
    precio
FROM 
    producto
WHERE 
    precio >= 180
ORDER BY 
    precio DESC,
    nombre ASC;
   
```
