# 🏥 Arquitectura — MediConnect Plus

> **Entrega N°2 — Consolidación de Documentación Viva y Trazabilidad**
> Proyecto Integrador — Modelado y Diseño Arquitectónico con el Modelo C4

---

## 👥 Equipo de Arquitectura

| Nombre | Rol | Responsabilidad |
| :--- | :--- | :--- |
| **Milton César Machado Baneto** | Arquitecto de Software | Diseño C4, ADRs, integración |
| **Raúl Andrés Triana Ortega** | Arquitecto de Software | Seguridad, HL7 FHIR, ADRs |
| **Santiago Molina Maldonado** | Arquitecto de Software | Frontend, UX/UI, ADRs |

**Curso:** Modelado y Diseño Arquitectónico con el Modelo C4
**Institución:** [Nombre de la institución]
**Fecha:** Octubre 2026
**Entrega:** N°2 — Documentación Viva

---

## 📋 Tabla de Contenidos

1. [Visión Global](#-visión-global)
2. [Decisiones Arquitectónicas (ADRs)](#-decisiones-arquitectónicas-adrs)
3. [Diagramas PlantUML](#-diagramas-plantuml)
4. [Trazabilidad Completa](#-trazabilidad-completa)
5. [Mitigación de Deuda Técnica](#-mitigación-de-deuda-técnica)
6. [Onboarding](#-onboarding)
7. [Estructura del Repositorio](#-estructura-del-repositorio)

---

## 📖 Visión Global

**MediConnect Plus** es un Sistema de Gestión Hospitalaria diseñado para digitalizar la gestión de pacientes, médicos, citas, historia clínica electrónica, prescripciones, órdenes médicas y procesos administrativos.

### Arquitectura Híbrida

| Capa | Componentes | Propósito |
| :--- | :--- | :--- |
| **Núcleo Síncrono** | Aplicación Web, API, PostgreSQL, Redis | Operaciones críticas con respuesta inmediata |
| **Capa Asíncrona** | Broker de Eventos, Notificaciones, Integración | Procesos secundarios sin bloquear operación principal |
| **Integración** | REST/FHIR/Webhooks | Comunicación con EPS y laboratorios |
| **Seguridad** | HTTPS/TLS, JWT, RBAC/ABAC | Protección de datos clínicos |

### Principios de Diseño

- ✅ **Disponibilidad:** una falla en notificaciones no detiene la creación de citas.
- ✅ **Escalabilidad:** los consumidores de eventos escalan independientemente.
- ✅ **Interoperabilidad:** HL7 FHIR R4 para integración clínica.
- ✅ **Trazabilidad:** los eventos permiten auditar acciones.
- ✅ **Seguridad:** cifrado en reposo y en tránsito.

---

## 📝 Decisiones Arquitectónicas (ADRs)

| ADR | Título | Estado | Enlace |
| :--- | :--- | :--- | :--- |
| **ADR-0001** | Patrón Arquitectónico Híbrido (Síncrono + Eventos) | ✅ **Aceptado** | [Ver](Docs/Arquitectura/Adr/0001-patron-arquitectonico.md) |
| **ADR-0002** | Sistema de Persistencia: PostgreSQL vs MongoDB | 🟡 **Propuesto** | [Ver](Docs/Arquitectura/Adr/0002-sistema-de-persistencia.md) |
| **ADR-0003** | Estrategia de Seguridad: JWT + RBAC/ABAC | ✅ **Aceptado** | [Ver](Docs/Arquitectura/Adr/0003-estrategia-seguridad.md) |

---

## 📐 Diagramas PlantUML

| Diagrama | Archivo | Descripción | ADR Relacionado |
| :--- | :--- | :--- | :--- |
| **C4 Nivel 1 — Contexto** | [`c4-contexto.puml`](Docs/Arquitectura/Diagrams/c4-contexto.puml) | Sistema central, actores y sistemas externos | ADR-0001, ADR-0002 |
| **Secuencia — Proceso Clave** | [`secuencia-proceso-clave.puml`](Docs/Arquitectura/Diagrams/secuencia-proceso-clave.puml) | Flujo completo de agendar cita con eventos asíncronos | ADR-0001, ADR-0003 |

### Cómo visualizar los diagramas

1. Abre [PlantUML Online](https://www.plantuml.com/plantuml/uml/)
2. Copia el contenido de cada archivo `.puml`
3. Pégalo en el editor y visualiza el diagrama

---

## 🔗 Trazabilidad Completa

| Necesidad | Decisión (ADR) | Diseño | Diagrama |
| :--- | :--- | :--- | :--- |
| Operaciones críticas deben ser rápidas y confiables | ADR-0001: Arquitectura híbrida | Núcleo síncrono + Capa de eventos | [`c4-contexto.puml`](Docs/Arquitectura/Diagrams/c4-contexto.puml) |
| Datos clínicos requieren integridad y cifrado | ADR-0002: PostgreSQL | Base de datos transaccional | [`c4-contexto.puml`](Docs/Arquitectura/Diagrams/c4-contexto.puml) |
| Acceso seguro por roles | ADR-0003: JWT + RBAC/ABAC | Autenticación y Autorización | [`secuencia-proceso-clave.puml`](Docs/Arquitectura/Diagrams/secuencia-proceso-clave.puml) |
| Notificaciones no deben bloquear operaciones | ADR-0001: Broker de Eventos | Publicador + Broker + Notificaciones | [`secuencia-proceso-clave.puml`](Docs/Arquitectura/Diagrams/secuencia-proceso-clave.puml) |
| Trazabilidad de acciones | ADR-0001: Eventos | Registro de eventos | [`secuencia-proceso-clave.puml`](Docs/Arquitectura/Diagrams/secuencia-proceso-clave.puml) |

---

## 🛡️ Mitigación de Deuda Técnica

| ADR | Deuda Técnica Mitigada | Impacto |
| :--- | :--- | :--- |
| **ADR-0001** | Desacopla notificaciones e integraciones del núcleo transaccional | Evita que una falla externa bloquee operaciones clínicas |
| **ADR-0002** | Garantiza integridad ACID para datos clínicos | Evita corrupción o inconsistencias en HCE |
| **ADR-0003** | Centraliza autenticación y autorización por roles | Evita accesos no autorizados y facilita auditoría |

---

## 🚀 Onboarding

### Para nuevos desarrolladores

1. **Lee este README** para entender la visión general.
2. **Revisa los ADRs** en orden (0001 → 0002 → 0003) para conocer las decisiones.
3. **Consulta los diagramas PlantUML** para visualizar la arquitectura.
4. **Revisa el código fuente** en el repositorio principal.

### Para asistentes de IA

- Los ADRs usan numeración secuencial (0001, 0002, 0003...).
- Los estados de ADR son: `Propuesto`, `Aceptado`, `Rechazado`, `Obsoleto`.
- Cada ADR enlaza a sus diagramas relacionados.
- Los diagramas están en formato PlantUML (texto plano, versionable en Git).

### Convenciones del Proyecto

| Elemento | Convención |
| :--- | :--- |
| ADRs | `000X-titulo-descriptivo.md` |
| Diagramas | `nombre-diagrama.puml` |
| Estados ADR | Propuesto, Aceptado, Rechazado, Obsoleto |
| Ramas Git | `feature/`, `fix/`, `docs/` |

---

## 📁 Estructura del Repositorio

```text
MediConnect Plus/
├── README.md
└── Docs/
    └── Arquitectura/
        ├── Adr/
        │   ├── 0001-patron-arquitectonico.md
        │   ├── 0002-sistema-de-persistencia.md
        │   └── 0003-estrategia-seguridad.md
        └── Diagrams/
            ├── c4-contexto.puml
            └── secuencia-proceso-clave.puml
```

## 📚 Referencias

- Modelo C4
- Architecture Decision Records
- PlantUML
- HL7 FHIR
