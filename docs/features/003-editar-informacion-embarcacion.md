# Feature Specification: Editar Información Embarcación

**Created**: 2026-09-07
**Updated**: 2026-09-29

> **Alcance**: esta spec cubre únicamente el comportamiento de la edición de la información de una embarcación ya registrada: la pantalla de detalle como origen y destino del flujo, la pantalla de edición, el modal de descarte y el mensaje de confirmación de éxito. El control de acceso por propiedad y las restricciones generales de la cuenta no se redefinen aquí; "Propietario" se usa únicamente como el nombre del rol/actor que ejecuta las acciones.

## User Scenarios & Testing _(mandatory)_

### User Story 1 - Editar y guardar la información de una embarcación (Priority: P1)

El propietario accede a la pantalla de edición desde la pantalla de detalle de una de sus embarcaciones. El sistema muestra el formulario con la información actual precargada, permite modificar únicamente los campos permitidos (fotografía, nombre, capacidad máxima de pasajeros, tarifa base por hora, puerto de atraque y servicios adicionales) y muestra bloqueados la matrícula legal y el tipo de embarcación. Al pulsar **Guardar cambios**, el sistema aplica los cambios de inmediato, muestra un mensaje de confirmación con el botón **Aceptar** y, al aceptar, lleva al propietario a la pantalla de detalle con la información actualizada.

**Why this priority**: Es la funcionalidad central que permite al propietario mantener actualizada la información de su embarcación para ofrecerla y administrarla correctamente. Sin ella, los datos quedarían fijos desde el registro.

**Independent Test**: Puede probarse abriendo la edición de una embarcación en estado Disponible, modificando los campos permitidos, pulsando Guardar cambios y verificando que aparece el mensaje de confirmación y que, tras Aceptar, el detalle muestra la información actualizada.

**Acceptance Scenarios**:

1. **Scenario**: Pantalla de edición con información actual
   - **Given** el propietario está autenticado y abre la edición de una embarcación en estado Disponible
   - **When** se muestra la pantalla de edición
   - **Then** todos los campos editables aparecen precargados con sus valores actuales, la matrícula legal y el tipo de embarcación aparecen bloqueados (no editables), los servicios base Capitán y Combustible aparecen fijos y los servicios adicionales aparecen marcados según lo registrado

2. **Scenario**: Edición guardada correctamente
   - **Given** el propietario está en la pantalla de edición y todos los campos cumplen las reglas de validación
   - **When** modifica uno o más campos editables y pulsa Guardar cambios
   - **Then** el sistema aplica los cambios de inmediato, sin pedir confirmación previa, y muestra el mensaje "¡Cambios guardados!" con el botón Aceptar

3. **Scenario**: Regreso al detalle tras guardar
   - **Given** el sistema mostró el mensaje de confirmación de cambios guardados
   - **When** el propietario pulsa Aceptar
   - **Then** el sistema lo lleva a la pantalla de detalle de la embarcación con la información ya editada

4. **Scenario**: Edición de un solo campo conservando los demás
   - **Given** el propietario está en la pantalla de edición con los campos precargados
   - **When** modifica un único campo (por ejemplo, la tarifa base) y no toca los demás
   - **Then** el sistema guarda el nuevo valor y conserva sin cambios los valores de los demás campos

5. **Scenario**: Guardar sin haber modificado nada
   - **Given** el propietario está en la pantalla de edición y no ha modificado ningún campo
   - **When** pulsa Guardar cambios
   - **Then** el botón está habilitado y el sistema sigue el flujo normal de guardado (mensaje de confirmación y regreso al detalle)

6. **Scenario**: Edición de servicios adicionales
   - **Given** el propietario está en la pantalla de edición
   - **When** marca o desmarca servicios adicionales (Chalecos salvavidas, Nevera con hielo, Equipo de sonido, Equipo de pesca, Equipo de buceo), incluso dejando todos desmarcados, y pulsa Guardar cambios
   - **Then** el sistema guarda la selección; los servicios base Capitán y Combustible permanecen siempre incluidos y no se pueden quitar

7. **Scenario**: Edición del puerto de atraque
   - **Given** el propietario está en la pantalla de edición
   - **When** selecciona una nueva ubicación en el mapa interactivo y pulsa Guardar cambios
   - **Then** el sistema reemplaza la ubicación guardada (nombre, latitud y longitud) por la nueva seleccionada

