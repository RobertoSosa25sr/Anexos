# Planificación de Sprints y Requerimientos de Plataforma

## Sprint 1 - Acceso a la plataforma

Durante este sprint se llevará a cabo la configuración inicial del proyecto, tanto para el *backend* como para el *frontend*. Por esta razón, se priorizará una única historia de usuario, la cual, tras ser refinada, se describe a continuación:

**HU01 - Acceso a la plataforma**  
*Como miembro de la organización, quiero ingresar a la plataforma con mi número de cédula, contraseña y rol, para desempeñar mis funciones y visualizar la información acorde al rol con el que accedí.*

### Backend
- **Crear la base de datos en PostgreSQL:**
  - Crear el proyecto utilizando Laravel y modificar las migraciones por defecto para establecer el número de cédula como clave primaria de la tabla de usuarios.
  - Reemplazar el campo `name` por los campos `first_name`, `last_name` y `full_name`.
  - Crear las tablas necesarias para la gestión de roles y control de acceso:
    - `roles`: contendrá los roles disponibles en el sistema, incluyendo *Administrador*, *Usuario* y *Cliente*.
    - `menu_items`: almacenará la información de los distintos menús disponibles en la interfaz, tales como *Inicio*, *Reportes*, *Citas*, *Casos*, *Clientes* y *Usuarios*.
    - `menu_item_role`: relacionará los elementos del menú con los roles, definiendo así las rutas y funcionalidades disponibles para cada tipo de usuario.
- **Conectar el backend con la base de datos:**
  - Configurar el archivo `.env` para establecer la conexión con la base de datos en el entorno local.
- **Crear el servicio de inicio de sesión:**
  - Desarrollar un servicio de autenticación que utilice la ruta `/api/login` con el método `POST`. Este servicio deberá recibir como parámetros de entrada el número de cédula, la contraseña y el rol del usuario. En la respuesta se incluirá la información del usuario autenticado, los menús a los que tiene acceso según su rol, y un token de autenticación que permita validar futuras solicitudes.
- **Crear el servicio de cierre de sesión:**
  - Implementar un servicio que permita cerrar sesión mediante la ruta `/api/logout` utilizando el método `POST`. Este servicio deberá eliminar el token de autenticación previamente generado, el cual será proporcionado en la solicitud.

### Frontend
- **Crear la página de inicio de sesión:**
  - Diseñar e implementar la interfaz de inicio de sesión. Además, desarrollar el servicio `auth`, encargado de consumir las APIs de inicio y cierre de sesión.
- **Desarrollar componentes reutilizables:**
  - `input-field`: componente configurable para campos de entrada como cédula, contraseña y selector de rol.
  - `Button`: componente parametrizable para definir estilos y comportamientos según el diseño.
  - `form-container`: componente que agrupará los `input-field` y enviará la información al servicio de inicio de sesión.
- **Crear vistas principales según el rol:**
  - Desarrollar el componente `side-menu`, el cual deberá mostrar las opciones de navegación correspondientes al rol del usuario autenticado. Este menú incluirá botones de *Configuración* y *Cerrar sesión*.
- **Integrar el formulario con el servicio de inicio de sesión:**
  - Configurar el componente `form-container` para enviar correctamente los datos capturados por los `input-field` a través del `Button`, consumiendo el servicio de autenticación.
- **Crear la interfaz de cierre de sesión:**
  - Implementar un componente `modal` que mostrará un mensaje de advertencia antes de ejecutar la acción de cierre de sesión, permitiendo al usuario confirmar o cancelar la operación.
  - Desarrollar un servicio y un componente denominado `notification`, cuyo propósito será mostrar mensajes de error u otras notificaciones relevantes al usuario durante el proceso de autenticación o en caso de fallos en la comunicación con los servicios.

#### Criterios de aceptación
- Cuando el usuario ingresa sus credenciales correctamente (número de cédula, contraseña y rol), debe iniciar sesión exitosamente y visualizar únicamente los ítems de menú correspondientes a su rol.
- Si el usuario introduce credenciales incorrectas, el sistema debe mostrar un mensaje de error indicando que las credenciales no son válidas.
- Si el usuario intenta iniciar sesión sin completar todos los campos requeridos, el sistema debe mostrar un mensaje de advertencia indicando que hay campos obligatorios sin llenar.
- Si el usuario intenta iniciar sesión con un rol que no tiene asignado, el sistema debe mostrar un mensaje de error que indique que el usuario no dispone de dicho rol.
- Al cerrar sesión, el token de autenticación debe ser eliminado tanto de la base de datos como del almacenamiento del navegador.
- Si el usuario no dispone de un token válido, debe ser redirigido automáticamente a la página de inicio de sesión.

