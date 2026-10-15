
---

## 📄 Evidencia 3: `Entrega N°2/docs/architecture/adr/0003-estrategia-seguridad.md`

```markdown
# ADR-0003: Estrategia de Seguridad — JWT + RBAC/ABAC

## 📋 Información

| Campo | Valor |
| :--- | :--- |
| **ADR** | 0003 |
| **Título** | Estrategia de Seguridad: JWT + RBAC/ABAC |
| **Estado** | ✅ Aceptado |
| **Fecha** | Octubre 2026 |
| **Autores** | Milton Machado, Raúl Triana, Santiago Molina |
| **Proyecto** | MediConnect Plus |

---

## 📖 Contexto

MediConnect Plus debe proteger el acceso a datos clínicos y administrativos según el rol del usuario (Paciente, Médico, Administrativo). Se requiere:

- Autenticación segura.
- Autorización basada en roles y atributos.
- Protección de datos en tránsito y en reposo.
- Trazabilidad de accesos.

---

## ✅ Decisión

Se adopta **JWT (JSON Web Tokens)** para autenticación y **RBAC/ABAC** (Role-Based + Attribute-Based Access Control) para autorización.

### Componentes

| Componente | Responsabilidad |
| :--- | :--- |
| **JWT** | Tokens firmados con expiración configurable |
| **RBAC** | Permisos por rol (Paciente, Médico, Administrativo) |
| **ABAC** | Permisos adicionales por atributos |
| **HTTPS/TLS** | Cifrado en tránsito |
| **Cifrado en reposo** | PostgreSQL con cifrado nativo |

---

## ⚖️ Consecuencias

### Positivas ✅

- Autenticación stateless y escalable.
- Control granular de acceso.
- Trazabilidad de sesiones y accesos.
- Cumplimiento con RB01, RB02, RB06, RB13.

### Negativas ⚠️

- Complejidad en la gestión de tokens (refresh, revocación).
- Requiere configuración cuidadosa de RBAC/ABAC.

### Neutras 🔄

- Se debe definir política de expiración de tokens.
- Se debe implementar bloqueo por inactividad (15 min).

---

## 🔗 Referencias

- [Diagrama de Secuencia](../diagrams/secuencia-proceso-clave.puml)
- **Reglas de negocio:** RB01, RB02, RB06, RB13

---

## 📊 Diagrama Asociado

```plantuml
@startuml ADR-0003
!theme cerulean-outline
title ADR-0003: Estrategia de Seguridad

actor Usuario
participant "Aplicación Web" as WEB
participant "API MediConnect Plus" as API
participant "Autenticación" as AUTH
database "PostgreSQL" as DB

Usuario -> WEB: Credenciales
WEB -> API: POST /api/v1/auth/login
API -> AUTH: validarCredenciales()
AUTH -> DB: SELECT usuario
DB --> AUTH: usuario
AUTH -> AUTH: Generar JWT
AUTH --> API: token
API --> WEB: 200 OK + token
WEB --> Usuario: Sesión iniciada

note right of AUTH
  JWT + RBAC/ABAC
  para control de acceso
  por roles y atributos.
end note

@enduml