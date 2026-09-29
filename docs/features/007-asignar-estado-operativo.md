# Feature Specification: Asignar Estado Operativo
Created: 2026-09-08
Actualizado: 2026-09-29

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Cambio automático de estado a Reservado o En Navegación por parte del sistema de reservas (Priority: P1)

El sistema de reservas necesita actualizar de forma automática el estado operativo de una embarcación (pasándola a "Reservado" o "En Navegación" cuando corresponda según el ciclo de vida del alquiler) para reflejar su disponibilidad real en la plataforma.

**Why this priority**: Es fundamental para evitar la sobreventa o el alquiler simultáneo de una embarcación que ya está ocupada o apartada por un cliente.

**Independent Test**: Puede probarse enviando una señal simulada desde el módulo de reservas y verificando que el estado de la embarcación cambie correctamente a "Reservado" o "En Navegación".

**Acceptance Scenarios**:

1. **Scenario**: Cambio de estado a Reservado al concretar una reserva
   - **Given** una embarcación se encuentra en estado Disponible
   - **When** el módulo de reservas confirma una reserva para dicha embarcación
   - **Then** el sistema actualiza automáticamente su estado operativo a Reservado

2. **Scenario**: Cambio de estado a En Navegación al iniciar el servicio
   - **Given** una embarcación se encuentra en estado Reservado
   - **When** el módulo de reservas notifica el inicio de la actividad de alquiler
   - **Then** el sistema actualiza automáticamente su estado operativo a En Navegación

---

### User Story 2 - Retorno automático a Disponible al finalizar el alquiler (Priority: P2)

Cuando el alquiler concluye, el sistema devuelve la embarcación al estado "Disponible" para que pueda recibir nuevas reservas. El sistema NO la envía a mantenimiento por sí solo: esa decisión es siempre manual (Propietario o Administrador).

**Why this priority**: Evita que una embarcación que ya no está en uso quede bloqueada como "En Navegación" y permite que vuelva al ciclo productivo.

**Independent Test**: Concluir un alquiler de unaством "En Navegación" y verificar que pase a "Disponible" y que no cambie a "En Mantenimiento" automáticamente.

**Acceptance Scenarios**:

1. **Scenario**: Retorno a Disponible al concluir el alquiler
   - **Given** una embarcación se encuentra en estado En Navegación
   - **When** el módulo de reservas notifica que el alquiler concluyó
   - **Then** el sistema cambia su estado operativo a Disponible y no la envía a mantenimiento automáticamente

2. **Scenario**: El Propietario decide enviar a mantenimiento después del alquiler
   - **Given** una embarcación volvió a Disponible tras un alquiler
   - **When** el Propietario considera necesaria una limpieza o revisión
   - **Then** puede enviarla a mantenimiento manualmente desde "Disponible" (ver User Story 3)

---

### User Story 3 - Envío manual a En Mantenimiento por Propietario o Administrador (Priority: P1)

El estado "En Mantenimiento" solo se asigna mediante una acción manual con el botón "Enviar a Mantenimiento" y confirmación en un modal. El Propietario puede hacerlo desde "Disponible" o "En Navegación" (por ejemplo, una avería durante el alquiler); el Administrador solo desde "Disponible", con motivo obligatorio. Desde "Reservado" no está permitido. El detalle de la interfaz está en el spec "Enviar a Mantenimiento".

**Why this priority**: Es la única vía para poner una embarcación fuera de servicio y debe respetar las reglas de rol y de estado.

**Independent Test**: Intentar enviar a mantenimiento embarcaciones en cada estado y con cada rol, verificando qué combinaciones se permiten y cuáles se rechazan.

**Acceptance Scenarios**:

1. **Scenario**: Propietario envía desde Disponible o En Navegación
   - **Given** una embarcación en estado Disponible o En Navegación
   - **When** el Propietario confirma "Sí, enviar" en el modal
   - **Then** el estado pasa a En Mantenimiento y se registra al Propietario como quien inició el mantenimiento

