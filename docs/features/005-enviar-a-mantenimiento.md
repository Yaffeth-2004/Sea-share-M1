# Feature Specification: Enviar a Mantenimiento
Created: 2026-09-07
Updated: 2026-09-29

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Propietario envía su embarcación a mantenimiento (Priority: P1)

Desde la ficha de una embarcación Disponible, el propietario pulsa "Enviar a Mantenimiento" y confirma. La embarcación pasa a "En Mantenimiento" y deja de recibir reservas.

**Why this priority**: Permite al propietario retirar su embarcación del servicio para limpieza, revisión o adecuación.

**Independent Test**: Con una embarcación Disponible, enviarla a mantenimiento como propietario y verificar que el estado cambia a "En Mantenimiento" en la ficha y en el listado.

**Acceptance Scenarios**:

1. **Scenario**: Abrir la confirmación
   - **Given** el propietario está en la ficha de una embarcación en estado Disponible
   - **When** pulsa "Enviar a Mantenimiento"
   - **Then** el sistema muestra el modal "¿Enviar a Mantenimiento?" con el texto "La embarcación no recibirá reservas temporalmente." y los botones "Cancelar" y "Sí, enviar"

2. **Scenario**: Confirmar el envío
   - **Given** se muestra el modal "¿Enviar a Mantenimiento?" del propietario
   - **When** pulsa "Sí, enviar"
   - **Then** el sistema cambia el estado de la embarcación a "En Mantenimiento", registra que el propietario inició el mantenimiento y notifica al administrador

3. **Scenario**: Cancelar el envío
   - **Given** se muestra el modal "¿Enviar a Mantenimiento?" del propietario
   - **When** pulsa "Cancelar"
   - **Then** el modal se cierra y la embarcación conserva el estado Disponible

---

### User Story 2 - Administrador envía una embarcación a mantenimiento con motivo (Priority: P1)

Desde la ficha de una embarcación Disponible, el administrador pulsa "Enviar a Mantenimiento", escribe el motivo y confirma. La embarcación pasa a "En Mantenimiento" y el propietario recibe una notificación cuyo motivo puede consultar.

**Why this priority**: Permite al administrador retirar del servicio una embarcación con un problema que el propietario no ha reportado.

**Independent Test**: Como administrador, enviar una embarcación Disponible a mantenimiento con un motivo y verificar el cambio de estado y la notificación al propietario.

**Acceptance Scenarios**:

1. **Scenario**: Abrir la confirmación
   - **Given** el administrador está en la ficha de una embarcación en estado Disponible
   - **When** pulsa "Enviar a Mantenimiento"
   - **Then** el sistema muestra el modal "¿Enviar a Mantenimiento?" con el texto "La embarcación no recibirá reservas temporalmente. Se enviará una notificación al propietario.", el campo "Motivo del mantenimiento" (placeholder "Describe la razón del mantenimiento (ej. revisión de motor...)") y los botones "Cancelar" y "Sí, enviar"

2. **Scenario**: Confirmar con motivo
   - **Given** el administrador escribió un motivo en el modal
   - **When** pulsa "Sí, enviar"
   - **Then** el sistema cambia el estado a "En Mantenimiento", registra que el administrador inició el mantenimiento, guarda el motivo y notifica al propietario

3. **Scenario**: Motivo vacío
   - **Given** el modal está abierto y el campo "Motivo del mantenimiento" está vacío
   - **When** el administrador pulsa "Sí, enviar"
   - **Then** el sistema resalta el campo y no envía la embarcación a mantenimiento

4. **Scenario**: Cancelar el envío
   - **Given** se muestra el modal del administrador
   - **When** pulsa "Cancelar"
   - **Then** el modal se cierra, no se guarda el motivo y la embarcación conserva el estado Disponible

---

### User Story 3 - Propietario consulta la notificación del administrador (Priority: P1)

El propietario ve la notificación en la campana y consulta el motivo registrado por el administrador.

**Why this priority**: Es la única vía por la que el propietario conoce el motivo del mantenimiento.

**Independent Test**: Tras un envío del administrador, ingresar como propietario y verificar la campana, el desplegable y el modal con el motivo.

**Acceptance Scenarios**:

1. **Scenario**: Notificación recibida
   - **Given** el administrador envió una embarcación del propietario a mantenimiento
   - **When** el propietario ingresa al sistema
   - **Then** la campana muestra un contador rojo con el número de notificaciones nuevas y el listado muestra la embarcación como "En Mantenimiento"

2. **Scenario**: Abrir la campana
   - **Given** el propietario tiene notificaciones nuevas
   - **When** pulsa la campana
   - **Then** el sistema despliega "Notificaciones del Sistema" con la etiqueta "1 NUEVA" (según el número), el nombre de la embarcación, el texto "El administrador ha enviado una notificación sobre el estado de la embarcación." y el botón "Revisar completamente"

3. **Scenario**: Revisar la notificación
   - **Given** el desplegable está abierto
   - **When** el propietario pulsa "Revisar completamente"
   - **Then** el sistema muestra el modal "Detalle de Notificación" con el subtítulo "Aviso enviado por el Administrador General", la embarcación afectada con su estado, y el motivo bajo "Motivo registrado por administración"

4. **Scenario**: Cerrar el detalle
   - **Given** se muestra el modal "Detalle de Notificación"
   - **When** el propietario pulsa "Entendido y cerrar" o la X
   - **Then** el modal se cierra y la notificación se marca como leída, reduciendo el contador de la campana

---

### User Story 4 - Administrador consulta la notificación del propietario (Priority: P2)

El administrador ve en su campana la notificación generada cuando un propietario envía su embarcación a mantenimiento.

