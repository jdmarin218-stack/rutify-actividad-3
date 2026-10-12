# Modelo de dominio — Entidades y objetos de valor

## Entidades

Las entidades tienen identidad propia; dos entidades con los mismos atributos siguen siendo distintas si su identidad es diferente.

### Entrega
- **Identidad:** `EntregaId`
- **Atributos:** dirección de destino, estado actual, repartidor asignado (puede ser nulo).
- **Responsabilidad:** protege la invariante de que no puede tener dos repartidores activos al mismo tiempo.

### Repartidor
- **Identidad:** `RepartidorId`
- **Atributos:** nombre, ruta actual asignada, capacidad disponible.
- **Responsabilidad:** informa si tiene capacidad libre para recibir una nueva entrega.

### Ruta
- **Identidad:** `RutaId`
- **Atributos:** lista de entregas asignadas, capacidad máxima.
- **Responsabilidad:** determina si está saturada o tiene espacio disponible.

## Objetos de valor

Los objetos de valor no tienen identidad propia; se describen completamente por sus atributos y son inmutables.

| Objeto de valor | Atributos |
|---|---|
| **DireccionEntrega** | calle, ciudad, referencia |
| **CapacidadRuta** | número máximo de entregas permitidas |
| **EstadoEntrega** | uno de: Pendiente, Asignada, EnCamino, Entregada, Fallida |

## Estados y transiciones de la Entrega

Pendiente ──(se asigna repartidor)──> Asignada
Asignada ──(repartidor recoge el pedido)──> EnCamino
EnCamino ──(entrega exitosa)──> Entregada
EnCamino ──(entrega no exitosa)──> Fallida

**Reglas de transición:**
- Solo se puede pasar de `Pendiente` a `Asignada` si la entrega no tiene ya un repartidor activo.
- No se puede pasar directamente de `Pendiente` a `EnCamino` sin pasar por `Asignada`.
- Una entrega en estado `Entregada` o `Fallida` es un estado final: no admite más transiciones.
