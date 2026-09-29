# Feature Specification: Consultar Información de Embarcación

Created: 2026-09-08

## User Scenarios & Testing _(mandatory)_

### User Story 1 - Propietario consulta el detalle de una de sus embarcaciones (Priority: P1)

El propietario, desde su listado de embarcaciones, pulsa "Ver Detalles" en una de ellas y visualiza su ficha completa: nombre, estado actual, fotografía, matrícula legal, tipo, capacidad máxima, tarifa base por hora, puerto de atraque con ubicación GPS y servicios (base y adicionales). Puede regresar a su listado con "Volver".

**Why this priority**: Es la vía principal por la que el propietario verifica los datos y el estado de sus embarcaciones de forma individual sin alterar la información.

**Independent Test**: Iniciar sesión como propietario, pulsar "Ver Detalles" en una de sus embarcaciones y verificar que la pantalla muestre correctamente todos los datos registrados.

**Acceptance Scenarios**:

1. **Scenario**: Consulta exitosa del detalle por parte del propietario
   - **Given** el propietario está autenticado y tiene embarcaciones registradas
   - **When** pulsa "Ver Detalles" en una de sus embarcaciones
   - **Then** el sistema muestra la ficha completa con nombre, estado actual, fotografía, matrícula legal, tipo, capacidad máxima, tarifa base por hora, puerto y coordenadas GPS, servicios base y servicios adicionales

2. **Scenario**: Regreso al listado
   - **Given** el propietario está en el detalle de una embarcación
   - **When** pulsa "Volver"
   - **Then** el sistema lo regresa a su listado de embarcaciones

3. **Scenario**: Embarcación ajena o inexistente
   - **Given** el propietario intenta acceder mediante enlace directo o ID a una embarcación que no le pertenece o que ya no existe
   - **When** carga la página de detalle
   - **Then** el sistema no muestra los datos y presenta un mensaje indicando que la embarcación no está disponible o no existe

---

### User Story 2 - Administrador consulta el detalle de cualquier embarcación (Priority: P1)

El administrador, desde el listado global de la flota, pulsa "Ver Detalles" en cualquier embarcación y visualiza su ficha completa con la misma información que ve el propietario, sin importar quién sea el dueño. Puede regresar al listado global con "Volver".

**Why this priority**: Le permite verificar el estado y los datos de cualquier embarcación de la plataforma sin alterar la información.

**Independent Test**: Iniciar sesión como administrador, pulsar "Ver Detalles" en embarcaciones de distintos propietarios y verificar que se muestre la información completa en todas.

**Acceptance Scenarios**:

1. **Scenario**: Consulta exitosa del detalle de cualquier embarcación
   - **Given** el administrador está autenticado
   - **When** pulsa "Ver Detalles" en una embarcación de cualquier propietario
   - **Then** el sistema muestra la ficha completa con nombre, estado actual, fotografía, matrícula legal, tipo, capacidad máxima, tarifa base por hora, puerto y coordenadas GPS, servicios base y servicios adicionales, independientemente de quién sea el propietario

2. **Scenario**: Regreso al listado global
   - **Given** el administrador está en el detalle de una embarcación
   - **When** pulsa "Volver"
   - **Then** el sistema lo regresa al listado global de la flota

3. **Scenario**: Embarcación no encontrada o eliminada
   - **Given** el administrador intenta acceder mediante enlace directo o ID a una embarcación que ya no existe
   - **When** carga la página de detalle
   - **Then** el sistema muestra un mensaje indicando que la embarcación no está disponible o no existe

---

### User Story 3 - El módulo de reservas consulta la información completa de la embarcación (Priority: P2)

El sistema de reservas necesita consultar de forma automática toda la información de la embarcación (características, capacidad, puerto, servicios incluidos, tarifas y estado) para procesar correctamente las solicitudes de alquiler de los clientes.

**Why this priority**: Permite la integración y comunicación fluida con el módulo de reservas para que disponga de todos los datos reales y actualizados al momento de cotizar o gestionar un alquiler.

**Independent Test**: Puede probarse simulando una solicitud desde el módulo de reservas hacia una embarcación y verificando que el sistema retorna su ficha informativa completa.

**Acceptance Scenarios**:

1. **Scenario**: Consulta de datos completos para el módulo de reservas
   - **Given** el módulo de reservas requiere procesar una solicitud de alquiler
   - **When** realiza una solicitud de consulta sobre una embarcación específica
   - **Then** el sistema retorna toda la información de la embarcación (datos básicos, capacidad, puerto de atraque, tarifa base, servicios incluidos y estado operativo actual)

---

### Edge Cases

- ¿Qué pasa si el sistema de reservas intenta consultar una embarcación que se encuentra en mantenimiento o en estado borrador? El sistema responde al módulo externo indicando que la embarcación no está apta para operar en ese momento.
- ¿Qué pasa si hay fallas de conexión al momento en que el módulo de reservas intenta consultar la información? El sistema externo maneja un tiempo de espera (timeout) y reintenta la consulta para evitar bloqueos en el flujo del usuario.
- ¿Qué pasa si un propietario intenta acceder al detalle de una embarcación de otro propietario mediante un enlace directo? El sistema no muestra los datos y responde con el mismo mensaje de embarcación no disponible o inexistente.

