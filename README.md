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

# 19. Encriptación en S3 - Llaves del Usuario

En esta forma de encriptación, el usuario tiene mayor participación en cuanto a la administración de las llaves.

	-El usuario proporciona las llaves de cifrado y AWS administra el cifrado de los objetos. AWS no almacena las llaves, lo hace el usuario y es totalmente su responsabilidad.
	-Para las solicitudes se deben realizar con HTTPS o serán negadas por AWS.
	-La rotación de las llaves es responsabilidad del usuario, no de AWS.
	
# 20. Encriptación en S3
Nota:
En la nueva versión de la consola AWS, ya no se crean por IAM si no por, Key Management Service (KMS)

# 21. Introducción a Políticas en S3

# 22. Ejemplos de Políticas en S3
hola, estos ejemplos son utilizes como guia.https://static.platzi.com/media/public/uploads/storage-en-aws_4d1da0f8-775e-4e1a-b99a-31345b11284b.pdf

# 23. ACL en S3
ACL en S3.

ACL (Lista de control de acceso)
Son permisos a nivel de cuentas; es decir, permitir o denegar el acceso de otras cuentas a nuestro Bucket.
Cuando se vaya a referenciar una cuenta en la configuración, hay que tener presente que no todas las regiones admiten que se coloquen como valor un correo electrónico; en cambio, deberíamos poner el id del usuario.

En lo posible y bajo ninguna circunstancia se recomienda dejar un Bucket público. Para eso ya están estas reglas y medidas de seguridad.

Las ACL son un mecanismo de seguridad que se complementan de forma adecuada con las Buckets Policies. Porque con las ACL puedo definir qué cuentas pueden acceder a mi Buckets, y con las Buckets Policies puedo establecer qué puedo hacer cada usuario.

# 24. Características de storage gateway
Storage Gateway es un servicio que conecta el mundo onpremise con la nube. Básicamente es un servicio híbrido que nos va a permitir aprovechar todas las ventajas, funcionalidades y características del almacenamiento en nube para arquitecturas e infraestructuras que tengamos implementadas onpremise. Este servicio va a actuar como intermediario entre nuestro datacenter físico con la nube, y nos va a proveer esa conexión para movernos hacia la nube y también nos va a permitir aprovechar todas las ventajas que hemos visto de la nube como seguridad, durabilidad, disponibilidad, replicación y demás.

**Características**

	-Definición: Almacenamiento híbrido con integración onpremise optimizado para transferencia de datos.
	-Casos de uso: Backups, archiving, disaster recovery, y cloud data processing.
	-Protocolos: Utiliza protocolos como NFS, SMB y iSCSI.
	-Integración: S3, EBS, Glacier.
	-Uso: Descargar e instalar una VM, configure y puede usarla.
	-Seguridad: Brinda todas las ventajas de seguridad y durabilidad que provee la nube de AWS.
	

# 25. File Gateway
Es un tipo de Storage Gateway, el cual permite que aplicaciones on-premise accedan a Storage a través de SMB o NFS.
Con File Gateway podemos empezar a pasar nuestros objetos de on-premise a S3, nuestros logs para procesamiento, y básicamente va a ser a nivel de objetos.
Este tipo de Storage provee una opción que se llama The Cache, el cual hace un caché local de los objetos que se están transfiriendo para que puedan ser accesibles de manera frecuente mientras se va haciendo la réplica a la nube.

# 26. Virtual Tape Library
	- Reemplaza el backup en cintas aprovechando el cloud.
	- El backup existente es generado directamente desde on-premise en virtual tape.

# 27. Volume Gateway
	-Crear caché de archivos locales. Mejora la latencia de archivos locales.
	-Crear snapshots locales en AWS. Estos backups son cargados asíncronamente a AWS.


# 28 Elastic File System
Elastic File System (EFS)

Sistemas de archivos elástico.

	-Pricing: El valor es por GB consumido, no por GB aprovisionado.
	-Uso: Aumento y reducción automática de su capacidad.
	-Funcionalidad: Concede un acceso compartido paralelo masivo a miles de instancias Amazon EC2.
	-IOPS: Permite altos niveles de IOPS.
	-Red: Permite mejor rendimiento de red.
	-Funcionalidad: Permite cifrado en reposo.
	-Compatibilidad: Sólo es compatible con sistemas operativos Linux. Usando Direct Connect, ESF puede ser utilizado desde On-Premise.
	-Montaje: Provee un paso a paso de montaje del sistema de archivos en Linux.

