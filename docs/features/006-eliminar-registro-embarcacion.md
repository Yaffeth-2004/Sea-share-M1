# Feature Specification: Eliminar Registro Embarcación

**Created**: 2026-09-07


## User Scenarios & Testing _(mandatory)_

### User Story 1 - Eliminar una embarcación como Propietario (Priority: P1)

El Propietario elimina una de las embarcaciones de su lista "Mis Embarcaciones Registradas" desde la pantalla de detalle de la embarcación. Al pulsar el botón **Eliminar**, el sistema muestra un mensaje de confirmación que advierte que la acción elimina el registro de forma permanente, libera la matrícula asociada y no se puede deshacer, con las opciones **Conservar** y **Sí, eliminar**. Para el usuario la eliminación es definitiva; internamente, el sistema conserva el registro únicamente como historial.

**Why this priority**: Permite al Propietario retirar del servicio una embarcación de forma segura y controlada, impidiendo que se siga ofreciendo o reservando.

**Independent Test**: Puede probarse abriendo el detalle de una embarcación en estado Disponible o En Mantenimiento/Limpieza, pulsando Eliminar, eligiendo cada una de las opciones del mensaje y verificando el resultado.

**Acceptance Scenarios**:

1. **Scenario**: Mensaje de confirmación al eliminar
   - **Given** el Propietario está autenticado y se encuentra en el detalle de una embarcación en estado Disponible o En Mantenimiento/Limpieza
   - **When** pulsa el botón Eliminar
   - **Then** el sistema muestra el mensaje "¿Eliminar embarcación?" indicando que la acción eliminará el registro de forma permanente, liberará su matrícula asociada y no se puede deshacer, con las opciones Conservar y Sí, eliminar

2. **Scenario**: Conservar la embarcación
   - **Given** el sistema muestra el mensaje de confirmación de eliminación
   - **When** el Propietario elige Conservar
   - **Then** el sistema no elimina la embarcación, cierra el mensaje y lo devuelve a la pantalla de detalle de la embarcación sin ningún cambio

3. **Scenario**: Eliminación confirmada
   - **Given** el sistema muestra el mensaje de confirmación de eliminación
   - **When** el Propietario elige Sí, eliminar
   - **Then** el sistema elimina la embarcación y lo lleva a la pantalla "Mis Embarcaciones Registradas" ya actualizada, sin mostrar la embarcación eliminada

4. **Scenario**: Sin mensaje adicional tras eliminar
   - **Given** el Propietario confirmó la eliminación con Sí, eliminar
   - **When** el sistema completa la eliminación
   - **Then** el mensaje de confirmación de eliminación es el único mensaje del flujo; el sistema no muestra un mensaje de éxito adicional

---

### User Story 2 - Eliminar una embarcación como Administrador (Priority: P1)

El Administrador elimina la embarcación de cualquier propietario del sistema desde la pantalla de detalle de la embarcación, a la que accede desde la pantalla "Control General de Flota Fluvial". El mensaje de confirmación y sus opciones son los mismos que ve el Propietario, y las reglas de estado también son las mismas.

**Why this priority**: Permite al Administrador retirar del servicio embarcaciones de cualquier propietario del sistema cuando sea necesario.

**Independent Test**: Puede probarse, como Administrador, abriendo el detalle de una embarcación de otro propietario en estado Disponible o En Mantenimiento/Limpieza, pulsando Eliminar y verificando el resultado de cada opción.

**Acceptance Scenarios**:

1. **Scenario**: Mensaje de confirmación al eliminar
   - **Given** el Administrador está autenticado y se encuentra en el detalle de una embarcación de cualquier propietario en estado Disponible o En Mantenimiento/Limpieza
   - **When** pulsa el botón Eliminar
   - **Then** el sistema muestra el mismo mensaje "¿Eliminar embarcación?" con las opciones Conservar y Sí, eliminar

2. **Scenario**: Conservar la embarcación
   - **Given** el sistema muestra el mensaje de confirmación de eliminación
   - **When** el Administrador elige Conservar
   - **Then** el sistema no elimina la embarcación y lo devuelve a la pantalla "Control General de Flota Fluvial"

