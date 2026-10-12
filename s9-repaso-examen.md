# 📡 Guía de Redes Inalámbricas
Resumen de conceptos clave: bandas de frecuencia, tipos de redes inalámbricas, antenas, MIMO y configuración de laboratorio.

## 1. Bandas de frecuencia y estándares
📶 Banda de 2.4 GHz
|Estándar	|Velocidad máxima|	Características|
|-------|----|----------|
|802.11b	|11 Mbps	|Primer estándar popular, lento|
|802.11g	|54 Mbps	|Compatible con 802.11b|
|802.11n	|600 Mbps	|Doble banda (2.4 y 5 GHz)|

Ventajas: Mayor alcance, mejor penetración de obstáculos.

Desventajas: Más interferencias (microondas, Bluetooth, otros Wi-Fi).

📶 Banda de 5 GHz
|Estándar	|Velocidad máxima	|Características|
|-------|----|----------|
|802.11a	|54 Mbps	|Primer estándar en 5 GHz|
|802.11n	|600 Mbps	|Doble banda|
|802.11ac	|1.3 Gbps+	|Solo 5 GHz, muy rápido|
|802.11ax |(Wi-Fi 6)	|9.6 Gbps	Doble banda, más eficiente|

Ventajas: Menos interferencias, mayor velocidad.

Desventajas: Menor alcance, peor penetración de paredes.

### 📊 Comparativa rápida
| Banda	| Estándares| Alcance| Velocidad	| Interferencias|
|-------|-----------|--------|--------------|--------------|
| 2.4 GHz |	802.11b/g/n |	Mayor|	Menor	|Muchas|
| 5 GHz	 | 802.11a/n/ac/ax |	Menor|	Mayor|	Pocas|

## 2. Tipos de redes inalámbricas

|Tipo|	Nombre completo	|Cobertura	|Estándar IEEE	|Ejemplos|
|-------|----|----------|----------|----------|
|WPAN	|Wireless Personal Area Network	|~10 metros	|802.15|	Bluetooth, ZigBee|
|WLAN	|Wireless Local Area Network	|Hogar, oficina, campus	|802.11|	Wi-Fi|
|WMAN	|Wireless Metropolitan Area Network	|Ciudad (varios km)|	802.16	|WiMAX|
|WWAN	|Wireless Wide Area Network	|País / global|	802.20|	3G, 4G, 5G|

### 🌐 Resumen visual
```text
WPAN  →  WLAN  →  WMAN  →  WWAN
10 m    100 m    10 km    Global
802.15  802.11   802.16  802.20
```

## 3. Tipos de antenas

### 📡 Antenas omnidireccionales
 - Patrón de radiación: 360° (forma de "rosquilla").
 - Uso: Cobertura en interiores (oficinas, hogares, almacenes).
 - Ventaja: Cubren todas las direcciones.
 - Desventaja: Menor alcance que las direccionales.

### 📡 Antenas direccionales
 - Patrón de radiación: Concentrado en una dirección (forma de "haz").
 - Tipos comunes:
 - Yagi: Alcance medio, fácil instalación.
 - Parabólica: Alcance muy largo, enlaces punto a punto.
 - Uso: Enlaces entre edificios, transmisión a larga distancia.
 - Ventaja: Mayor alcance y potencia en una dirección.
 - Desventaja: Requieren apuntado preciso.

### 📊 Comparativa
|Característica|	Omnidireccional|	Direccional|
|-------|----|----------|
Cobertura|	360°	|Una dirección|
Alcance|	Corto/medio	|Largo|
Uso típico|	Interiores	|Enlaces punto a punto|
Ejemplos|	Antena de router	|Yagi, parabólica|

## 4. MIMO (Multiple-Input Multiple-Output)
Tecnología que usa múltiples antenas en el transmisor y en el receptor para mejorar el rendimiento de la comunicación.

### 🎯 Funciones principales
|Función|	¿Qué hace?|	Beneficio|
|-------|----|----------|
|Multiplexación espacial	|Envía varios flujos de datos en el mismo canal	|Mayor velocidad|
|Diversidad de transmisión	|Envía la misma señal por varias antenas	|Mayor fiabilidad|
|Conformación de haz (Beamforming)	|Concentra la señal hacia el usuario	|Menos interferencias|

### 📊 Resumen

| Ventaja | Descripción |
|---------|-------------|
| 🚀 Velocidad | Multiplica el throughput |
| 🛡️ Fiabilidad | Reduce errores de transmisión |
| 📡 Cobertura | Mejor alcance efectivo |
| 🔇 Interferencias | Reduce interferencia a otros |

---

## 5. Pasos del laboratorio

### 🔧 Configuración de red inalámbrica

| Paso | Elemento | ¿Qué es? | ¿Para qué sirve? |
|------|----------|----------|------------------|
| 1 | **SSID** | Nombre de la red inalámbrica | Identificar la red |
| 2 | **WPA2 Personal** | Método de autenticación | Verificar identidad con contraseña |
| 3 | **AES** | Algoritmo de cifrado | Proteger los datos transmitidos |
| 4 | **DHCP** | Protocolo de configuración dinámica | Asignar IPs automáticamente |

### 📝 Detalle de cada paso

#### 1. SSID (Service Set Identifier)
- Es el **nombre visible** de la red Wi-Fi.
- Los dispositivos lo usan para **identificar y conectarse**.
- Ejemplo: `MiRedCasa`, `Oficina_WiFi`.

#### 2. WPA2 Personal
- **WPA2** = Wi-Fi Protected Access 2.
- **Personal** = usa **contraseña precompartida (PSK)**.
- Es el método más seguro para hogares y pequeñas oficinas.
- Reemplaza al obsoleto WEP.

#### 3. AES (Advanced Encryption Standard)
- **Algoritmo de cifrado** usado con WPA2.
- Cifra los datos para que **no puedan ser interceptados**.
- Es más seguro y rápido que TKIP (usado en WPA).

#### 4. DHCP (Dynamic Host Configuration Protocol)
- Asigna **automáticamente**:
  - Dirección IP
  - Máscara de subred
  - Puerta de enlace
  - Servidores DNS
- **Ventaja:** No hay que configurar cada dispositivo manualmente.

### 🔄 Flujo del laboratorio

```
1. Configurar SSID         → Nombre de la red
2. Configurar WPA2 Personal → Contraseña
3. Seleccionar AES          → Cifrado
4. Activar DHCP             → IPs automáticas
5. Conectar dispositivos    → Verificar conectividad
```

---

## 📋 Resumen final

| Tema | Puntos clave |
|------|--------------|
| **Bandas** | 2.4 GHz (802.11b/g/n), 5 GHz (802.11a/n/ac/ax) |
| **Redes** | WPAN (802.15), WLAN (802.11), WMAN (802.16), WWAN (802.20) |
| **Antenas** | Omnidireccionales (360°) y direccionales (haz) |
| **MIMO** | Múltiples antenas → más velocidad, fiabilidad y cobertura |
| **Laboratorio** | SSID → WPA2 Personal → AES → DHCP |

---

> 💡 **Tip:** En Packet Tracer, para configurar una red inalámbrica segura, ve al router inalámbrico → pestaña **Config** → **Wireless** → configura SSID, WPA2, AES y DHCP en ese orden.