8. **Scenario**: Reemplazo de la fotografía
   - **Given** el propietario está en la pantalla de edición
   - **When** pulsa "Cambiar fotografía" y selecciona una imagen válida
   - **Then** el sistema reemplaza la fotografía anterior por la nueva al guardar; la fotografía solo puede reemplazarse, no eliminarse

---

### User Story 2 - Restricción del botón Editar según el estado de la embarcación (Priority: P1)

El botón **Editar** se encuentra en la pantalla de detalle de la embarcación. El sistema lo habilita únicamente cuando la embarcación está en estado **Disponible** o **En Mantenimiento/Limpieza**. Cuando la embarcación está en estado **Reservada** o **En Navegación**, el botón aparece deshabilitado y no permite acceder a la pantalla de edición.

**Why this priority**: Evita que se modifique información de una embarcación que está en uso (reservada o navegando), lo que podría afectar reservas o servicios en curso.

**Independent Test**: Puede probarse abriendo la pantalla de detalle de una embarcación en cada estado (Disponible, En Mantenimiento/Limpieza, Reservada, En Navegación) y verificando que el botón Editar solo está habilitado en Disponible y En Mantenimiento/Limpieza.

**Acceptance Scenarios**:

1. **Scenario**: Botón Editar habilitado en estado Disponible
   - **Given** el propietario está en el detalle de una embarcación en estado Disponible
   - **When** se muestra la pantalla
   - **Then** el botón Editar aparece habilitado y, al pulsarlo, lleva a la pantalla de edición

2. **Scenario**: Botón Editar habilitado en estado En Mantenimiento/Limpieza
   - **Given** el propietario está en el detalle de una embarcación en estado En Mantenimiento/Limpieza
   - **When** se muestra la pantalla
   - **Then** el botón Editar aparece habilitado y, al pulsarlo, lleva a la pantalla de edición

3. **Scenario**: Botón Editar deshabilitado en estado Reservada
   - **Given** el propietario está en el detalle de una embarcación en estado Reservada
   - **When** se muestra la pantalla
   - **Then** el botón Editar aparece deshabilitado y no permite acceder a la pantalla de edición

4. **Scenario**: Botón Editar deshabilitado en estado En Navegación
   - **Given** el propietario está en el detalle de una embarcación en estado En Navegación
   - **When** se muestra la pantalla
   - **Then** el botón Editar aparece deshabilitado y no permite acceder a la pantalla de edición

5. **Scenario**: Editar no cambia el estado de la embarcación
   - **Given** una embarcación en estado Disponible o En Mantenimiento/Limpieza
   - **When** el propietario guarda una edición
   - **Then** la embarcación conserva el mismo estado operativo que tenía antes de editar

---

### User Story 3 - Validación de campos al editar (Priority: P1)

Mientras el propietario edita, el sistema valida cada campo editable. Si algún campo queda vacío o incumple su regla de validación, el sistema muestra un mensaje de error en el campo correspondiente y deshabilita el botón **Guardar cambios** hasta que todos los campos sean válidos. Los servicios adicionales son la única selección opcional: pueden quedar todos desmarcados.

**Why this priority**: Garantiza que la información de la embarcación nunca quede incompleta o inválida después de una edición.

**Independent Test**: Puede probarse borrando el nombre de la embarcación y verificando que aparece el mensaje en el campo y que Guardar cambios se deshabilita; luego escribiendo un valor válido y verificando que el botón se habilita de nuevo.

**Acceptance Scenarios**:

1. **Scenario**: Campo vacío bloquea el guardado
   - **Given** el propietario está en la pantalla de edición
   - **When** borra el contenido de un campo editable (por ejemplo, el nombre de la embarcación) y lo deja vacío
   - **Then** el sistema muestra un mensaje en ese campo (por ejemplo, "Este campo no puede estar vacío") y deshabilita el botón Guardar cambios

2. **Scenario**: Campo con valor inválido bloquea el guardado
   - **Given** el propietario está en la pantalla de edición
   - **When** ingresa un valor que incumple la regla del campo (capacidad mayor al máximo del tipo, tarifa negativa o cero, o fotografía en formato o tamaño no permitido)
   - **Then** el sistema muestra un mensaje de error en ese campo y deshabilita el botón Guardar cambios

