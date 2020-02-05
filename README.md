# Práctica Docker Compose


### ElasticSearch
![Alt text](images/cap1.png?raw=true "Title")

Se trata de un motor de búsqueda y análisis. Es distribuible y fácilmente escalable, enfocado sobre todo al mundo empresarial y científico. Es accesible a través de una extensa y elaborada API. Con esta herramienta podemos impulsar búsquedas extremadamente rápidas que respalden nuestras aplicaciones de descubrimientos de datos.

Está escrito en Java, utilizando la filosofía de desarrollo de código abierto y utiliza una licencia Apache, similar a la de software libre.

Trabajo conjuntamente con un motor de recopilación de datos llamado Logtash, y una plataforma de análisis y visualización llamada Kibana. Los tres productos están diseñados para trabajar de manera conjunta, una solución llamada “Elastic Stack” (anteriormente llamada ELK)

- Nombre del servicio: elasticsearch
- Imagen: docker.elastic.co/elasticsearch/elasticsearch-oss:6.8.2
- Variables de entorno (debéis averiguar los valores asociados)
    - http.host
    - network.host
    - trasnport.host
    - ES_JAVA_OPTS
- Volumen asociado a /usr/share/elasticsearch/data
- Pertence a la red "practica_network"
- Dispone de healthcheck (averiguar qué instrucción puede ser útil para verificar el estado de salud del servicio)
- Otras propiedades
    - ulimits:
        - memlock:
            - soft: -1
            - hard: -1

Asi se quedaria nuestro docker-compose.yml con la configuracion de Elasticsearch:
![Alt text](images/cap7.png?raw=true "Title")

Y este seria el resultado al entrar a "http://0.0.0.0:9200":
![Alt text](images/cap6.png?raw=true "Title")


### Mongo

![Alt text](images/cap2.png?raw=true "Title")

En un artículo anterior ya hablamos sobre MongoDB. Recordemos. MongoDB es una base de datos orientada a documentos. Esto quiere decir que en lugar de guardar los datos en registros, guarda los datos en documentos. Estos documentos son almacenados en BSON, que es una representación binaria de JSON.

Una de las diferencias más importantes con respecto a las bases de datos relacionales, es que no es necesario seguir un esquema. Los documentos de una misma colección - concepto similar a una tabla de una base de datos relacional -, pueden tener esquemas diferentes.

- Nombre del servicio: mongo
- Imagen: mongo:3
- Valumen asociado a /data/db
- Tanto el servicio expone en el puerto 27017, como el host publica en este mismo
- PErtenece a la red "practica_network"
- Dispone de healthcheck (averiguar qué instrucción puede ser útil para verificar el estado de salud del servicio)




### Graylog

![Alt text](images/cap3.png?raw=true "Title")

Graylog es efectivo cuando se trabaja con cadenas en bruto (es decir, syslog): la herramienta lo analiza en los datos estructurados que necesitamos.

También permite la búsqueda personalizada avanzada en los registros utilizando consultas estructuradas.

En otras palabras, cuando se integra correctamente con una aplicación web, Graylog ayuda a los ingenieros a analizar el comportamiento del sistema casi por línea de código.

La principal ventaja de Graylog es que proporciona una única instancia perfecta de recopilación de registros para todo el sistema.

Esto es útil si la infraestructura del sistema es grande y compleja. Podría distribuirse en múltiples lugares y no todos los miembros del equipo podrían tener acceso inmediato a todos sus componentes.

- Nombre del servicio: gralog
- imagen: graylo/graylog:3.1
- Variables de entrono (debéis averiguar los valores asociados)
    - GRAYLOG_PASSWORD_SECRET
    - GRAYLOG_ROOT_PASSWORD_SHA2
    - GRAYLOG_HTTP_EXTERNAL_URI
- Valumen asociado a /usr/share/elasticsearch/data
- El servicio se reinicia en caso de fallo
- Depende de los servicios de mongo y elasticsearch
- Pública en las siguientes puertos
    - 9000 -> 9000
    - 12201 -> 12201 (upd)
    - 12201 -> 12201
    - 1514 -> 1514
    - 1514 -> 1514 (upd)

### Mysql

![Alt text](images/cap4.jpeg?raw=true "Title")

Primero lo primero, tienes que saber cómo pronunciarlo: MY-ES-KYU-EL’ [maɪˌɛsˌkjuːˈɛl]. Una compañía sueca llamada MySQL AB originalmente desarrolló MySQL en 1994. La compañía de tecnología de los Estados Unidos Sun Microsystems luego tomó el control por completo cuando compró MySQL AB en el 2008. El gigante de la tecnología estadounidense Oracle adquirió Sun Microsystems en el 2010, y MySQL ha sido propiedad de Oracle desde entonces.

En cuanto a la definición general, MySQL es un sistema de gestión de bases de datos relacionales de código abierto (RDBMS, por sus siglas en inglés) con un modelo cliente-servidor. RDBMS es un software o servicio utilizado para crear y administrar bases de datos basadas en un modelo relacional.

- Nombre del servicio: mysql
- Imagen: mysql:5.7
- Valumen asociado a /var/lib/mysql
- Variables de entrono (debéis averiguar los valores asociados)
    - MYSQL_ROOT_PASSWORD
    - MYSQL_DATABASE
    - MYSQL_USER
    - MYSQL_PASSWORD
- Pertenece a la red "practica_network"
- Dispone de heathcheck (averiguar qué instrucción puede ser útil para verificar el estado de salud del servicio)

### Wordpress

![Alt text](images/cap5.png?raw=true "Title")

La definición más simple de WordPress es que se trata de una aplicación software para crear y gestionar sitios web (crear sus contenidos, etc.).

Hay muchas aplicaciones de este tipo, las cuales se conocen también como CMS (“Content Management System”, sistema de gestión de contenidos).

WordPress tiene, además, la peculiaridad de ser un proyecto de software libre y de fuentes abiertas que depende de la Fundación WordPress, una fundación sin ánimo de lucro, presidida por Matt Mullenweg, el creador de WordPress.

Esto quiere decir que su código es creado y mantenido por una comunidad de miles de desarrolladores voluntarios (lo que garantiza su continuidad) y que puedes disponer de la aplicación completamente gratis.


- Nombre del servicio: wordpress
- Imagen: wordpress:latest
- Variables de entorno (debéis averiguar los valor asociados)
    - WORDPRESS_DB_HOST
    - WORDPRESS_DB_USER
    - WORDPRESS_DB_PASSWORD
    - WORDPRESS_DB_NAME
- Volumen asociado a /var/www/html
- El servicio se reinicia en caso de fallo
- Pertenece a la red "practica_network"
- Depende del servicio de mysql
- Publica en los siguientes puertos
    - 9090 -> 80
- Dispone de un sistema de loggin (investigar cómo hacerlo) cuyo driver es gelf y con las siguientes opciones:
    - gelf-address_ udp://localhost:12201
    - tag: primeros_logs

Tras implementar todo deberéis verificar que los logs de wordpress se muestran en graylog. Para ello será necesario añadir un input global de tipo GELF UDP


```markdown
```