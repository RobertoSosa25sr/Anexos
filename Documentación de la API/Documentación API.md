# API Documentation

## Autenticación

**Método:** `POST`  
**Ruta:** `/api/login`

**Control de acceso:**
- Acceso público (no autenticado)

**Cuerpo de solicitud:**
```json
{
    "id": "string (10 digits)",
    "password": "string",
    "role": "string (Administrator|User|Client)"
}
```

**Respuesta esperada (200 - OK):**
```json
{
    "user": {
        "id": "string",
        "first_name": "string",
        "last_name": "string",
        "full_name": "string",
        "email": "string",
        "phone": "string",
        "roles": ["string"]
    },
    "menu_items": [
        {
            "id": "integer",
            "label": "string",
            "icon": "string",
            "route": "string",
            "order": "integer",
            "actions": ["string"]
        }
    ],
    "token": "string"
}
```

---

## Cierre de sesión

**Método:** `POST`  
**Ruta:** `/api/logout`

**Control de acceso:**
- Cualquier usuario autenticado

**Cuerpo de solicitud:** No se requiere cuerpo para esta solicitud, únicamente el token de sesión.

**Respuesta esperada (200 - OK):**
```json
{
  "message": "Successfully logged out"
}
```

---

## Gestión de Usuarios

### Listar Usuarios

**Método:** `GET`  
**Ruta:** `/api/users`

**Control de acceso:**
- `Administrator`: puede ver todos los usuarios.
- `User`: puede ver solo su propia información.
- `Client`: puede ver solo información básica de usuarios con rol `User` (`id`, `first_name`, `last_name`, `full_name`).

**Parámetros de consulta:**
- `search`: término de búsqueda opcional.
- `per_page`: cantidad de elementos por página (por defecto: 10).
- `page`: número de página (por defecto: 1).
- `roles`: arreglo opcional de roles para filtrar.

**Respuesta (200 - Éxito):**
```json
{
  "users": [
    {
      "id": "string",
      "first_name": "string",
      "last_name": "string",
      "full_name": "string",
      "email": "string",
      "phone": "string",
      "profile_photo_url": "string",
      "roles": ["string"]
    }
  ],
  "pagination": {
    "total": "integer",
    "per_page": "integer",
    "current_page": "integer",
    "last_page": "integer",
    "from": "integer",
    "to": "integer"
  }
}
```

### Crear Usuario

**Método:** `POST`  
**Ruta:** `/api/users`

**Control de acceso:**
- Requiere rol `Administrator`

**Cuerpo de la solicitud:**
```json
{
  "id": "string (10 digits)",
  "first_name": "string",
  "last_name": "string",
  "email": "string",
  "phone": "string",
  "password": "string",
  "roles": ["string"]
}
```

**Respuesta (201 - Éxito):**
```json
{
  "message": "User created successfully",
  "user": {
    "id": "string",
    "first_name": "string",
    "last_name": "string",
    "full_name": "string",
    "email": "string",
    "phone": "string",
    "roles": ["string"]
  }
}
```

### Actualizar Usuario

**Método:** `PUT`  
**Ruta:** `/api/users`

**Control de acceso:**
- `Administrator`: puede actualizar cualquier usuario y sus roles.
- `User`: solo puede actualizar su propia información (sin incluir roles).

**Cuerpo de la solicitud:**
```json
{
  "id": "string (10 digits)",
  "first_name": "string",
  "last_name": "string",
  "email": "string",
  "phone": "string",
  "password": "string",
  "current_password": "string",
  "roles": ["string"]
}
```

**Reglas para actualizar contraseña:**
- `Administrator`: puede cambiar cualquier contraseña sin proporcionar `current_password`.
- `User`, `Administrator` y `Client`: deben proporcionar `current_password` para actualizar la suya.

