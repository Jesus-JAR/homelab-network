# Fase 1 · Segmentación y seguridad L2

| | |
|---|---|
| **Fecha** | 2026-09-28 / 2026-09-29 |
| **Autor** | Jesus-JAR |
| **Estado** | Cerrada |
| **Equipo** | core-sw01 · Zyxel GS1900-8 · firmware V2.90(AAHH.2) |

## 1. Objetivo

Crear el plan de VLAN en el switch, sacar todo de la VLAN 1 y dejar un estado transitorio seguro: la casa sigue funcionando igual, ahora en la VLAN 99, y el trunk hacia fw01 queda probado con la VLAN 40. El enrutado entre VLAN llega en la Fase 2.

## 2. VLAN creadas

| ID | Nombre | Uso |
|---|---|---|
| 10 | MGMT | Gestión de infraestructura (Fase 2) |
| 20 | USERS | Equipos de confianza (Fase 2) |
| 30 | IOT | TV, consola y descodificador (Fase 2) |
| 40 | LAB | Laboratorio aislado (activa en prueba) |
| 50 | SRV | Servicios (Fase 2) |
| 99 | UPLINK | Red del router: estado transitorio de toda la casa |
| 999 | BLACKHOLE | Bocas aparcadas y destino SPAN |
| 1 | default | **Vacía**: ninguna boca ni la gestión |

## 3. Estado de las bocas al cerrar la fase

| Boca | Equipo | PVID | Untagged | Tagged | Estado |
|---|---|---|---|---|---|
| 1 | edge-rt01 | 99 | 99 | — | Activa |
| 2 | Reservada | 999 | 999 | — | **Deshabilitada** |
| 3 | Roseta LAB | 40 | 40 | — | Activa |
| 4 | fw01 · trunk | 99 | 99 | 40 | Activa |
| 5 | fw01 · SPAN | 999 | 999 | — | Activa |
| 6 | Roseta del cuarto | 99 | 99 | — | Activa |
| 7 | Descodificador | 99 | 99 | — | Activa |
| 8 | srv-rpi01 | 99 | 99 | — | Activa |

Gestión del switch: **VLAN 99**, 192.168.0.3, HTTPS.

## 4. Seguridad L2

| Control | Configuración |
|---|---|
| Port security | boca 3: 8 MAC · boca 6: 16 · boca 7: 1 · boca 8: 1 · bocas 1 y 4 sin límite |
| RSTP | Activado |
| Loop Guard | Bocas de acceso |
| IGMP Snooping | Activado |
| Ingress Check | Todas las bocas |
| 802.1X | Desactivado (ampliación opcional) |

## 5. Verificación

| Prueba | Resultado |
|---|---|
| Internet, TV, Jellyfin y VPN con la casa en la VLAN 99 | ✅ |
| Gestión del switch por la VLAN 99 | ✅ |
| VLAN 1 sin miembros | ✅ |
| Portátil en la VLAN 40 → fw01 (10.10.40.1) | ✅ < 1 ms |
| Portátil en la VLAN 40 → router (192.168.0.1) | ✅ Sin acceso (aislamiento) |
| Tramas 802.1Q `vlan 40` en fw01 | ✅ [evidencia](evidencias/fase1-trunk-vlan40.txt) |

## 6. Decisiones

- **D11 · La boca 6 (cuarto) irá a USERS en la Fase 2.** Allí está el PC principal. Riesgo aceptado: la TV y la consola comparten VLAN con él. Mejora futura: switch gestionable en el cuarto con trunk 20+30.

## 7. Cambios e incidencias

| Registro | Descripción |
|---|---|
| [CAMBIO-004](cambios/CAMBIO-004-firmware-switch.md) | Firmware V2.80 → V2.90 con imagen dual |
| [CAMBIO-005](cambios/CAMBIO-005-vlan99.md) | Migración a la VLAN 99, VLAN 1 vacía y prueba de la VLAN 40 |
| [CAMBIO-006](cambios/CAMBIO-006-seguridad-l2.md) | Seguridad L2 |
| [Post-mortem](../postmortems/2026-09-29-perdida-gestion-switch.md) | Pérdida del acceso de gestión durante la migración |

## 8. Lecciones

- Construir el camino nuevo antes de cortar el antiguo: membresía → PVID → gestión → retirar la VLAN antigua.
- Gestionar por un camino que el cambio no toque, y comprobarlo con `ip route get`.
- No guardar hasta verificar: un reinicio devuelve la última configuración guardada.
- En un equipo con varias interfaces, Linux puede responder ARP por la interfaz equivocada (*ARP flux*); se corrige con `arp_ignore=1` y `arp_announce=2`.

**Siguiente:** Fase 2 · Routing, firewall de zonas y DHCP en fw01.
