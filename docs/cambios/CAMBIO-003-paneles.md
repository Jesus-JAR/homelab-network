# CAMBIO-003 · 2026-09-25 · Retirada de paneles de administración expuestos
Fase: 0 · Riesgo: medio

## Motivo
Paneles de administración accesibles desde Internet (hallazgos H1, H2, H3):
- Panel de wg-easy por reenvío directo 51821/tcp y por subdominio en Caddy
- Panel de AdGuard Home por subdominio en Caddy
- Entradas de Caddy hacia servicios que ya no existían

## Estado anterior
Caddy publicaba cinco subdominios; reenvíos 80, 443/tcp, 51820/udp y 51821/tcp.

## Pasos
1. Copia del Caddyfile (`Caddyfile.bak-2026-09-25`)
2. Caddyfile reducido a Jellyfin y recarga de Caddy
3. Borrado del reenvío 51821/tcp en edge-rt01

## Verificación
- [x] Jellyfin accesible desde fuera
- [x] Subdominios de wg-easy y AdGuard sin respuesta desde fuera
- [x] Panel de wg-easy accesible por la VPN
- [x] Reenvíos restantes: 80, 443/tcp y 51820/udp

## Reversión
Restaurar `Caddyfile.bak-2026-09-25`, recargar Caddy y recrear el reenvío.

## Incidencia asociada
Ver post-mortem 2026-09-25: Jellyfin inaccesible desde fuera tras el cambio.

## Resultado
Hecho
