# redis_report.md - Biblioteca Digital

## Resumen del uso de Redis

Por qué Redis:
- Latencia baja para sesiones y operaciones de reserva.
- Soporta estructuras (Hash, List, Sorted Set) necesarias para colas, inventario en memoria y rankings.

Claves usadas:
- `session:user:<id>` (String) - sesión con TTL.
- `inventory:<isbn>` (Hash) - copia en memoria y reservas.
- `reservation_queue` (List) - cola de reservas.
- `book_popularity` (Sorted Set) - ranking por vistas/préstamos.
- `hold:user:<id>:<isbn>` (String con TTL) - hold temporal al reservar.

Ejemplos de operaciones:
- `HGETALL inventory:978-0143127741` -> muestra stock y reserved.
- `ZINCRBY book_popularity 1 "978-0143127741"` -> incrementa popularidad.
- `LPUSH reservation_queue "{...}"` -> añade reserva a la cola.

*(En la versión PDF final añade capturas de `redis-cli` o RedisInsight mostrando salidas reales.)*
