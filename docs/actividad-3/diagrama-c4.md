# Diagrama C4 — Flujo de asignación de repartidor

## Nivel 1 — Contexto

```mermaid
flowchart LR
    Cliente(["Cliente<br/>Realiza pedidos y espera su entrega"])
    Operador(["Operador logístico<br/>Gestiona la operación de entregas"])
    Repartidor(["Repartidor<br/>Realiza la entrega física"])
    Rutify["Rutify<br/>Plataforma de logística de última milla"]

    Cliente -->|Crea pedidos| Rutify
    Operador -->|Gestiona asignaciones| Rutify
    Rutify -->|Asigna entregas| Repartidor
```

## Nivel 2 — Contenedores (dentro de Rutify)

```mermaid
flowchart LR
    Pedidos["Contexto de Pedidos<br/>Gestiona pedidos y clientes"]
    Entregas["Contexto de Entregas<br/>Gestiona el ciclo de vida de la entrega"]
    Ruteo["Contexto de Ruteo<br/>Gestiona repartidores y capacidad"]
    Notificaciones["Contexto de Notificaciones<br/>Envía notificaciones al cliente"]

    Pedidos -->|Crea entrega| Entregas
    Entregas -->|Consulta disponibilidad| Ruteo
    Entregas -->|Publica resultado| Notificaciones
```

## Nivel 3 — Componentes (dentro del Contexto de Entregas)

```mermaid
flowchart TD
    UseCase["AsignarRepartidorUseCase<br/>Aplicación"]
    Entidad["Entrega<br/>Dominio"]
    Repo["IEntregaRepository<br/>Puerto"]
    Disp["IDisponibilidadRepartidor<br/>Puerto"]
    AdapterDb["Adaptador EF Core<br/>Infraestructura"]
    AdapterRuteo["Adaptador HTTP Ruteo<br/>Infraestructura"]

    UseCase --> Entidad
    UseCase --> Repo
    UseCase --> Disp
    AdapterDb -->|Implementa| Repo
    AdapterRuteo -->|Implementa| Disp
```