**Respuesta (200 - Éxito):**
```json
{
  "message": "User updated successfully",
  "user": {
    "id": "string",
    "first_name": "string",
    "last_name": "string",
    "full_name": "string",
    "email": "string",
    "phone": "string",
    "roles": ["string"]
  }
}
```

### Eliminar Usuario

**Método:** `DELETE`  
**Ruta:** `/api/users`

**Control de acceso:**
- Requiere rol `Administrator`

**Cuerpo de la solicitud:**
```json
{
  "id": "string (10 digits)"
}
```

**Respuesta (200 - Éxito):**
```json
{
  "message": "User deleted successfully"
}
```

---

## Gestión de Clientes

### Listar Clientes

**Método:** `GET`  
**Ruta:** `/api/clients`

**Control de acceso:**
- `Administrator`, `User`: pueden ver todos los clientes.
- `Client`: solo puede ver su propia información.

**Parámetros de consulta:**
- `search`: término de búsqueda opcional.
- `per_page`: cantidad de elementos por página (por defecto: 10).
- `page`: número de página (por defecto: 1).

**Respuesta esperada:**
```json
{
  "clients": [
    {
      "id": "string",
      "first_name": "string",
      "last_name": "string",
      "full_name": "string",
      "email": "string",
      "phone": "string",
      "profile_photo_url": "string",
      "roles": ["Client"]
    }
  ],
  "pagination": {
    "total": "integer",
    "per_page": "integer",
    "current_page": "integer",
    "last_page": "integer",
    "from": "integer",
    "to": "integer"
  }
}
```

### Crear Cliente

**Método:** `POST`  
**Ruta:** `/api/clients`

**Control de acceso:**
- Requiere rol `Administrator` o `User`

**Cuerpo de la solicitud:**
```json
{
  "id": "string (10 digits)",
  "first_name": "string",
  "last_name": "string",
  "password": "string (optional, min: 6 characters)",
  "email": "string (required)",
  "phone": "string (required, 10 digits)"
}
```

**Comportamiento:**
- Si el usuario ya existe se añade el rol `Client` y se retorna código `200`.
- Si no existe se crea el usuario con rol `Client`, si no se proporciona contraseña, se genera una aleatoria y se retorna código `201`.

**Respuesta esperada:**
```json
{
  "message": "Client created successfully" | "Client role added to existing user successfully",
  "client": {
    "id": "string",
    "first_name": "string",
    "last_name": "string",
    "full_name": "string",
    "email": "string",
    "phone": "string",
    "roles": ["Client"]
  }
}
```

### Actualizar Cliente

**Método:** `PUT`  
**Ruta:** `/api/clients`

**Control de acceso:**
- Requiere rol `Administrator` o `User` para actualizar un cliente.
- Un `Cliente` solo puede actualizar su propia información.

**Cuerpo de la solicitud:**
```json
{
  "id": "string (10 digits)",
  "email": "string (optional)",
  "phone": "string (optional, 10 digits)"
}
```

**Reglas de validación:**
- El campo `email`, si se proporciona, debe ser único.
- El campo `phone`, si se proporciona, debe ser único.
- El usuario debe tener el rol `Client`.

**Respuesta esperada:**
```json
{
  "message": "Client updated successfully",
  "client": {
    "id": "string",
    "first_name": "string",
    "last_name": "string",
    "full_name": "string",
    "email": "string",
    "phone": "string",
    "roles": ["Client"]
  }
}
```

### Eliminar Cliente

**Método:** `DELETE`  
**Ruta:** `/api/clients`

**Control de acceso:**
- Requiere rol `Administrator` o `User`

**Cuerpo de la solicitud:**
```json
{
  "id": "string (10 digits)"
}
```

**Comportamiento:**
- Se remueve el rol `Client` del usuario.
- El usuario permanece en el sistema con sus otros roles.

**Respuesta esperada:**
```json
{
  "message": "Client role removed successfully",
  "user": {
    "id": "string",
    "first_name": "string",
    "last_name": "string",
    "full_name": "string",
    "email": "string",
    "phone": "string",
    "roles": []
  }
}
```

