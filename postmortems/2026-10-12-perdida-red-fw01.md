# Post-mortem · 2026-10-12 · fw01 sin red tras reinicio

## Resumen
Durante la preparación de la Fase 2 se comentó la configuración de `enp5s0` en
`/etc/network/interfaces` antes de la ventana de conmutación. Tras un reinicio, fw01 arrancó
sin dirección IP ni ruta por defecto.

## Impacto
- fw01 inaccesible por red (sin SSH).
- Jellyfin caído.
- Fallo de `apt` (repositorios de Docker y NVIDIA).
- Resto de la casa sin afectación: fw01 aún no enruta.

## Cronología
| Momento | Evento |
|---|---|
| … | Se comentan `allow-hotplug enp5s0` e `iface enp5s0 inet dhcp` anticipando el paso a systemd-networkd |
| … | Reinicio: ifupdown no levanta `enp5s0` y networkd está desactivado → interfaz DOWN, sin IP |
| … | Detección: fallos de `apt` y Jellyfin inaccesible |
| … | Recuperación por consola local (monitor y teclado): se descomentan ambas líneas |
| … | `sudo ifup enp5s0` → dhcpcd obtiene 192.168.0.192/24 y ruta por defecto vía 192.168.0.1 |

## Causa raíz
Se aplicó un paso de la ventana de conmutación (retirar la configuración antigua) sin que
existiera aún la nueva (networkd inactivo). La interfaz quedó sin ningún gestor de red.
Es el mismo patrón que el post-mortem de la Fase 1: **cortar antes de construir**.

## Qué funcionó
- Todos los ficheros de cambio estaban en `~/fase2`, fuera de `/etc`: el estado era fácil de
  revertir y ninguna configuración a medias se activó.
- Acceso físico disponible: la consola local permitió recuperar sin depender de la red.
- No se usó `git -C /etc checkout`: el autocommit diario de etckeeper podía haber guardado
  ya la versión rota.

## Lecciones y acciones
- `/etc/network/interfaces` no se toca fuera de la ventana de conmutación, y en ella el
  orden es: `ifdown` → comentar → activar networkd → verificar, con consola local delante.
- Antes de reiniciar tras un cambio de red: `grep -v '^#' /etc/network/interfaces` y
  `systemctl is-enabled systemd-networkd` para confirmar que algún gestor levantará la interfaz.
- Acción: montar wg-admin como camino de gestión alternativo antes de la conmutación
  (hecho en CAMBIO-007).
