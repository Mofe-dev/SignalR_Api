# SignalR_Api

API en ASP.NET Core 8 protegida con Azure Entra ID (Azure AD), que expone un hub SignalR para notificaciones en tiempo real y un controlador para enviar notificaciones desde el backend.

## Características

- .NET 8 Web API
- SignalR para notificaciones en tiempo real
- Autenticación y autorización con Azure Entra ID (Azure AD)
- CORS habilitado para desarrollo (localhost)
- Envío de notificaciones mediante endpoint REST
- Ejemplo de DTO para notificaciones: sender, message, date

## Configuración

### 1. Variables en `appsettings.json`

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "TenantId": "<tu-tenant-id>",
  "ClientId": "<tu-client-id-api>",
  "Audience": "api://<tu-client-id-api>"
}
```

- Todos los valores (excepto TenantId) deben ser del registro de la API en Entra ID.

### 2. CORS

Permite orígenes de desarrollo como `http://localhost:5173` (Vite), `http://localhost:3000` (React), etc.

### 3. Endpoints principales

- **SignalR Hub:** `/notificationHub` (requiere autenticación)
- **Enviar notificación:** `POST /api/notification/send`
  - Body ejemplo:
    ```json
    {
      "sender": "user@domain.com",
      "message": "Texto de la notificación",
      "date": "2025-08-29T12:00:00"
    }
    ```
- **Ping:** `GET /api/notification/ping` (requiere autenticación)

## Autenticación

Para consumir la API y SignalR necesitas un token JWT válido emitido por Azure Entra ID para el Audience configurado.

- El scope para obtener el token debe ser:
  ```
  api://<tu-client-id-api>/.default
  ```
- El token debe enviarse en la conexión SignalR usando `accessTokenFactory`.

## Ejemplo de conexión SignalR desde JS

```js
const connection = new signalR.HubConnectionBuilder()
  .withUrl("http://localhost:5274/notificationHub", {
    accessTokenFactory: () => "AQUI_TU_TOKEN",
  })
  .build();
```

## Requisitos

- .NET 8 SDK
- Registro de la API y del cliente en Azure Entra ID

## Ejecución

1. Restaura dependencias y ejecuta:
   ```bash
   dotnet restore
   dotnet run --project SignalR_Api/SignalR_Api.csproj
   ```
2. Accede a Swagger en `http://localhost:5274/swagger` para probar los endpoints.

---

> Si tienes problemas de CORS, autenticación o SignalR, revisa que el token sea válido y el Audience coincida exactamente con el configurado en la API.
