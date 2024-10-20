# Aplicación de carrito de compras

Una aplicación de microservicios desarrollada en Spring.

Consiste de un microservicio que administra bases de datos de productos disponibles, carritos y sus ítems. Tiene un servicio secundario de usuarios para integrar conceptos de conexiones entre microservicios (específicamente FeignClient).

Emplea una variedad de conceptos y utilidades:
- Patrones de diseño (principalmente Circuit Breaker)
- Genéricos
- Mapeadores
- Manejo de excepciones
- Pruebas unitarias
- Conexiones entre microservicios con FeignClient
- Open API / Swagger

# Componentes de la aplicación
- Una API Gateway
  - https://github.com/digitar120/api-gateway
- Servicio de carrito de compras:
  - El componente principal. Administra carritos, items y productos
  - https://github.com/digitar120/shopping-cart-app
- Servicio de usuarios
  - Un servicio secundario. Principalmente, sirve para implementar conexiones con FeignClient. Aloja usuarios.
  - https://github.com/digitar120/users-app
- Un servicio de autenticación Keycloak
  - https://hub.docker.com/r/digitar120/auth
- Un servicio RabbitMQ.
- Un servidor de configuración Spring Cloud Config
  - https://github.com/digitar120/config-server-public.
  - Configurado para apuntar a https://github.com/digitar120/service-configuration-public.
- Un Discovery Service (Eureka).
  - https://github.com/digitar120/discovery-service

# Cómo probar la aplicación
Se puede ejecutar la composición con `docker compose up` en cualquier plataforma.

**Por ahora, el API Gateway solo autentica por usuario y contraseña en el navegador. También tengo que resolver un problema con las redirecciones, ya que genera un error al autenticar. Planeo exponer los endpoints de Swagger, lo que obviaría todo lo siguiente.**

Se pueden probar los endpoints directamente con Postman ([o con éste Gist](https://gist.github.com/digitar120/ca652cd8c925785da6ffc4ee00e074e7), usando Bash y [la herramienta JQ](https://jqlang.github.io/jq/))

- Authorization -> Type: OAuth2
- Access Token URL: http://host.docker.internal:9090/realms/digitar120-shopping-cart-project-realm/protocol/openid-connect/token
- Client ID: spring-cloud-gateway-client
- Client secret: Cl9WL7FhnazS9uDPmdmVarAehvyr44ek

Los servicios se alojan en:
- host.docker.internal:9000 (carritos)
- host.docker.internal:9001 (usuarios)

Algunos endpoints rápidos:
- GET /cart
  - Lista todos los carritos
- GET /cart/by-userid/{userId}
  - Lista todos los carritos pertenecientes a un usuario.
  - Realiza una llamada a Usuarios.
  - Puede usar el `userId` 36693120
- POST /cart
  - Crea un nuevo carrito. Admite un JSON con el siguiente formato:

```json
{
	"description": "Description",
	"userId": 1
}
```

- GET /cart/{id}/items
  - Lista los items de un carrito, junto con sus productos asociados.
  - Puede usar el `id` 1

- POST /cart/{cart_id}/product/{product_id}/quantity/{quantity}
  - En un carrito, añade un producto o actualiza su cantidad.
  - Puede probar `/cart/1/product/1/quantity/15`


También dejo la lista de todos los endpoints:
- [Shopping Cart](https://github.com/digitar120/shopping-cart-app/tree/dev/src/main/java/com/digitar120/shoppingcartapp/controller)
- [Users](https://github.com/digitar120/users-app/blob/main/src/main/java/com/digitar120/usersapp/controller/UserController.java)

# Pendientes
- Gateway
  - Exponer endpoints de Swagger de los servicios de usuarios y carritos
  - Permitir llamadas por herramientas como Postman
  - Mover configuración al servicio afín
- Resolver una falla de FeignClient relacionada a fallbacks y CircuitBreaker
- Pruebas unitarias para la capa de controlador
- Pruebas de integración

***¡Gracias por ver!***