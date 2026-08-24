# Redes I — Referencia rápida

Guía para identificar redes, subredes y conceptos básicos de redes de computadoras.

---

## 1. Identificar una red o subred

Una **red** es el rango de direcciones IP que comparten el mismo prefijo. Una **subred** es una división de esa red.

### Fórmula básica

Dada una IP y una máscara:

```
Dirección de red = IP AND Máscara
Broadcast        = Dirección de red OR (NOT Máscara)
Hosts válidos    = desde (red + 1) hasta (broadcast - 1)
```

### Ejemplo

```
IP:      192.168.10.50
Máscara: 255.255.255.0 (/24)

Red:         192.168.10.0
Broadcast:   192.168.10.255
Hosts:       192.168.10.1 - 192.168.10.254
Gateway:     normalmente 192.168.10.1
```

---

## 2. Trucos para reconocer redes rápido

### a) Clases de red (classful)

| Clase | Primer octeto | Máscara por defecto | Uso típico |
|-------|---------------|---------------------|------------|
| A | 1 - 126 | 255.0.0.0 (/8) | Grandes redes |
| B | 128 - 191 | 255.255.0.0 (/16) | Redes medianas |
| C | 192 - 223 | 255.255.255.0 (/24) | Redes pequeñas |
| D | 224 - 239 | - | Multicast |
| E | 240 - 255 | - | Experimental |

**Truco:** mirá el primer número de la IP y sabés de qué clase es.

```
10.0.0.1      → Clase A
172.16.0.1    → Clase B
192.168.1.1   → Clase C
```

### b) Máscaras más comunes

| CIDR | Máscara | Hosts útiles |
|------|---------|--------------|
| /8  | 255.0.0.0 | 16.777.214 |
| /16 | 255.255.0.0 | 65.534 |
| /24 | 255.255.255.0 | 254 |
| /26 | 255.255.255.192 | 62 |
| /27 | 255.255.255.224 | 30 |
| /28 | 255.255.255.240 | 14 |
| /29 | 255.255.255.248 | 6 |
| /30 | 255.255.255.252 | 2 |

**Truco:** para calcular hosts útiles rápido:

```
hosts = 2^(bits de host) - 2
```

Ejemplo: `/26` tiene 6 bits de host → `2^6 - 2 = 62` hosts.

### c) Redes privadas (RFC 1918)

| Clase | Rango privado | Máscara por defecto |
|-------|---------------|---------------------|
| A | 10.0.0.0 - 10.255.255.255 | /8 |
| B | 172.16.0.0 - 172.31.255.255 | /12 |
| C | 192.168.0.0 - 192.168.255.255 | /16 |

**Truco:** si la IP empieza con `10.`, `172.16-31.` o `192.168.`, es privada.

### d) IP pública vs privada

- **Privada:** usada dentro de una red local; no se enruta en internet.
- **Pública:** única en internet; asignada por ISP o registrada.

```
192.168.1.10   → privada
200.0.0.50     → pública
10.0.0.5       → privada
8.8.8.8        → pública (Google DNS)
```

---

## 3. Tipos de redes

| Tipo | Sigla | Alcance | Ejemplo |
|------|-------|---------|---------|
| Personal Area Network | PAN | Pocos metros | Bluetooth, USB |
| Local Area Network | LAN | Edificio, oficina | Red de una casa o empresa |
| Metropolitan Area Network | MAN | Ciudad | Red municipal de fibra |
| Wide Area Network | WAN | País/continente/mundo | Internet, conexión entre sucursales |
| Wireless LAN | WLAN | LAN sin cables | Wi-Fi |

### Diferencia clave: LAN vs WAN

| LAN | WAN |
|-----|-----|
| Corta distancia | Larga distancia |
| Alta velocidad | Velocidad variable |
| Administrada por el usuario | Administrada por ISP/operador |
| Ejemplo: red interna | Ejemplo: internet |

---

## 4. Conceptos base de Redes I

### Dirección IP

Identificador único de un dispositivo en una red. Tiene 32 bits en IPv4 y se escribe en notación decimal punteada.

```
192.168.10.5
```

### Máscara de subred

Define qué parte de la IP es la red y qué parte es el host.

```
255.255.255.0  →  los primeros 24 bits son red, últimos 8 bits son host
```

### Gateway

Puerta de salida predeterminada. Es la IP del router que conecta la red local con otras redes.

```
Gateway: 192.168.10.1
```

### Broadcast

Dirección especial que envía un mensaje a todos los hosts de la red.

```
Red: 192.168.10.0/24
Broadcast: 192.168.10.255
```

### DNS

