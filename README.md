
# Plataforma Smart Campus UIS

[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

Repositorio con las versiones de producción para la plataforma Smart Campus UIS, el sistema actual despliega los siguientes contenedores: 

- **MONGODB**: Base de datos documental para almacenamiento de los datos o mensajes enviados por los dispositivos, actualmente esta desactivada la seguidad y permite la conexión por el puerto 27017.

- **MONGO-EXPRESS**: Es una interface web que permite consultar las colecciones en base de datos, por defecto la plataforma guarda los datos en la base de datos **iot** en la colección **messages**, para el acceso se hace mediante el puerto 8081.

- **ECLIPSE MOSQUITTO**: Broker MQTT que permite el envió de mensajes hacía el servicio de datos, expone el puerto 1883, el topico para envio de datos desde los dispositivos es **device-messages**.

- **DATA MICROSERVICES**: Microservicio programado en JAVA que se suscribe al topico **device-messages** los mensajes que se reciven por este topico son almecandos en base de datos, tambien expone una API para consultar los datos de un dispositivo por el parametro uuid en el puerto **8091**.


## Formato de los mensajes

Los mensajes deben ser enviados al broker usando el tópico **device-messages**, estos mensajes deben estar en formato JSON y contener los siguientes atributos "respetar el nombre de los atributos para el correcto funcionamiento":

- **deviceUUID**: Es el identificador único universal de cada dispositivo, por el momento puede enviarse un String con el valor que se requiera, a futuro se validará con el microservicio de administración. 

- **topic**: Es el topico del mensaje, por ejemplo, humedad, temperatura, nivelCO2.

- **timeStamp**: Es la fecha y hora de generación del dato, acepta formato de envio DD-MM--YY HH:MM:SS, por ejemplo "14-03-2023 12:42:57".

- **values**: El valor o los valores enviados por los sensores en formato de objeto JSON, por elemplo un mensaje puede ser {"temperatura":25}, otro ejemplo puede ser {"Oxigeno":95, "CO2":22,"Lugar":"RUTA P8"}. 

- **status** (opcional): Estado del dispositivo cuando se envió el mensaje, por defecto de puede enviar "OK".

- **alert** (opcional): Booleano que indica si se ha disparado una alerta en el dispositivo.  



## Despliegue

El despliegue se debe realizar mediante Docker-Compose una vez descargado el respositorio se puede desplegar con el comando "docker-compose up -d" en la carpeta raíz del proyecto, aspectos de comunicación a tener en cuenta se describen a continuación: 

### Metodos de envió de mensajes

Actualmente están configurados los siguientes métodos para el envió de mensajes:
- **MQTT**: se pueden publicar mensajes desde dispositivos en la plataforma mediante el tópico *device-messages* siguiendo el formato de los mensajes especificado anteriormente.

### Data Messages API

Para consumir los mensajes la API REST que expone el microservicio en el puerto 8091 se cuenta con un endpoint que admite el parametro *uuid* que es el identificador único del dispositivo, con este parámetro se puede obtener los mensajes que ha enviado el dispositivo, un ejemplo de solicitud por metodo GET sería: 

```
http://localhost:8091/device/?uuid=1
```

Y la respuesta que se obtiene si existen mensajes guardados para este dispositivos en base de datos es: 


```json
[
    {
        "deviceUUID": "1",
        "topic": "temperatura",
        "timeStamp": "30-04-2023 10:39:02",
        "values": {
            "temperature": 10.0,
            "co2": 15.0,
            "location": "AP2"
        },
        "status": "OK",
        "alert": false
    },
    {
        "deviceUUID": "1",
        "topic": "temperatura",
        "timeStamp": "30-04-2023 10:39:07",
        "values": {
            "temperature": 10.0,
            "co2": 15.0,
            "location": "AP2"
        },
        "status": "OK",
        "alert": false
    }
]
```
## API Document. 

Para conocer los EndPoin de la API que expone el microservicio se instalo el paquete de Swagger que permite documenta fácilmente, para acceder se expone por la URL base del servicio + */swagger-ui.html#/*