3. **Scenario**: Corrección del campo habilita el guardado
   - **Given** un campo vacío o inválido tiene el mensaje de error visible y Guardar cambios está deshabilitado
   - **When** el propietario ingresa un valor válido y no quedan otros campos vacíos o inválidos
   - **Then** el mensaje desaparece y el botón Guardar cambios vuelve a habilitarse

4. **Scenario**: Servicios adicionales opcionales
   - **Given** el propietario está en la pantalla de edición y los demás campos son válidos
   - **When** desmarca todos los servicios adicionales
   - **Then** el botón Guardar cambios sigue habilitado y el sistema permite guardar, ya que los servicios base Capitán y Combustible permanecen incluidos

---

### User Story 4 - Descartar los cambios con el botón Descartar (Priority: P2)

Cuando el propietario pulsa **Descartar** en la pantalla de edición, el sistema muestra siempre el mensaje de confirmación "¿Descartar cambios?", incluso si no se modificó ningún campo. El mensaje ofrece dos opciones: **Seguir editando** y **Sí, descartar**.

**Why this priority**: Evita que el propietario pierda por accidente los cambios que realizó en la edición.

**Independent Test**: Puede probarse modificando un campo, pulsando Descartar y verificando el mensaje; luego eligiendo cada opción y comprobando el resultado.

**Acceptance Scenarios**:

1. **Scenario**: Mensaje de confirmación al descartar
   - **Given** el propietario está en la pantalla de edición, con o sin cambios realizados
   - **When** pulsa Descartar
   - **Then** el sistema muestra el mensaje "¿Descartar cambios?" indicando que las modificaciones sin guardar se perderán, con las opciones Seguir editando y Sí, descartar

2. **Scenario**: Seguir editando
   - **Given** el sistema muestra el mensaje de confirmación de descarte
   - **When** el propietario elige Seguir editando
   - **Then** el sistema cierra el mensaje y lo devuelve a la pantalla de edición conservando la información que había ingresado

3. **Scenario**: Sí, descartar
   - **Given** el sistema muestra el mensaje de confirmación de descarte
   - **When** el propietario elige Sí, descartar
   - **Then** el sistema elimina la información ingresada y lo devuelve a la pantalla de detalle de la embarcación con la información que estaba guardada anteriormente

---

### User Story 5 - Salir de la edición con el botón Volver (Priority: P2)

El propietario puede pulsar **Volver** en la pantalla de edición. Esta acción descarta los cambios no guardados y lleva de vuelta a la pantalla de detalle de la embarcación, sin mostrar el mensaje de confirmación de descarte.

**Why this priority**: Ofrece una salida rápida de la edición sin necesidad de pasar por el mensaje de confirmación.

**Independent Test**: Puede probarse modificando un campo, pulsando Volver y verificando que el sistema regresa al detalle con la información anterior, sin mostrar el mensaje de confirmación.

**Acceptance Scenarios**:

1. **Scenario**: Volver descarta los cambios sin confirmación
   - **Given** el propietario está en la pantalla de edición con cambios sin guardar
   - **When** pulsa Volver
   - **Then** el sistema descarta los cambios, no muestra el mensaje de confirmación y lo lleva a la pantalla de detalle con la información que estaba guardada anteriormente

2. **Scenario**: Volver sin cambios
   - **Given** el propietario está en la pantalla de edición sin haber modificado nada
   - **When** pulsa Volver
   - **Then** el sistema lo lleva a la pantalla de detalle de la embarcación

---

### Edge Cases

- **Cambio de estado durante la edición**: si el estado operativo de la embarcación cambia a uno no editable (por ejemplo, pasa a Reservada por una nueva reserva) mientras el propietario tiene abierta la pantalla de edición, el sistema revalida el estado al momento de guardar. Si ya no es Disponible ni En Mantenimiento/Limpieza, rechaza el guardado y muestra un mensaje indicando que la embarcación cambió de estado y ya no puede editarse.

- **Pérdida de conexión al guardar**: si la petición de guardado falla (por ejemplo, por pérdida de conexión), el sistema no guarda datos parciales, muestra un error y permite al propietario reintentar sin perder lo ya ingresado en el formulario.

- **Edición concurrente**: si el propietario tiene la misma embarcación abierta en dos sesiones o pestañas y guarda cambios desde ambas, el sistema aplica el último guardado exitoso (last-write-wins), sin combinar ni detectar conflicto entre los cambios de una sesión y otra.

