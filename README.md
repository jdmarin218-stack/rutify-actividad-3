# Rutify — Diseño y dominio

## Flujo de negocio elegido

**Asignar un repartidor a una entrega.**

Cuando una entrega está lista para salir (pedido confirmado, ruta calculada), el sistema debe asignarle un repartidor disponible. Este flujo es crítico porque protege una regla de negocio central: una entrega no puede quedar con dos repartidores activos al mismo tiempo, ni asignarse a un repartidor cuya ruta ya está saturada.

## Actores

- **Operador logístico:** dispara o confirma la asignación de un repartidor a una entrega (puede ser manual o automática según disponibilidad).
- **Repartidor:** recibe la asignación y queda responsable de la entrega.
- **Sistema de ruteo:** informa si la ruta del repartidor tiene capacidad disponible.
- **Cliente:** recibe la notificación de que su pedido ya tiene repartidor asignado (efecto indirecto del flujo).

## Propósito

Garantizar que cada entrega quede asignada a un único repartidor válido y disponible, protegiendo la invariante de negocio y dejando trazabilidad de la decisión, sin acoplar esta lógica a la base de datos ni a servicios externos de ubicación o mapas.

## Resultado esperado

- La entrega queda con un repartidor asignado y su estado se actualiza.
- Si la entrega ya tiene un repartidor activo, la operación se rechaza explícitamente (se protege la invariante).
- La decisión queda persistida a través de un repositorio, sin que la lógica de negocio conozca detalles de infraestructura.

---

*(El glosario de lenguaje ubicuo, el modelo de dominio, las invariantes, los contextos delimitados, los casos de uso con puertos y adaptadores, el diagrama C4 y el ADR están en `/docs/actividad-3`.)*
