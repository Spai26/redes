# VLANs (Virtual Local Area Networks)

## ¿Qué es una VLAN?

Una VLAN (Red de Área Local Virtual) es una agrupación lógica de dispositivos de red que se comunican como si estuvieran en la misma red física, independientemente de su ubicación geográfica. Las VLANs permiten segmentar una red física en múltiples redes lógicas.

---

## Tipos de VLANs

| Tipo | Descripción | Uso Común |
|------|-------------|-----------|
| VLAN de Datos | Tráfico de aplicaciones y usuarios | Oficinas, laboratorios |
| VLAN de Voz | Tráfico de telefonía IP | Comunicaciones |
| VLAN de Video | Tráfico multimedia | Vigilancia, conferencias |
| VLAN de Gestión | Acceso administrativo a switches | Administración de red |
| VLAN Nativa | VLAN por defecto (1) | Tráfico sin etiqueta |

---

## Ventajas de las VLANs

| Ventaja | Descripción |
|---------|-------------|
| **Segmentación** | Aísla tráfico entre departamentos |
| **Seguridad** | Controla acceso entre grupos de trabajo |
| **Rendimiento** | Reduce tráfico broadcast innecesario |
| **Flexibilidad** | Permite reorganizar la red sin cambios físicos |
| **Reducción de costos** | No requiere infraestructura física adicional |

---

## Configuración de VLANs en Switches Cisco

### 1. Acceder al modo de configuración

```bash
enable
configure terminal
```

### 2. Crear una VLAN

```bash
vlan 10
name Marketing
exit

vlan 20
name Ventas
exit
```

### 3. Asignar puertos a una VLAN

```bash
interface fastethernet 0/1
switchport mode access
switchport access vlan 10
exit
```

### 4. Configurar trunk (enlace entre switches)

```bash
interface fastethernet 0/24
switchport mode trunk
switchport trunk allowed vlan 1,10,20,30
exit
```

### 5. Verificar configuración

```bash
show vlan
show vlan brief
show interfaces switchport
show interfaces trunk
```

---

## Rango de VLANs Estándar

| Rango | Tipo | Descripción |
|-------|------|-------------|
| 1 | VLAN Nativa | Reservada por defecto |
| 2-1001 | Normal | VLANs estándar |
| 1002-1005 | Reservadas | Para Token Ring y FDDI |
| 1006-4094 | Extended | VLANs extendidas |

---

## VLAN Trunking Protocol (VTP)

VTP permite administrar VLANs de forma centralizada en múltiples switches.

### Modos de VTP

| Modo | Función | Propagación |
|------|---------|-------------|
| **Server** | Crea, modifica y elimina VLANs | Sí |
| **Client** | Recibe configuración de VLANs | Sí |
| **Transparent** | Procesa pero no participa | No |

### Configurar VTP

```bash
vtp mode server
vtp domain redempresa
vtp password cisco123

show vtp status
```

---

## Enrutamiento entre VLANs (Inter-VLAN Routing)

### Usando Router-on-a-Stick

```bash
! En el router
interface fastethernet 0/0.10
encapsulation dot1q 10
ip address 192.168.10.1 255.255.255.0
exit

interface fastethernet 0/0.20
encapsulation dot1q 20
ip address 192.168.20.1 255.255.255.0
exit

! En el switch (trunk)
interface fastethernet 0/24
switchport mode trunk
switchport trunk allowed vlan 10,20
```

### Usando Switch de Capa 3

```bash
ip routing
interface vlan 10
ip address 192.168.10.1 255.255.255.0
exit

interface vlan 20
ip address 192.168.20.1 255.255.255.0
exit
```

---

## Tabla de Direccionamiento por VLAN

| VLAN | Nombre | Red | Gateway | Broadcast |
|------|--------|-----|---------|-----------|
| 10 | Marketing | 192.168.10.0/24 | 192.168.10.1 | 192.168.10.255 |
| 20 | Ventas | 192.168.20.0/24 | 192.168.20.1 | 192.168.20.255 |
| 30 | TI | 192.168.30.0/24 | 192.168.30.1 | 192.168.30.255 |
| 40 | Invitados | 192.168.40.0/24 | 192.168.40.1 | 192.168.40.255 |

---

## Comandos Útiles de Verificación

```bash
! Ver VLANs existentes
show vlan

! Ver interfaces en una VLAN específica
show vlan id 10

! Ver configuración de trunking
show interfaces trunk

! Ver VLANs permitidas en un trunk
show interfaces fastethernet 0/24 trunk

! Ver información de routing entre VLANs
show ip route

! Ver tabla MAC de una VLAN
show mac address-table vlan 10

! Monitorear tráfico
show interfaces switchport
```

---

## Seguridad en VLANs

### Hopping de VLAN

| Técnica | Descripción | Prevención |
|---------|-------------|-----------|
| Switch Spoofing | Actuar como switch | Deshabilitar DTP |
| Double Tagging | Insertar tags VLAN | Usar VLAN nativa dedicada |
| VLAN Hopping | Cambiar entre VLANs | Implementar ACLs |

### Comandos de Seguridad

```bash
! Desactivar DTP (Dynamic Trunking Protocol)
interface fastethernet 0/24
switchport mode access
switchport nonegotiate

! Configurar VLAN nativa no utilizada
interface fastethernet 0/24
switchport trunk native vlan 999

! Implementar ACL de VLAN
access-list 100 deny icmp any any
vlan access-map restrict-traffic 10
match ip address 100
action drop
```

---

## Troubleshooting Común

| Problema | Causa Probable | Solución |
|----------|---------------|---------| 
| Dispositivos no comunican entre VLANs | Enrutamiento no configurado | Configurar gateway en cada VLAN |
| Tráfico no atraviesa trunk | VLAN no permitida en trunk | Agregar VLAN a `allowed vlan` |
| VTP no propaga VLANs | Modo VTP incorrecto | Verificar modo y dominio VTP |
| Dispositivos en VLAN errada | Puerto mal configurado | Verificar modo de puerto (access/trunk) |

---

## Referencias y Recursos

- Documentación oficial Cisco Networking Academy
- Estándar IEEE 802.1Q (VLAN Tagging)
- RFC 3069 - Problema de Tramas sobre Tramas

---

*Última actualización: 2026-08-18*
