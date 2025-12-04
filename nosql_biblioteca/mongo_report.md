# mongo_report.md - Biblioteca Digital

## Resumen del diseño en MongoDB

Caso de uso: Biblioteca digital con catálogo, préstamos y usuarios.

Colecciones:
- **books**: isbn, title, authors, editions, genres, copies, created_at
- **users**: username, email, full_name, created_at
- **loans**: user_id, isbn, loan_date, due_date, status

Ejemplo de documento (books):
{
  "isbn": "978-0143127741",
  "title": "The Martian",
  "authors": ["Andy Weir"],
  "genres": ["Ciencia ficción"],
  "copies": 5,
  "created_at": "2025-06-01T12:00:00Z"
}

Operaciones realizadas:
- Inserciones de ejemplo en `books`, `users`, `loans`.
- Consulta text-search por título/autores.
- Actualización de stock con `$inc`.
- Agregación para ranking de libros prestados.

En la entrega final en PDF añade capturas de `mongosh` mostrando las salidas reales de los comandos anteriores.