---

## Sprint 2 - Administración de Usuarios

Durante este sprint se desarrollarán los componentes fundamentales para la administración de usuarios, los cuales serán reutilizados en las vistas de gestión de otras entidades en los siguientes sprints.

### Historia de usuario HU02 - Administración de usuarios
*Como administrador, quiero registrar nuevos usuarios, gestionar sus roles y modificar sus contraseñas, para controlar el acceso a la plataforma de acuerdo con sus funciones.*

### Tareas técnicas
- **Crear el servicio CRUD para usuarios:**
  - Implementar un servicio RESTful en la ruta `/api/users` que soporte los métodos `POST`, `PUT`, `DELETE` y `GET` para realizar operaciones de creación, actualización, eliminación y listado de usuarios. El acceso a estos métodos estará restringido exclusivamente a usuarios con rol de *Administrador*.
  - El servicio de listado deberá incluir paginación, utilizando el siguiente modelo de solicitud:

    ```json
    {
        "per_page": 10,
        "page": 1
    }
    ```

  - Para actualizar un usuario (solo contraseña o roles):
    ```json
    {
        "id": "0123456789",
        "password": "admin123",
        "roles": ["Administrator", "User"]
    }
    ```
  - Para eliminar un usuario:
    ```json
    {
        "id": "0123456789"
    }
    ```
  - Para crear un nuevo usuario:
    ```json
    {
        "id": "0502886252",
        "first_name": "Roberto Ricardo",
        "last_name": "Sosa Salazar",
        "password": "admin123",
        "roles": ["User", "Administrator"]
    }
    ```
    El campo `email` es opcional.
- **Crear la vista de administración de usuarios:**
  - Actualizar la tabla `menu_items` para incluir un campo `actions`, el cual definirá las acciones disponibles sobre cada registro de acuerdo con el menú y el rol del usuario autenticado.
- **Crear el componente `data-table`:**
  - Este componente presentará la información de los usuarios y mostrará dinámicamente las acciones disponibles según la configuración obtenida del campo `actions`.
- **Crear el servicio `user`:**
  - Este servicio permitirá consumir los endpoints del API para usuarios desde el frontend.
- **Integrar formularios con componentes modales:**
  - Adaptar el componente `modal` para aceptar en su interior el componente `form-container`, permitiendo el envío de datos al backend mediante el servicio `user`.
- **Agregar funcionalidad de creación de usuarios:**
  - Incorporar un botón que permita abrir un modal con el formulario de creación de usuario. Al completarse correctamente, el nuevo registro deberá reflejarse inmediatamente en el `data-table`.
- **Agregar funcionalidad de edición y eliminación de usuarios:**
  - Agregar modales específicos para la edición (limitada a contraseña y roles) y la eliminación de registros.
- **Actualizar el componente `input-field`:**
  - Mejorar el componente para que:
    - Muestre un *label* descriptivo.
    - Marque los campos obligatorios con un asterisco.
    - Deshabilite los campos no editables (como el número de cédula o nombres).

#### Criterios de aceptación
- Los usuarios pueden ser creados, editados o eliminados correctamente a través del formulario correspondiente.
- Al editar un usuario, los campos de nombres y número de cédula no deben ser editables.
- Las modificaciones realizadas a la contraseña o roles de un usuario deben reflejarse tanto en la base de datos como en el componente `data-table`.
- La eliminación de un usuario debe impactar de manera inmediata tanto en el `data-table` como en la base de datos.
- Cuando exista un número de registros mayor al definido por página, se deberá mostrar un componente de paginación funcional.

---

## Sprint 3 - Administración de Clientes y Agendamiento de Citas

Durante este sprint se trabajará en la administración de clientes y el desarrollo de funcionalidades básicas para el agendamiento de citas. Aunque los clientes son técnicamente usuarios con el rol *Cliente*, se desarrollarán servicios específicos para su gestión con el fin de separar responsabilidades y evitar conflictos en los endpoints utilizados exclusivamente por el rol *Administrador*.

### Historia de usuario HU05 - Administración de Clientes
*Como administrador, quiero gestionar la información de los clientes para mantener un registro actualizado y completo de los mismos.*

