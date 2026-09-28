# CAMBIO-001 · 2026-09-25 · Versionado de /etc en fw01 con etckeeper
Fase: 0 · Riesgo: bajo

## Motivo
Poder ver y revertir cualquier cambio de configuración de fw01 antes de
convertirlo en router y firewall.

## Pasos
1. `sudo apt install etckeeper`

## Verificación
- [x] `sudo git -C /etc log` muestra el commit inicial
- [x] Cada `apt install` genera un commit automático

## Reversión
`sudo apt purge etckeeper` (el historial de /etc/.git se puede conservar).

## Nota de seguridad
El repositorio de /etc contiene hashes de contraseñas y claves SSH del host:
se mantiene solo en local y no se sube a GitHub.

## Resultado
Hecho
