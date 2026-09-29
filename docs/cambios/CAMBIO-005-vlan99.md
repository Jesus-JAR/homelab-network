# CAMBIO-005 · 2026-09-29 · Migración de core-sw01 a la VLAN 99 y VLAN 1 vacía
Fase: 1 · Riesgo: alto (corta la red de casa)

## Motivo
Dejar de usar la VLAN 1 (decisión D1), crear el plan de VLAN y preparar el trunk hacia fw01.

## Estado anterior
Todas las bocas en la VLAN 1 (PVID 1) y gestión del switch en la VLAN 1.

## Pasos
1. Crear las VLAN 10 MGMT, 20 USERS, 30 IOT, 40 LAB, 50 SRV, 99 UPLINK y 999 BLACKHOLE (vacías)
2. VLAN 99: bocas 1, 3, 4, 6, 7 y 8 como *Untagged* (primero la membresía) y después PVID 99
3. Gestión del switch → VLAN 99, con el camino ya disponible por la boca 1
4. Bocas 2 y 5 → VLAN 999 (*Untagged*, PVID 999); boca 2 deshabilitada
5. VLAN 1 sin ninguna boca
6. Prueba de la VLAN 40: boca 3 *Untagged* con PVID 40 y boca 4 *Tagged* en la VLAN 40

## Verificación
- [x] Internet, TV, Jellyfin y VPN funcionan con la casa en la VLAN 99
- [x] Gestión accesible en `http://192.168.0.3` por la VLAN 99
- [x] VLAN 1 sin miembros
- [x] Portátil en la boca 3 (10.10.40.50) llega a fw01 (10.10.40.1) por la VLAN 40
- [x] Desde la boca 3 no se alcanza el router (LAB aislada)
- [x] Tramas 802.1Q con `vlan 40` en fw01 (ver `docs/evidencias/fase1-trunk-vlan40.txt`)

## Reversión
Antes de guardar: apagar y encender el switch (vuelve a la configuración guardada).
Después de guardar: restaurar la copia de configuración previa desde el repositorio privado.

## Incidencias
Dos pérdidas del acceso de gestión durante el cambio: ver
[post-mortem](../../postmortems/2026-09-29-perdida-gestion-switch.md).

## Resultado
Hecho
