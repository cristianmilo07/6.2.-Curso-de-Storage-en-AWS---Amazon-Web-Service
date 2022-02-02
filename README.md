#  6.2.- Curso de Storage en AWS
_____________________________________________________________________
![](https://static.platzi.com/media/courses/og-aws-storage.png)
_____________________________________________________________________
### Introducción al documento
_____________________________________________________________________
El contenido de este documento esta basado en el curso del mismo nombre dictado por Carlos Andrés Zambrano Barrera en Platzi.

**Table of Contents**

[TOCM]

[TOC]

# 1. Introducción al storage en AWS

Bienvenido a este curso impartido por Carlos Zambrano. A lo largo del curso verás las diferentes opciones de almacenamiento en AWS junto con sus características y casos de uso.

# 2. Proyecto de arquitectura e implementación de almacenamiento

¡Hola! Como proyecto de este curso vas a hacer de cuenta que eres el arquitecto de soluciones de una empresa y el CEO te ha pedido diseñar una arquitectura de almacenamiento para la empresa de seguros. Además, debes implementar una prueba de concepto que como mínimo cuente con lo siguiente:
- 2 buckets en diferentes regiones.
- Replicación de archivos entre las regiones.
- Pruebas de replicación entre regiones.
- Configuración del bucket principal con las siguientes funcionalidades:
	Versionamento.
	Encriptación con KMS.
	Ciclo de vida de la siguiente forma (no para objetos versiones anteriores):
		1. Objetos mayores a 2 meses pasan a S3-IA.
		2. Objetos con 6 meses de antigüedad pasan a Glacier.
		3. Objetos con 2 años de antigüedad deben borrarse.
- Crear un servidor con un volumen EBS agregado a la instancia.
- A través de la CLI consultar los buckets generados y migrar la información que se
tiene en el EBS al bucket usando la CLI.
- Genera un snapshot del volumen y mígralo a la región en donde se encuentra el bucket secundario.

# 3. Características de S3
S3 es almacenamiento de objetos como archivos, PDF’s, imágenes, etc. Dentro de S3 contamos con diferentes tipos de almacenamiento:

- S3 Standar
- S3 IA
- S3 IA One Zone
- Glacier
- Dependiendo de la clase de S3 va a variar la durabilidad y disponibilidad.

Bucket es la unidad donde vamos a almacenar la información en S3, su identificador se encuentra compuesto por la región donde fue creado, la dirección de Amazon AWS y el nombre del bucket. Para los casos cuando queramos acceder a un objeto simplemente se le suma el nombre del objeto, este debe ser único, en minúsculas y no se permiten los caracteres especiales salvo _ y -. El nombre de un Bucket debe ser único a nivel global.

![](https://static.platzi.com/media/user_upload/Captura%20de%20Pantalla%202019-09-05%20a%20la%28s%29%2020.44.09-a1b1d483-4586-4b43-b6a0-b2162aa47be2.jpg)

# 4. Versionamiento de archivos en S3

Tener un control de versiones de tus archivos es importante y necesario cuando manejamos información muy delicada. En los casos donde tenemos un error o cargamos un archivo incompleto siempre podremos volver a la versión anterior de nuestro archivo.

Al momento de ir añadiendo varias versiones de un archivo AWS va a poner un tag al último archivo para tener claro que es esta la última versión. Es importante tener en cuenta que la característica de versionamiento te va a cobrar por el almacenamiento total de tus archivos, es decir la última versión y todas sus versiones anteriores.

# 5. Sitio web estático

Podremos utilizar nuestro propio dominio como en cualquier sitio web estático, para ello usaremos Route 53 que es el servicio encargado de la parte de DNS y gestión de dominios en S3.

En los sitios web estáticos debes tener en cuenta que el dominio deberá llamarse igual al bucket, los archivos index y error deben ser públicos, debe ser configurado con el servicio Route 53.

# 6. Logs a nivel de objetos

Podemos activar el Object-level Logging dentro de un bucket en S3 para llevar registro de todas las acciones que se realicen en este, esta funcionalidad nos sirve cuando tenemos buckets con información crítica. Al activar el Object-level Logging debemos conectarlo a CloudTrail.

# 7. Logs a nivel de objetos
Tomando ventaja del servicio de CDN de AWS podemos cargar nuestra información de forma más rápida, esta característica no se encuentra disponible en buckets que contengan puntos (.) en su nombre.

La transferencia acelerada te será sumamente útil cuando tengas que subir información a tu bucket, pero tú no te encuentres en la misma región donde creaste tu bucket.

# 8. Eventos en S3

Los eventos nos servirán en los casos donde queremos recibir notificaciones cuando se ejecute determinada acción dentro de un bucket con información importante.

Al momento de crear un evento debemos ponerle un nombre, indicarle la acción que debe notificar, además podemos especificarle la carpeta y el tipo de archivo. Por último, debemos indicarle hacia donde debe mandar la notificación, puede ser hacia:

- SNS Topic.
- SQS Queue.
- Lambda Function.

# 9. Replicación
La característica de replicar información se realiza solamente para buckets de una región a otra, no es posible pasar de un bucket de una misma región a otro de la misma.

El proceso de replicación se realiza de forma asíncrona. Es común realizar réplicas para Data Recovery, Auditorías y Compliance.

Al momento de replicar la información podemos indicarle que sean todos los objetos del bucket, los objetos que se encuentren dentro de determinada carpeta o aquellos que tengan cierto tag. Además, podemos replicar objetos encriptados.

#Clases de Storage en S3 ---

# 10. S3 - Estándar

s3 standard> durabilidad completamente alta,
s3 standard acceso poco frecuente
s3 unica zona acceso poco frecuente
glacier el mas barato y el que guarda con mayor longevidad la info

# 11. S3-IA
S3 Infrequent Access o de acceso poco frecuente está diseñado para almacenar objetos que son accedidos con menor frecuencia que S3 Estándar, su costo de almacenamiento es menor, pero el costo de solicitudes es mayor.

# 12. S3-IA única zona
Es para acceso poco frecuente, similar a S3-IA, pero con la diferencia de que solamente se encontrará en una zona de disponibilidad y tiene un costo menor en almacenamiento por GB.

# 13. Glacier
Glacier solamente será utilizado para backups y data histórica, el precio de almacenamiento por GB es sumamente menor siendo el más económico. Al ser data histórica la disponibilidad de la información es menor, siendo que pedimos la información una vez cada seis meses o cada año.

# 14. Ciclo de vida
Esta funcionalidad va a mover la información de una clase de almacenamiento a otra cada que pase cierto tiempo. No tendrá la misma frecuencia de accesibilidad un archivo de hace 1 año que uno de hace una semana, por ello el ciclo de vida nos será de utilidad para disminuir los costos de nuestros archivos.

El mínimo de tiempo para pasar objetos a S3-IA es de 30 días. Asimismo, deben pasar 120 días para mover la información a Glacier.

____
Hago una observación: algo que no fue mencionado en este modulo es esto al hacer una transición de datos a Glacier:
Cargos de solicitud de archivado de Glacier: cada objeto que pasa a la clase de almacenamiento GLACIER constituye una solicitud de archivo. Se aplica un costo para cada solicitud. Si tiene previsto pasar una cantidad grande de objetos, tenga en cuenta los costos de solicitud
fuente: https://docs.aws.amazon.com/es_es/AmazonS3/latest/dev/lifecycle-transition-general-considerations.html

Espero pueda servirle a alguien puesto que yo me llevé un sorpresa al revisar mi factura de AWS despues de aplicar politicas de ciclo de vida en un bucket con unos cuaaantos GB de informacion. Una sopresa algo co$to$a…

# 15. Estrategias de migración a la nube
# 16. Casos de uso.

### Segurida en S3 ---

# 17. Encriptación en S3 - Llaves administradas por AWS

Este área es importante porque si vamos a utilizar S3 para almacenar información crítica, debemos tomar todas las medidas necesarias para que esta información quede completamente segura y salvaguardada en S3.

Protección de datos mediante cifrado en S3

	- Server Side Encryption: Cuando AWS provee y administra las llaves de cifrado.
		- SSE - S3
		- SSE - KMS
		- SSE - l C
Client Side Encryption: Cuando el cliente quiere hacer la encriptación de los objetos.
SSE - S3

AWS se encarga de generar las llaves de cifrado, administrarlas y almacenarlas.
El sistema de cifrado utiliza AES-256 (Advanced Encryption Standard de 256 bits).
Las llaves quedan almacenadas en IAM > Encryption Keys, y son agrupadas por servicios, por ejemplo: aws/ebs.

¿Por qué vamos a usar este tipo en encriptación?

	Porque nosotros no queremos tener cargas de administración en el tema de la creación de las llaves o rotación de las llaves.
	Es completamente manejado por AWS, tanto la llave, como la rotación, como la encripción, como el almacenamiento de las llaves.


# 18. Encriptación en S3 - Llaves almacenadas en AWS creadas por el Usuario.

SSE - KMS (Key Management Service)

Este servicio se fundamenta de la siguiente forma:
Tenemos nuestro archivo plano, y utilizamos KMS para cifrar el archivo. La principal diferencia con el servicio anterior es que en esta ocasión nosotros vamos a crear las llaves, aunque AWS la va a almacenar.

Características

	-Llaves: Se crea la llave en IAM, y se debe especificar quiénes puedes administrarlas y usarlas.
	-Integración: Se encuentra integrado con CloudTrail para auditar el uso de las llaves.
	-Rotación: La rotación de las llaves es responsabilidad del usuario, no de AWS.

