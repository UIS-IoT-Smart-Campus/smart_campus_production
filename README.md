
# Plataforma Smart Campus UIS

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

- **data**: Dato del sensor o dispositivo en String. 

- **status** (opcional): Estado del dispositivo cuando se envió el mensaje, por defecto de puede enviar "OK".

- **alert** (opcional): Booleano que indica si se ha disparado una alerta en el dispositivo.  

