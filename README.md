# springBootConWLS
Demuestra como podemos desplegar un spring boot en un Web Logic Server

Los pasos son:
## La aplicación debe implementar WebApplicationInitializer

public class DemoWeblogicApplication extends SpringBootServletInitializer implements WebApplicationInitializer{

## Debemos configurar el empaquetado como war en el pom

<packaging>war</packaging> 

## Debemos añadir en el pom el plugin de maven que empaqueta un war

<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-war-plugin</artifactId>

## Indicar que se usaran las librerias de Tomcat del WLS (Opcional)

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-tomcat</artifactId>
    <scope>provided</scope>
</dependency>

## Crear un direction /src/main/webapp/WEB-INF

En el directorio crear dos archivos:
### weblogic.xml
Aqui debemos especificar el contexto del servlet, y los paquetes que queremos tomar del war (en el caso de que tambien estuvieran en el classpath del servidor). Básicamente decimos que queremos usar las clases de springboot que se incluyen en el war

<context-root>/demo-weblogic</context-root>
<container-descriptor>
    <prefer-application-packages>
        <package-name>org.slf4j.*</package-name>
        <package-name>org.springframework.*</package-name>

### dispatcherServlet-servlet.xml
Este archivo esta prácticamente vacio. Simplemente colocar lo que se puede ver en este proyecto

## Actualizar el application.properties
Especificamos el root context del servlet (que coincidira con el que indicamos en weblogic.xml) y el basepath del servlet:

server.servlet.context-path=/demo-weblogic

//This is deprecated with Spring 2.1.x.RELEASE
//server.servlet.path=/api/v1.0
//This is the new way of settinh the servlet path with 2.1.x.RELEASE
spring.mvc.servlet.path=/api/v1.0

# Notas
- Es importante comprobar que las beans de spring, en particular el @RestController este incluido en el contexto, bien porque el @Autoconfigure lo encuentr o porque lo hayamos @Configure explicitamente

- Cuando instalamos la aplicación en el WLS, no nos mostrara ningun Rest endpoint, sin embargo, si miramos en los logs del WLS, veremos que arranca Sprint

- Podemos invocar nuestro controlador en:
http://localhost:7101/demo-weblogic/api/v1.0/resource
-> Root

# Referencias
https://medium.com/@raphaelrodrigues_74842/spring-boot-2-how-to-deploy-on-oracle-weblogic-12-d28ce9f854e

https://o7planning.org/en/11901/deploying-spring-boot-application-on-oracle-weblogic-server

http://www.virtual7.de/blog/2016/07/oracle-weblogic-server-spring-boot/