- **Sesión expirada durante la edición**: si la sesión de autenticación del propietario expira mientras tiene abierta la pantalla de edición, el sistema rechaza el guardado y le solicita autenticarse nuevamente, sin perder lo ya ingresado en el formulario.

## Requirements _(mandatory)_

### Functional Requirements

- **FR-001**: El sistema DEBE mostrar el botón Editar en la pantalla de detalle de la embarcación
- **FR-002**: El sistema DEBE habilitar el botón Editar únicamente cuando la embarcación está en estado Disponible o En Mantenimiento/Limpieza
- **FR-003**: El sistema DEBE mostrar el botón Editar deshabilitado, sin permitir acceder a la pantalla de edición, cuando la embarcación está en estado Reservada o En Navegación
- **FR-004**: El sistema DEBE mostrar en la pantalla de edición la información actual de la embarcación, con cada campo editable precargado con su valor actual
- **FR-005**: El sistema DEBE permitir editar únicamente los siguientes campos: fotografía, nombre de la embarcación, capacidad máxima de pasajeros, tarifa base (por hora), puerto de atraque y servicios adicionales
- **FR-006**: El sistema DEBE mostrar bloqueados, sin posibilidad de edición, la matrícula legal y el tipo de embarcación
- **FR-007**: El sistema DEBE tratar como obligatorios el nombre, la capacidad máxima, la tarifa base, el puerto de atraque y la fotografía; los servicios adicionales son opcionales y pueden quedar todos desmarcados
- **FR-008**: El sistema DEBE aplicar las mismas reglas de validación que en el registro: la capacidad debe ser un número entero mayor a 0 que no exceda el máximo según el tipo (Lancha ≤12, Velero ≤15, Catamarán ≤30, Yate ≤40), la tarifa debe ser un valor numérico positivo, y la fotografía debe ser JPG o PNG con tamaño máximo de 5 MB
- **FR-009**: El sistema DEBE mostrar un mensaje de error en el campo correspondiente cuando un campo editable queda vacío o incumple su regla de validación
- **FR-010**: El sistema DEBE deshabilitar el botón Guardar cambios mientras exista algún campo editable vacío o inválido, y NO DEBE permitir guardar en ese caso
- **FR-011**: El sistema DEBE mantener habilitado el botón Guardar cambios cuando todos los campos son válidos, aunque el propietario no haya modificado ningún campo
- **FR-012**: El sistema DEBE aplicar los cambios de forma inmediata al pulsar Guardar cambios, sin pedir una confirmación previa
- **FR-013**: El sistema DEBE mostrar, tras guardar correctamente, el mensaje de confirmación "¡Cambios guardados!" con el botón Aceptar
- **FR-014**: El sistema DEBE llevar al propietario a la pantalla de detalle de la embarcación con la información actualizada cuando pulsa Aceptar en el mensaje de confirmación
- **FR-015**: El sistema DEBE mostrar el mensaje de confirmación "¿Descartar cambios?" cada vez que el propietario pulsa Descartar, con o sin cambios realizados, con las opciones Seguir editando y Sí, descartar
- **FR-016**: El sistema DEBE, al elegir Seguir editando, cerrar el mensaje y devolver al propietario a la pantalla de edición conservando la información ingresada
- **FR-017**: El sistema DEBE, al elegir Sí, descartar, eliminar la información ingresada y llevar al propietario a la pantalla de detalle con la información guardada anteriormente
- **FR-018**: El sistema DEBE, al pulsar Volver en la pantalla de edición, descartar los cambios no guardados y llevar al propietario a la pantalla de detalle sin mostrar el mensaje de confirmación de descarte
- **FR-019**: El sistema DEBE mantener los servicios base (Capitán y Combustible) siempre presentes y sin posibilidad de quitarlos; al editar los servicios solo se pueden modificar los servicios adicionales, seleccionados de un catálogo fijo (Chalecos salvavidas, Nevera con hielo, Equipo de sonido, Equipo de pesca, Equipo de buceo)
- **FR-020**: El sistema DEBE limitar la edición del puerto de atraque a la misma selección en un mapa interactivo utilizada en el registro, reemplazando la ubicación guardada por la nueva seleccionada
- **FR-021**: El sistema DEBE permitir únicamente reemplazar la fotografía de la embarcación, sin permitir eliminarla
- **FR-022**: El sistema NO DEBE modificar el estado operativo de la embarcación como resultado de una edición
- **FR-023**: El sistema DEBE revalidar el estado de la embarcación al momento de guardar y rechazar el guardado, con un mensaje explicativo, si ya no es Disponible ni En Mantenimiento/Limpieza
- **FR-024**: El sistema DEBE aplicar los cambios solo a los datos de la embarcación en este sistema, sin avisar ni actualizar a los módulos externos (reservas, liquidación)
- **FR-025**: El sistema DEBE permitir reintentar el guardado sin perder lo ya ingresado si la petición de guardado falla (por ejemplo, por pérdida de conexión)

