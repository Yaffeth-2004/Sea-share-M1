# Feature Specification: Registrar Embarcación
Created: 2026-09-05

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Registrar información básica de la embarcación (Priority: P1)

El propietario ingresa al sistema y completa un formulario con los datos básicos de su embarcación: nombre, matrícula legal, tipo y capacidad máxima de pasajeros. Al enviar el formulario, la embarcación queda guardada en el sistema en estado Borrador, hasta que se complete el resto de la información (puerto de atraque, tarifa y foto).

**Why this priority**: Es el primer paso del registro sin el cual no se puede continuar con el resto de la información.

**Independent Test**: Puede probarse ingresando los datos básicos y verificando que la embarcación queda guardada en estado Borrador.

**Acceptance Scenarios**:

1. **Scenario**: Datos básicos registrados correctamente
   - **Given** el propietario está autenticado en el sistema
   - **When** completa el formulario con nombre, matrícula válida (formato CP-NN-NNNN-X), tipo (Velero/Lancha/Yate/Catamarán) y capacidad de pasajeros
   - **Then** la embarcación se guarda en estado Borrador, asociada a ese propietario, y puede continuar agregando más información

2. **Scenario**: Matrícula con formato inválido
   - **Given** el propietario está en el formulario de registro
   - **When** ingresa una matrícula que no sigue el formato CP-NN-NNNN-X
   - **Then** el sistema muestra un error con el formato correcto

3. **Scenario**: Campos obligatorios vacíos
   - **Given** el propietario está en el formulario de registro
   - **When** intenta guardar sin completar todos los campos
   - **Then** el sistema resalta qué campos faltan y no permite guardar

4. **Scenario**: Matrícula ya registrada por otro propietario
   - **Given** el propietario está en el formulario de registro
   - **When** ingresa una matrícula que ya existe en el sistema
   - **Then** el sistema muestra un error indicando que la matrícula ya está registrada y no permite guardar

5. **Scenario**: Capacidad de pasajeros excede el máximo según el tipo de embarcación
   - **Given** el propietario está en el formulario de registro
   - **When** ingresa una capacidad de pasajeros mayor al máximo permitido para el tipo seleccionado (Lancha: 12, Velero: 15, Catamarán: 30, Yate: 40)
   - **Then** el sistema muestra un error indicando el máximo permitido para ese tipo de embarcación

6. **Scenario**: Servicios base incluidos automáticamente
   - **Given** el propietario registra una nueva embarcación
   - **When** el sistema crea el registro
   - **Then** Capitán y Combustible quedan incluidos automáticamente, sin que el propietario deba seleccionarlos

7. **Scenario**: Borrador vencido se elimina automáticamente
   - **Given** una embarcación en estado Borrador que superó los 10 días desde su creación sin completar la información restante
   - **When** el sistema verifica los borradores pendientes
   - **Then** el registro se elimina completamente y la matrícula queda disponible para un nuevo registro

---

### User Story 2 - Completar información de puerto, tarifa, servicios y foto (Priority: P1)

Después de guardar los datos básicos, el propietario busca el puerto de atraque en un mapa interactivo y coloca una etiqueta en la ubicación correspondiente (el sistema extrae automáticamente la latitud y longitud), define la tarifa base de alquiler, selecciona los servicios adicionales incluidos sin costo extra, y sube una foto de la embarcación. Cuando toda esta información está completa, la embarcación pasa de estado Borrador a Disponible.

**Why this priority**: Sin esta información la embarcación no puede ofrecerse a los clientes ni ubicarse ni cotizarse.

**Independent Test**: Puede probarse completando esta información sobre una embarcación con el registro inicializado (en estado Borrador) y verificando que pasa de Borrador a Disponible.

**Acceptance Scenarios**:

1. **Scenario**: Ubicación del puerto seleccionada en el mapa
   - **Given** el propietario está en el segundo paso del registro
   - **When** busca el nombre del puerto en el mapa y coloca una etiqueta en la ubicación correspondiente
   - **Then** el sistema extrae y guarda automáticamente la latitud y longitud de esa ubicación y las asocia como puerto de atraque de esa embarcación

2. **Scenario**: Puerto no encontrado en la búsqueda
   - **Given** el propietario está buscando el puerto en el mapa
   - **When** el nombre buscado no arroja resultados
   - **Then** el sistema permite colocar la etiqueta manualmente en el mapa y extraer las coordenadas de ese punto

