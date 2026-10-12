# Casos de uso, puertos y adaptadores — Rutify

## Caso de uso: AsignarRepartidorUseCase

Responde a la pregunta: *¿qué necesita hacer el sistema cuando se solicita asignar un repartidor a una entrega?*

**Pasos:**
1. Cargar la entrega mediante `IEntregaRepository`.
2. Verificar que la entrega exista y no tenga ya un repartidor activo.
3. Consultar disponibilidad del repartidor mediante un puerto (`IDisponibilidadRepartidor`), no mediante una llamada directa al módulo de Ruteo.
4. Ejecutar `entrega.AsignarRepartidor(repartidorId)` para proteger la invariante dentro de la propia entidad.
5. Guardar el cambio mediante el repositorio.
6. Publicar o registrar el resultado mediante un adaptador de notificaciones.

## Puertos (interfaces que expresan lo que el caso de uso necesita)

- **`IEntregaRepository`**: obtener y guardar entregas.
- **`IDisponibilidadRepartidor`**: consultar si un repartidor tiene capacidad disponible, sin conocer cómo se calcula.
- **`INotificador`**: informar el resultado de la asignación.

## Adaptadores (implementación concreta, en la capa de infraestructura)

- Adaptador de base de datos para `IEntregaRepository` (por ejemplo, con Entity Framework).
- Adaptador HTTP o de mensajería para `IDisponibilidadRepartidor`, que se comunica con el Contexto de Ruteo.
- Adaptador de notificaciones (correo, push, cola de eventos) para `INotificador`.

## Dirección de las dependencias

El dominio (`Entrega`) y la aplicación (`AsignarRepartidorUseCase`) **no dependen** de ninguna implementación concreta. Los puertos son interfaces definidas por el dominio/aplicación; los adaptadores viven en infraestructura y dependen de los puertos, nunca al revés.

## Ejemplo mínimo en C#

```csharp
public sealed record EntregaId(Guid Value);
public sealed record DireccionEntrega(string Value);

public sealed class Entrega
{
    public EntregaId Id { get; }
    public DireccionEntrega Direccion { get; }
    public Guid? RepartidorId { get; private set; }

    public void AsignarRepartidor(Guid repartidorId)
    {
        if (RepartidorId.HasValue)
            throw new InvalidOperationException("La entrega ya tiene un repartidor asignado");

        RepartidorId = repartidorId;
    }
}

public interface IEntregaRepository
{
    Task<Entrega?> GetAsync(EntregaId id);
    Task SaveAsync(Entrega entrega);
}

public interface IDisponibilidadRepartidor
{
    Task<bool> TieneCapacidadAsync(Guid repartidorId);
}

public sealed class AsignarRepartidorUseCase
{
    private readonly IEntregaRepository _repositorio;
    private readonly IDisponibilidadRepartidor _disponibilidad;

    public AsignarRepartidorUseCase(IEntregaRepository repositorio, IDisponibilidadRepartidor disponibilidad)
    {
        _repositorio = repositorio;
        _disponibilidad = disponibilidad;
    }

    public async Task ExecuteAsync(EntregaId entregaId, Guid repartidorId)
    {
        var entrega = await _repositorio.GetAsync(entregaId)
            ?? throw new InvalidOperationException("Entrega no encontrada");

        var tieneCapacidad = await _disponibilidad.TieneCapacidadAsync(repartidorId);
        if (!tieneCapacidad)
            throw new InvalidOperationException("El repartidor no tiene capacidad disponible");

        entrega.AsignarRepartidor(repartidorId);
        await _repositorio.SaveAsync(entrega);
    }
}
```

En este ejemplo, la entidad `Entrega` protege su propia invariante (no se puede reasignar si ya tiene repartidor). El caso de uso coordina la intención, y tanto el repositorio como la consulta de disponibilidad son puertos: su implementación real queda en infraestructura.
