
# API Multabot

La única API para consultar multas a infracciones en Argentina.

Esta documentación es para la API para empresas, en caso de querer integrarla ponerse en contacto desde la web.

Sitio web: [https://multabot.com.ar](https://multabot.com.ar)


# Autorización

La API funciona con Bearer Token desde un header `Authorization`.

Se pueden crear desde el panel de API en tu cuenta.

# Limites

No hay limites para las consultas.

# Endpoints

Los endpoints tienen dos tipo de respuesta.

* `200` Solicitud OK
* `204` Solicitud OK sin contenido
* `422` Validación incorrecta cuando hay parámetros incorrectos

## Entities

### GET `/entities`

Listado de jurisdicciones. Incluye el estado del servicio.

__Formato de respuesta__

* `name (string)` Nombre de la jurisdicción
* `slug (string)` Identificador de la jurisdicción para demás endpoints
* `is_working (boolean)` 

## Requests

### GET `/requests`

Listado de solicitudes a la API. 

__Parámetros opcionales de solicitud__

* `entity` Slug de jurisdicción
* `domain` Dominio (DNI/Patente)

## Penalties

### Formato de infracción

* `entity` `array`
    * `name` `string`
    * `slug` `string`
* `unique_id` `string` Acta
* `description` `string` Descripción/título de la multa
* `authority` `string|null` Autoridad que registra la infracción (nullable)
* `amount` `float` Monto, en caso de pago voluntario muestra el valor. _Post-vencimiento se actualiza._
* `emitted_at` `string` Fecha de emisión de la infracción. 
* `expires_at` `string` Fecha de vencimiento
* `requires_controller` `boolean` La multa requiere controlador
* `metadata` `array` Metadata cruda de la infracción. Varía según jurisdicción.

### POST `/penalties`

Consultar infracciones

__Parámetros de solicitud__

* `domain` Dominio, patente/DNI
* `entity` Identificador de la jurisdicción

__Formato de respuesta__

* `items` Array con listado de infracciones pendientes de pago
* `total` Cantidad de items en respuesta

## Monitoring

Monitoring permite configurar webhooks que notifican nuevas infracciones.

*Se pueden implementar alertas de vencimiento a pedido*

### GET `/monitoring`

Listado de monitores activos.

__Formato de respuesta__

Un `array` de dominios

* `uuid` `string`
* `url` `string` URL del webhook
* `domain` `string` Patente/DNI
* `type` `string` Puede ser `dni` o `license_plate`
* `entities` `array`
    * `name`
    * `slug`

### POST `/monitoring`

Registrar un nuevo monitor. Se auto-detecta si es Patente/DNI

__Formato de solicitud__

* `entity` `string` Identificador de jurisdicción
* `domain` `string` Patente/DNI
* `url` `string` URL del endpoint para webhook. Incluir `http` o `https`

__Formato de respuesta__

* `uuid` `string`
* `url` `string` URL del webhook
* `domain` `string` Patente/DNI
* `type` `string` Puede ser `dni` o `license_plate`
* `entities` `array`
    * `name`
    * `slug`

### DELETE `/monitoring/{uuid}`

Eliminar un monitor.

__Formato de solicitud__

* `uuid` `string` UUID

__Formato de respuesta__

Devuelve `204` en lugar a `200`, no hay contenido en la respuesta.
