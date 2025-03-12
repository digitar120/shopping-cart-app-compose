# Índice / Index
- [Descripción en Español](#español)
- [Description in English](#english)

# Español
## Aplicación de carrito de compras

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
- JavaDocs

## Componentes de la aplicación
- Una API Gateway
  - https://github.com/digitar120/api-gateway
- Servicio de carrito de compras:
  - El componente principal. Administra carritos, items y productos.
  - https://github.com/digitar120/shopping-cart-app
- Servicio de usuarios
  - Un servicio secundario. Principalmente, sirve para implementar conexiones con FeignClient. Aloja usuarios.
  - https://github.com/digitar120/users-app
- Un servicio de autenticación Keycloak.
  - https://hub.docker.com/r/digitar120/auth
    - Imágen de Keycloak preconfigurada.
- Un servicio RabbitMQ.
- Un servidor de configuración Spring Cloud Config.
  - https://github.com/digitar120/config-server-public
  - Configurado para apuntar a https://github.com/digitar120/service-configuration-public
- Un Discovery Service (Eureka).
  - https://github.com/digitar120/discovery-service

## Cómo probar la aplicación
*Este Compose es trabajo en progreso. La composición arranca, pero hay servicios que no se conectan entre sí.*

*Por ahora, el API Gateway solo autentica por usuario y contraseña en el navegador. También tengo que resolver un problema con las redirecciones, ya que genera un error al autenticar. Planeo exponer los endpoints de Swagger, lo que obviaría todo lo siguiente.*

---

Se puede ejecutar la composición con `docker compose up` en cualquier plataforma.

Se pueden probar los endpoints directamente, sin pasar por la Gateway, con Postman ([o con éste Gist](https://gist.github.com/digitar120/ca652cd8c925785da6ffc4ee00e074e7), usando Bash y [la herramienta JQ](https://jqlang.github.io/jq/))

- Authorization -> Type: OAuth2
- Access Token URL: http://host.docker.internal:9090/realms/digitar120-shopping-cart-project-realm/protocol/openid-connect/token
- Client ID: spring-cloud-gateway-client
- Client secret: Cl9WL7FhnazS9uDPmdmVarAehvyr44ek

Los servicios se alojan en:
- localhost:9000 (carritos).
- localhost:9001 (usuarios).

Algunos endpoints rápidos:
- GET /cart
  - Lista todos los carritos.
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

## Pendientes
- Docker: Networking entre contenedores.
- Gateway
  - Exponer endpoints de Swagger de los servicios de usuarios y carritos
  - Permitir llamadas por herramientas como Postman
  - Mover configuración al servicio afín
- Resolver una falla de FeignClient relacionada a fallbacks y CircuitBreaker
- Pruebas unitarias para la capa de controlador
- Pruebas de integración

***¡Gracias por ver!***

---
# English
## Shopping cart application

A Spring-based microservice application.

This application manages databases containing carts, items and products. There's also a secondary service to manage users and to implement a FeignClient communication.

The application employs a variety of concepts and utilities:
- Design patterns, focusing on Circuit Breaker
- Generics
- Mappers
- Exception handling
- Unit tests
- Feign Client connections
- Open API / Swagger
- JavaDocs


## Application components
- An API Gateway
  - https://github.com/digitar120/api-gateway
- Shopping cart service
  - El componente principal. Administra carritos, items y productos
  - https://github.com/digitar120/shopping-cart-app
- User service
  - A secondary service, mainly useful for implementing FeignClient connections. Manages user entries.
  - https://github.com/digitar120/users-app
- Keycloak authentication service
  - https://hub.docker.com/r/digitar120/auth
    - Preconfigured Keycloak image.
- RabbitMQ message broker
- Spring Cloud Config service
  - https://github.com/digitar120/config-server-public.
  - Configured to draw data from https://github.com/digitar120/service-configuration-public
- An Eureka discovery service
  - https://github.com/digitar120/discovery-service

## How to try out the app

*This Compose file is a WIP. The application succesfully runs, but there are services that do not communicate with their peers.*

*At the moment, the API Gateway only authenticates users via a browser. I also have to fix a problem with redirections to the authentication service.*

---

This file can be run on any platform, issuing `docker compose up`.

The endpoints can be tested directly (without calling to the Gateway) with Postman. You can also use [this Gist](https://gist.github.com/digitar120/ca652cd8c925785da6ffc4ee00e074e7), which uses cURL, Bash and [JQ](https://jqlang.github.io/jq/). Use these settings:

- Authorization -> Type: OAuth2
- Access Token URL: http://host.docker.internal:9090/realms/digitar120-shopping-cart-project-realm/protocol/openid-connect/token
- Client ID: spring-cloud-gateway-client
- Client secret: Cl9WL7FhnazS9uDPmdmVarAehvyr44ek


The main services are at:
- localhost:9000 (carts).
- localhost:9001 (users).

Some quick endpoints to try:
- GET /cart
  - Lists all carts.
- GET /cart/by-userid/{userId}
  - Lists all carts belonging to an user.
  - Calls the Users service.
  - Use `userId` 36693120.
- POST /cart
  - Creates a new cart. It takes JSON data with the following parameters:

```json
{
	"description": "Description",
	"userId": 1
}
```

- GET /cart/{id}/items
  - Lists items contained in a Cart object, with its associated Products.
  - Use `id` 1.

- POST /cart/{cart_id}/product/{product_id}/quantity/{quantity}
  - In a cart, adds a product or updates its assigned quantity.
  - Use `/cart/1/product/1/quantity/15`.


You can find the full list of endpoins in the source code:
- [Shopping Cart](https://github.com/digitar120/shopping-cart-app/tree/dev/src/main/java/com/digitar120/shoppingcartapp/controller)
- [Users](https://github.com/digitar120/users-app/blob/main/src/main/java/com/digitar120/usersapp/controller/UserController.java)

## To-Dos
- Docker: Container networking.
- Gateway
  - Expose Swagger endpoints from the Carts and Users services, for demonstration purposes.
  - Allow calls for regular, non-web-browser-based calls, like Postman or cURL.
  - Move configuration to the config repo
- Solve an edge case related to FeignCleint and the circuit breaker pattern.
- Unit tests for the controller layer.
- Integration tests.

***Thanks for reading!***