2. **Scenario**: Administrador envía desde Disponible con motivo
   - **Given** una embarcación en estado Disponible
   - **When** el Administrador escribe el motivo y confirma "Sí, enviar"
   - **Then** el estado pasa a En Mantenimiento, se registra al Administrador, su rol y el motivo, y se notifica al Propietario

3. **Scenario**: Envío no permitido desde Reservado
   - **Given** una embarcación en estado Reservado
   - **When** cualquier usuario intenta enviarla a mantenimiento
   - **Then** el botón aparece deshabilitado y el sistema rechaza la transición si se intenta por otra vía

4. **Scenario**: Envío no permitido para el Administrador desde En Navegación
   - **Given** una embarcación en estado En Navegación
   - **When** el Administrador intenta enviarla a mantenimiento
   - **Then** el botón aparece deshabilitado y el sistema rechaza la transición

---

### User Story 4 - Retorno a Disponible al terminar el mantenimiento (Priority: P2)

El mantenimiento se termina con el botón "Terminar mantenimiento" y un modal de confirmación ("Cancelar" / "Sí, terminar"). El estado resultante es Disponible. Si el mantenimiento lo inició el Propietario, lo termina el Propietario. Si lo inició el Administrador, el Propietario solicita la revisión y el Administrador lo termina.

**Why this priority**: Permite que la embarcación vuelva a recibir reservas una vez concluidas las labores técnicas.

**Independent Test**: Terminar un mantenimiento iniciado por cada rol y verificar quién puede hacerlo y que el estado final sea Disponible.

**Acceptance Scenarios**:

1. **Scenario**: Propietario termina un mantenimiento que él inició
   - **Given** una embarcación En Mantenimiento iniciado por el Propietario
   - **When** el Propietario confirma "Sí, terminar" en el modal
   - **Then** el estado pasa a Disponible

2. **Scenario**: Propietario solicita revisión de un mantenimiento iniciado por el Administrador
   - **Given** una embarcación En Mantenimiento iniciado por el Administrador
   - **When** el Propietario solicita la revisión
   - **Then** el Administrador recibe una notificación y el estado sigue En Mantenimiento; el Propietario no puede terminarlo directamente

3. **Scenario**: Administrador termina el mantenimiento que él inició
   - **Given** una embarcación En Mantenimiento iniciado por el Administrador
   - **When** el Administrador confirma "Sí, terminar" en el modal
   - **Then** el estado pasa a Disponible

4. **Scenario**: Cancelar la confirmación
   - **Given** el modal de "Terminar mantenimiento" está abierto
   - **When** el usuario hace clic en "Cancelar"
   - **Then** el estado no cambia y la embarcación sigue En Mantenimiento

---

### Edge Cases

- ¿Qué pasa si el módulo de reservas intenta cambiar el estado de una embarcación que ya no está disponible (por ejemplo, ya fue reservada por otro usuario en simultáneo)? El sistema rechaza la transición de estado y devuelve un error de conflicto al módulo externo para que se gestione la cancelación o reubicación.
- ¿Qué pasa si una embarcación en estado Borrador intenta recibir un cambio de estado operativo? El sistema bloquea la acción, ya que las embarcaciones en borrador no pueden ser reservadas ni operar hasta completar su registro.
- Si el estado de la embarcación no permite enviarla a mantenimiento para el rol del usuario, el botón "Enviar a Mantenimiento" aparece deshabilitado.
- Si una embarcación en estado Reservado necesita mantenimiento (por ejemplo, una avería), el Propietario debe cancelar primero la reserva desde el módulo de reservas y luego enviarla a mantenimiento.
- Si se envía a mantenimiento una embarcación Disponible que tiene reservas futuras, el sistema muestra una alerta con las reservas afectadas antes de confirmar y notifica al Propietario si la acción la hace el Administrador (por confirmar el comportamiento exacto: solo alerta o bloqueo).
- Si se cancela cualquier modal de confirmación, no se produce ningún cambio de estado ni notificación.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: El sistema DEBE permitir únicamente las transiciones de estado operativo de la siguiente tabla y rechazar cualquier otra:

  | Desde | Hacia | Quién / Cómo |
  |---|---|---|
  | Disponible | Reservado | Sistema (módulo de reservas) |
  | Reservado | En Navegación | Sistema (módulo de reservas) |
  | En Navegación | Disponible | Sistema, al terminar el alquiler |
  | Disponible | En Mantenimiento | Propietario o Administrador (manual, con confirmación) |
  | En Navegación | En Mantenimiento | Solo Propietario (manual, con confirmación) |
  | En Mantenimiento | Disponible | Quien inició el mantenimiento (manual, con confirmación) |
  | Reservado | En Mantenimiento | No permitido |

