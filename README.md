Este documento detalla una API REST para la gestión de circuitos turísticos, ciudades y extensiones de viaje, junto con la arquitectura y funcionalidades de su frontend.

## 🚀 Características Principales

  * **Gestión de Circuitos**: Permite la visualización, filtrado y ordenación de circuitos turísticos.
  * **Exploración de Ciudades**: Facilita la búsqueda y exploración de ciudades con circuitos disponibles.
  * **Filtros Avanzados**: Ofrece opciones de filtrado por país, duración y touroperador.
  * **Interfaz Responsive**: Cuenta con un diseño adaptativo para dispositivos móviles y de escritorio.
  * **Modo Oscuro**: Permite la alternancia entre un tema claro y uno oscuro.
  * **Arquitectura Modular**: Desarrollada con componentes reutilizables utilizando Lit Element.

## Equipo de Desarrollo

  * **Backend**: Desarrollado con Spring Boot, JPA y MySQL.
  * **Frontend**: Implementado con Lit Element, CSS3 y JavaScript ES6+.
  * **Diseño**: Interfaz moderna y responsive.

## BACKEND

La API está construida con Spring Boot y sigue una arquitectura por capas:

  * **Controllers**: Capa de presentación que maneja las peticiones HTTP.
  * **Services**: Capa de lógica de negocio.
  * **Repositories**: Capa de acceso a datos usando Spring Data JPA.
  * **Models**: Entidades JPA y DTOs para transferencia de datos.

### Endpoints Disponibles

#### 🗺️ Circuitos

`GET /circuitos`

Obtiene todos los circuitos disponibles con filtros opcionales.

**Parámetros de consulta**:

  * `dias` (opcional): Filtra por duración en días.
  * `touroperador` (opcional): Filtra por nombre del touroperador.

**Respuesta**:

```json
[
  {
    "id": 1,
    "nombre": "Circuito Andalucía",
    "pais": "España",
    "dias": 7,
    "precio": 850.0,
    "url": "https://catai.es/circuito-andalucia",
    "touroperador": "Catai"
  }
]
```

**Ejemplos de uso**:

```bash
# Todos los circuitos
GET /circuitos

# Circuitos de 7 días
GET /circuitos?dias=7

# Circuitos de un touroperador específico
GET /circuitos?touroperador=Catai

# Circuitos de 7 días de un touroperador específico
GET /circuitos?dias=7&touroperador=Catai
```

#### 🏙️ Ciudades

`GET /ciudades`

Obtiene todas las ciudades disponibles en los circuitos.

**Respuesta**:

```json
[
  {
    "id": 1,
    "nombre": "Madrid"
  },
  {
    "id": 2,
    "nombre": "Barcelona"
  }
]
```

#### 🔍 Búsqueda de Circuitos por Ciudad

`POST /buscar`

Busca circuitos que incluyan una ciudad específica.

**Cuerpo de la petición**:

```json
{
  "nombreCiudad": "Madrid"
}
```

**Respuesta**:

```json
[
  {
    "id": 1,
    "circuito": {
      "id": 1,
      "nombre": "Circuito España Imperial",
      "pais": "España",
      "dias": 8,
      "precio": 950.0,
      "url": "https://catai.es/circuito-espana-imperial",
      "touroperador": "Catai"
    },
    "ciudad": {
      "id": 1,
      "nombre": "Madrid"
    }
  }
]
```

**Códigos de respuesta**:

  * `200 OK`: Búsqueda exitosa.
  * `400 Bad Request`: Parámetros inválidos.
  * `500 Internal Server Error`: Error interno del servidor.

#### 🌴 Extensiones

`POST /extensiones/{id}`

Obtiene las extensiones disponibles para un circuito específico.

**Parámetros de ruta**:

  * `id`: ID del circuito.

**Respuesta**:

```json
[
  {
    "id": 1,
    "nombre": "Extensión Islas Baleares",
    "circuito": {
      "id": 1,
      "nombre": "Circuito España Imperial",
      "pais": "España",
      "dias": 8,
      "precio": 950.0,
      "url": "https://catai.es/circuito-espana-imperial",
      "touroperador": "Catai"
    }
  }
]
```

**Ejemplo de uso**:

```bash
POST /extensiones/1
```

### Modelo de Datos

#### Circuito