**Why this priority**: Mantiene informado al administrador de los cambios de estado de la flota.

**Independent Test**: Tras un envío del propietario, ingresar como administrador y verificar la campana y el acceso al detalle de la embarcación.

**Acceptance Scenarios**:

1. **Scenario**: Notificación recibida y desplegada
   - **Given** un propietario envió una embarcación a mantenimiento
   - **When** el administrador pulsa la campana, que muestra un contador rojo
   - **Then** el sistema despliega "Notificaciones Administrativas" con la etiqueta "1 NUEVA" (según el número), el nombre de la embarcación, el texto "El propietario ha solicitado la revisión de la embarcación." y el botón "Ver detalles"

2. **Scenario**: Ver detalles
   - **Given** el desplegable está abierto
   - **When** el administrador pulsa "Ver detalles"
   - **Then** el sistema muestra la ficha de esa embarcación

---

### User Story 5 - Terminar el mantenimiento (Priority: P2)

Desde la ficha de una embarcación En Mantenimiento, quien inició el mantenimiento pulsa "Terminar mantenimiento" y la embarcación vuelve a Disponible.

**Why this priority**: Sin esta acción, las embarcaciones enviadas a mantenimiento no volverían al servicio.

**Independent Test**: Terminar el mantenimiento como propietario (si él lo inició) y como administrador (si él lo inició) y verificar que la embarcación vuelve a Disponible.

**Acceptance Scenarios**:

1. **Scenario**: Propietario termina su mantenimiento
   - **Given** la embarcación está En Mantenimiento por envío del propietario
   - **When** el propietario pulsa "Terminar mantenimiento" en la ficha
   - **Then** la embarcación pasa a Disponible y puede recibir reservas

2. **Scenario**: Administrador termina su mantenimiento
   - **Given** la embarcación está En Mantenimiento por envío del administrador
   - **When** el administrador pulsa "Terminar mantenimiento" en la ficha
   - **Then** la embarcación pasa a Disponible y puede recibir reservas

---

## Requirements *(mandatory)*

### Functional Requirements

**Envío**
- **FR-001**: El sistema DEBE mostrar el botón "Enviar a Mantenimiento" en la ficha de una embarcación en estado Disponible, al propietario de la embarcación y al administrador
- **FR-002**: El sistema DEBE mostrar, al pulsarlo, el modal "¿Enviar a Mantenimiento?" con "Cancelar" y "Sí, enviar"; para el propietario con el texto "La embarcación no recibirá reservas temporalmente." y para el administrador con el texto "La embarcación no recibirá reservas temporalmente. Se enviará una notificación al propietario." más el campo "Motivo del mantenimiento"
- **FR-003**: El sistema DEBE exigir el motivo cuando el envío lo hace el administrador y no permitir enviar con el campo vacío
- **FR-004**: El sistema DEBE, al confirmar con "Sí, enviar", cambiar el estado de la embarcación de Disponible a "En Mantenimiento" y registrar quién inició el mantenimiento (propietario o administrador)
- **FR-005**: El sistema NO DEBE permitir nuevas reservas sobre una embarcación "En Mantenimiento"

**Notificaciones**
- **FR-006**: El sistema DEBE notificar al propietario cuando el administrador envía su embarcación a mantenimiento, mostrando en la campana un contador rojo de notificaciones nuevas
- **FR-007**: El sistema DEBE mostrar en la campana del propietario el desplegable "Notificaciones del Sistema" con la etiqueta de nuevas ("N NUEVA/S"), el nombre de la embarcación, el texto "El administrador ha enviado una notificación sobre el estado de la embarcación." y el botón "Revisar completamente"
- **FR-008**: El sistema DEBE mostrar, al pulsar "Revisar completamente", el modal "Detalle de Notificación" con el subtítulo "Aviso enviado por el Administrador General", la embarcación afectada con su estado y el motivo bajo "Motivo registrado por administración"
- **FR-009**: El sistema DEBE marcar la notificación como leída y reducir el contador al pulsar "Entendido y cerrar" o la X del modal
- **FR-010**: El sistema DEBE notificar al administrador cuando un propietario envía su embarcación a mantenimiento y mostrar en su campana el desplegable "Notificaciones Administrativas" con la etiqueta de nuevas, el nombre de la embarcación, el texto "El propietario ha solicitado la revisión de la embarcación." y el botón "Ver detalles", que lleva a la ficha de la embarcación
- **FR-011**: El sistema DEBE mostrar el motivo del mantenimiento únicamente en el modal "Detalle de Notificación"

**Terminar mantenimiento**
- **FR-012**: El sistema DEBE mostrar el botón "Terminar mantenimiento" en la ficha de una embarcación "En Mantenimiento" solo a quien inició el mantenimiento
- **FR-013**: El sistema DEBE, al pulsar "Terminar mantenimiento", cambiar el estado a Disponible sin modal de confirmación
- **FR-014**: El sistema NO DEBE mostrar "Terminar mantenimiento" al propietario cuando el mantenimiento lo inició el administrador

### Key Entities

- **Embarcación**: Estados en esta funcionalidad: Disponible ↔ En Mantenimiento. Atributos adicionales: quién inició el mantenimiento (propietario o administrador) y motivo (solo si lo inició el administrador).
- **Notificación**: Aviso dirigido al propietario o al administrador. Atributos: embarcación, texto, motivo (solo la dirigida al propietario), leída o no leída.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: El 100% de las embarcaciones "En Mantenimiento" quedan sin recibir reservas nuevas
- **SC-002**: Solo quien inició el mantenimiento puede terminarlo