### Tareas técnicas
- **Crear el servicio CRUD para clientes:**
  - Desarrollar un servicio RESTful en la ruta `/api/clients` que soporte los métodos `POST`, `PUT`, `DELETE` y `GET`, permitiendo la creación, edición, eliminación y listado de clientes. Estos endpoints estarán disponibles exclusivamente para usuarios con rol *Administrador* o *Usuario*.
  - El listado de clientes incluirá soporte para paginación utilizando el mismo modelo que en los sprints anteriores.
  - Para actualizar la información de un cliente:
    ```json
    {
        "id": "4444444444",
        "phone": "1234567890",
        "email": "client@email.com"
    }
    ```
  - Para eliminar un cliente:
    ```json
    {
        "id": "0123456789"
    }
    ```
    En este caso, la eliminación consistirá únicamente en remover el rol de `Cliente` del usuario, sin borrar completamente su registro de la base de datos.
  - Para registrar un nuevo cliente:
    ```json
    {
        "id": "9876543210",
        "first_name": "Client",
        "last_name": "One",
        "email": "client@gmail.com",
        "phone": "0987654321"
    }
    ```
    Este endpoint generará un nuevo usuario con el rol exclusivo de `Cliente`. En este caso, tanto `email` como `phone` serán campos obligatorios.
- **Crear la vista de administración de clientes:**
  - Utilizar el componente `data-table` previamente desarrollado para listar a los clientes. Además, crear modelos en el frontend para las entidades `User` y `Client` que permitan diferenciarlos correctamente. También desarrollar los modelos auxiliares `ApiResponse`, `FormConfig` y `Notification` para estandarizar la configuración de formularios, el manejo de respuestas de la API y la gestión de notificaciones en el sistema.
- **Desarrollar la interfaz para actualizar datos de un cliente:**
  - Actualizar el componente `form-container` para permitir la validación de campos modificados. Solo en caso de que se detecten cambios, se habilitará el envío del formulario.
- **Desarrollar la interfaz para eliminar datos de un cliente:**
  - Agregar funcionalidad para eliminar el rol `Cliente` de un usuario mediante la interfaz.

#### Criterios de aceptación
- Los clientes podrán ser creados, editados o eliminados (su rol como cliente) correctamente mediante el formulario correspondiente en la interfaz de administración de clientes.
- Durante la edición de un cliente, únicamente los campos `email` y `phone` estarán habilitados para su modificación.
- Cualquier modificación realizada sobre un cliente deberá reflejarse tanto en la base de datos como en la interfaz.
- El proceso de eliminación de un cliente consistirá únicamente en eliminar su rol como `Cliente` en la base de datos, sin afectar el registro principal del usuario.

### Historia de usuario HU06 - Administración de Citas
*Como usuario con rol de Cliente o Usuario, quiero agendar, consultar, actualizar o cancelar citas, de manera que pueda gestionar adecuadamente la atención brindada.*

### Tareas técnicas
- **Crear el servicio CRUD para citas:**
  - Desarrollar un servicio RESTful en la ruta `/api/appointments` que implemente los métodos `POST`, `PUT`, `DELETE` y `GET` para la creación, actualización, eliminación y consulta de citas. El servicio incluirá soporte para paginación, siguiendo el mismo modelo de solicitud empleado anteriormente.
  - Para el modelo de datos, se deberán crear las siguientes entidades:
    - **Case:** Se deberá generar un identificador único que siga el patrón `[NNN][AA][DDMMAA]`, donde:
      - `NNN` representa un número secuencial por cliente, comenzando en `001`.
      - `AA` corresponde a las iniciales del cliente.
      - `DDMMAA` indica la fecha de creación del caso.
    - **Appointment:** El modelo de citas deberá incluir los siguientes campos:
      - `ticket_number`: número de cita generado secuencialmente (1--99).
      - `responsible_id`: referencia al ID del usuario con rol *User*.
      - `client_id`: referencia al ID del usuario con rol *Cliente*.
      - `case_id`: identificador del caso asociado.
      - `subject`: asunto de la cita.
      - `start_datetime`: fecha y hora de inicio.
      - `time`: duración estimada de la cita.
      - `status`: estado de la cita (*pendiente*, *completada*, *en progreso*, *cancelada*, *reprogramada*).
      - `result`: descripción de lo alcanzado durante la cita.
    - Para crear una cita, los campos obligatorios son: `case_id`, `responsible_id` y `client_id`.
