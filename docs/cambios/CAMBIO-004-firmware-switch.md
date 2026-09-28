# CAMBIO-004 · 2026-09-28 · Actualización de firmware de core-sw01
Fase: 1 · Riesgo: medio

## Motivo
Aplicar la última versión de firmware antes de configurar las VLAN.

## Estado anterior
Firmware V2.80(AAHH.1) · 08/08/2024

## Estado nuevo
Firmware V2.90(AAHH.2) · 05/07/2026

## Pasos
1. Descarga del firmware oficial del GS1900-8 desde la web de Zyxel
2. Subida por HTTP a la imagen de reserva (*Backup*)
3. Imagen de reserva marcada como activa y reinicio del switch

## Verificación
- [x] V2.90(AAHH.2) activa
- [x] V2.80(AAHH.1) conservada como imagen de reserva
- [x] Contraseña, IP 192.168.0.3 y SNMP conservados
- [x] Red de casa y VPN operativas tras el reinicio

## Reversión
En *Management*, marcar V2.80(AAHH.1) como imagen activa y reiniciar.

## Resultado
Hecho · corte de ~X min durante el reinicio
