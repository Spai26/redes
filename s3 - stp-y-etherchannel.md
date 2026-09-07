
# S3 - STP y EtherChannel

## STP (Spanning Tree Protocol)

Protocolo de capa 2 que evita bucles en redes conectadas con múltiples caminos entre switches. Cuando hay enlaces redundantes, STP bloquea los puertos que generan bucles y solo deja activa la mejor ruta.
<img width="609" height="294" alt="designated-bridge-en-STP" src="https://github.com/user-attachments/assets/e7fdabc5-62b6-4212-a2da-7f464a5f14d7" />

### Conceptos clave

| Término | Significado |
|---------|-------------|
| **BID** | Bridge ID: identificador del switch. Combina prioridad y MAC. |
| **Root Bridge** | Switch elegido como raíz de la topología. Tiene el BID más bajo. |
| **Root Port** | Puerto con mejor camino hacia el Root Bridge. |
| **Designated Port** | Puerto que reenvía tráfico hacia un segmento. |
| **Blocked Port** | Puerto bloqueado para evitar bucles. |

### Comandos básicos de STP

```bash
! Ver estado de STP
show spanning-tree

! Cambiar prioridad para forzar Root Bridge
spanning-tree vlan 1 priority 4096

! Habilitar Rapid PVST+ (modo rápido)
spanning-tree mode rapid-pvst
```

---

## EtherChannel

Tecnología que agrupa varios enlaces físicos Ethernet en un solo enlace lógico llamado **Port Channel**. Se usa entre switches, o entre switch y servidor/router.

### Ventajas

- Mayor ancho de banda agregado.
- Redundancia sin que STP bloquee los enlaces.
- Balanceo de carga entre los enlaces físicos.
- No requiere actualizar a un enlace más costoso.

### Protocolos de negociación

| Protocolo | Modo activo | Modo pasivo | Descripción |
|-----------|-------------|-------------|-------------|
| **PAgP** | `desirable` | `auto` | Propio de Cisco. |
| **LACP** | `active` | `passive` | Estándar IEEE 802.3ad. Recomendado. |
| **Estático** | `on` | `on` | Sin negociación. Ambos lados en `on`. |

**Regla:** ambos lados deben usar el mismo protocolo.

---

## Configuración de EtherChannel

### Paso 1: configurar los puertos como trunk (antes de agruparlos)

```bash
interface range fa0/21 - 22
 switchport mode trunk
 switchport trunk allowed vlan 1,10,20,30
```

### Paso 2: crear el EtherChannel con LACP

```bash
interface range fa0/21 - 22
 channel-group 1 mode active
```

### Paso 3: verificar

```bash
show etherchannel summary
show interfaces port-channel 1
show interfaces trunk
```

---

## Ejemplo completo: dos switches con EtherChannel

### Switch S1

```bash
enable
configure terminal
hostname S1

interface range fa0/21 - 22
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
 channel-group 1 mode active

interface port-channel 1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30

end
show etherchannel summary
```

### Switch S2

```bash
enable
configure terminal
hostname S2

interface range fa0/21 - 22
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
 channel-group 1 mode active

interface port-channel 1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30

end
show etherchannel summary
```

---

## Ejercicio práctico: tres switches con tres EtherChannels

### Topología

```
        S1
       /  \
  Po1 /    \ Po2
     /      \
   S3 ------ S2
      Po3
```

### Tabla de Port Channels

| EtherChannel | Switches | Puertos físicos | Protocolo |
|--------------|----------|-----------------|-----------|
| Po1 | S1 - S2 | Fa0/21 - Fa0/22 | LACP active |
| Po2 | S1 - S3 | Fa0/23 - Fa0/24 | LACP active |
| Po3 | S2 - S3 | Gi0/1 - Gi0/2 | LACP active |

### Configuración en cada switch

```bash
! S1
interface range fa0/21 - 22
 switchport mode trunk
 channel-group 1 mode active

interface range fa0/23 - 24
 switchport mode trunk
 channel-group 2 mode active

! S2
interface range fa0/21 - 22
 switchport mode trunk
 channel-group 1 mode active

interface range gi0/1 - 2
 switchport mode trunk
 channel-group 3 mode active

! S3
interface range fa0/23 - 24
 switchport mode trunk
 channel-group 2 mode active

interface range gi0/1 - 2
 switchport mode trunk
 channel-group 3 mode active
```

---

## Tips

1. **Configurá los puertos antes de agruparlos:** modo trunk, VLANs permitidas, velocidad y dúplex iguales.
2. **Ambos lados del EtherChannel deben coincidir:** mismo protocolo, mismos puertos, misma configuración.
3. **No configures la interfaz física después de agruparla:** la configuración se aplica al `port-channel`.
4. **Si un enlace falla, el Port Channel sigue funcionando** con los enlaces restantes.
5. **LACP es estándar; usalo en lugar de PAgP** cuando haya equipos de diferentes marcas.
6. **STP ve al EtherChannel como un solo enlace**, por eso no bloquea puertos redundantes dentro del grupo.

---

## Verificación

```bash
show etherchannel summary
show etherchannel port-channel
show interfaces port-channel 1
show spanning-tree
```

---

## Caso Cisco: servidor conectado con EtherChannel

```bash
! Switch
interface range gi0/1 - 2
 channel-group 5 mode active

interface port-channel 5
 switchport mode access
 switchport access vlan 10

! Servidor (con NIC teaming)
Configurar LACP en el teaming de la tarjeta de red.
```

---

## Referencias

- Kurose, J. (2017). *Redes de computadoras: un enfoque descendente basado en internet*. Pearson.
- Robledo Sosa, C. (2002). *Redes de computadoras*. IPN.
- Documentación Cisco: EtherChannel.
