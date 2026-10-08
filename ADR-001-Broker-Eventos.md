# ADR-001: Uso de Broker de Eventos para Comunicación Asíncrona

## Estado
Aceptado

## Contexto
MediConnect Plus requiere que operaciones secundarias como notificaciones, integraciones con EPS y laboratorios, y auditoría no bloqueen las operaciones clínicas críticas (agendar cita, consultar HCE). Actualmente, si el proveedor de notificaciones falla, la creación de una cita podría verse afectada. Se necesita una estrategia que desacople estos procesos.

## Decisión
Se decide incorporar un **Broker de Eventos** (RabbitMQ o Kafka, pendiente de validación) como mecanismo de comunicación asíncrona entre la API MediConnect Plus y los servicios secundarios (Notificaciones, Integración). Las operaciones críticas permanecen síncronas vía REST/JSON.

## Consecuencias
### Positivas
- Mayor disponibilidad: una falla en notificaciones no detiene la creación de citas.
- Escalabilidad: los consumidores de eventos escalan independientemente.
- Modificabilidad: nuevos consumidores sin modificar el publicador.
- Trazabilidad: los eventos permiten auditar acciones.

### Negativas
- Mayor complejidad operativa (monitoreo del broker).
- Riesgo de duplicación de eventos (mitigado con idempotencia).
- Curva de aprendizaje para el equipo.

### Neutras
- Se requiere definir política de reintentos y colas muertas.
- La tecnología definitiva (RabbitMQ vs Kafka) queda pendiente de validación grupal.