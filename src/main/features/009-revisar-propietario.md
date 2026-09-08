# Feature Specification: Revisar Información Propietario

**Created**: 2026-09-07

## User Scenarios & Testing _(mandatory)_

### User Story 1 - Consultar la información de un propietario (Priority: P1)

El Administrador busca propietarios por nombre, correo o documento, ve una lista de resultados y selecciona uno para consultar su información en detalle. La funcionalidad es de solo lectura: no permite modificar ningún dato del propietario.

**Why this priority**: Es la funcionalidad que permite al Administrador identificar y revisar la información de los propietarios del sistema, útil como apoyo en otras gestiones (por ejemplo, identificar al responsable de una embarcación).

**Independent Test**: Puede probarse buscando un propietario por nombre, correo o documento, viendo la lista de resultados y entrando al detalle de un propietario para ver su información completa.

**Acceptance Scenarios**:

1. **Scenario**: Búsqueda de propietario por nombre
   - **Given** el Administrador está autenticado
   - **When** busca propietarios ingresando un nombre como criterio
   - **Then** el sistema muestra una lista de resultados con los propietarios que coinciden, mostrando nombre, correo y teléfono

2. **Scenario**: Búsqueda de propietario por correo
   - **Given** el Administrador está autenticado
   - **When** busca propietarios ingresando un correo como criterio
   - **Then** el sistema muestra una lista de resultados con los propietarios que coinciden, mostrando nombre, correo y teléfono

3. **Scenario**: Búsqueda de propietario por documento
   - **Given** el Administrador está autenticado
   - **When** busca propietarios ingresando un documento de identidad como criterio
   - **Then** el sistema muestra una lista de resultados con los propietarios que coinciden, mostrando nombre, correo y teléfono

4. **Scenario**: Consulta del detalle de un propietario
   - **Given** el Administrador vio la lista de resultados
   - **When** selecciona un propietario de la lista
   - **Then** el sistema muestra la información completa de ese propietario: nombre, correo, teléfono, documento, fecha de registro y estado de cuenta

5. **Scenario**: Búsqueda sin resultados
   - **Given** el Administrador está autenticado
   - **When** busca con un criterio que no coincide con ningún propietario
   - **Then** el sistema muestra un mensaje indicando que no se encontraron propietarios con ese criterio

6. **Scenario**: Búsqueda sin criterio
   - **Given** el Administrador está en la pantalla de búsqueda de propietarios
   - **When** intenta buscar sin ingresar ningún criterio (ni nombre, ni correo, ni documento)
   - **Then** el sistema no permite buscar y le solicita ingresar al menos un dato de búsqueda

---

### Edge Cases 

- **Pérdida de conexión durante la búsqueda**: si la petición de búsqueda falla (por ejemplo, por pérdida de conexión), el sistema muestra un error y permite al Administrador reintentar.

      ## Requirements _(mandatory)_

      ### Functional Requirements

      - **FR-001**: El sistema DEBE permitir al Administrador buscar propietarios por nombre
      - **FR-002**: El sistema DEBE permitir al Administrador buscar propietarios por correo
      - **FR-003**: El sistema DEBE permitir al Administrador buscar propietarios por documento de identidad
      - **FR-004**: El sistema DEBE permitir al Administrador revisar la información de cualquier propietario del sistema
      - **FR-005**: El sistema DEBE mostrar una lista de resultados con los propietarios que coinciden con el criterio de búsqueda, mostrando para cada uno: nombre, correo y teléfono
      - **FR-006**: El sistema DEBE mostrar la información completa de un propietario al seleccionarlo de la lista: nombre, correo, teléfono, documento, fecha de registro y estado de cuenta
      - **FR-007**: El sistema DEBE ser de solo lectura; NO DEBE permitir al Administrador modificar ningún dato del propietario en esta funcionalidad
      - **FR-008**: El sistema DEBE solicitar al Administrador ingresar al menos un criterio de búsqueda y NO permitir buscar sin haberlo ingresado
      - **FR-009**: El sistema DEBE mostrar un mensaje indicando que no se encontraron propietarios cuando el criterio de búsqueda no arroja resultados
      - **FR-010**: El sistema DEBE mostrar un error y permitir reintentar si la petición de búsqueda falla (por ejemplo, por pérdida de conexión)

### Key Entities

- **Propietario**: Usuario del sistema dueño de embarcaciones. Atributos consultables en esta funcionalidad: nombre, correo, teléfono, documento de identidad, fecha de registro y estado de cuenta. Su registro de cuenta se define en una especificación aparte.

- **Administrador**: Usuario autenticado con permiso para consultar la información de cualquier propietario del sistema, sin poder modificarla.

## Success Criteria _(mandatory)_

### Measurable Outcomes

- **SC-001**: El Administrador puede encontrar a un propietario y ver su información completa en menos de 1 minuto
- **SC-002**: El 100% de las búsquedas se pueden realizar por nombre, correo o documento
- **SC-003**: El 100% de las revisiones del detalle muestran todos los datos del propietario (nombre, correo, teléfono, documento, fecha de registro y estado de cuenta)
- **SC-004**: El 100% de las búsquedas sin criterio son bloqueadas con la solicitud correspondiente
- **SC-005**: El 100% de las búsquedas sin resultados muestran el mensaje correspondiente
- **SC-006**: El 100% de las consultas son de solo lectura; ningún dato del propietario puede modificarse desde esta funcionalidad
