
---

## 📄 Evidencia 1: `Entrega N°2/docs/architecture/adr/0001-patron-arquitectonico.md`

```markdown
# ADR-0001: Patrón Arquitectónico Híbrido (Síncrono + Eventos)

## 📋 Información

| Campo | Valor |
| :--- | :--- |
| **ADR** | 0001 |
| **Título** | Patrón Arquitectónico Híbrido (Síncrono + Eventos) |
| **Estado** | ✅ Aceptado |
| **Fecha** | Octubre 2026 |
| **Autores** | Milton Machado, Raúl Triana, Santiago Molina |
| **Proyecto** | MediConnect Plus |

---

## 📖 Contexto

MediConnect Plus requiere que operaciones críticas (agendar cita, consultar HCE) tengan respuesta inmediata, mientras que procesos secundarios (notificaciones, integraciones con EPS/laboratorios, auditoría) no deben bloquear la operación principal.

### Problema

- Un enfoque **puramente síncrono** generaría acoplamiento y baja disponibilidad.
- Un enfoque **puramente asíncrono** complicaría la experiencia del usuario en operaciones críticas.
- Una falla en el proveedor de notificaciones no debería impedir agendar una cita.

---

## ✅ Decisión

Se adopta una **arquitectura híbrida**:

### Núcleo Transaccional Síncrono

| Componente | Responsabilidad |
| :--- | :--- |
| Aplicación Web | Interfaz de usuario |
| API MediConnect Plus | Lógica de negocio |
| PostgreSQL | Persistencia transaccional |
| Redis | Caché de consultas frecuentes |

### Capa Asíncrona Orientada a Eventos

| Componente | Responsabilidad |
| :--- | :--- |
| Broker de Eventos | Distribución de eventos |
| Servicio de Notificaciones | Envío de correos/SMS |
| Servicio de Integración | Comunicación con EPS y laboratorios |

**Regla:** Las operaciones críticas usan REST/JSON. Los procesos secundarios usan eventos publicados al Broker.

---

## ⚖️ Consecuencias

### Positivas ✅

- **Disponibilidad:** una falla en notificaciones no detiene la creación de citas.
- **Escalabilidad:** los consumidores de eventos escalan independientemente.
- **Modificabilidad:** nuevos consumidores sin modificar el publicador.
- **Trazabilidad:** los eventos permiten auditar acciones.

### Negativas ⚠️

- Mayor complejidad operativa (monitoreo del broker).
- Riesgo de duplicación de eventos (mitigado con idempotencia).
- Curva de aprendizaje para el equipo.

### Neutras 🔄

- Se requiere definir política de reintentos y colas muertas.
- La tecnología definitiva del broker (RabbitMQ vs Kafka) queda pendiente de validación grupal.

---

## 🔗 Referencias

- [Diagrama C4 Nivel 1](../diagrams/c4-contexto.puml)
- [Diagrama de Secuencia](../diagrams/secuencia-proceso-clave.puml)
- **Reglas de negocio:** RB08, RB09, RB16

---

## 📊 Diagrama Asociado

```plantuml
@startuml ADR-0001
!theme cerulean-outline
title ADR-0001: Comunicación Asíncrona mediante Broker de Eventos

actor Paciente
participant "Aplicación Web" as WEB
participant "API MediConnect Plus" as API
participant "Gestión de Citas" as CIT
database "PostgreSQL" as DB
participant "Publicador de Eventos" as EVT
queue "Broker de Eventos" as BUS
participant "Servicio de Notificaciones" as NOT
participant "Servicio de Integración" as INT

Paciente -> WEB: Agenda cita
WEB -> API: POST /api/v1/citas
API -> CIT: agendarCita(datos)
CIT -> DB: INSERT cita
DB --> CIT: OK
CIT -> EVT: publicarEvento(CitaAgendada)
EVT -> BUS: envía evento
CIT --> API: citaDTO
API --> WEB: 201 Created
WEB --> Paciente: Cita confirmada

BUS --> NOT: CitaAgendada (asíncrono)
BUS --> INT: CitaAgendada (asíncrono)
NOT -> NOT: Preparar notificación
INT -> INT: Sincronizar con EPS/Lab

note right of BUS
  La operación principal (crear cita)
  NO espera a que notificaciones
  o integración terminen.
end note

@enduml