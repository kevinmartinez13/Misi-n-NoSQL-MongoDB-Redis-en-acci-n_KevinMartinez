# NoSQL Lab - Biblioteca Digital (MongoDB + Redis) Kevin Martinez

**Caso de uso:** Biblioteca digital para gestionar catálogo de libros, usuarios, préstamos, reservas y funcionalidades en tiempo real (ranking de libros populares, sesiones, cola de reservas).

---

## 1. ¿Qué es una base de datos NoSQL y en qué se diferencia, a nivel conceptual, de una base de datos relacional (SQL)? Explicar al menos dos diferencias clave.

Una base de datos NoSQL es un conjunto de tecnologías de almacenamiento diseñadas para modelos de datos no tabulares (documentos, grafos, columnas, pares clave–valor). Están pensadas para alta escalabilidad, modelado flexible y rendimiento en lecturas/escrituras distribuidas.

**Diferencias clave:**
- **Estructura de datos:** SQL usa tablas con filas y columnas y relaciones normalizadas; NoSQL (como MongoDB) usa documentos que embeben relaciones (objetos anidados), reduciendo la necesidad de joins.
- **Consistencia y escalabilidad:** Bases SQL suelen priorizar la consistencia estricta (ACID) y escalar verticalmente; muchas soluciones NoSQL priorizan la disponibilidad y particionamiento (CAP trade-offs) y escalan horizontalmente más fácilmente.

---

## 2. ¿Cuál es el rol de MongoDB dentro del ecosistema NoSQL?

MongoDB es una **base de datos orientada a documentos**. Almacena documentos BSON (similar a JSON), lo que permite representar libros, usuarios y préstamos con estructuras anidadas (metadatos, ediciones, historial) en un solo documento. Es idónea cuando se requieren consultas flexibles, índices complejos y agregaciones sin un esquema fijo.

---

## 3. ¿Qué son una “base de datos”, una “colección” y un “documento” en MongoDB, y cómo se relacionan entre sí? Incluir ejemplo JSON.

- **Base de datos:** Contenedor lógico (ej.: `biblioteca`).
- **Colección:** Grupo de documentos (ej.: `books`, `users`, `loans`).
- **Documento:** Registro JSON/BSON que contiene los datos (unidad básica).

**Ejemplo (documento en `books`):**
```json
{
  "isbn": "978-0143127741",
  "title": "The Martian",
  "authors": ["Andy Weir"],
  "editions": [
    { "edition": 1, "publisher": "Crown", "year": 2014 }
  ],
  "genres": ["Ciencia ficción", "Aventura"],
  "copies": 5,
  "created_at": { "$date": "2025-06-01T12:00:00Z" }
}
```

---

## 4. ¿Qué ventajas aporta el esquema flexible de MongoDB y un riesgo si no se gestiona bien?

**Ventajas:**
1. **Adaptabilidad:** Añadir nuevos campos (por ejemplo, `subtitle`, `awards`) por libro sin migraciones.
2. **Agilidad en desarrollo:** Permite iterar el modelo de datos rápidamente durante cambios de producto.

**Riesgo:**  
Si no se controla, diferentes documentos pueden usar nombres de campo distintos (p. ej. `author` vs `authors`), provocando inconsistencia y errores en consultas y en el análisis. Mitigación: validadores JSON Schema, convenciones y tests.

---

## 5. ¿Qué es Redis y por qué es un "data structure server"? Mencionar 3 tipos de datos.

Redis es una base de datos en memoria que expone estructuras de datos eficientes (más que simples strings). Se le llama "data structure server" porque opera directamente sobre estructuras como **Hashes**, **Lists**, **Sorted Sets**, **Sets**, **Streams**, etc., con operaciones atómicas y latencias sub-milisegundo.

Tipos: **Hash**, **List**, **Sorted Set** (entre otros).

---

## 6. Elegir dos tipos de datos de Redis y describir uso y caso de uso.

### Hash
- **Comandos:** `HSET user:1001 name "Ana" email "ana@x.com"`, `HGETALL user:1001`
- **Caso de uso:** Cache de perfil de usuario con TTL corto (session-data), evitando consulta frecuente a MongoDB.

### Sorted Set
- **Comandos:** `ZADD book_popularity 25 "978-0143127741"`, `ZRANGE book_popularity 0 9 WITHSCORES`
- **Caso de uso:** Ranking de libros por vistas o préstamos; ideal para mostrar los "más populares" en la home.

---

## 7. En el laboratorio de turnos (analógico en la biblioteca: sistema de reservas):

### ¿Qué información se almacena en MongoDB?
- Datos persistentes: catálogo de libros (`books`), usuarios (`users`), historial de préstamos (`loans`), registros de reservas y auditoría.

### ¿Qué información se maneja o acelera con Redis?
- Estado volátil y contadores: sesiones de usuario, cola de reservas (`reservation_queue`), ranking de popularidad (`book_popularity`), stock en memoria para evitar oversell de copias.

**Lógica:** MongoDB para historial y consultas complejas; Redis para latencia baja y operaciones frecuentemente actualizadas.

---

## 8. En el laboratorio de tamagochi (analógico: estado de lectura en tiempo real):

### ¿Qué partes del "estado" conviene guardar en MongoDB?
- Preferencias del usuario, listas de lectura, historial de préstamos.

### ¿Qué partes se benefician de Redis?
- Temporizadores de reserva (TTL), contador de lecturas en la sesión, progreso de lectura en tiempo real (si la app muestra posición por capítulo).

---

## 9. Criterio para decidir qué va en MongoDB y qué va en Redis. Ejemplos concretos.

**Criterio:** Persistencia y necesidad analítica → MongoDB. Alta frecuencia de lectura/escritura con tolerancia a pérdida (recreable) → Redis.

**Ejemplos:**
- **MongoDB:** `books` (documento completo con ediciones y metadatos), `loans` (historial de préstamos).
- **Redis:** `session:user:<id>` (String con TTL), `book_popularity` (Sorted Set), `reservation_queue` (List).

---

## 10. Reflexión final:

**Usaría MongoDB como base principal para:** almacenar el catálogo, registros de usuarios, préstamos y reportes históricos (consultas agregadas).

**Usaría Redis como apoyo para:** sesiones, caché de perfil, colas de reserva, rankings y contadores en tiempo real—por ejemplo, usar `ZINCRBY book_popularity 1 <isbn>` cada vez que un libro se visualiza.

---

### Instrucciones para entrega
En el repositorio público incluye: `README.md` (este archivo), `mongo_commands.md`, `redis_commands.md`, `mongo_report.md`, `redis_report.md`. Los archivos adjuntos en el ZIP contienen comandos y ejemplos ejecutables para que puedas correrlos en tu entorno.