---

## Gestión de Casos

### Listar Casos

**Método:** `GET`  
**Ruta:** `/api/cases`

**Control de acceso:**
- `Administrator`: puede ver todos los casos.
- `User`: puede ver casos donde es encargado o responsable de citas.
- `Client`: puede ver únicamente sus propios casos no cancelados.

**Parámetros de consulta:**
- `search`: Término de búsqueda opcional (en ID del caso, nombre/email del cliente o encargado).
- `per_page`: Cantidad de elementos por página (por defecto: 10).
- `page`: Número de página (por defecto: 1).
- `status`: Arreglo opcional de estados (`En progreso`, `Cerrado`, `Abierto`).
- `manager_id`: Filtro opcional por ID de encargado (solo para `Administrator`).
- `client_id`: Filtro opcional por ID de cliente.

**Respuesta (200 - Éxito):**
```json
{
  "cases": [
    {
      "id": "string",
      "manager": {
        "id": "string",
        "first_name": "string",
        "last_name": "string",
        "email": "string",
        "phone": "string"
      },
      "client": {
        "id": "string",
        "first_name": "string",
        "last_name": "string",
        "email": "string",
        "phone": "string"
      },
      "status": "string",
      "created_at": "datetime",
      "updated_at": "datetime",
      "appointments": [
        {
          "id": "integer",
          "subject": "string",
          "start_datetime": "datetime",
          "duration_hours": "integer",
          "duration_minutes": "integer",
          "status": "string"
        }
      ]
    }
  ],
  "pagination": {
    "total": "integer",
    "per_page": "integer",
    "current_page": "integer",
    "last_page": "integer",
    "from": "integer",
    "to": "integer"
  }
}
```

### Obtener Caso

Devuelve información adicional sobre las citas relacionas al caso y el resultado de estas.

**Método:** `GET`  
**Ruta:** `/api/cases/{id}`

**Control de acceso:**
- `Administrator`: puede ver cualquier caso.
- `User`: puede ver casos donde es encargado o responsable de citas.
- `Client`: puede ver únicamente sus propios casos.

**Parámetros de consulta:**
- `per_page`: Cantidad de elementos por página para citas (por defecto: 10).
- `page`: Número de página para citas (por defecto: 1).

**Respuesta (200 - Éxito):**
```json
{
  "cases": [
    {
      "id": "string",
      "manager": {
        "id": "string",
        "first_name": "string",
        "last_name": "string",
        "email": "string",
        "phone": "string"
      },
      "client": {
        "id": "string",
        "first_name": "string",
        "last_name": "string",
        "email": "string",
        "phone": "string"
      },
      "status": "string",
      "created_at": "datetime",
      "updated_at": "datetime",
      "appointments": [
        {
          "id": "integer",
          "subject": "string",
          "start_datetime": "datetime",
          "duration_hours": "integer",
          "duration_minutes": "integer",
          "status": "string"
        }
      ]
    }
  ],
  "pagination": {
    "total": "integer",
    "per_page": "integer",
    "current_page": "integer",
    "last_page": "integer",
    "from": "integer",
    "to": "integer"
  }
}
```

### Crear Caso

**Método:** `POST`  
**Ruta:** `/api/cases`

**Control de acceso:**
- `Administrator`: puede crear casos para cualquier cliente y asignar cualquier usuario como encargado.
- `User`: puede crear casos para cualquier cliente y será asignado automáticamente como encargado.
- `Client`: solo puede crear casos para sí mismo.

**Cuerpo de la solicitud:**
```json
{
  "client_id": "string (10 digits)",
  "status": "string (optional, En progreso|Cerrado|Abierto)",
  "manager_id": "string (10 digits, required for Administrator only)"
}
```

**Reglas de validación:**
- El cliente debe tener el rol `Client`.
- El encargado debe tener el rol `User`.
- Si no se especifica el estado, se asigna por defecto `Abierto`.
- El campo `manager_id` es obligatorio solo para `Administrator`.
- Un cliente solo puede crear casos para sí mismo.

