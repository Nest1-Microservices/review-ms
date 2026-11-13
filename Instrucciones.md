# PARTE 2 · Microservicio de Reseñas (50 pts)

Permite calificar productos. Se comunica por TCP con el microservicio de productos, igual que órdenes lo hace para confirmar productos.

## Modelo Prisma Review

| Campo       | Tipo       | Restricciones                   |
| ----------- | ---------- | ------------------------------- |
| `id`        | `Int`      | Clave primaria, autoincremental |
| `productId` | `Int`      | Id del producto reseñado        |
| `rating`    | `Int`      | Calificación de 1 a 5           |
| `comment`   | `String?`  | Opcional                        |
| `createdAt` | `DateTime` | Por defecto, fecha actual       |

## Patrones de mensaje

| Patrón                               | Payload                           | Respuesta                                              |
| ------------------------------------ | --------------------------------- | ------------------------------------------------------ |
| `{ cmd: 'create_review' }`           | `productId`, `rating`, `comment?` | La reseña creada                                       |
| `{ cmd: 'find_reviews_by_product' }` | `productId`                       | Lista de reseñas del producto, la más reciente primero |
| `{ cmd: 'product_rating_summary' }`  | `productId`                       | `{ productId, totalReviews, averageRating }`           |

## Reglas

- `productId`: entero positivo. 
- `rating`: entero entre 1 y 5. 
- `comment`: opcional, máximo 300 caracteres**.
- Antes de crear una reseña, verifica que el producto exista consultando al microservicio de productos con el mismo mecanismo que usa `orders`. Si no existe, el error del microservicio de productos debe llegar al cliente (`404`).
- `averageRating`: promedio redondeado a 1 decima. Si el producto no tiene reseñas: `totalReviews: 0` y `averageRating: 0`.
- Las mismas reglas globales de validación y manejo de errores con `RpcException` de la Parte 1.

## Conexión con el gateway

| Método | Ruta                                  | Descripción               |
| ------ | ------------------------------------- | ------------------------- |
| `POST` | `/reviews`                            | Crear reseña              |
| `GET`  | `/reviews/product/:productId`         | Reseñas de un producto    |
| `GET`  | `/reviews/product/:productId/summary` | Resumen de calificaciones |

`productId` debe llegar como número entero; una ruta con un valor no numérico debe responder `400`.

## PRUEBAS Y ENTREGA (10 pts)

1. Levanta products-ms, categorias-ms, reseñas-ms y client-gateway al mismo tiempo.
2. Prueba todos los endpoints con Postman. Guarda la colección en la raíz como evidencia, incluyendo al menos un caso de error `404`, uno `409` y uno `400`.
3. Sigue los pasos de `README.md` para la entrega