3. **Scenario**: Tarifa base definida
   - **Given** el propietario está en el segundo paso del registro
   - **When** ingresa un valor de tarifa base
   - **Then** el sistema guarda la tarifa asociada a la embarcación

4. **Scenario**: Tarifa inválida
   - **Given** el propietario está en el segundo paso del registro
   - **When** ingresa una tarifa negativa o un valor no numérico
   - **Then** el sistema muestra un error indicando que la tarifa debe ser un valor numérico positivo

5. **Scenario**: Selección de servicios adicionales
   - **Given** el propietario está en el segundo paso del registro
   - **When** selecciona uno o más servicios adicionales que su embarcación realmente tiene (Chalecos salvavidas, Equipo de pesca, Equipo de sonido, Nevera con hielo, Equipo de buceo)
   - **Then** esos servicios quedan asociados a la embarcación, además de los servicios base

6. **Scenario**: Ningún servicio adicional seleccionado
   - **Given** el propietario está en el segundo paso del registro
   - **When** no selecciona ningún servicio adicional
   - **Then** el sistema permite continuar; la embarcación queda únicamente con los servicios base (Capitán y Combustible)

7. **Scenario**: Foto con formato no permitido
   - **Given** el propietario está subiendo la fotografía
   - **When** intenta subir un archivo que no es JPG o PNG
   - **Then** el sistema muestra un error indicando los formatos aceptados

8. **Scenario**: Foto excede el tamaño máximo
   - **Given** el propietario está subiendo la fotografía
   - **When** la imagen supera los 5 MB
   - **Then** el sistema muestra un error indicando el tamaño máximo permitido

9. **Scenario**: Embarcación pasa de Borrador a Disponible
   - **Given** el propietario completó datos básicos, puerto de atraque, tarifa y foto sobre su embarcación en estado Borrador
   - **When** guarda esa información
   - **Then** el sistema cambia el estado de la embarcación de Borrador a Disponible

---

### Edge Cases

