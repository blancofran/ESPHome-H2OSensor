# Sensor de Nivel de Agua con ESPHome (ESP8266)

## Descripción

Este proyecto utiliza un microcontrolador ESP8266 con ESPHome para monitorear el nivel de agua de un tanque mediante un sensor analógico conectado al pin A0.

El dispositivo envía la información a Home Assistant a través de la API nativa de ESPHome y calcula automáticamente el porcentaje de llenado del tanque utilizando una fórmula de conversión basada en valores calibrados del sensor.

---

## Características

- Conexión nativa con Home Assistant.
- Actualizaciones automáticas vía OTA (Over The Air).
- Medición del voltaje del sensor analógico.
- Conversión automática a porcentaje de llenado.
- Sensor de tiempo en línea (uptime).
- Punto de acceso de respaldo en caso de pérdida de WiFi.
- Comunicación cifrada con Home Assistant.

---

## Hardware Requerido

### Microcontrolador

- ESP8266 (ESP-01 con ADC disponible o cualquier placa ESP8266 compatible con entrada analógica A0)

### Sensor

- Sensor analógico de nivel de agua con salida de voltaje proporcional al nivel medido.

### Alimentación

- Fuente de alimentación adecuada para el ESP8266.

---

## Configuración Inicial

### 1. Configurar la clave API

Reemplazar:

yaml api:   encryption:     key: "[*****REEMPLAZAR CON SU PROPIO API KEY*****]" 

Por la clave generada por Home Assistant o ESPHome.

---

### 2. Configurar el punto de acceso de respaldo

Reemplazar:

yaml ap:   ssid: "Esphome-Web-06A4Cb"   password: "[*****REEMPLAZAR CON SU PROPIA CONTRASEÑA*****]" 

Por una contraseña segura.

---

### 3. Configurar las credenciales WiFi

En el archivo secrets.yaml:

yaml wifi_ssid: MiRedWiFi wifi_password: MiContraseña 

---

## Sensores Expuestos

### Tiempo en línea

Permite conocer cuánto tiempo ha permanecido encendido el dispositivo.

Entidad:

text sensor.tiempo_en_linea 

---

### Voltaje del Sensor

Lee el valor analógico del pin A0.

Configuración:

yaml - platform: adc   pin: A0 

Características:

- Actualización cada 90 segundos.
- Valor interno (no visible en Home Assistant).
- Multiplicado por 1000 para facilitar la calibración.

Rango observado durante la calibración:

| Estado del tanque | Valor |
|------------------|--------|
| Vacío            | 315 |
| Lleno            | 450 |

---

### Porcentaje de Agua

Calcula el porcentaje de llenado a partir del valor analógico leído.

Entidad:

text sensor.porcentaje_de_agua 

Actualización:

text Cada 90 segundos 

Unidad:

text % 

---

## Fórmula de Conversión

La lógica utilizada es:

cpp if (voltaje >= 450)     porcentaje = 100; else if (voltaje <= 315)     porcentaje = 0; else     porcentaje = ((voltaje - 315) * 100) / (450 - 315); 

### Valores de referencia

| Lectura | Nivel |
|----------|--------|
| ≤ 315 | 0% |
| 382 | 50% |
| ≥ 450 | 100% |

---

## Calibración

Cada sensor puede variar ligeramente.

Para recalibrar:

1. Vacíe completamente el tanque.
2. Anote el valor reportado por el sensor.
3. Sustituya el valor:

cpp 315 

4. Llene completamente el tanque.
5. Anote el valor reportado.
6. Sustituya el valor:

cpp 450 

Ejemplo:

cpp return (((id(voltaje_sensor).state) - VALOR_VACIO) * 100) /        (VALOR_LLENO - VALOR_VACIO); 

---

## Actualización OTA

El dispositivo soporta actualizaciones remotas mediante:

yaml ota: 

Una vez instalado por primera vez mediante USB, las siguientes actualizaciones podrán realizarse desde ESPHome sin necesidad de volver a conectar físicamente el dispositivo.

---

## Seguridad

Se recomienda:

- Utilizar una clave API única.
- Configurar una contraseña robusta para el hotspot de respaldo.
- Mantener el dispositivo en una red WiFi protegida.
- Restringir el acceso a Home Assistant mediante usuarios y contraseñas seguras.

---

## Funcionamiento General

text Sensor de Nivel        │        ▼ Pin A0 del ESP8266        │        ▼ Lectura ADC        │        ▼ Conversión a porcentaje        │        ▼ ESPHome        │        ▼ Home Assistant 

---

## Posibles Mejoras Futuras

- Promediado de lecturas para reducir ruido.
- Alertas de nivel bajo o nivel alto.
- Detección de tanque vacío.
- Históricos de consumo de agua.
- Dashboard dedicado en Home Assistant.
- Integración con bombas de llenado automáticas.
- Compensación por variaciones de voltaje de alimentación.

---

## Licencia

Uso libre para proyectos personales, educativos y de automatización residencial.
Este proyecto puede modificarse y adaptarse según las necesidades de cada instalación.