- **FR-002**: El sistema DEBE proveer un endpoint o mecanismo interno seguro para que el Módulo de Reservas pueda actualizar automáticamente el estado de la embarcación a "Reservado" o "En Navegación" al concretar o iniciar un alquiler.
- **FR-003**: El sistema DEBE actualizar automáticamente el estado de la embarcación a "Disponible" cuando el Módulo de Reservas notifique el fin del alquiler, sin pasar por "En Mantenimiento".
- **FR-004**: El sistema DEBE validar y rechazar cualquier intento de cambiar a estado "Reservado" o "En Navegación" una embarcación que no se encuentre en el estado previo permitido según FR-001 (Disponible para Reservado, Reservado para En Navegación).
- **FR-005**: El sistema DEBE validar y rechazar cualquier intento de cambiar a "En Mantenimiento" desde un estado o con un rol no permitido según FR-001, tanto desde la interfaz como desde cualquier otra vía.
- **FR-006**: El sistema DEBE requerir que el cambio a "En Mantenimiento" y el cambio de "En Mantenimiento" a "Disponible" sean acciones manuales con modal de confirmación ("Cancelar" / "Sí, enviar" y "Cancelar" / "Sí, terminar").
- **FR-007**: El sistema DEBE permitir terminar el mantenimiento solo a quien lo inició. Si lo inició el Administrador, el Propietario solo puede solicitar la revisión y el Administrador lo termina.
- **FR-008**: El sistema DEBE almacenar el registro de los cambios de estado operativo con fecha y hora, usuario que los realizó (o "Sistema"), su rol y, en el caso de mantenimiento iniciado por el Administrador, el motivo (obligatorio).
- **FR-009**: El sistema DEBE notificar al Propietario cuando el Administrador envía su embarcación a mantenimiento, e incluir el motivo. El sistema DEBE permitir al Propietario notificar al Administrador para solicitar la revisión.
- **FR-010**: El sistema DEBE impedir nuevas reservas mientras la embarcación esté "En Mantenimiento".
- **FR-011**: El sistema DEBE mostrar deshabilitado el botón "Enviar a Mantenimiento" cuando el estado o el rol no permitan la acción.
- **FR-012**: El sistema DEBE alertar sobre reservas futuras existentes al enviar a mantenimiento una embarcación Disponible (comportamiento exacto por confirmar).

### Key Entities

- **Embarcación**: Objeto principal afectado por la asignación de estados. Sus estados operativos posibles son: Disponible, Reservado, En Navegación y En Mantenimiento. Registra quién inició el mantenimiento (Propietario o Administrador) y el motivo cuando corresponde.
- **Registro de cambio de estado**: Estado anterior, estado nuevo, fecha y hora, usuario (o Sistema), rol y motivo (si aplica).
- **Notificación**: Tipo (Sistema/Administrativa), embarcación asociada, texto, motivo (si aplica) y estado leída/nueva.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: El 100% de las solicitudes de cambio de estado enviadas por el módulo de reservas se procesan y reflejan en menos de 1 segundo.
- **SC-002**: Se previene en un 100% la asignación de reservas a embarcaciones que no se encuentran en estado Disponible.
- **SC-003**: El 100% de los alquileres concluidos devuelven la embarcación a "Disponible" sin pasar automáticamente por "En Mantenimiento".
- **SC-004**: El 100% de los intentos de transición no incluidos en la tabla de FR-001 (incluido Reservado → En Mantenimiento) son rechazados.
- **SC-005**: El 100% de los cambios de estado a "En Mantenimiento" quedan registrados con usuario, rol y fecha/hora, y con motivo cuando los inicia el Administrador.