3. **Scenario**: Eliminación confirmada
   - **Given** el sistema muestra el mensaje de confirmación de eliminación
   - **When** el Administrador elige Sí, eliminar
   - **Then** el sistema elimina la embarcación y lo lleva a la pantalla "Control General de Flota Fluvial" ya actualizada, sin mostrar la embarcación eliminada

---

### User Story 3 - Restricción del botón Eliminar según el estado de la embarcación (Priority: P1)

El botón **Eliminar** se encuentra en la pantalla de detalle de la embarcación. El sistema lo habilita, tanto para el Propietario como para el Administrador, únicamente cuando la embarcación está en estado **Disponible** o **En Mantenimiento/Limpieza**. Cuando la embarcación está en estado **Reservada** o **En Navegación**, el botón aparece deshabilitado y no permite iniciar la eliminación.

**Why this priority**: Evita eliminar una embarcación que está en uso (reservada o navegando), lo que podría afectar servicios en curso o con riesgo operativo o de seguridad.

**Independent Test**: Puede probarse abriendo el detalle de una embarcación en cada estado (Disponible, En Mantenimiento/Limpieza, Reservada, En Navegación), con ambos actores, y verificando que el botón Eliminar solo está habilitado en Disponible y En Mantenimiento/Limpieza.

**Acceptance Scenarios**:

1. **Scenario**: Botón Eliminar habilitado en estado Disponible
   - **Given** el Propietario o el Administrador está en el detalle de una embarcación en estado Disponible
   - **When** se muestra la pantalla
   - **Then** el botón Eliminar aparece habilitado

2. **Scenario**: Botón Eliminar habilitado en estado En Mantenimiento/Limpieza
   - **Given** el Propietario o el Administrador está en el detalle de una embarcación en estado En Mantenimiento/Limpieza
   - **When** se muestra la pantalla
   - **Then** el botón Eliminar aparece habilitado

3. **Scenario**: Botón Eliminar deshabilitado en estado Reservada
   - **Given** el Propietario o el Administrador está en el detalle de una embarcación en estado Reservada
   - **When** se muestra la pantalla
   - **Then** el botón Eliminar aparece deshabilitado y no permite iniciar la eliminación

4. **Scenario**: Botón Eliminar deshabilitado en estado En Navegación
   - **Given** el Propietario o el Administrador está en el detalle de una embarcación en estado En Navegación
   - **When** se muestra la pantalla
   - **Then** el botón Eliminar aparece deshabilitado y no permite iniciar la eliminación

---

### Edge Cases

- **Irreversibilidad**: una vez eliminada, la embarcación no se puede recuperar ni reactivar, y ni el Propietario ni el Administrador pueden volver a acceder a ella. El sistema conserva el registro únicamente como historial interno.
- **Cambio de estado con el mensaje de confirmación abierto**: si el estado de la embarcación cambia a Reservada o En Navegación mientras el mensaje de confirmación está abierto, el sistema revalida el estado al pulsar Sí, eliminar. Si el estado ya no permite eliminar, rechaza la eliminación, conserva la embarcación y muestra un mensaje indicando que la embarcación cambió de estado y ya no puede eliminarse.
- **Pérdida de conexión al confirmar la eliminación**: si la petición de eliminación falla (por ejemplo, por pérdida de conexión), el sistema no elimina la embarcación, muestra un error y permite reintentar.

## Requirements _(mandatory)_

### Functional Requirements

