# Invariantes y escenarios de aceptación

## Invariantes

1. Una entrega no puede tener dos repartidores activos al mismo tiempo.
2. Una entrega no puede asignarse a un repartidor si la ruta de ese repartidor ya está saturada.
3. Una entrega solo puede pasar de `Pendiente` a `Asignada`; no puede saltarse estados.
4. Un repartidor no puede tener asignada una entrega que ya está en estado `Entregada` o `Fallida`.

## Escenarios de aceptación (Dado-Cuando-Entonces)

### Escenario 1 — Asignación exitosa

**Dado** que una entrega está en estado `Pendiente` y no tiene repartidor asignado,
**Cuando** se solicita asignar un repartidor que tiene capacidad disponible en su ruta,
**Entonces** la entrega pasa a estado `Asignada` y queda vinculada a ese repartidor.

### Escenario 2 — Rechazo por entrega ya asignada

**Dado** que una entrega ya tiene un repartidor activo asignado,
**Cuando** se intenta asignarle un segundo repartidor,
**Entonces** la operación se rechaza y la entrega conserva su repartidor original.

### Escenario 3 — Rechazo por ruta saturada

**Dado** que un repartidor tiene su ruta al límite de capacidad,
**Cuando** se intenta asignarle una nueva entrega,
**Entonces** la operación se rechaza y se informa que el repartidor no tiene capacidad disponible.

### Escenario 4 — Entrega inexistente

**Dado** que se solicita asignar un repartidor a una entrega que no existe en el sistema,
**Cuando** se ejecuta la operación de asignación,
**Entonces** el sistema rechaza la operación indicando que la entrega no fue encontrada.

### Escenario 5 — Transición inválida de estado

**Dado** que una entrega está en estado `Entregada`,
**Cuando** se intenta asignarle un repartidor,
**Entonces** la operación se rechaza porque el estado es final y no admite más transiciones.
