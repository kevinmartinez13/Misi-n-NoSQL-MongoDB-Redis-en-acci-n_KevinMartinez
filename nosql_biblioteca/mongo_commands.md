# mongo_commands.md - Biblioteca Digital

## Conexión
# mongosh "mongodb://localhost:27017/biblioteca"

use biblioteca

## Insertar libros
db.books.insertOne({
  isbn: "978-0143127741",
  title: "The Martian",
  authors: ["Andy Weir"],
  genres: ["Ciencia ficción", "Aventura"],
  editions: [{ "edition": 1, "publisher": "Crown", "year": 2014 }],
  copies: 5,
  created_at: new Date()
})

db.books.insertMany([
  {
    isbn: "978-0307387899",
    title: "The Road",
    authors: ["Cormac McCarthy"],
    genres: ["Distopía"],
    copies: 3,
    created_at: new Date()
  },
  {
    isbn: "978-0061120084",
    title: "To Kill a Mockingbird",
    authors: ["Harper Lee"],
    genres: ["Ficción"],
    copies: 4,
    created_at: new Date()
  }
])

## Insertar usuarios
db.users.insertOne({
  username: "ana123",
  full_name: "Ana Gómez",
  email: "ana@example.com",
  created_at: new Date()
})

## Registrar préstamo (loan)
db.loans.insertOne({
  user_id: ObjectId(), /* sustituir _id real */
  isbn: "978-0143127741",
  loan_date: new Date(),
  due_date: new Date(Date.now() + 14*24*60*60*1000),
  status: "on_loan"
})

## Consultas
# Buscar por título parcial
db.books.find({ title: /martian/i })

# Libros por género
db.books.find({ genres: "Ciencia ficción" }).pretty()

# Historial de préstamos de un usuario
db.loans.find({ user_id: ObjectId() })

## Actualizaciones
# Disminuir copia disponible al prestar
db.books.updateOne({ isbn: "978-0143127741" }, { $inc: { copies: -1 } })

# Marcar loan como returned
db.loans.updateOne({ _id: ObjectId() }, { $set: { status: "returned", returned_at: new Date() } })

## Aggregation (libros más prestados)
db.loans.aggregate([
  { $group: { _id: "$isbn", totalLoans: { $sum: 1 } } },
  { $sort: { totalLoans: -1 } },
  { $limit: 10 }
])

## Índices
db.books.createIndex({ isbn: 1 }, { unique: true })
db.books.createIndex({ title: "text", authors: "text", genres: "text" })

## Validación de esquema simple para books
db.createCollection("books", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["isbn","title","copies"],
      properties: {
        isbn: { bsonType: "string" },
        title: { bsonType: "string" },
        copies: { bsonType: "int" }
      }
    }
  }
})