- **FR-001**: El sistema DEBE permitir al Propietario eliminar, desde la pantalla de detalle, las embarcaciones que visualiza en su lista "Mis Embarcaciones Registradas"
- **FR-002**: El sistema DEBE permitir al Administrador eliminar, desde la pantalla de detalle, la embarcación de cualquier propietario
- **FR-003**: El sistema DEBE tratar la eliminación como una eliminación lógica: el registro de la embarcación se conserva en el sistema únicamente como historial, aunque para el usuario se presenta como una eliminación permanente
- **FR-004**: El sistema DEBE tratar la eliminación como definitiva e irreversible; la embarcación eliminada NO se puede reactivar y ni el Propietario ni el Administrador pueden volver a acceder a ella
- **FR-005**: El sistema DEBE dejar de mostrar la embarcación eliminada en "Mis Embarcaciones Registradas", en "Control General de Flota Fluvial" y en las búsquedas, dejar de permitir su reserva y liberar su matrícula asociada
- **FR-006**: El sistema DEBE mostrar el botón Eliminar en la pantalla de detalle de la embarcación, tanto para el Propietario como para el Administrador
- **FR-007**: El sistema DEBE habilitar el botón Eliminar únicamente cuando la embarcación está en estado Disponible o En Mantenimiento/Limpieza
- **FR-008**: El sistema DEBE mostrar el botón Eliminar deshabilitado, sin permitir iniciar la eliminación, cuando la embarcación está en estado Reservada o En Navegación
- **FR-009**: El sistema DEBE mostrar, al pulsar Eliminar, el mensaje de confirmación "¿Eliminar embarcación?" que indica que la acción eliminará el registro de forma permanente y liberará su matrícula asociada, advierte que la acción no se puede deshacer y ofrece las opciones Conservar y Sí, eliminar
- **FR-010**: El sistema DEBE, al elegir Conservar, no eliminar la embarcación y cerrar el mensaje: al Propietario lo devuelve a la pantalla de detalle de la embarcación y al Administrador a la pantalla "Control General de Flota Fluvial"
- **FR-011**: El sistema DEBE, al elegir Sí, eliminar, eliminar la embarcación y llevar al usuario a la lista actualizada sin la embarcación eliminada: al Propietario a "Mis Embarcaciones Registradas" y al Administrador a "Control General de Flota Fluvial"
- **FR-012**: El sistema NO DEBE mostrar un mensaje de éxito adicional tras eliminar; el único mensaje del flujo es el de confirmación de eliminación
- **FR-013**: El sistema DEBE revalidar el estado de la embarcación al pulsar Sí, eliminar y rechazar la eliminación, con un mensaje explicativo, si ya no es Disponible ni En Mantenimiento/Limpieza
- **FR-014**: El sistema DEBE permitir reintentar la eliminación si la petición falla (por ejemplo, por pérdida de conexión); en ese caso no elimina la embarcación y muestra un error

### Key Entities

- **Embarcación**: Representa una embarcación registrada. En esta funcionalidad se aplica una eliminación lógica y definitiva: el registro se conserva solo como historial interno, deja de ser accesible para el Propietario y el Administrador, deja de aparecer en listas y búsquedas, y su matrícula queda liberada. Los estados operativos posibles son: Disponible, Reservado, En Navegación y En Mantenimiento/Limpieza.
- **Propietario**: Usuario autenticado que gestiona sus embarcaciones y puede eliminar las que visualiza en su lista, cuando están en un estado que lo permite.
- **Administrador**: Usuario autenticado con permiso para eliminar la embarcación de cualquier propietario, con las mismas reglas de estado que el Propietario.


## Success Criteria _(mandatory)_

### Measurable Outcomes

- **SC-001**: El 100% de las eliminaciones requieren confirmación explícita, mediante un mensaje que advierte que la acción es permanente e irreversible, antes de ejecutarse
- **SC-002**: El 100% de las embarcaciones en estado Reservada o En Navegación muestran el botón Eliminar deshabilitado, para ambos actores
- **SC-003**: El 100% de las embarcaciones en estado Disponible o En Mantenimiento/Limpieza muestran el botón Eliminar habilitado, para ambos actores
- **SC-004**: El 100% de las embarcaciones eliminadas dejan de aparecer en las listas y en las búsquedas, dejan de poder reservarse y liberan su matrícula
- **SC-005**: El 100% de las eliminaciones confirmadas llevan al usuario a su lista actualizada sin la embarcación eliminada: "Mis Embarcaciones Registradas" para el Propietario y "Control General de Flota Fluvial" para el Administrador
- **SC-006**: El 100% de las veces que el usuario elige Conservar, la embarcación permanece sin ningún cambio
- **SC-007**: El 100% de las eliminaciones son rechazadas si el estado de la embarcación cambió a uno que no permite eliminar entre la apertura del mensaje de confirmación y su confirmación
- **SC-008**: El 100% de las embarcaciones eliminadas conservan su registro como historial interno del sistema
