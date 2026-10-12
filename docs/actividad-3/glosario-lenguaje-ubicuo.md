# Glosario de lenguaje ubicuo — Rutify (Asignación de repartidor)

## Términos del dominio

| Término | Significado |
|---------|-------------|
| **Entrega** | Una instancia concreta de envío de un pedido confirmado, lista para ser asignada a un repartidor. |
| **Repartidor** | Persona responsable de trasladar físicamente una entrega hasta el cliente. |
| **Asignación** | Acción de vincular una entrega con un repartidor específico, protegiendo la regla de que no puede haber dos repartidores activos para la misma entrega. |
| **Ruta** | Secuencia de entregas asignadas a un repartidor, calculada por el módulo de ruteo. |
| **Capacidad de ruta** | Número máximo de entregas que un repartidor puede atender en su ruta actual antes de considerarse saturada. |
| **Estado de entrega** | Fase en la que se encuentra una entrega (por ejemplo: pendiente, asignada, en camino, entregada, fallida). |
| **Repartidor disponible** | Un repartidor cuya ruta actual no está saturada y puede recibir una nueva entrega asignada. |
| **Invariante** | Regla de negocio que debe cumplirse siempre, sin excepción, en cualquier estado del sistema. |

## Términos ambiguos que deben confirmarse con el negocio

- **"Disponible":** este término cambia de significado según el contexto. Para el módulo de Ruteo, "disponible" significa que el repartidor tiene capacidad libre en su ruta. Para el módulo de Inventario, "disponible" significa stock suficiente para un pedido. Hay que confirmar con el negocio que, dentro de este flujo de asignación, "disponible" se refiere **únicamente** a la capacidad del repartidor, no a inventario.
- **"Entrega activa":** no queda claro si una entrega en estado "en camino" sigue contando como activa para efectos de bloquear una reasignación, o si solo las entregas en estado "asignada" (aún no recogidas) aplican. Debe confirmarse con el negocio.
- **"Saturada":** el umbral exacto de cuántas entregas hacen que una ruta se considere saturada no está definido en el caso de negocio; se deja como parámetro configurable hasta que el negocio lo precise.