## Requirements _(mandatory)_

### Functional Requirements

**Consulta por rol**

- **FR-001**: El sistema DEBE permitir al propietario acceder al detalle de sus propias embarcaciones mediante el botón "Ver Detalles" de su listado.
- **FR-002**: El sistema DEBE permitir al administrador acceder al detalle de cualquier embarcación registrada mediante el botón "Ver Detalles" del listado global, sin importar quién sea el propietario.
- **FR-003**: El sistema DEBE restringir el detalle de una embarcación a su propietario y a los administradores, salvo las consultas automatizadas permitidas del módulo de reservas.

**Contenido de la ficha de detalle**

- **FR-004**: El sistema DEBE mostrar en la ficha el nombre de la embarcación y su estado actual con distintivo de color (_Disponible_ en verde, _En Mantenimiento_ en amarillo).
- **FR-005**: El sistema DEBE mostrar la fotografía oficial de la embarcación.
- **FR-006**: El sistema DEBE mostrar la matrícula legal, el tipo de embarcación y la capacidad máxima expresada en pasajeros (ej. "12 Pasajeros").
- **FR-007**: El sistema DEBE mostrar la tarifa base **por hora** en pesos colombianos (COP), con formato de moneda (ej. "$ 180.000 COP").
- **FR-008**: El sistema DEBE mostrar el puerto de atraque con su nombre y muelle, junto con las coordenadas GPS en formato Lat N / Long O.
- **FR-009**: El sistema DEBE mostrar la sección "Servicios y Dotación Incluida", separando los servicios **obligatorios (base)**, Capitán y Combustible, de los **adicionales** (chalecos salvavidas, equipo de pesca, equipo de sonido, nevera con hielo, buceo).
- **FR-010**: El sistema DEBE ofrecer un botón "Volver" que regrese al usuario al listado desde el que entró (listado del propietario o listado global del administrador).
- **FR-011**: La vista de detalle DEBE ser de solo lectura: consultar no modifica ningún dato de la embarcación.

**Diferencias entre roles**

- **FR-012**: El subtítulo de la sección de servicios DEBE adaptarse al rol: para el propietario indica el equipamiento adicional seleccionado para el alquiler, y para el administrador indica el equipamiento configurado por el propietario.
- **FR-013**: Las acciones Editar, Enviar a Mantenimiento y Eliminar que aparecen en el detalle pertenecen a otras funcionalidades y están fuera del alcance de esta spec. Su visibilidad por rol se define en esas specs: el propietario ve las tres y el administrador solo Enviar a Mantenimiento y Eliminar.

**Casos de error**

- **FR-014**: El sistema DEBE mostrar un mensaje indicando que la embarcación no está disponible o no existe cuando el usuario acceda mediante enlace directo o ID a una embarcación eliminada o inexistente.
- **FR-015**: Cuando un propietario intente acceder a una embarcación que no le pertenece, el sistema NO DEBE mostrar sus datos y DEBE presentar el mismo mensaje de embarcación no disponible.

**Integración con el Módulo de Reservas**

- **FR-016**: El sistema DEBE proveer un mecanismo o API interna para que el Módulo de Reservas pueda consultar en tiempo real la información completa y actualizada de la embarcación (incluyendo capacidad máxima, ubicación GPS del puerto, servicios, tarifa base y estado) para sus validaciones operativas.
- **FR-017**: Cuando el Módulo de Reservas consulte una embarcación en mantenimiento o en borrador, el sistema DEBE responder indicando que no está apta para operar en ese momento.

### Key Entities

- **Embarcación**: Objeto central de la consulta. Atributos visibles: nombre, matrícula legal, tipo, capacidad máxima de pasajeros, tarifa base por hora (COP), estado, fotografía, puerto de atraque con coordenadas GPS y servicios asociados.
- **Puerto de Atraque**: Ubicación geográfica vinculada a la embarcación (nombre, muelle, latitud y longitud) utilizada también para la sincronización de zonas horarias y reglas de tiempo en las reservas.
- **Servicio**: Inclusiones de la embarcación divididas en servicios base obligatorios (Capitán y Combustible) y servicios adicionales (chalecos salvavidas, equipo de pesca, sonido, nevera con hielo, buceo).

### Fuera de alcance

- Listados de embarcaciones (del propietario y global), filtros y búsqueda. Los listados solo se usan como punto de entrada al botón "Ver Detalles".
- Las acciones Editar, Enviar a Mantenimiento, Eliminar y Registrar Nueva Embarcación, cubiertas por otras specs.

## Success Criteria _(mandatory)_

### Measurable Outcomes

- **SC-001**: Los usuarios internos (propietarios y administradores) pueden visualizar la información completa de una embarcación en menos de 2 segundos.
- **SC-002**: El 100% de las consultas provenientes del módulo de reservas obtienen respuestas correctas y sincronizadas con el estado actual de la embarcación.
- **SC-003**: Ningún usuario no autorizado puede acceder a la consulta detallada de una embarcación que no le pertenece, ni siquiera mediante enlace directo o ID.