### Key Entities

- **Embarcación**: Representa una embarcación registrada. Atributos relevantes para edición: nombre, capacidad máxima de pasajeros, tarifa base (por hora), puerto de atraque, servicios adicionales, fotografía, matrícula legal (bloqueada), tipo (bloqueado), estado operativo, fecha de creación. Estados operativos posibles: Borrador, Disponible, Reservado, En Navegación, En Mantenimiento/Limpieza.
- **Propietario**: Usuario autenticado que gestiona sus embarcaciones. Solo visualiza y edita las embarcaciones que le pertenecen.
- **Puerto de Atraque**: Ubicación geográfica de la embarcación (nombre, latitud, longitud), obtenida mediante selección en un mapa interactivo.


## Success Criteria _(mandatory)_

### Measurable Outcomes

- **SC-001**: El propietario puede guardar la edición de una embarcación en menos de 2 minutos
- **SC-002**: El 100% de las embarcaciones en estado Reservada o En Navegación muestran el botón Editar deshabilitado
- **SC-003**: El 100% de las embarcaciones en estado Disponible o En Mantenimiento/Limpieza muestran el botón Editar habilitado
- **SC-004**: Ninguna edición guardada deja un campo obligatorio vacío o con un valor que incumpla sus reglas de validación
- **SC-005**: Los servicios base (Capitán y Combustible) permanecen presentes en el 100% de las embarcaciones editadas
- **SC-006**: El 100% de los guardados exitosos muestran el mensaje de confirmación y, tras Aceptar, llevan al propietario a la pantalla de detalle con la información actualizada
- **SC-007**: El 100% de las veces que se pulsa Descartar se muestra el mensaje de confirmación, y el 100% de los descartes confirmados restauran en el detalle la información guardada anteriormente
- **SC-008**: El 100% de las ediciones dejan sin cambios el estado operativo de la embarcación
- **SC-009**: El 100% de los guardados son rechazados si el estado de la embarcación cambió a uno no editable entre la apertura del formulario y el guardado

## Registro de Cambios

| # | Corrección aplicada | Referencia |
|---|---|---|
| 1 | Punto de entrada al flujo documentado y estado operativo localizado en la pantalla de detalle | Revisión anterior, US1 Scenarios 1 y 8, SC-001 |
| 2 | Modal de descarte reescrito: "Sí, descartar" vuelve a la pantalla de detalle con la información original y "Seguir editando" permanece en el formulario con lo ingresado | Revisión anterior, US2 Scenarios 2 y 3 |
| 3 | Confirmado el destino tras guardar: al pulsar "Aceptar" en "¡Cambios guardados!" el sistema lleva a la pantalla de detalle con la información ya actualizada | Revisión anterior, US1 Scenario 2 |
| 4 | Reestructuración completa de la spec (2026-09-27): se eliminan los requisitos de literarismo de UI, se añade el gating del botón Editar por estado operativo y se incorporan los Edge Cases de fallo de guardado, edición concurrente y sesión expirada | FR-002, FR-010, FR-013, FR-014, FR-018 |
| 5 | Revisión 2026-09-29: se reescribe la spec. Los campos obligatorios pasan a bloquear el guardado (se elimina la conservación de valores al dejar un campo en blanco, FR-009/FR-010 anteriores), el puerto de atraque vuelve a editarse mediante mapa interactivo, se incorpora el botón Volver sin confirmación, la revalidación de estado al guardar, el reemplazo de fotografía y el catálogo fijo de servicios adicionales; se separa el gating del botón Editar en su propia user story y se añade la sección de supuestos | FR-002, FR-007, FR-010, FR-018, FR-020, FR-021, FR-023, FR-025, US2, US3, US4, US5, SC-002, SC-004, SC-009 |
