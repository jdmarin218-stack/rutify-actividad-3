# Contextos delimitados — Rutify

## Contextos involucrados en este flujo

### Contexto de Entregas
Dueño de la entidad `Entrega` y de las reglas sobre asignación de repartidor. Es el contexto principal de este flujo.

### Contexto de Ruteo
Dueño de la entidad `Repartidor` y `Ruta`. Sabe si un repartidor tiene capacidad disponible, pero no conoce los detalles del pedido ni del cliente.

### Contexto de Pedidos
Dueño del pedido original y del cliente. Entregas se origina a partir de un pedido confirmado en este contexto, pero una vez creada la Entrega, Pedidos no participa en la asignación del repartidor.

### Contexto de Notificaciones
Recibe el resultado de la asignación (éxito o rechazo) para avisar al cliente, pero no participa en la decisión.

## Qué información intercambian

| De → A | Información que viaja |
|--------|------------------------|
| Pedidos → Entregas | Datos mínimos para crear la entrega: identificador de pedido, dirección de destino. |
| Entregas → Ruteo | Solicitud de disponibilidad: "¿este repartidor tiene capacidad?" (no envía detalles del pedido ni del cliente). |
| Ruteo → Entregas | Respuesta simple: disponible / no disponible (sin exponer el detalle interno de cómo se calcula la capacidad). |
| Entregas → Notificaciones | Resultado de la asignación (entrega asignada / rechazada) y el identificador de la entrega. |

## Regla de límite de contexto

El Contexto de Entregas **no debe conocer** cómo Ruteo calcula la capacidad de un repartidor (algoritmo interno, umbral de saturación, etc.). Solo consume una respuesta booleana a través de un puerto (`IDisponibilidadRepartidor` o similar), manteniendo el acoplamiento mínimo entre contextos.
