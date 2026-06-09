# Sensor de Nivel de Agua con ESPHome (ESP8266)

## Descripción

Este proyecto utiliza un microcontrolador ESP8266 con ESPHome para monitorear el nivel de agua de un tanque mediante un sensor analógico conectado al pin A0.

El dispositivo envía la información a Home Assistant a través de la API nativa de ESPHome y calcula automáticamente el porcentaje de llenado del tanque utilizando una fórmula de conversión basada en valores calibrados del sensor.

---

## Características

- Conexión nativa con Home Assistant.
- Actualizaciones automáticas vía OTA (Over The Air).
- Medición del valor analógico del sensor.
- Conversión automática a porcentaje de llenado.
- Sensor de tiempo en línea (uptime).
- Punto de acceso de respaldo en caso de pérdida de WiFi.
- Comunicación cifrada con Home Assistant.

---

## Hardware Requerido

### Microcontrolador

- ESP8266 compatible con ESPHome.
- Entrada analógica A0 disponible.

### Sensor

- Sensor analógico de nivel de agua con salida proporcional al nivel del tanque.

### Alimentación

- Fuente de alimentación adecuada para el microcontrolador utilizado.

---

## Configuración Inicial

### 1. Configurar la clave API

Reemplazar:

yaml api:   encryption:     key: "[*****REEMPLAZAR CON SU PROPIO API KEY*****]" 

Por una clave generada para su instalación de Home Assistant.

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

---

### Lectura del Sensor

Este sensor realiza la lectura analógica del pin A0 y se utiliza internamente para calcular el porcentaje de llenado.

Características:

- Actualización cada 90 segundos.
- No se muestra en Home Assistant (internal: true).
- El valor es multiplicado por 1000 para facilitar la calibración.

---

### Porcentaje de Agua

Calcula el porcentaje de llenado del tanque utilizando una interpolación lineal entre un valor de tanque vacío y un valor de tanque lleno.

Entidad:

text sensor.porcentaje_de_agua 

Unidad:

text % 

Actualización:

text Cada 90 segundos 

---

## Calibración del Sensor

Cada instalación puede producir lecturas diferentes debido a:

- Tipo de sensor utilizado.
- Altura y forma del tanque.
- Fuente de alimentación.
- Longitud del cableado.
- Tolerancias del hardware.

Por este motivo, es necesario calibrar los valores de referencia para cada instalación.

### Paso 1: Obtener el valor de tanque vacío

1. Vacíe completamente el tanque.
2. Observe el valor reportado por el sensor.
3. Anote ese valor como:

text VALOR_VACIO 

### Paso 2: Obtener el valor de tanque lleno

1. Llene completamente el tanque.
2. Observe el valor reportado por el sensor.
3. Anote ese valor como:

text VALOR_LLENO 

### Paso 3: Actualizar la fórmula

Sustituya los valores utilizados en el código por los obtenidos durante la calibración.

La lógica general es:

cpp if (lectura >= VALOR_LLENO)     return 100; else if (lectura <= VALOR_VACIO)     return 0; else     return ((lectura - VALOR_VACIO) * 100) /            (VALOR_LLENO - VALOR_VACIO); 

---

## Ejemplo de Funcionamiento

Supongamos que durante la calibración se obtienen:

text VALOR_VACIO = 300 VALOR_LLENO = 500 

Entonces:

| Lectura | Nivel calculado |
|----------|----------------|
| 300 | 0% |
| 400 | 50% |
| 500 | 100% |

Los valores anteriores son únicamente un ejemplo y no deben utilizarse directamente sin realizar la calibración correspondiente.

---

## Recomendaciones de Calibración

Para obtener mejores resultados:

- Realice la calibración con el tanque realmente vacío y realmente lleno.
- Tome varias lecturas y utilice un promedio.
- Espere algunos minutos después de llenar o vaciar el tanque para que el nivel se estabilice.
- Si observa fluctuaciones, considere implementar filtros o promedios adicionales en ESPHome.

---

## Actualización OTA

El dispositivo soporta actualizaciones remotas mediante:

yaml ota: 

Después de la primera carga del firmware, las siguientes actualizaciones pueden realizarse desde ESPHome sin necesidad de conectar nuevamente el dispositivo por cable.

---

## Seguridad

Se recomienda:

- Utilizar una clave API única.
- Configurar una contraseña robusta para el hotspot de respaldo.
- Mantener el dispositivo dentro de una red WiFi segura.
- Limitar el acceso a Home Assistant mediante usuarios y contraseñas seguras.

---

## Flujo de Funcionamiento

text Sensor de Nivel        │        ▼ Pin A0        │        ▼ Lectura Analógica        │        ▼ Conversión a %        │        ▼ ESPHome        │        ▼ Home Assistant 

---

## Posibles Mejoras Futuras

- Promediado de lecturas para reducir ruido.
- Alertas por nivel bajo o nivel alto.
- Detección automática de tanque vacío.
- Historial de consumo de agua.
- Dashboard dedicado en Home Assistant.
- Integración con sistemas automáticos de llenado.
- Compensación por variaciones de voltaje de alimentación.

---

## Licencia

Uso libre para proyectos personales, educativos y de automatización residencial.

Puede modificarse y adaptarse libremente según las necesidades de cada instalación.
