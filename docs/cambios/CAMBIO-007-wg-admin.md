# CAMBIO-007 · 2026-10-12 · VPN de administración (wg-admin) en fw01
Fase: 2 · Riesgo: bajo

## Motivo
Disponer de un camino de gestión independiente de la red que se va a modificar antes de
conmutar fw01 a router entre VLAN (*make-before-break*, lección del post-mortem de la Fase 1).
Será además el acceso de administración definitivo y sustituirá a las reglas `TEMPORAL`
del firewall.

## Diseño
| Parámetro | Valor |
|---|---|
| Interfaz | `wg-admin` (gestionada por `wg-quick@wg-admin`) |
| Red | 10.10.60.0/24 (VPN-ADMIN) |
| Servidor | fw01 · 10.10.60.1 · escucha en **51821/udp** |
| Cliente | portátil · 10.10.60.2/32 |
| Exposición | Solo red interna: **sin reenvío de puertos en el router** |

El puerto 51820/udp queda reservado para wg-easy (Raspberry), que fw01 reenviará por DNAT.
Usar 51820 para wg-admin haría que ese DNAT capturase el tráfico de administración.

## Pasos
1. Generación de pares de claves con `wg genkey | wg pubkey` y `umask 077` (permisos 600).
2. Servidor: la clave privada se carga desde fichero (`PostUp = wg set %i private-key ...`),
   fuera del `.conf`.
3. Claves públicas cruzadas: cada extremo conoce la pública del otro.
4. Servicio habilitado en el arranque: `systemctl enable wg-quick@wg-admin`.
5. Reglas preparadas en `nftables.conf` (aún sin cargar):
   - `input`: `udp dport 51821 accept`
   - `input`: SSH y paneles de fw01 desde `{ $MGMT, $VPNADM }`
   - `forward`: `iifname { $MGMT, $VPNADM } accept`

## Verificación
- [x] `wg show`: *latest handshake* y tráfico en ambos sentidos
- [x] `ping 10.10.60.1` sin pérdidas (~5 ms en régimen estable)
- [x] `ip route get 10.10.60.1` → `dev wg-admin`
- [x] SSH a `mrrobot@10.10.60.1`; huella de host idéntica a la conocida por 192.168.0.192
- [x] `systemctl is-enabled wg-quick@wg-admin` → `enabled`

## Incidencias durante el cambio
| Síntoma | Causa | Solución |
|---|---|---|
| `Key is not the correct length or format` | Carácter `º` pegado al final de la clave privada | Eliminado; comprobado con `cat -A` |
| Interfaz arriba pero sin handshake (`0 B received`) | Endpoint en 51820 en vez de 51821 | Endpoint corregido |
| Clave privada del cliente mostrada en un mensaje de error compartido | Exposición accidental | **Par de claves regenerado** y pública actualizada en el servidor |

## Pendiente asociado
- En la ventana de conmutación, cambiar el `Endpoint` del portátil a `192.168.0.10:51821`
  (desaparece la reserva .192).
- Bastionado SSH: acceso por clave (`ssh-copy-id`) y `PasswordAuthentication no`.

## Reversión
`systemctl disable --now wg-quick@wg-admin`. No afecta a ningún otro servicio.

## Resultado
Hecho
