
# S3 - STP y EtherChannel

## STP (Spanning Tree Protocol)

Protocolo de capa 2 que evita bucles en redes conectadas con múltiples caminos entre switches. Cuando hay enlaces redundantes, STP bloquea los puertos que generan bucles y solo deja activa la mejor ruta.
<img width="609" height="294" alt="designated-bridge-en-STP" src="https://github.com/user-attachments/assets/e7fdabc5-62b6-4212-a2da-7f464a5f14d7" />

### Conceptos clave

| Término | Significado |
|---------|-------------|
| **BPDU** | Unidad de datos del protocolo. Los switches se intercambian BPDUs para elegir el Root Bridge. |
| **BID** | Bridge ID: identificador del switch. Combina prioridad extendida + MAC. |
| **Root Bridge** | Switch elegido como raíz de la topología. Tiene el BID más bajo. |
| **Root Port** | Puerto con mejor camino hacia el Root Bridge. |
| **Designated Port** | Puerto que reenvía tráfico hacia un segmento. |
| **Blocked Port** | Puerto bloqueado para evitar bucles. |
| **PID** | Port ID: prioridad de puerto + número de puerto. Se usa como desempate. |

---

### Elección del Root Bridge

El switch con el **Bridge ID (BID) más bajo** gana.

```
BID = Prioridad del switch + Dirección MAC
```

**Ejemplo:**

| Switch | Prioridad | MAC | BID |
|--------|-----------|-----|-----|
| S1 | 32768 | 0000.0C11.1111 | 32768:0000.0C11.1111 |
| S2 | 32768 | 0000.0C11.2222 | 32768:0000.0C11.2222 |
| S3 | 4096 | 0000.0C11.3333 | 4096:0000.0C11.3333 |

**Resultado:** S3 es el Root Bridge porque tiene la prioridad más baja (4096).

**Regla de desempate:**
1. Gana el que tenga **menor prioridad**.
2. Si la prioridad es igual, gana el que tenga **menor MAC**.

#### Forzar Root Bridge

```bash
! Cambiar prioridad del switch
spanning-tree vlan 1 priority 4096

! O usar macro de Cisco para forzar Root
spanning-tree vlan 1 root primary
```

---

### Costo de los puertos (path cost)

STP elige la mejor ruta sumando el costo de cada enlace. **Menor costo = mejor ruta.**

| Velocidad | Costo STP original | Costo STP RSTP/MST |
|-----------|--------------------|--------------------|
| 10 Mbps | 100 | 2.000.000 |
| 100 Mbps | 19 | 200.000 |
| 1 Gbps | 4 | 20.000 |
| 10 Gbps | 2 | 2.000 |
| 100 Gbps | - | 200 |

**Truco para el examen:**
- FastEthernet (100 Mbps) = **19**
- GigabitEthernet (1 Gbps) = **4**
- TenGigabitEthernet (10 Gbps) = **2**

---

### Elección de puertos

1. **Root Bridge:** todos sus puertos activos son **Designated Ports**.
2. **En los demás switches:** se elige un **Root Port** por switch (el de menor costo hacia la raíz).
3. **En cada segmento:** se elige un **Designated Port**.
4. **El puerto que sobra** queda en estado **Blocking**.

#### Desempate para Root Port

Si dos puertos tienen el mismo costo hacia la raíz, se usa:

1. **Sender BID más bajo** (BID del switch vecino).
2. **Port ID (PID) más bajo** del vecino.
3. Si sigue empatado, **PID propio más bajo**.

```
PID = Prioridad de puerto (por defecto 128) + Número de puerto
```

**Ejemplo:**
- Puerto Fa0/1 → PID 128.1
- Puerto Fa0/2 → PID 128.2

Gana Fa0/1 porque tiene menor número de puerto.

---

### Estados de los puertos STP

| Estado | Aprende MAC | Reenvía tráfico | Duración |
|--------|-------------|-----------------|----------|
| Blocking | No | No | 20 seg |
| Listening | No | No | 15 seg |
| Learning | Sí | No | 15 seg |
| Forwarding | Sí | Sí | - |
| Disabled | No | No | - |

**Con Rapid PVST+ (RSTP)** los estados se reducen a: Discarding, Learning, Forwarding.

---

### Comandos básicos de STP

```bash
! Ver estado de STP
show spanning-tree

! Ver resumen de STP
show spanning-tree summary

! Ver STP en una VLAN específica
show spanning-tree vlan 1

! Cambiar prioridad para forzar Root Bridge
spanning-tree vlan 1 priority 4096

! Habilitar Rapid PVST+ (modo rápido)
spanning-tree mode rapid-pvst

! Cambiar costo de un puerto
interface fa0/1
 spanning-tree cost 10

! Cambiar prioridad de un puerto
interface fa0/1
 spanning-tree port-priority 64
```

---

### Ejercicio: identificar el Root Bridge

Dados tres switches conectados en triángulo:

| Switch | Prioridad | MAC |
|--------|-----------|-----|
| S1 | 32768 | 0000.1111.1111 |
| S2 | 32768 | 0000.1111.2222 |
| S3 | 32768 | 0000.1111.1110 |

**Pregunta:** ¿Cuál es el Root Bridge?

<details>
<summary>Respuesta</summary>
S3, porque tiene la MAC más baja al tener todos la misma prioridad.
</details>

**Si ahora S1 tiene prioridad 4096:**

<details>
<summary>Respuesta</summary>
S1, porque la prioridad es el primer criterio de elección.
</details>

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
