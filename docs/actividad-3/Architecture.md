# Architecture.md — Rutify

## Propósito de este documento

Este archivo documenta la arquitectura del flujo de **asignación de repartidor a una entrega**, sus límites y las decisiones de diseño tomadas, para que cualquier persona que llegue al proyecto entienda por qué el sistema se construyó de esta manera.

## Resumen

Rutify sigue un enfoque de **monolito modular** (decisión heredada de la Actividad 2), organizado internamente por dominios con límites claros, siguiendo principios de Domain-Driven Design y arquitectura de puertos y adaptadores.

## Estructura del código

```
/src
  /Domain          → Entidades, objetos de valor, invariantes y reglas (Entrega, Repartidor, Ruta)
  /Application      → Casos de uso, DTOs, contratos y puertos (AsignarRepartidorUseCase)
  /Infrastructure   → Repositorios, APIs externas, notificaciones y adaptadores concretos
/tests              → Pruebas unitarias del dominio y pruebas de integración del flujo
```

## Principios aplicados

- **Inversión de dependencias:** las capas internas (`Domain`, `Application`) no dependen de infraestructura. La infraestructura depende de los puertos definidos en `Application`.
- **SOLID**, en particular responsabilidad única (cada entidad protege sus propias reglas) y segregación de interfaces (puertos pequeños y específicos: `IEntregaRepository`, `IDisponibilidadRepartidor`).
- **Separación de contextos:** el Contexto de Entregas no conoce los detalles internos del Contexto de Ruteo; solo consume una respuesta a través de un puerto.

## Límites de esta actividad

En esta etapa (Actividad 3) se evalúa el **diseño** del dominio y su justificación. No se exige implementar persistencia real ni la integración real con el Contexto de Ruteo — esos adaptadores concretos (base de datos, API) se construyen en la Actividad 4.

## Documentos relacionados

- [Glosario de lenguaje ubicuo](docs/actividad-3/glosario-lenguaje-ubicuo.md)
- [Modelo de dominio](docs/actividad-3/modelo-dominio.md)
- [Invariantes y escenarios de aceptación](docs/actividad-3/invariantes-escenarios.md)
- [Contextos delimitados](docs/actividad-3/contextos-delimitados.md)
- [Casos de uso, puertos y adaptadores](docs/actividad-3/casos-de-uso-puertos-adaptadores.md)
- [Diagrama C4](docs/actividad-3/diagrama-c4.md)
- [ADR de decisiones de diseño](docs/actividad-3/adr-decision-diseno.md)
