# Feature Specification: Cancelar Registro
Created: 2026-09-07
Updated: 2026-09-29

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Cancelar durante el Paso 1 "Datos Básicos" (Priority: P1)

El propietario abandona el registro mientras está en el Paso 1 (nombre, matrícula legal, tipo y capacidad máxima). Como en este paso aún no se ha guardado nada, al confirmar se descarta lo ingresado y no se crea ninguna embarcación.

**Why this priority**: Es la forma básica de descartar un registro que el propietario ya no desea completar.

**Independent Test**: Abrir el Paso 1, ingresar datos, pulsar "Cancelar", confirmar con "Sí, cancelar" y verificar que se vuelve al listado sin ninguna embarcación nueva ni Borrador.

**Acceptance Scenarios**:

1. **Scenario**: Cancelar con o sin datos ingresados
   - **Given** el propietario está en el Paso 1, con o sin campos completados
   - **When** pulsa "Cancelar"
   - **Then** el sistema muestra el modal "¿Cancelar registro?" con el texto "Los datos ingresados hasta el momento se perderán y no se guardará ninguna embarcación." y los botones "Continuar registro" y "Sí, cancelar"

2. **Scenario**: Confirmar la cancelación
   - **Given** se muestra el modal "¿Cancelar registro?"
   - **When** el propietario pulsa "Sí, cancelar"
   - **Then** el sistema descarta lo ingresado, no crea ninguna embarcación ni Borrador y vuelve al listado "Mis Embarcaciones Registradas"

3. **Scenario**: No cancelar
   - **Given** se muestra el modal "¿Cancelar registro?"
   - **When** el propietario pulsa "Continuar registro"
   - **Then** el modal se cierra y el propietario permanece en el Paso 1 con lo ingresado intacto

---

### User Story 2 - Salir durante el Paso 2 "Datos Complementarios" (Priority: P2)

El propietario abandona el registro mientras está en el Paso 2 (puerto de atraque, tarifa, servicios adicionales y foto). Los datos del Paso 1 ya están guardados en el Borrador, por lo que el sistema los conserva y descarta lo ingresado en el Paso 2.

**Why this priority**: Permite suspender el registro sin perder el Paso 1 y retomarlo después.

**Independent Test**: Completar el Paso 1, ingresar datos en el Paso 2, pulsar "Cancelar", confirmar con "Salir" y verificar que se vuelve al listado, que el Borrador se conserva y que al pulsar "Registrar Nueva Embarcación" aparece el modal "Borrador detectado".

**Acceptance Scenarios**:

1. **Scenario**: Cancelar con o sin datos ingresados en el Paso 2
   - **Given** el propietario está en el Paso 2, con o sin datos ingresados
   - **When** pulsa "Cancelar"
   - **Then** el sistema muestra el modal "¿Suspender registro?" con el texto "Los datos de identificación básica (Paso 1) se almacenarán de forma temporal en estado borrador, permitiendo reanudar el proceso de alta en futuras sesiones." y los botones "Continuar registro" y "Salir"

2. **Scenario**: Salir
   - **Given** se muestra el modal "¿Suspender registro?"
   - **When** el propietario pulsa "Salir"
   - **Then** el sistema descarta lo ingresado en el Paso 2 (puerto, tarifa, servicios adicionales y foto), conserva el Borrador con los datos del Paso 1 y vuelve al listado "Mis Embarcaciones Registradas"

3. **Scenario**: No salir
   - **Given** se muestra el modal "¿Suspender registro?"
   - **When** el propietario pulsa "Continuar registro"
   - **Then** el modal se cierra y el propietario permanece en el Paso 2 con lo ingresado intacto

4. **Scenario**: Retomar tras salir
   - **Given** el propietario salió del Paso 2 y tiene un Borrador
   - **When** pulsa "Registrar Nueva Embarcación"
   - **Then** el sistema muestra el modal "Borrador detectado" definido en la especificación "Registrar Embarcación"

---

## Edge Cases

- **Clics múltiples en "Cancelar"**: el sistema muestra el modal una sola vez.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: El sistema DEBE mostrar el botón "Cancelar" en el Paso 1 y en el Paso 2 del registro
- **FR-002**: El sistema DEBE mostrar un modal de confirmación cada vez que se pulse "Cancelar"
- **FR-003**: En el Paso 1, el modal DEBE titularse "¿Cancelar registro?", mostrar el texto "Los datos ingresados hasta el momento se perderán y no se guardará ninguna embarcación." y ofrecer "Continuar registro" y "Sí, cancelar"
- **FR-004**: En el Paso 2, el modal DEBE titularse "¿Suspender registro?", mostrar el texto "Los datos de identificación básica (Paso 1) se almacenarán de forma temporal en estado borrador, permitiendo reanudar el proceso de alta en futuras sesiones." y ofrecer "Continuar registro" y "Salir"
- **FR-005**: Al confirmar en el Paso 1 ("Sí, cancelar"), el sistema NO DEBE guardar ni crear nada y DEBE volver al listado "Mis Embarcaciones Registradas"
- **FR-006**: Al confirmar en el Paso 2 ("Salir"), el sistema DEBE descartar puerto, tarifa, servicios adicionales y foto, conservar el Borrador con los datos del Paso 1 y volver al listado "Mis Embarcaciones Registradas"
- **FR-007**: Al pulsar "Continuar registro" en cualquiera de los modales, el sistema DEBE cerrar el modal y mantener el formulario con lo ingresado intacto
- **FR-008**: El sistema NO DEBE mostrar más de un modal de confirmación por clics repetidos en "Cancelar"

### Key Entities

- **Embarcación (Borrador)**: Creada al pulsar "Siguiente" en el Paso 1 (definido en "Registrar Embarcación"). Cancelar en el Paso 1 no la crea; salir en el Paso 2 la conserva en estado Borrador.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: El 100% de los datos ingresados y no guardados se descartan al confirmar la cancelación
- **SC-002**: El 100% de los datos del Paso 1 se conservan al salir desde el Paso 2