- **Desarrollar la interfaz para agendar una cita:**
  - Actualizar el componente `input-field` para permitir la búsqueda dinámica de usuarios a través de los servicios existentes. Para ello, se implementará un nuevo componente denominado `search-bar`, que actuará como buscador reutilizable.
  - Agregar un campo `search` en las solicitudes, lo que implicará la actualización de los servicios ya creados y futuros para admitir este filtro. La estructura esperada del cuerpo de la solicitud será:
    ```json
    {
      "search": "criterio de búsqueda",
      "per_page": 10,
      "page": 1
    }
    ```
- **Desarrollar la interfaz para actualizar una cita:**
  - Permitir la edición de los campos modificables: `start_datetime` (fecha y hora), `subject` (asunto), `time` (duración), `responsible_id` (únicamente si el usuario tiene rol de Administrador) y `result` (resultado de la cita). El formulario debe validar que la cita no se encuentre en estado *completada* o *cancelada* antes de habilitar su edición.
- **Desarrollar la interfaz para eliminar una cita:**
  - Implementar un componente `modal` que muestre una advertencia antes de confirmar la eliminación de la cita. Si la acción es realizada por un usuario con rol `Administrador` o `Usuario`, la cita será eliminada de forma lógica o definitiva según las reglas del sistema.

#### Criterios de aceptación
- Las citas pueden ser creadas, editadas o eliminadas correctamente desde la interfaz correspondiente.
- Los datos modificados se reflejan tanto en la base de datos como en el componente `data-table`.
- El número de cita `ticket_number` se genera automáticamente respetando la secuencia 1--99 por día o por caso (según la lógica implementada).
- Cuando se superan los registros por página, se activa el sistema de paginación para navegar entre los resultados.
- La interfaz de agendamiento de citas permite buscar usuarios dinámicamente mediante el componente `search-bar`, el cual realiza consultas a la API utilizando el parámetro `search` como filtro.
- Los servicios actualizados responden correctamente al incluir el parámetro `search` junto con `per_page` y `page`, retornando resultados paginados y filtrados según el texto ingresado, el cual puede coincidir con cualquiera de los campos relevantes como nombres, ID, nombre del caso, entre otros.

---

## Sprint 4 - Administración de Casos

Durante este sprint se trabajará en conectar los clientes, usuarios y citas con un caso, para completar las funcionalidades principales del sistema.

### Historia de usuario HU07 - Administración de Casos
*Como administrador, quiero gestionar los casos de la organización para trazar y culminar el proceso de asesoría legal.*

### Tareas técnicas
- **Crear el servicio CRUD para Casos:**
  - Siguiendo el mismo patrón de solicitud en el endpoint `/api/cases`, se desarrollará un método `GET` adicional que permita obtener un caso específico utilizando la ruta `/api/cases/{id_caso}`. Este servicio devolverá únicamente el caso consultado junto con la lista de citas relacionadas. La paginación, en este caso, aplicará exclusivamente a las citas del caso.
- **Crear la vista de Casos para el administrador:**
  - La vista de Casos seguirá el mismo patrón de las demás pantallas. Sin embargo, dentro de las acciones disponibles para cada registro, existirá la opción de visualizar un caso en detalle. Al seleccionarla, se mostrará un componente `data-table` adicional con la información de las citas relacionadas al caso.
  - Durante este proceso:
    - El registro del caso no podrá ser modificado.
    - Tampoco será posible modificar los registros de las citas asociadas.
    - Solo se permitirá la visualización de los datos.
    - Se podrá regresar a la vista anterior mediante un botón.
- **Desarrollar la interfaz para crear un caso:**
  - Durante la creación de un caso, será necesario seleccionar al cliente y al usuario responsable mediante su nombre completo, utilizando un campo de búsqueda.
- **Desarrollar la interfaz para editar la información de un caso:**
  - Solo el campo correspondiente al encargado del caso podrá ser modificado.
- **Desarrollar la interfaz para eliminar un caso:**
  - Se implementará la opción de eliminación del caso con su respectiva confirmación previa.

#### Criterios de aceptación
- Los casos pueden ser creados, editados o eliminados correctamente mediante la interfaz del administrador.
- Al visualizar un caso, se muestra correctamente la información detallada del mismo junto con sus citas asociadas, utilizando un componente `data-table`.
- Durante la vista detallada, tanto el caso como las citas solo se pueden consultar, no editar.
- La edición de un caso permite únicamente cambiar al responsable asignado.
- La eliminación de un caso se realiza con confirmación previa y refleja los cambios en la base de datos.

