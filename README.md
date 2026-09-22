# 🛍️ Webew — Backend (Proyecto Full Stack)

![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat&logo=node.js&logoColor=white)
![Express](https://img.shields.io/badge/Express-000000?style=flat&logo=express&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-47A248?style=flat&logo=mongodb&logoColor=white)
![Vitest](https://img.shields.io/badge/Vitest-6E9F18?style=flat&logo=vitest&logoColor=white)
![JWT](https://img.shields.io/badge/Auth-JWT-black?style=flat&logo=jsonwebtokens)

API REST completa para **Webew**, una plataforma de **compra-venta de productos**, desarrollada como proyecto final del bootcamp Full Stack de Neoland. Incluye autenticación de usuarios, gestión de productos, sistema de favoritos y una suite de tests de integración.

🔗 **API en producción:** [https://backend-proyecto-final-sergio.onrender.com](https://backend-proyecto-final-sergio.onrender.com)

---

## ✨ Características

- CRUD completo de productos, con categorías y productos destacados
- Registro e inicio de sesión de usuarios con JWT
- Contraseñas encriptadas con bcrypt
- Autenticación mediante Bearer Token en rutas protegidas
- **Sistema de favoritos** por usuario (añadir, listar, eliminar uno o todos)
- Seeder de datos iniciales para poblar la base de datos
- Tests de integración con Vitest y Supertest
- Desplegado en Render con MongoDB Atlas

## 🛠 Tecnologías utilizadas

- Node.js + Express
- MongoDB Atlas + Mongoose
- JWT (jsonwebtoken)
- bcryptjs
- dotenv, cors
- Vitest + Supertest (testing)

## 📁 Estructura del proyecto

```
backend-proyecto-final-sergio/
├── src/
│   ├── config/
│   │   └── db.js
│   ├── controllers/
│   │   ├── auth.controller.js
│   │   ├── favorite.controller.js
│   │   └── product.controller.js
│   ├── middlewares/
│   │   └── auth.middleware.js
│   ├── models/
│   │   ├── Favorite.js
│   │   ├── Product.js
│   │   └── User.js
│   ├── routes/
│   │   ├── auth.router.js
│   │   ├── favorite.router.js
│   │   └── product.router.js
│   └── seeders/
│       └── product.seeder.js
├── test/
│   ├── app.test.js
│   ├── auth.test.js
│   ├── product.test.js
│   └── setup.js
├── .env.example
├── .gitignore
├── app.js
├── index.js
├── package.json
└── README.md
```

## ⚙️ Instalación

1. Clona el repositorio:
   ```bash
   git clone <repository_url>
   cd backend-proyecto-final-sergio
   ```

2. Instala las dependencias:
   ```bash
   npm install
   ```

3. Crea un archivo `.env` usando `.env.example` como referencia:
   ```env
   PORT=3000
   MONGODB_URI=mongodb+srv://usuario:password@cluster.mongodb.net/tudb
   JWT_SECRET=mi-clave-secreta
   ```

## ▶️ Uso

```bash
npm run dev     # desarrollo
npm start       # producción
npm test        # tests
```

Cargar datos de ejemplo en la base de datos:

```bash
node src/seeders/product.seeder.js
```

Una vez en marcha, la API está disponible en `http://localhost:<PORT>/api`.

---

## 📡 Endpoints

### Home

#### `GET /`
Devuelve un mensaje de bienvenida.

- `200 OK`
```json
{ "message": "Bienvenido a la API de compra-venta" }
```

---

### 🔐 Autenticación

#### `POST /api/auth/register`

**Body:**
```json
{
  "name": "Sergio Pérez",
  "email": "example2@example.com",
  "password": "123456"
}
```

- `201 Created` — `{ "message": "Usuario registrado correctamente" }`
- `400 Bad Request` — `"Todos los campos son obligatorios"` / `"El correo ya esta"`
- `500 Internal Server Error`

#### `POST /api/auth/login`

**Body:**
```json
{ "email": "juan@example.com", "password": "123456" }
```

- `200 OK`
```json
{
  "message": "Inicio de sesión correctamente",
  "token": "jwt_generado_aquí",
  "user": { "_id": "...", "name": "Sergio Pérez", "email": "example2@example.com" }
}
```
- `400 Bad Request` — `"Todos los campos son obligatorios"`
- `422 Unprocessable Entity` — `"El correo no es válido"` / `"Contraseña muy corta, mínimo 6 caracteres"`
- `401 Unauthorized` — `"Credenciales inválidas"`
- `500 Internal Server Error`

---

### 📦 Productos

#### `GET /api/products`
Devuelve todos los productos.
- `200 OK` — array de productos
- `500 Internal Server Error` — `"Error al obtener los productos"`

#### `GET /api/products/categories`
Devuelve las categorías disponibles.
- `200 OK` — `["Tecnología", "Moda", "Hogar", "Deportes"]`
- `500 Internal Server Error`

#### `GET /api/products/featured`
Devuelve los productos destacados.
- `200 OK` — `{ "products": [...] }`
- `500 Internal Server Error`

#### `GET /api/products/:id`
Devuelve un producto por ID.
- `200 OK` — objeto del producto
- `404 Not Found` — `"Product not found"`
- `500 Internal Server Error`

#### `POST /api/products` 🔒
**Body:**
```json
{ "name": "iPhone 15", "price": 999, "category": "Tecnología y electrónica", "image": "https://...", "featured": false }
```
- `201 Created`
- `422 Unprocessable Entity` — `"All fields are required"`
- `401 Unauthorized` — `"No autorizado"`
- `500 Internal Server Error`

#### `PUT /api/products/:id` 🔒
Actualiza los campos indicados del producto.
- `200 OK`
- `422 Unprocessable Entity`
- `404 Not Found` — `"CastError: Invalid ID"`
- `500 Internal Server Error`

#### `DELETE /api/product/:id` 🔒
- `200 OK` — `{ "message": "Producto borrado correctamente" }`
- `401 Unauthorized` — `"Credenciales inválidas"`
- `404 Not Found` — `"Producto no encontrado"`
- `500 Internal Server Error`

---

### ⭐ Favoritos

Todas las rutas de favoritos requieren autenticación (`Authorization: Bearer TOKEN`) y actúan sobre los favoritos del usuario autenticado.

#### `GET /api/favorites` 🔒
Devuelve la lista de favoritos del usuario.

- `200 OK`
```json
{ "User": "userId", "products": [ { "product": { "...": "producto populado" } } ] }
```
- Si el usuario no tiene favoritos aún, devuelve `products: []` en vez de error.

#### `POST /api/favorites` 🔒
Añade un producto a favoritos.

**Body:**
```json
{ "product": "productId" }
```

- `201 Created`
```json
{ "message": "Product added to favorites", "favorites": { "...": "favoritos actualizados y populados" } }
```
- `422 Unprocessable Entity` — `"Product ID is required"`
- `404 Not Found` — `"Product not found"`
- `400 Bad Request` — `"The product is already listed in favorites"`
- `500 Internal Server Error`

#### `DELETE /api/favorites/:productId` 🔒
Elimina un producto concreto de favoritos.

- `200 OK` — `{ "message": "Product removed from favorites", "favorites": { ... } }`
- `404 Not Found` — `"Favorites not found"`
- `500 Internal Server Error`

#### `DELETE /api/favorites` 🔒 / `DELETE /api/favorites/clear` 🔒
Vacía todos los favoritos del usuario.

- `200 OK` — `{ "message": "Favorites cleared" }`
- `404 Not Found` — `"No favorites found"`
- `500 Internal Server Error`

---

## 🔮 Roadmap

- [ ] Carrito de compras (próximamente)
- [ ] Paginación en el listado de productos
- [ ] Documentación interactiva con Swagger

## 👤 Autor

**Sergio Pérez Pérez** — Proyecto desarrollado como práctica final del curso Full Stack de Neoland.