- **Pérdida de conexión durante el envío de un paso**: el sistema no guarda datos parciales de ese envío; si falla, se muestra un error y el propietario puede reintentar sin perder lo ya escrito en el formulario.
- **Solicitudes simultáneas con la misma matrícula (condición de carrera)**: la unicidad de la matrícula se garantiza a nivel de base de datos; si dos registros llegan casi al mismo tiempo, el segundo falla con el mismo mensaje de matrícula duplicada.
- **Registro en Borrador abandonado**: se mantiene guardado por un plazo máximo de 10 días desde su creación; si se cumple el plazo sin completar la información, el borrador se elimina automáticamente y su matrícula queda libre para un nuevo registro.
- **Nombre de embarcación duplicado**: se permite; el nombre no es un identificador único, solo la matrícula lo es.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: El sistema DEBE mostrar un formulario con los campos: nombre, matrícula legal, tipo de embarcación y capacidad máxima de pasajeros
- **FR-002**: El sistema DEBE validar que la matrícula legal tenga el formato CP-NN-NNNN-X donde CP es la sigla de Capitanía de Puerto, NN el número de capitanía (00-99), NNNN el número consecutivo (0000-9999) y X una letra mayúscula
- **FR-003**: El sistema DEBE ofrecer como opciones de tipo de embarcación: Velero, Lancha, Yate, Catamarán
- **FR-004**: El sistema DEBE validar que la capacidad máxima de pasajeros sea un número entero mayor a 0 y no exceda el máximo definido según el tipo de embarcación: Lancha ≤12, Velero ≤15, Catamarán ≤30, Yate ≤40
- **FR-005**: El sistema DEBE permitir buscar el puerto de atraque mediante un mapa interactivo y colocar una etiqueta en la ubicación seleccionada
- **FR-006**: El sistema DEBE extraer y guardar automáticamente la latitud y longitud a partir de la etiqueta colocada en el mapa
- **FR-007**: El sistema DEBE permitir colocar la etiqueta manualmente en el mapa cuando la búsqueda del nombre del puerto no arroje resultados
- **FR-008**: El sistema DEBE permitir ingresar una tarifa base de alquiler, definida libremente por el propietario
- **FR-009**: El sistema DEBE validar que la tarifa base sea un valor numérico positivo
- **FR-010**: El sistema DEBE asociar automáticamente Capitán y Combustible como servicios base incluidos en toda embarcación registrada, sin que el propietario deba seleccionarlos
- **FR-011**: El sistema DEBE permitir seleccionar servicios adicionales, sin costo extra para el arrendatario, de una lista: Chalecos salvavidas, Equipo de pesca, Equipo de sonido, Nevera con hielo, Equipo de buceo
- **FR-012**: El sistema DEBE permitir guardar la embarcación sin que se haya seleccionado ningún servicio adicional (los servicios adicionales son opcionales; los servicios base siempre están presentes)
- **FR-013**: El sistema DEBE permitir subir una fotografía de la embarcación en formato JPG o PNG con tamaño máximo de 5 MB
- **FR-014**: El sistema DEBE crear toda embarcación en estado Borrador al guardar los datos básicos por primera vez
- **FR-015**: El sistema DEBE guardar la embarcación con la información disponible en cada paso, sin exigir que esté completa desde el primer envío
- **FR-016**: El sistema DEBE cambiar el estado de la embarcación de Borrador a Disponible únicamente cuando estén completos: datos básicos, puerto de atraque, tarifa y foto (los servicios base ya están incluidos automáticamente; los adicionales son opcionales)
- **FR-017**: El sistema DEBE validar que los campos obligatorios de cada paso estén completos antes de permitir guardar ese paso
- **FR-018**: El sistema DEBE confirmar el registro exitoso de cada paso mostrando un mensaje al propietario
- **FR-019**: El sistema DEBE almacenar la fecha y hora de la creación inicial del registro
- **FR-020**: El sistema DEBE rechazar el registro de una matrícula que ya exista en el sistema, incluyendo solicitudes simultáneas (garantizado mediante restricción de unicidad a nivel de base de datos), mostrando un mensaje específico al propietario
- **FR-021**: El sistema NO DEBE exigir que el nombre de la embarcación sea único entre distintos propietarios
- **FR-022**: El sistema NO DEBE guardar datos parciales si la petición de envío de un paso falla (ej. pérdida de conexión), permitiendo al propietario reintentar sin perder lo ya ingresado en el formulario
- **FR-023**: El sistema DEBE permitir al propietario listar y retomar sus registros que estén en estado Borrador en cualquier momento, dentro del plazo de los 10 días posteriores a su creación
- **FR-024**: El sistema DEBE asociar automáticamente la embarcación registrada al propietario autenticado que realiza el registro
- **FR-025**: El sistema DEBE eliminar automáticamente todo registro en estado Borrador que supere los 10 días desde su creación sin haberse completado
- **FR-026**: El sistema DEBE liberar de forma automática la matrícula asociada a un borrador eliminado para permitir su reutilización en nuevos registros

### Key Entities

- **Embarcación**: Representa una embarcación registrada. Atributos: nombre, matrícula legal (única), tipo, capacidad máxima de pasajeros, tarifa base, fecha de registro, estado. Pertenece a un único propietario (quien la registró). Ciclo de vida del estado: Borrador (recién creada, información incompleta) → Disponible (información completa, ofrecida para alquiler) → Reservado → En Navegación → En Mantenimiento/Limpieza (pudiendo volver a Disponible).
- **Puerto de Atraque**: Ubicación geográfica de la embarcación, obtenida mediante selección en un mapa interactivo. Atributos: nombre del puerto, latitud, longitud
- **Servicio**: Inclusiones sin costo adicional para el arrendatario, cubiertas dentro del precio del alquiler. Se dividen en dos categorías: base (Capitán, Combustible), asignados automáticamente a toda embarcación; y adicionales (Chalecos salvavidas, Equipo de pesca, Equipo de sonido, Nevera con hielo, Equipo de buceo), que el propietario selecciona solo si su embarcación efectivamente los tiene
- **Propietario**: Usuario que registra la embarcación (el registro de su cuenta se define en otra especificación). Relación: un propietario puede registrar múltiples embarcaciones

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: El propietario puede completar el registro de una embarcación (todos los pasos) en menos de 3 minutos
- **SC-002**: El 100% de las matrículas registradas cumplen con el formato CP-NN-NNNN-X y son únicas en el sistema
- **SC-003**: Las fotografías subidas son válidas (formato y tamaño correcto) en el 95% de los intentos
- **SC-004**: No se permiten registros con campos obligatorios vacíos
- **SC-005**: La embarcación solo pasa de Borrador a Disponible cuando tiene toda la información obligatoria completa
- **SC-006**: El 100% de las embarcaciones en estado Borrador que superan el límite de 10 días sin completarse se eliminan automáticamente y liberan su matrícula