---

## Sprint 5 - Reportes

Durante este sprint se trabajará en la creación de los servicios que permitan obtener un resumen del estado de la carga de trabajo de cada uno de los usuarios del sistema, dirigido al rol *Administrador*.

### Historia de usuario HU08 - Reportes
*Como administrador, quiero visualizar la información de citas, clientes y casos de cada usuario, así como un resumen del total de estos a su cargo, para tener una visión clara de la carga de trabajo de cada uno.*

### Tareas técnicas
- **Crear el servicio de reportes:**
  - Desarrollar un servicio en la ruta `/api/reports`, al cual únicamente tendrán acceso los usuarios con el rol *Administrador*. Siguiendo el patrón de solicitudes previamente implementado, este servicio contará con soporte para paginación.
  - El servicio deberá retornar, por cada usuario del sistema:
    - Las citas asignadas (las cuales incluirán el caso correspondiente).
    - Las estadísticas generales:
      - Total de casos asignados.
      - Casos por estado: *abiertos*, *cerrados*, *en progreso*.
      - Total de citas.
      - Total de clientes (aquellos que pertenezcan a un caso en el que el usuario es el encargado).
- **Crear la interfaz de reportes para el administrador:**
  - La interfaz seguirá un diseño similar al de las pantallas anteriores, pero no contará con acciones sobre los registros, ya que su función será exclusivamente de visualización.
  - Esta vista deberá incluir:
    - Un `checkbox` para alternar entre:
      - Vista detallada: muestra el usuario, las citas asignadas, los clientes relacionados a esas citas, y el caso correspondiente.
      - Vista resumen: muestra únicamente totales por usuario (total de casos, total de citas y total de clientes).
    - En la vista resumen, los casos deberán mostrar su distribución por estado: *abiertos*, *cerrados*, y *en progreso*.
    - En la vista detallada, cada cita y caso incluirá un recuadro de color que indique visualmente su estado actual.

#### Criterios de aceptación
- Solo los usuarios con rol `Administrador` pueden acceder a los reportes.
- El servicio de reportes devuelve correctamente las citas, casos y clientes asignados a cada usuario.
- Las estadísticas por usuario reflejan con precisión el número total de casos, su estado, citas y clientes.
- La interfaz permite alternar entre vista detallada y vista resumen mediante un `checkbox`.
- En la vista detallada, los estados de los casos y citas se representan con colores diferenciados mediante recuadros.
- La información está paginada correctamente según el diseño común del sistema.

---

## Sprint 6 - Casos y Agendamiento de Citas para los Usuarios

Durante este sprint se trabajará en los roles *User* y *Cliente*, y cómo estos visualizan y modifican la información según su rol.

### Historia de usuario HU10 - Casos Asignados
*Como usuario, quiero gestionar mis casos o abrir uno nuevo de ser necesario para llevar un control, actualizar su progreso y asegurar que se aborden de manera oportuna.*

### Tareas técnicas
- **Crear o modificar los servicios de casos para el rol `User`:**
  - Un usuario con rol `User`, al consultar la lista de usuarios para la creación de un caso, solo podrá visualizar su propio registro. La vista de casos mostrará exclusivamente aquellos casos pertenecientes al usuario que realiza la consulta.
- **Crear los servicios de casos para el rol `Cliente`:**
  - Un usuario con rol `Cliente`, al consultar la lista de clientes para la creación de un caso, solo podrá visualizar su propio registro. La vista de casos mostrará exclusivamente los casos del cliente que se encuentren en un estado diferente de `Cerrado`. El cliente únicamente podrá crear o eliminar casos; al eliminarlos, simplemente se cambiará su estado a `Cerrado`.

### Historia de usuario HU11 - Agendamiento de Citas
*Como usuario, quiero agendar, modificar o cancelar citas relacionadas a mis casos para gestionar de manera flexible mi agenda y asegurar que las citas se ajusten tanto a la disponibilidad de los clientes como a la mía.*

### Tareas técnicas
- **Crear o modificar los servicios de citas para el rol `User`:**
  - Durante el agendamiento, los formularios solo deben mostrar usuarios y casos que correspondan al contexto del usuario autenticado con rol `User`.
- **Crear los servicios de citas para el rol `Cliente`:**
  - El cliente podrá visualizar únicamente las citas que no se encuentren en estado `Cancelada`. El cliente solo podrá crear o eliminar (cancelar) citas. Al eliminar una cita, su estado será actualizado a `Cancelada`.

