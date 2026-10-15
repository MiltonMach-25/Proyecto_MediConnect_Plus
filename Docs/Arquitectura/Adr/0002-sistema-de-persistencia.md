
---

## 📄 Evidencia 2: `Entrega N°2/docs/architecture/adr/0002-sistema-de-persistencia.md`

```markdown
# ADR-0002: Sistema de Persistencia — PostgreSQL vs MongoDB

## 📋 Información

| Campo | Valor |
| :--- | :--- |
| **ADR** | 0002 |
| **Título** | Sistema de Persistencia: PostgreSQL vs MongoDB |
| **Estado** | 🟡 Propuesto |
| **Fecha** | Octubre 2026 |
| **Autores** | Milton Machado, Raúl Triana, Santiago Molina |
| **Proyecto** | MediConnect Plus |

---

## 📖 Contexto

MediConnect Plus maneja datos clínicos y administrativos que requieren:

- Integridad transaccional (ACID).
- Relaciones complejas (pacientes, citas, HCE, órdenes).
- Cifrado en reposo.
- Cumplimiento de estándares clínicos (HL7 FHIR).

### Alternativas Evaluadas

| Alternativa | Tipo | Ventajas | Desventajas |
| :--- | :--- | :--- | :--- |
| **PostgreSQL** | Relacional | ACID, relaciones, madurez | Menor flexibilidad de esquema |
| **MongoDB** | NoSQL | Flexibilidad de esquema | Menor garantía ACID |

---

## ✅ Decisión Propuesta

Se propone **PostgreSQL** como base de datos principal, **sujeta a validación grupal**.

### Justificación

| Criterio | PostgreSQL | MongoDB |
| :--- | :--- | :--- |
| **ACID** | ✅ Garantizado | ⚠️ Limitado |
| **Relaciones** | ✅ Nativo | ❌ Manual |
| **Cifrado en reposo** | ✅ Nativo | ✅ Soportado |
| **HL7 FHIR** | ✅ Mejor integración | ⚠️ Menos maduro |
| **Madurez** | ✅ Amplia comunidad | ✅ Buena comunidad |

---

## ⚖️ Consecuencias

### Positivas ✅

- Integridad garantizada para datos clínicos.
- Mejor soporte para consultas relacionales complejas.
- Cifrado en reposo nativo.

### Negativas ⚠️

- Menor flexibilidad de esquema que MongoDB.
- Requiere migraciones controladas.

### Neutras 🔄

- Decisión pendiente de validación grupal.
- Redis se mantiene como caché complementario.

---

## 🔗 Referencias

- [Diagrama C4 Nivel 1](../diagrams/c4-contexto.puml)
- **Reglas de negocio:** RB11, RB13, RB15

---

## 📊 Diagrama Asociado

```plantuml
@startuml ADR-0002
!theme cerulean-outline
title ADR-0002: Sistema de Persistencia

rectangle "MediConnect Plus" {
  rectangle "API MediConnect Plus"
  database "PostgreSQL" as DB
  database "Redis" as CACHE
}

rectangle "Alternativa Descartada" {
  database "MongoDB" as MONGO
}

API --> DB : SQL (cifrado)
API --> CACHE : Consulta frecuente
MONGO -[#red]-> DB : ❌ Descartado

note right of DB
  PostgreSQL garantiza ACID
  para datos clínicos.
end note

@enduml