Sistema que traduce nombres de dominio a direcciones IP.

```
www.google.com → 142.250.80.46
```

### DHCP

Protocolo que asigna automáticamente IP, máscara, gateway y DNS a los dispositivos.

### MAC

Dirección física de la tarjeta de red. Tiene 48 bits.

```
00:1A:2B:3C:4D:5E
```

### Switch

Dispositivo de capa 2 que conecta dispositivos dentro de la misma red usando direcciones MAC.

Los **switches capa 3** también pueden enrutar entre VLANs usando SVI, funcionando como switch y router al mismo tiempo.

### Router

Dispositivo de capa 3 que conecta diferentes redes y enruta paquetes entre ellas.

### VLAN (Virtual LAN)

División lógica de una red de capa 2. Dispositivos en VLANs distintas están aislados entre sí a menos que exista un dispositivo de capa 3 que las ruteé. Ver métodos de enrutamiento interVLAN en `s2 - enrutamiento-intervlan.md`.

### Protocolo

Conjunto de reglas que permiten la comunicación entre dispositivos.

Ejemplos:
- **HTTP/HTTPS:** navegación web
- **FTP/SFTP:** transferencia de archivos
- **SSH:** acceso remoto seguro
- **DNS:** resolución de nombres
- **TCP:** transporte confiable
- **UDP:** transporte rápido, no confiable
- **IP:** enrutamiento de paquetes

### Modelo TCP/IP

| Capa | Función | Ejemplos |
|------|---------|----------|
| Aplicación | Servicios para el usuario | HTTP, FTP, DNS, SSH |
| Transporte | Comunicación extremo a extremo | TCP, UDP |
| Internet | Enrutamiento lógico | IP, ICMP |
| Acceso a la red | Transmisión física | Ethernet, Wi-Fi |

### Modelo OSI

| Capa | Nombre | Ejemplo |
|------|--------|---------|
| 7 | Aplicación | Navegador, correo |
| 6 | Presentación | Cifrado, compresión |
| 5 | Sesión | Control de sesión |
| 4 | Transporte | TCP, UDP |
| 3 | Red | IP, routers |
| 2 | Enlace de datos | Switches, MAC |
| 1 | Física | Cables, señales |

**Truco mnemotécnico:** "A todos señores, transporten redes, enlaces físicos" → Aplicación, Presentación, Sesión, Transporte, Red, Enlace, Física.

---

## 5. Ejercicios rápidos

### Ejercicio 1

¿A qué clase pertenece `172.20.5.1`?

<details>
<summary>Respuesta</summary>
Clase B (primer octeto entre 128 y 191).
</details>

### Ejercicio 2

¿Es pública o privada la IP `10.50.3.20`?

<details>
<summary>Respuesta</summary>
Privada (rango 10.0.0.0/8).
</details>

### Ejercicio 3

Para `192.168.5.0/26`, ¿cuántos hosts válidos hay?

<details>
<summary>Respuesta</summary>
62 hosts. Bits de host = 6 → 2^6 - 2 = 62.
</details>

### Ejercicio 4

¿Cuál es la dirección de red de `200.0.10.50/24`?

<details>
<summary>Respuesta</summary>
200.0.10.0
</details>

---

## 6. Tabla de referencia para subneteo rápido

| Necesitas | Máscara | Hosts |
|-----------|---------|-------|
| 2 subredes de 100 hosts | /25 | 126 |
| 4 subredes de 50 hosts | /26 | 62 |
| 8 subredes de 20 hosts | /27 | 30 |
| 16 subredes de 10 hosts | /28 | 14 |
| 32 subredes de 4 hosts | /29 | 6 |
| 64 subredes de 2 hosts | /30 | 2 |

**Regla:** cada vez que tomás 1 bit de la parte de host, duplicás la cantidad de subredes y dividís a la mitad los hosts por subred.

---

## 7. Dudas comunes

### ¿Por qué restamos 2 en el cálculo de hosts?

Porque la primera IP es la dirección de red y la última es el broadcast. Ambas no se asignan a hosts.

### ¿Qué es CIDR?

Classless Inter-Domain Routing. Es la notación `/24`, `/26`, etc., que indica cuántos bits de la IP corresponden a la red.

### ¿Cuándo uso /30?

Para enlaces punto a punto, como entre dos routers. Solo necesitás 2 IPs usable.

---

## Referencias

- Kurose, J. (2017). *Redes de computadoras: un enfoque descendente basado en internet*. Pearson.
- Robledo Sosa, C. (2002). *Redes de computadoras*. IPN.
- RFC 1918 — Address Allocation for Private Internets.