**Respuesta (201 - Éxito):**
```json
{
  "case": {
    "id": "string (format: XXXINITIALSdmy)",
    "manager": {
      "id": "string",
      "first_name": "string",
      "last_name": "string",
      "email": "string",
      "phone": "string"
    },
    "client": {
      "id": "string",
      "first_name": "string",
      "last_name": "string",
      "email": "string",
      "phone": "string"
    },
    "status": "string",
    "created_at": "datetime",
    "updated_at": "datetime",
    "appointments": [
      {
        "id": "integer",
        "subject": "string",
        "start_datetime": "datetime",
        "duration_hours": "integer",
        "duration_minutes": "integer",
        "status": "string"
      }
    ]
  }
}
```

### Actualizar Caso

**Método:** `PUT`  
**Ruta:** `/api/cases/{id}`

**Control de acceso:**
- `Administrator`: puede actualizar cualquier caso y cambiar el encargado.
- `User`: puede actualizar solo casos donde es encargado o responsable de citas.
- `Client`: no puede actualizar casos.

**Cuerpo de la solicitud:**
```json
{
  "status": "string (opcional, En progreso|Cerrado|Abierto)",
  "manager_id": "string (opcional, 10 dígitos, solo para Administrator)"
}
```

**Reglas de validación:**
- Solo los administradores pueden cambiar el encargado.
- El nuevo encargado debe tener rol `User`.
- El usuario debe tener acceso al caso.

**Respuesta (200 - Éxito):**
```json
{
  "case": {
    "id": "string",
    "manager": {
      "id": "string",
      "first_name": "string",
      "last_name": "string",
      "email": "string",
      "phone": "string"
    },
    "client": {
      "id": "string",
      "first_name": "string",
      "last_name": "string",
      "email": "string",
      "phone": "string"
    },
    "status": "string",
    "created_at": "datetime",
    "updated_at": "datetime",
    "appointments": [
      {
        "id": "integer",
        "subject": "string",
        "start_datetime": "datetime",
        "duration_hours": "integer",
        "duration_minutes": "integer",
        "status": "string"
      }
    ]
  }
}
```

### Eliminar Caso

**Método:** `DELETE`  
**Ruta:** `/api/cases/{id}`

**Control de acceso:**
- `Administrator`: puede eliminar cualquier caso.
- `Client`: puede cancelar únicamente sus propios casos (cambia el estado a `Cerrado`).
- `User`: Puede eliminar casos donde es el encargado.

**Comportamiento:**
- Para `Administrator` o `User`: el caso se elimina permanentemente.
- Para `Client`: el estado del caso cambia a `Cerrado`.

**Respuesta (200 - Éxito):**
```json
{
  "status": "success",
  "message": "Case deleted successfully" | "Case cancelled successfully"
}
```

---

## Gestión de Citas

### Listar Citas

**Método:** `GET`  
**Ruta:** `/api/appointments`

**Control de acceso:**
- `Administrator`: puede ver todas las citas.
- `User`: puede ver únicamente sus propias citas.
- `Client`: puede ver únicamente sus propias citas futuras no canceladas (con al menos 10 minutos de anticipación).

**Parámetros de consulta:**
- `search`: Término de búsqueda opcional (en asunto, resultado, nombre/email de cliente o responsable).
- `per_page`: Cantidad de elementos por página (por defecto: 10).
- `page`: Número de página (por defecto: 1).
- `client_id`: Filtro opcional por ID de cliente.
- `responsible_id`: Filtro opcional por ID de responsable (solo para `Administrator`).
- `status`: Arreglo opcional de estados (`pendiente`, `cancelada`, `en progreso`, `reprogramada`, `completada`).
- `date_range`: Filtro opcional por rango de fechas:
  - `Todos`, `Hoy`, `Ayer`, `Semana pasada`, `Mes pasado`,
    `Mañana`, `Esta semana`, `Siguiente semana`, `Este mes`, `Siguiente mes`, `DD/MM/YYYY`.

