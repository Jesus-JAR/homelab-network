# CAMBIO-002 · 2026-09-25 · Reserva de fw01 y reducción del pool DHCP
Fase: 0 · Riesgo: bajo

## Motivo
El pool (.3–.253) ocupaba las IP fijas previstas (fw01 en .10) y el servidor
no tenía reserva, aunque Caddy apunta a su IP.

## Estado anterior
Pool .3–.253 · reservas: core-sw01 (.3) y srv-rpi01 (.112)

## Pasos
1. Reserva de fw01 en 192.168.0.192
2. Pool reducido a 192.168.0.100–192.168.0.199

## Verificación
- [ ] Jellyfin accesible desde fuera
- [ ] VPN de usuarios operativa
- [ ] Clientes dinámicos en .100–.199 tras 2 h

## Reversión
Pool .3–.253 y borrar la reserva de fw01

## Resultado
Hecho / revertido · notas
