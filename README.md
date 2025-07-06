
# API Multabot

La única API para consultar multas de tránsito en Argentina.

Esta documentación está orientada a integraciones empresariales. Para solicitar acceso, visitá [https://multabot.com.ar/desarrolladores](https://multabot.com.ar/desarrolladores).

---

## Autenticación

La API utiliza autenticación por **Bearer Token** en el encabezado `Authorization`.

Los tokens se generan desde el panel de tu cuenta.

---

## Límites

Actualmente no existen límites de uso para las consultas.

---

## API Reference

### Obtener listado de jurisdicciones

```http
GET /entities
```

Devuelve el listado de jurisdicciones disponibles y el estado actual de cada servicio.

| Parámetro | Tipo | Descripción |
|----------|------|-------------|
| –        | –    | No requiere parámetros |

**Respuesta**

```json
[
  {
    "name": "CABA",
    "slug": "caba",
    "is_working": true
  }
]
```

---

### Consultar infracciones

```http
POST /penalties
```

Consulta las infracciones pendientes para una patente o DNI en una jurisdicción determinada.

| Parámetro  | Tipo     | Descripción                             |
|-----------|----------|------------------------------------------|
| `domain`  | `string` | **Requerido.** Patente o DNI a consultar |
| `entity`  | `string` | **Requerido.** Slug de la jurisdicción   |

**Respuesta**

```json
{
  "total": 2,
  "items": [
    {
      "entity": {
        "name": "CABA",
        "slug": "caba"
      },
      "unique_id": "Q31189527",
      "description": "Exceso de velocidad leve",
      "authority": "GCBA",
      "amount": 12345.67,
      "emitted_at": "2025-01-22 14:30",
      "expires_at": "2025-02-15",
      "requires_controller": false,
      "metadata": { ... }
    }
  ]
}
```

---

### Obtener historial de consultas realizadas

```http
GET /requests
```

Devuelve un historial de las consultas hechas a la API.

| Parámetro   | Tipo     | Descripción                                 |
|------------|----------|----------------------------------------------|
| `entity`   | `string` | (Opcional) Slug de jurisdicción consultada   |
| `domain`   | `string` | (Opcional) Patente o DNI consultado          |

**Respuesta**

```json
[
  {
    "domain": "ABC123",
    "entity": "caba",
    "status": 200,
    "timestamp": "2025-07-06T14:00:00Z"
  }
]
```

---

## Monitoreo de infracciones

Permite configurar webhooks para recibir alertas cuando se detecten nuevas infracciones.  
*Alertas de vencimiento disponibles bajo pedido.*

---

### Obtener monitores activos

```http
GET /monitoring
```

Devuelve un listado de dominios actualmente monitoreados.

**Respuesta**

```json
[
  {
    "uuid": "aabbcc-dd1122",
    "url": "https://miweb.com/webhook",
    "domain": "12345678",
    "type": "dni",
    "entities": [
      { "name": "CABA", "slug": "caba" }
    ]
  }
]
```

---

### Registrar un nuevo monitor

```http
POST /monitoring
```

| Parámetro | Tipo     | Descripción                                                        |
|----------|----------|---------------------------------------------------------------------|
| `entity` | `string` | **Requerido.** Slug de la jurisdicción                             |
| `domain` | `string` | **Requerido.** Patente o DNI                                       |
| `url`    | `string` | **Requerido.** URL del webhook receptor (`http` o `https`)         |

**Respuesta**

```json
{
  "uuid": "aabbcc-dd1122",
  "url": "https://miweb.com/webhook",
  "domain": "ABC123",
  "type": "license_plate",
  "entities": [
    { "name": "CABA", "slug": "caba" }
  ]
}
```

---

### Eliminar un monitor

```http
DELETE /monitoring/{uuid}
```

| Parámetro | Tipo     | Descripción                  |
|----------|----------|------------------------------|
| `uuid`   | `string` | **Requerido.** ID del monitor |

**Respuesta**

- `204 No Content` – Eliminación exitosa

---

## Códigos de estado

| Código | Significado                       |
|--------|-----------------------------------|
| `200`  | OK, la solicitud fue exitosa      |
| `204`  | OK, pero sin contenido disponible |
| `422`  | Error de validación               |

---

¿Querés integrar Multabot?  
Contactanos desde [https://multabot.com.ar](https://multabot.com.ar)