#### Criterios de aceptación
- Los usuarios con rol `User` solo pueden ver, crear y gestionar casos propios.
- Los clientes solo pueden ver y crear sus propios casos, y únicamente si no están cerrados.
- Al eliminar un caso desde el rol `Cliente`, su estado se actualiza a `Cerrado`.
- Durante el agendamiento de citas, los formularios presentan solo los datos del contexto del usuario autenticado.
- Los clientes no visualizan citas en estado `Cancelada` y al eliminar una cita, su estado cambia correctamente a `Cancelada`.

---

## Sprint 7 - Filtros y Búsqueda

Durante este sprint se agregará la funcionalidad de búsqueda y filtro en las diferentes páginas del sistema.

### HU09 - Información de Clientes

**Como** usuario, **quiero** poder modificar la información de contacto de los clientes **para** mantener los datos actualizados y asegurar la comunicación con ellos.

#### Tareas técnicas
- **Actualizar el servicio de clientes para el rol `User`:**
  - Permitir a este rol visualizar, editar o eliminar (de manera lógica, eliminando el rol `Client`) un registro.

#### Criterios de aceptación
- Los usuarios con rol `User` pueden acceder a la información de los clientes.
- Se permite la edición de los campos de contacto del cliente.
- Al eliminar un cliente, su rol `Client` se elimina sin borrar el registro en la base de datos.

### HU02 - Actualización de Contraseña

**Como** usuario de la plataforma, **quiero** poder cambiar la contraseña actual de mi cuenta por una nueva **para** mantener la seguridad de la misma.

#### Tareas técnicas
- **Crear el servicio de actualización de contraseña:**
  - El servicio debe verificar que la contraseña actual coincida con la registrada en la base de datos y que la nueva contraseña coincida con su repetición.
- **Crear la interfaz de actualización de contraseña:**
  - El botón `Configuración` del `side-menu` debe mostrar un `modal` con un `form-container` que solicite:
    - Contraseña actual del usuario autenticado.
    - Nueva contraseña.
    - Confirmación de la nueva contraseña.

#### Criterios de aceptación
- El servicio valida correctamente la contraseña actual.
- La nueva contraseña y su confirmación deben coincidir.
- Se proporciona retroalimentación clara al usuario en caso de error o éxito.

### HU04 - Aplicación de Filtros

**Crear el componente `filter-bar`:**  
Este componente utilizará los ya existentes `search-bar` e `input-field`, además de un nuevo filtro enviado como `range_date`, que puede contener:

- Una fecha específica en formato `dd/mm/aaaa`.
- Rangos predefinidos como: *Hoy*, *Ayer*, *Mañana*, *Este mes*, etc.

Por defecto, se mostrará la opción *Todos*.

**Filtros aplicados por pantalla:**
- **Usuarios:** campo `search` y por `rol` (*Client*, *Administrator*, *User*, *Sin rol*).
- **Clientes:** únicamente por campo `search`.
- **Casos:** campo `search` y estado del caso (*Abierto*, *Cerrado*, *En progreso*).
- **Citas:** campo `search`, estado (*Pendiente*, *Cancelada*, *En progreso*, *Reprogramada*) y `range_date`.
- **Reportes:** estado de cita, estado de caso y `range_date`.

**Actualizar los servicios para incluir los filtros:**
- Servicio de usuarios: incluir un arreglo de roles a filtrar.
- Servicio de casos: incluir el campo de estado a filtrar.
- Servicio de citas: incluir el campo `range_date` y estado.
- Servicio de reportes: incluir filtros por estado de citas, estado de casos y `range_date`.

#### Criterios de aceptación
- El componente `filter-bar` permite combinar filtros de texto (`search`) con filtros específicos por campo (estado, rol, fecha).
- Los filtros se aplican correctamente y actualizan la información mostrada en pantalla sin necesidad de recargar la página.
- El filtro por fecha acepta tanto formatos específicos (`dd/mm/aaaa`) como rangos predefinidos (Hoy, Ayer, Mañana, Este mes, etc.).
- Los servicios actualizados responden correctamente al recibir los parámetros `search`, `range_date` y los filtros correspondientes (estado, rol, etc.).
- La paginación se mantiene funcional cuando se aplican filtros.
- En caso de no haber coincidencias con los filtros aplicados, se muestra un mensaje claro de "No se encontraron resultados".
- Los filtros se pueden limpiar (resetear) para volver a mostrar todos los registros. 