**Respuesta (200 - Éxito):**
```json
{
  "appointments": [
    {
      "id": "integer",
      "ticket_number": "integer",
      "responsible_id": "string",
      "client_id": "string",
      "case_id": "string",
      "subject": "string",
      "start_datetime": "datetime",
      "duration": "time",
      "status": "string",
      "result": "string",
      "created_at": "datetime",
      "updated_at": "datetime",
      "client": {
        "id": "string",
        "first_name": "string",
        "last_name": "string",
        "email": "string",
        "phone": "string"
      },
      "responsible": {
        "id": "string",
        "first_name": "string",
        "last_name": "string",
        "email": "string",
        "phone": "string"
      },
      "case": {
        "id": "string",
        "manager_id": "string",
        "client_id": "string",
        "status": "string"
      }
    }
  ],
  "pagination": {
    "total": "integer",
    "per_page": "integer",
    "current_page": "integer",
    "last_page": "integer",
    "from": "integer",
    "to": "integer"
  }
}
```

### Crear Cita

**Método:** `POST`  
**Ruta:** `/api/appointments`

**Control de acceso:**
- `Administrator`: puede crear citas para cualquier cliente y asignar a cualquier `User` como responsable.
- `User`: puede crear citas para cualquier cliente.
- `Client`: Puede crear citas para si mismo y seleccionar a un `User`.

**Cuerpo de solicitud:**
```json
{
  "responsible_id": "string (10 digits)",
  "client_id": "string (10 digits)",
  "case_id": "string (12 digits, optional)",
  "subject": "string",
  "start_datetime": "datetime",
  "duration": "time (HH:mm:ss)",
  "status": "string (pendiente|cancelada|en progreso|reprogramada|completada)",
  "result": "string (optional)"
}
```

**Reglas de validación:**
- El responsable debe tener el rol `User`.
- El cliente debe tener el rol `Client`.
- Si se proporciona `case_id`, debe pertenecer al cliente.
- Todos los campos son obligatorios excepto `case_id` y `result`.

**Respuesta esperada (201 - Creado):**
```json
{
  "appointment": {
    "id": "integer",
    "ticket_number": "integer",
    "responsible_id": "string",
    "client_id": "string",
    "case_id": "string",
    "subject": "string",
    "start_datetime": "datetime",
    "duration": "time",
    "status": "string",
    "result": "string",
    "created_at": "datetime",
    "updated_at": "datetime",
    "client": {
      "id": "string",
      "first_name": "string",
      "last_name": "string",
      "email": "string",
      "phone": "string"
    },
    "responsible": {
      "id": "string",
      "first_name": "string",
      "last_name": "string",
      "email": "string",
      "phone": "string"
    },
    "case": {
      "id": "string",
      "manager_id": "string",
      "client_id": "string",
      "status": "string"
    }
  }
}
```

### Actualizar Cita

**Método:** `PUT`  
**Ruta:** `/api/appointments/{id}`

**Control de acceso:**
- `Administrator`: puede actualizar cualquier cita.
- `User`: puede actualizar únicamente sus propias citas.
- `Client`: no puede actualizar citas.

**Cuerpo de solicitud:**
```json
{
  "responsible_id": "string (opcional, 10 dígitos)",
  "subject": "string (opcional)",
  "start_datetime": "datetime (opcional)",
  "duration": "time (opcional, HH:mm:ss)",
  "status": "string (opcional, pendiente|cancelada|reprogramada)",
  "result": "string (opcional)"
}
```

**Reglas de validación:**
- Si se proporciona `responsible_id`, debe tener rol `User`.
- El usuario debe tener acceso a la cita.

