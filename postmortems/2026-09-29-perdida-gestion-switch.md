# Post-mortem · 2026-09-29 · Pérdida del acceso de gestión de core-sw01

## Resumen
Durante la migración a la VLAN 99 se perdió dos veces el acceso a la gestión del switch.
La red de casa siguió funcionando en el primer caso; en el segundo hubo un corte breve.

## Impacto
- Incidente 1: sin gestión del switch hasta la recuperación. Tráfico de la casa sin cambios.
- Incidente 2: casa sin Internet durante unos minutos y sin gestión por WiFi.

## Cronología
| Hora | Evento |
|---|---|
| … | Cambio de la Management VLAN a 99 con la VLAN 99 todavía sin bocas → gestión inaccesible |
| … | Reinicio sin éxito (el cambio estaba guardado) → reset de fábrica y restauración de la copia |
| … | PVID 99 aplicado antes de la membresía y gestión por WiFi (entra por la boca 1) → sin acceso |
| … | Reinicio del switch sin haber guardado → vuelta al estado anterior |
| … | Repetición en orden correcto: membresía → PVID → gestión, con el portátil por cable |

## Causa raíz
No se respetó el principio de construir el camino nuevo antes de cortar el antiguo:
1. La gestión se movió a una VLAN sin ninguna boca asignada: no existía camino hasta ella.
2. Se cambió el PVID antes de la membresía, y la gestión se hacía por un camino (WiFi → boca 1)
   afectado por el propio cambio.

## Qué funcionó
- La copia de configuración previa permitió restaurar el switch en minutos.
- No guardar hasta verificar convirtió el segundo incidente en un simple reinicio.

## Lecciones y acciones
- Orden obligatorio en cambios de VLAN: membresía → PVID → gestión → retirar lo antiguo.
- Durante cambios en el switch, gestionar por un camino que el cambio no toque
  (portátil por cable, WiFi apagado, comprobado con `ip route get`).
- Guardar solo tras verificar; exportar la configuración antes de cada cambio.
- No hacer cambios remotos (VPN) sobre el camino del propio acceso.
