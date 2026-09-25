# Post-mortem · 2026-09-25 · Jellyfin inaccesible desde fuera

## Resumen
Tras retirar paneles de Caddy (CAMBIO-003), Jellyfin no cargaba desde datos móviles.

## Impacto
Solo acceso externo a Jellyfin; en casa funcionaba. Duración: 20 min.

## Diagnóstico (capa por capa)
1. DNS público → resolvía a la IP pública correcta ✅
2. Caddy → logs sin errores, certificado válido ✅
3. tcpdump en srv-rpi01 → el tráfico del móvil llegaba al 443: router y operador OK ✅
4. La conexión se cortaba en la negociación TLS: Caddy respondía con una alerta
5. curl con --resolve desde la LAN → TLS y Jellyfin OK (HTTP 302) ✅
Conclusión: el cliente pedía un nombre sin certificado en Caddy.

## Causa raíz
<la que fuera: dirección antigua guardada / nombre mal escrito…>

## Solución
<qué hiciste>

## Lecciones
- Tras cambiar publicaciones, revisar marcadores y apps que apunten a los nombres retirados.
- Verificar siempre desde fuera con la URL exacta.
