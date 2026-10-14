# RPA Movimientos Bancarios · Descarga multibanco y carga de extractos en SAP

Solución de **Power Automate** (nube + escritorio) que entra a los portales de banca empresarial, descarga los movimientos de todas las cuentas de la empresa, los convierte al formato **MultiCash** y los **carga en SAP** para la conciliación bancaria.

> ⚠️ Proyecto de portafolio. Sociedades, cuentas, montos, usuarios, correos y transacciones SAP son **ficticios**. Se retiraron las capturas de pantalla de los portales, por lo que la solución no se puede importar tal cual.

## Resultado

| Métrica | Manual | Con el robot |
|---|---|---|
| Cuentas procesadas | 108 cuentas de 23 sociedades | Las mismas, sin intervención |
| Bancos | Interbank, BCP, BBVA y Banco de la Nación | Los 4, en una sola ejecución |
| Tiempo diario | 10 h | **3 h (−70 %)** |

## Cómo funciona

```
Flujo de nube (lunes a sábado, 6:00 · 7:00 · 8:00)
   └─► Ejecuta el flujo de escritorio con las credenciales SAP
          │
          ├─► Login SAP ─► descarga el reporte de cuentas (transacción Z)
          ├─► Lee la configuración de cuentas y la fecha del último extracto
          ├─► Por cada banco:  Interbank · BCP · BBVA · Banco de la Nación
          │      login ─► selecciona la cuenta ─► rango de fechas ─► descarga
          │      └─ valida el archivo descargado y el monto
          ├─► Genera los TXT en formato MultiCash
          ├─► Carga cabecera y detalle en SAP
          ├─► Registra el avance por cuenta en MySQL / SQL Server
          ├─► Publica el reporte por cuenta y por banco en SharePoint (con gráfico)
          └─► Correo de resultado; si algo falla, el flujo de nube envía el detalle del error
```

### Puntos técnicos destacados

- **86 funciones** en el flujo de escritorio, organizadas por banco y por etapa (login, descarga, validación, carga y reporte).
- **Control por cuenta:** cada cuenta tiene su estado en base de datos (pendiente, descargada, TXT generado, cargada en SAP, sin movimientos), así que una ejecución se puede retomar sin repetir trabajo.
- **Ventana horaria por banco:** el robot valida si corresponde procesar cada banco según el horario.
- **Manejo de errores Try/Catch** en el flujo de nube: si el robot falla, se arma un correo HTML con el error y se notifica a soporte.
- **Reportes de seguimiento:** Excel por cuenta y por banco publicado en SharePoint, con gráfico de avance.
- **CAPTCHA:** si un portal lo solicita, el robot lo registra y lo deja para resolución manual.

## Tecnologías

Power Automate (nube y escritorio) · SAP GUI · Automatización web (portales bancarios) · Excel · MySQL / SQL Server · SharePoint · Outlook

## Contenido

| Archivo / carpeta | Contenido |
|---|---|
| `Workflows/` | Flujo de nube programado y definición del flujo de escritorio |
| `customizations.xml` | Script del flujo de escritorio, referencias de conexión y variables |
| `solution.xml` | Manifiesto de la solución |
| `desktopflowbinaries/` | Repositorio de elementos de interfaz (sin capturas de pantalla) |

## Autor

**Giovanni Herrera Castilla** · Automation & AI Specialist
[LinkedIn](https://www.linkedin.com/in/giovanni-herrera-castilla-801146191/)
