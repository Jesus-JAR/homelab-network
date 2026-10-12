# CAMBIO-006 · 2026-09-29 · Seguridad L2 en core-sw01
Fase: 1 · Riesgo: medio

## Motivo
Aplicar los controles de capa 2 que ofrece el switch (port security, protección de bucles,
filtrado de VLAN en la entrada) antes de enrutar entre VLAN.

## Pasos
| Control | Configuración |
|---|---|
| Port security (acción: descartar) | boca 3: 8 MAC · boca 6: 16 · boca 7: 1 · boca 8: 1 · bocas 1 y 4 sin límite |
| Spanning Tree | RSTP activado |
| Loop Guard | Bocas de acceso (1, 3, 6, 7 y 8) |
| IGMP Snooping | Activado |
| Ingress Check | Activado en todas las bocas |
| 802.1X | Desactivado (ampliación opcional en la Fase 5) |

## Verificación
- [x] PC, TV y consola del cuarto (boca 6) funcionan dentro del límite de MAC
- [x] Descodificador (boca 7) y Raspberry (boca 8) funcionan
- [x] Internet y VPN operativas

## Reversión
Desactivar el control afectado en la web del switch; si no hay acceso, reinicio o restauración
de la copia de configuración.

## Resultado
Hecho