# 29. Casos de uso de EFS

# 30. Características de Elastic Block Storage
Elastic Block Storage es un servicio de almacenamiento basado en bloques; y al ser basado en bloques, nos va a brindar dos características que no tenemos en otros sistemas de archivos: podemos instalar sistemas operativos y podemos instalar aplicaciones. Este tipo de almacenamiento lo podemos ver como un disco duro virtual en la nube.

Características

Podemos adherirlo a una instancia. Sin embargo, a diferencia de EFS, en este servicio se paga por almacenamiento aprovisionado, y esto es porque como se utiliza como almacenamiento de sistemas operativos, no puede ser dinámico. Si se desea cambiar el tamaño del almacenamiento del disco, en Linux se realiza el cambio a través de la terminal; y en caso de utilizar Windows Server, se debe primero realizar el cambio en la consola de AWS y luego en la administración de discos dentro del servidor. Aunque se recomienda siempre aprovisionar desde el comienzo el tamaño de almacenamiento necesario para evitar tener que redimencionarlo, debido a que puede ser peligroso para la información.

	-Replicación: Cada volumen se replica dentro de una AZ (zona de disponibilidad) para proteger ante un error.
	-Diseño: Está diseñado para ayudar a diferentes cargas de trabajo.
	-Montaje: Un EBS puede estar asociado sólo a una instancia EC2, y una instancia EC2 puede estar asociada a varios EBS.
	-Boot: No se pueden encriptar y no permiten todos los tipos de EBS disponibles.
	-Volumen adicional: Pueden encriptarse y usar todos los tipos de EBS disponibles.
	-Montaje: Se debe hacer por la consola de AWS y a nivel de sistema operativo.
	*-Tipos: Hay diferentes tipos de EBS.
	-Protección: Se puede proteger el borrado accidental al crear la instancia.
	-Límites: Pueden ser de hasta 16TB. El almacenamiento origen varía según el tipo de EBS.

# 31. Tipos de EBS - GP2 - IO1
**SSD GP2 (General purpose)**
	-Balance entre performance y precio. 3 IOPS por cada DB hasta 10.000 IOPS. Son de uso general.
	-Hasta 3.000 IOPS para periodos cortos debajo de 1GB. Puede ser Boot de una instancia. Entre 1GB y 16TB.
**
SSD IO1**
	-Diseñados para I/O intensiva. Se usan para más de 10.000 IOPS. Hasta 20.000 IOPS por volumen.
	-Para BD no relacionales o uso intensivo I/O. Puede ser Boot de una instancia. Entre 4GB y 16TB.

# 32. Tipos de EBS - ST1 - SC1
**HDD ST1**

	-BigData, Data Warehouse, Log Process o Streaming. No pueden ser Boot de una EC2.
	-Entre 500GB y 16TB.
**HDD SC1**

	-Volumen de menor costo para cargas de acceso con poca frecuencia. No pueden ser Boot de una EC2.
	-Escenarios donde el costo es importante. Entre 500GB y 16TB.
**Magnetic (standard)**

	-Es el que tiene la instancia por defecto al ser encendida. No es recomendable porque no garantiza el almacenamiento de la información luego de que se reinicie o se apague.

# 33. Snapshots y AMI

	-Los snapshots son incrementales. Se pueden programar con el lifecycle manager.
	-Compatibles con cualquier sistema operativo.
**Diferencias entre Snapshots y AMIs**
La principal diferencia entre el snapshot y la AMI es, por decirlo de una manera informal, una foto en el tiempo del servidor; en cambio la AMI la podríamos utilizar para crear una plantilla de un Sistema Operativo para después replicarla. Por ejemplo, podemos crear una AMI que por defecto ya tenga una aplicación o un stack instalado y cuando copiemos esa AMI a otra región y la despleguemos, ya sabemos que siempre va a estar esa aplicación o ese stack en las instancias que despleguemos basadas en esa AMI. En el caso del snapshot, nos desplegaría el servidor que ya hemos estado utilizando con nuestra información, y en el caso de la AMI, podríamos venderla en el AWS MarketPlace compliendo algunas normas definidas por AWS.

# 34. Volumen EBS para Windows

# 35. Volumen EBS para Linux


# 36. AWS Storage S3 vs EBS vs EFS, Cuándo usar cada uno
https://www.youtube.com/watch?v=A8OcQkK9qQQ

# 37. Conclusiones