```java
{
  "id": Long,
  "nombre": String,
  "pais": String,
  "dias": int,
  "precio": float,
  "url": String,
  "touroperador": String
}
```

#### Ciudad

```java
{
  "id": Long,
  "nombre": String
}
```

#### Extension

```java
{
  "id": long,
  "nombre": String,
  "circuito": CircuitoDto
}
```

#### FiltroDto (para búsquedas)

```java
{
  "nombreCiudad": String,
  "idCircuito": long
}
```

### Relaciones entre Entidades

  * **Circuito ↔ Ciudad**: Relación Many-to-Many a través de la tabla `circuito_ciudad`.
  * **Circuito ↔ Extension**: Relación One-to-Many (un circuito puede tener múltiples extensiones).

## FRONTEND:

### 📁 Estructura del Proyecto

```
cliente/
├── src/
│   ├── app.js                          # Componente principal de la aplicación
│   ├── header/                         # Módulo de header/navegación
│   │   ├── app-header.js              # Componente de header
│   │   └── app-header-styles.js       # Estilos del header
│   ├── circuito/                       # Módulo de circuitos
│   │   ├── circuito-lista.js          # Componente lista de circuitos
│   │   ├── circuito-lista-styles.js   # Estilos de circuitos
│   │   └── circuitoService.js         # Servicio API circuitos
│   └── ciudad/                         # Módulo de ciudades
│       ├── ciudad-lista.js            # Componente lista de ciudades
│       ├── ciudad-lista-styles.js     # Estilos de ciudades
│       └── ciudadService.js           # Servicio API ciudades
├── index.html                          # Punto de entrada
└── style.css                          # Estilos globales
```

### 🎨 Arquitectura de Componentes

  * **Componente Principal (MyApp)**
      * **Responsabilidad**: Gestión de rutas y vistas principales.
      * **Estado**: `currentView` para controlar la vista activa.
      * **Eventos**: Escucha eventos de navegación del header.
  * **Header (AppHeader)**
      * **Responsabilidad**: Navegación entre secciones y toggle de modo oscuro.
      * **Props**: `currentPage` para indicar la página activa.
      * **Eventos**: Emite `page-change` para cambios de navegación.
  * **Lista de Circuitos (PageCircuits)**
      * **Responsabilidad**: Visualización y filtrado de circuitos.
      * **Estado**: `circuitos`, `loading`, `error`, `filtros` y `ordenación`.
      * **Funcionalidades**:
          * Filtrado por país, días y touroperador.
          * Ordenación por precio y duración.
          * Carga de extensiones (popup modal).
  * **Lista de Ciudades (PageCities)**
      * **Responsabilidad**: Exploración de ciudades y sus circuitos.
      * **Estado**: `ciudades`, `ciudadesFiltradas`, `selectedCiudad`.
      * **Funcionalidades**:
          * Búsqueda en tiempo real.
          * Visualización en grid responsive.
          * Modal con circuitos por ciudad.

## INSTALACION Y EJECUCION DEL PROYECTO

**Requisitos previos**:

  * Java 17+.
  * Maven 3.6+.
  * Base de datos (configurada en `application.properties`).
  * Servidor web (Apache, Nginx, o servidor de desarrollo).

<!-- end list -->

```bash
# Clonar el repositorio
gh repo clone ItIsabel/APICatai

# Ejecutar backend
mvn spring-boot:run

# Navegar al directorio del proyecto
cd cliente

# Instalar las dependencias
npm install

# Ejecutar el servidor de desarrollo
npm run dev
# o con yarn:
yarn dev
```

## Ejemplos de Uso Completos

**Buscar circuitos de 7 días**

```bash
curl -X GET "http://localhost:8080/circuitos?dias=7"
```

**Buscar circuitos que pasan por Madrid**

```bash
curl -X POST "http://localhost:8080/buscar" \
-H "Content-Type: application/json" \
-d '{"nombreCiudad": "Madrid"}'
```

**Obtener extensiones de un circuito**

```bash
curl -X POST "http://localhost:8080/extensiones/1"
```

#### Diseño del front con Penpot:
https://design.penpot.app/#/view?file-id=518f9b2f-adb9-81b5-8006-75bb3fd7401d&page-id=518f9b2f-adb9-81b5-8006-75bb3fd7401e&section=interactions&index=0&share-id=dfec20eb-20e2-80c9-8006-75ce7cb8fe36
