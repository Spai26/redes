# S4 - DHCPv4 y DHCPv6

## ¿Qué es DHCP?

**DHCP (Dynamic Host Configuration Protocol)** asigna automáticamente direcciones IP y otros parámetros de red a los dispositivos. Evita configurar IPs manualmente.

### Parámetros que entrega DHCP

- Dirección IP
- Máscara de subred
- Gateway predeterminado
- Servidores DNS
- Dominio (opcional)
- Lease time (tiempo de préstamo)

---

## DHCPv4 en routers Cisco

Un router Cisco puede funcionar como servidor DHCPv4.

### Pasos de configuración

1. **Excluir direcciones** que no se asignarán por DHCP (routers, servidores, impresoras).
2. **Crear un pool** con `ip dhcp pool`.
3. **Definir red, gateway y DNS** dentro del pool.

### Ejemplo completo

```bash
enable
configure terminal
hostname R1

! Excluir direcciones estáticas
ip dhcp excluded-address 192.168.35.1 192.168.35.49
ip dhcp excluded-address 192.168.35.81 192.168.35.254

! Crear el pool
ip dhcp pool VLAN35
 network 192.168.35.0 255.255.255.0
 default-router 192.168.35.1
 dns-server 8.8.8.8 8.8.4.4
 domain-name empresa.local
 lease 2

end

show ip dhcp pool
show ip dhcp binding
show ip dhcp server statistics
```

---

## Ejercicio propuesto: DHCPv4 con rango limitado

### Requisitos

- Red: `192.168.35.0/24`
- Gateway: `192.168.35.1`
- Rango DHCP: `192.168.35.50` a `192.168.35.80`
- DNS: `8.8.8.8`

### Configuración del router

```bash
ip dhcp excluded-address 192.168.35.1 192.168.35.49
ip dhcp excluded-address 192.168.35.81 192.168.35.254

ip dhcp pool POOL_VLAN35
 network 192.168.35.0 255.255.255.0
 default-router 192.168.35.1
 dns-server 8.8.8.8
```

### Configuración de la PC

En Packet Tracer:
- IP Configuration → DHCP
- La PC debe recibir una IP entre `192.168.35.50` y `192.168.35.80`.

---

## DHCPv6 en routers Cisco

DHCPv6 puede operar en dos modos:

| Modo | Descripción | Comando en la PC |
|------|-------------|------------------|
| **Stateful DHCPv6** | El servidor asigna IPv6, DNS y otros parámetros. | DHCPv6 |
| **Stateless DHCPv6** | El servidor solo entrega DNS y dominio; la IP se obtiene por SLAAC. | Auto Config |

### Ejemplo: Stateful DHCPv6

```bash
! Habilitar IPv6 unicast routing
ipv6 unicast-routing

! Definir pool DHCPv6
ipv6 dhcp pool VLAN35_IPV6
 address prefix 2001:DB8:ACAD:35::/64
 dns-server 2001:DB8:ACAD::1
 domain-name empresa.local

! Aplicar el pool a la interfaz
interface gigabitEthernet0/0/1.35
 encapsulation dot1Q 35
 ipv6 address 2001:DB8:ACAD:35::1/64
 ipv6 nd managed-config-flag
 ipv6 nd other-config-flag
 ipv6 dhcp server VLAN35_IPV6
```

### Ejemplo: Stateless DHCPv6

```bash
ipv6 dhcp pool DNS_ONLY
 dns-server 2001:DB8:ACAD::1
 domain-name empresa.local

interface gigabitEthernet0/0/1.35
 encapsulation dot1Q 35
 ipv6 address 2001:DB8:ACAD:35::1/64
 ipv6 nd other-config-flag
 ipv6 dhcp server DNS_ONLY
```

---

## Ejercicio combinado: DHCPv4 y DHCPv6 en la misma red

### Topología

```
Router-on-a-stick con VLAN 35
  └── PC1 y PC2 en VLAN 35
```

### Router

```bash
! DHCPv4
ip dhcp excluded-address 192.168.35.1 192.168.35.49
ip dhcp excluded-address 192.168.35.81 192.168.35.254

ip dhcp pool POOL_VLAN35
 network 192.168.35.0 255.255.255.0
 default-router 192.168.35.1
 dns-server 8.8.8.8

! DHCPv6
ipv6 unicast-routing

ipv6 dhcp pool VLAN35_IPV6
 address prefix 2001:DB8:ACAD:35::/64
 dns-server 2001:DB8:ACAD::1

interface gigabitEthernet0/0/1.35
 encapsulation dot1Q 35
 ip address 192.168.35.1 255.255.255.0
 ipv6 address 2001:DB8:ACAD:35::1/64
 ipv6 nd managed-config-flag
 ipv6 nd other-config-flag
 ipv6 dhcp server VLAN35_IPV6
```

### PCs

- PC1: IPv4 → DHCP; IPv6 → DHCPv6
- PC2: IPv4 → DHCP; IPv6 → DHCPv6

Verificar que ambas obtengan IP en ambos protocolos.

---

## Tips

1. **Siempre excluí el gateway y las IPs estáticas** del pool DHCP para evitar conflictos.
2. **Un router Cisco puede ser servidor DHCP y router al mismo tiempo.** No necesitás un servidor separado.
3. **Si la PC no obtiene IP:** verificá que esté en la VLAN correcta, que el gateway esté configurado y que el pool tenga la red correcta.
4. **Para DHCPv6 recordá activar `ipv6 unicast-routing`.** Sin eso no funciona el ruteo IPv6.
5. **`managed-config-flag`** indica a la PC que use DHCPv6 stateful para obtener la IP.
6. **`other-config-flag`** indica que use DHCPv6 para DNS/dominio, incluso en modo stateless.
7. **`show ip dhcp binding`** te muestra qué IPs fueron asignadas y a qué MACs.

---

## Verificación

```bash
! DHCPv4
show ip dhcp pool
show ip dhcp binding
show ip dhcp server statistics

! DHCPv6
show ipv6 dhcp pool
show ipv6 dhcp binding
```

---

## Caso Cisco: DHCP relay

Cuando el servidor DHCP no está en la misma red que los clientes, se usa `ip helper-address` en el router para reenviar las peticiones DHCP.

```bash
interface gigabitEthernet0/0/1.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
 ip helper-address 192.168.1.10
```

En este caso, `192.168.1.10` es el servidor DHCP ubicado en otra red.

---

## Referencias

- Kurose, J. (2017). *Redes de computadoras: un enfoque descendente basado en internet*. Pearson.
- Robledo Sosa, C. (2002). *Redes de computadoras*. IPN.
- RFC 2131 — DHCPv4.
- RFC 8415 — DHCPv6.
