# Redes y Comunicación de Datos II

Índice de archivos principales.

## Referencias

- [Notas de Redes I](notas-redes-i.md) — conceptos básicos, subneteo, tipos de redes y modelos.

## Sesiones

- [S2 - Enrutamiento interVLAN](s2%20-%20enrutamiento-intervlan.md) — router-on-a-stick y SVI.
- [S3 - STP y EtherChannel](s3%20-%20stp-y-etherchannel.md) — redundancia, agregación de enlaces y Port Channels.
- [S4 - DHCPv4 y DHCPv6](s4%20-%20dhcpv4-y-dhcpv6.md) — configuración de servidores DHCP en routers Cisco.
- [Sesión 1 - Separación de áreas con VLAN](session%201/separacion_areas_con_vlan.md) — ejercicio práctico de VLANs y router-on-a-stick.

## Recursos adicionales

- `comandos-cisco.pdf` — referencia de comandos Cisco.
- `modelo.jpg` — topología de referencia del modelo de VLANs.
- `topologia_r_on_stick.png` — topología actual del ejercicio.



## Sesión 08 -  mitigación de ataques a SW
que parte gestiona la ip todas las interfaces (vlan1) 


via telnet -> 192.168.10.10  de manera local.

Switch(config)#line vty 0 4
Switch(config-line)#password cisco
Switch(config-line)#login
Switch(config-line)#int vlan 1
Switch(config-if)#ip add 192.168.10.10 255.255.255.0


Switch(config)#interface f0/2
Switch(config-if)#sw mode access
Switch(config-if)#sw access vlan 1

Switch(config-if)#ip default-gateway 192.168.10.1
Switch(config)#hostname S-PARRA
S-PARRA(config)#ip domain-name miclase.com
S-PARRA(config)#crypto key generate rsa general
S-PARRA(config)#crypto key generate rsa general-keys mod
S-PARRA(config)#crypto key generate rsa general-keys modulus 2048
The name for the keys will be: S-PARRA.miclase.com

% The key modulus size is 2048 bits
% Generating 2048 bit RSA keys, keys will be non-exportable...[OK]
*Mar 1 0:23:44.876: %SSH-5-ENABLED: SSH 1.99 has been enabled
S-PARRA(config)#ip ssh version 2
S-PARRA(config)#line vty 0 5
S-PARRA(config-line)#transport input ssh
S-PARRA(config-line)#login local
S-PARRA(config-line)#username claseredes2 privilege 15 secret cisco
S-PARRA(config)#enable secret cisco
encriptación de acceso externo