**Respuesta (200 - Éxito):**
```json
{
  "appointment": {
    "id": "integer",
    "ticket_number": "integer",
    "responsible_id": "string",
    "client_id": "string",
    "case_id": "string",
    "subject": "string",
    "start_datetime": "datetime",
    "duration": "time",
    "status": "string",
    "result": "string",
    "created_at": "datetime",
    "updated_at": "datetime",
    "client": {
      "id": "string",
      "first_name": "string",
      "last_name": "string",
      "email": "string",
      "phone": "string"
    },
    "responsible": {
      "id": "string",
      "first_name": "string",
      "last_name": "string",
      "email": "string",
      "phone": "string"
    },
    "case": {
      "id": "string",
      "manager_id": "string",
      "client_id": "string",
      "status": "string"
    }
  }
}
```

### Eliminar Cita

**Método:** `DELETE`  
**Ruta:** `/api/appointments/{id}`

**Control de acceso:**
- `Administrator`: puede eliminar cualquier cita.
- `User`: puede eliminar únicamente sus propias citas.
- `Client`: solo puede cancelar sus propias citas (el estado cambia a `"cancelada"`).

**Comportamiento:**
- Administradores y Usuarios: la cita se elimina permanentemente.
- Clientes: la cita no se elimina, solo se cambia el estado a `cancelada` y el resultado a `Cancelada`.

**Respuesta (200 - Éxito):**
```json
{
  "status": "success",
  "message": "Appointment deleted successfully" | "Appointment cancelled successfully"
}
```

---

## Reports

Los endpoints bajo **Reports** requieren el rol de `Administrator` y proporcionan estadísticas detalladas sobre los usuarios y sus actividades.

**Método:** `GET`  
**Ruta:** `/api/reports`

**Control de acceso:**
- `Administrator`: requerido
- Otros roles recibirán respuesta `403 Forbidden`

**Parámetros de consulta:**
- `search`: término de búsqueda opcional (ID, nombre, apellido o email del usuario)
- `per_page`: cantidad de ítems por página (por defecto: 10)
- `page`: número de página (por defecto: 1)
- `appointment_status`: arreglo opcional de estados de citas (`pendiente|cancelada|en progreso|reprogramada`)
- `case_status`: arreglo opcional de estados de casos (`Abierto|Cerrado|En progreso`)
- `date_range`: filtro de fechas opcional. Valores posibles:
  - `Todos`, `Hoy`, `Ayer`, `Semana pasada`, `Mes pasado`,
    `Mañana`, `Esta semana`, `Siguiente semana`,
    `Este mes`, `Siguiente mes`, `DD/MM/YYYY`

**Respuesta esperada (200 - OK):**
```json
{
  "reports": [
    {
      "user": {
        "id": "string",
        "full_name": "string"
      },
      "appointments": [
        {
          "id": "integer",
          "subject": "string",
          "start_datetime": "datetime",
          "status": "string",
          "client": {
            "id": "string",
            "full_name": "string"
          },
          "case": {
            "id": "string",
            "status": "string"
          }
        }
      ],
      "statistics": {
        "total_appointments": "integer",
        "total_clients": "integer",
        "total_cases": {
          "total": "integer",
          "closed": "integer",
          "open": "integer",
          "in_progress": "integer"
        }
      }
    }
  ],
  "pagination": {
    "total": "integer",
    "per_page": "integer",
    "current_page": "integer",
    "last_page": "integer",
    "from": "integer",
    "to": "integer"
  }
}
```

**Detalles de la estadística:**
- Solo se incluyen usuarios con el rol `User`.
- Las citas se filtran por:
  - Estado (si se provee `appointment_status`)
  - Estado del caso (si se provee `case_status`)
  - Rango de fecha (si se provee `date_range`)
- Los casos se filtran por `case_status`, si es provisto.
- **Clientes totales:** número de clientes únicos con citas.
- **Estadísticas de casos:**
  - Total de casos
  - Casos cerrados
  - Casos abiertos
  - Casos en progreso