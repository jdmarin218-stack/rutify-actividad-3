# ADR-002: Diseño del dominio para la asignación de repartidor

## Estado
Aceptado

## Contexto

Rutify necesita modelar el flujo de asignación de un repartidor a una entrega, protegiendo la invariante de que una entrega no puede tener dos repartidores activos simultáneamente, y sin acoplar la lógica de negocio a la base de datos ni a los detalles internos del módulo de Ruteo.

## Decisión

Se modela `Entrega` como una entidad de dominio que protege su propia invariante (la asignación de repartidor ocurre dentro de un método de la propia entidad, no desde fuera). El caso de uso `AsignarRepartidorUseCase` coordina la operación, dependiendo únicamente de puertos (`IEntregaRepository`, `IDisponibilidadRepartidor`), nunca de implementaciones concretas.

La consulta de disponibilidad de un repartidor se expone como un puerto simple (`IDisponibilidadRepartidor`) que devuelve una respuesta booleana, en lugar de que el Contexto de Entregas consulte directamente la lógica interna del Contexto de Ruteo.

## Alternativas consideradas

**Opción descartada — Validar la invariante desde el caso de uso:** poner la validación "¿ya tiene repartidor?" directamente en `AsignarRepartidorUseCase`, en vez de en la entidad `Entrega`. Se descartó porque dispersaría la regla de negocio fuera del dominio: cualquier otro punto del sistema que manipule una `Entrega` podría romper la invariante sin pasar por el caso de uso.

**Opción descartada — Consultar Ruteo directamente desde Entregas:** que el caso de uso llamara directamente a una API o a la base de datos de Ruteo para saber la capacidad del repartidor. Se descartó porque acoplaría el Contexto de Entregas a los detalles internos de Ruteo (cómo calcula la capacidad, qué proveedor usa), dificultando el mantenimiento si Ruteo cambia su implementación.

## Costo aceptado

El uso de puertos añade una capa de abstracción (interfaces) que no sería necesaria si todo se implementara de forma directa. Se acepta ese costo porque permite que el dominio y la aplicación se prueben de forma aislada (sin base de datos real) y que los adaptadores concretos se reemplacen en la Actividad 4 sin tocar la lógica de negocio.

## Condición de revisión

Esta decisión se revisará si, al implementar la integración real en la Actividad 4, se descubre que el puerto `IDisponibilidadRepartidor` no captura correctamente la información que Ruteo necesita exponer, en cuyo caso se ajustará el contrato del puerto sin modificar la entidad `Entrega` ni el caso de uso.

## Responsable

Equipo de arquitectura de Rutify.
