# VLANs

## Registro de control — Plan de direccionamiento por VLAN

Diseño final: 2 redes independientes, cada una en su propio switch. Cada switch tiene VLANs 10, 20, 30 y se conecta al router mediante un trunk en `Gig0/1`. **No hay trunk entre switches**, por lo que los IDs 10/20/30 se repiten sin colisión.

### Topología

```
                    Router-PT
                       │
           ┌───────────┴───────────┐
           │                       │
      Gig0/0 trunk             Gig0/1 trunk
           │                       │
      Gig0/1 S1               Gig0/1 S2
     192.168.x.0              200.0.x.0
    VLAN 10, 20, 30          VLAN 10, 20, 30
```

### Red 1: 192.168.0.0/16 — Switch S1 (trunk al router por Gig0/0)

| VLAN | Nombre | Subred | Máscara | Gateway | Interfaces (8 c/u) | PCs asignados (2 por VLAN) |
|------|--------|--------|---------|---------|--------------------|----------------------------|
| 10 | SISTEMAS | 192.168.10.0/24 | 255.255.255.0 | 192.168.10.1 | fa0/1 - fa0/8 | PC1-S1, PC2-S1 |
| 20 | CONTABILIDAD | 192.168.20.0/24 | 255.255.255.0 | 192.168.20.1 | fa0/9 - fa0/16 | PC3-S1, PC4-S1 |
| 30 | RRHH | 192.168.30.0/24 | 255.255.255.0 | 192.168.30.1 | fa0/17 - fa0/24 | PC5-S1, PC6-S1 |

### Red 2: 200.0.0.0/8 — Switch S2 (trunk al router por Gig0/1)

| VLAN | Nombre | Subred | Máscara | Gateway | Interfaces (8 c/u) | PCs asignados (2 por VLAN) |
|------|--------|--------|---------|---------|--------------------|----------------------------|
| 10 | SISTEMAS | 200.0.10.0/24 | 255.255.255.0 | 200.0.10.1 | fa0/1 - fa0/8 | PC1-S2, PC2-S2 |
| 20 | CONTABILIDAD | 200.0.20.0/24 | 255.255.255.0 | 200.0.20.1 | fa0/9 - fa0/16 | PC3-S2, PC4-S2 |
| 30 | RRHH | 200.0.30.0/24 | 255.255.255.0 | 200.0.30.1 | fa0/17 - fa0/24 | PC5-S2, PC6-S2 |

Notas:
- Cada switch es un dominio VLAN independiente; los IDs 10/20/30 se repiten porque no hay trunk entre switches.
- El router usa una interfaz física por switch (`Gig0/0` para S1, `Gig0/1` para S2).
- `200.0.0.0/8` es pública (solo para laboratorio); `192.168.x.x` es privada.

## Configuración en switches

### Switch S1

```bash
enable
configure terminal
hostname S1

vlan 10
 name SISTEMAS
vlan 20
 name CONTABILIDAD
vlan 30
 name RRHH
exit

interface range fa0/1 - 8
 switchport mode access
 switchport access vlan 10

interface range fa0/9 - 16
 switchport mode access
 switchport access vlan 20

interface range fa0/17 - 24
 switchport mode access
 switchport access vlan 30

interface gig0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30

end
show vlan brief
show interfaces trunk
```

### Switch S2

```bash
enable
configure terminal
hostname S2

vlan 10
 name SISTEMAS
vlan 20
 name CONTABILIDAD
vlan 30
 name RRHH
exit

interface range fa0/1 - 8
 switchport mode access
 switchport access vlan 10

interface range fa0/9 - 16
 switchport mode access
 switchport access vlan 20

interface range fa0/17 - 24
 switchport mode access
 switchport access vlan 30

interface gig0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30

end
show vlan brief
show interfaces trunk
```

## Configuración en el router

```bash
enable
configure terminal
hostname R1

interface GigabitEthernet0/0
 no shutdown

interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0

interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0

interface GigabitEthernet0/1
 no shutdown

interface GigabitEthernet0/1.10
 encapsulation dot1Q 10
 ip address 200.0.10.1 255.255.255.0

interface GigabitEthernet0/1.20
 encapsulation dot1Q 20
 ip address 200.0.20.1 255.255.255.0

interface GigabitEthernet0/1.30
 encapsulation dot1Q 30
 ip address 200.0.30.1 255.255.255.0

end
write memory
show ip interface brief
show ip route
```

## Configuración de PCs

### Switch S1

| PC | VLAN | IP | Máscara | Gateway |
|----|------|----|---------|---------|
| PC1-S1 | 10 | 192.168.10.2 | 255.255.255.0 | 192.168.10.1 |
| PC2-S1 | 10 | 192.168.10.3 | 255.255.255.0 | 192.168.10.1 |
| PC3-S1 | 20 | 192.168.20.2 | 255.255.255.0 | 192.168.20.1 |
| PC4-S1 | 20 | 192.168.20.3 | 255.255.255.0 | 192.168.20.1 |
| PC5-S1 | 30 | 192.168.30.2 | 255.255.255.0 | 192.168.30.1 |
| PC6-S1 | 30 | 192.168.30.3 | 255.255.255.0 | 192.168.30.1 |

### Switch S2

| PC | VLAN | IP | Máscara | Gateway |
|----|------|----|---------|---------|
| PC1-S2 | 10 | 200.0.10.2 | 255.255.255.0 | 200.0.10.1 |
| PC2-S2 | 10 | 200.0.10.3 | 255.255.255.0 | 200.0.10.1 |
| PC3-S2 | 20 | 200.0.20.2 | 255.255.255.0 | 200.0.20.1 |
| PC4-S2 | 20 | 200.0.20.3 | 255.255.255.0 | 200.0.20.1 |
| PC5-S2 | 30 | 200.0.30.2 | 255.255.255.0 | 200.0.30.1 |
| PC6-S2 | 30 | 200.0.30.3 | 255.255.255.0 | 200.0.30.1 |

## Verificación

```bash
show vlan brief
show interfaces trunk
show ip interface brief
show ip route
ping [ip-destino]
```

## Configuración de nombres

```bash
enable
configure terminal
hostname [nombre]
```

## Verificación de VLANs

```bash
show vlan brief
show running-config
```
