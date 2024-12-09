<h1> SquidProxy</h1>

 <p align="left">
   <img src="https://img.shields.io/badge/SquidProxy-green">
   </p>

<p>
Este reporte es creado para el regirtro de instalacion y configuracion de squidproxy en un servidor ubuntu(xubuntu).
</p>

## Comenzamos

<p>

 Antes de realizar cualquier instalacion debemos actualizar nuestro equipo. en ubuntu es con el siguiente comando
</p>

```
sudo apt-get update

```

<p>
 Este paso es opcional si desean trabajar remotamente, es decir usar el ssh, y es con los sigueintes pasos 
</p>

```
sudo apt-get install openssh-server

```

<p>
 
 Ahora si, continuamos con la instalacion de squidproxy ebn ubuntu  
 
</p>

```
sudo apt-get install squid

```
<p>
 Verificamos su instalacion con los siguientes comandos, uno para ver la version de squid que sea instalado y el otro para ayuda.
</p>

```
squid -v
squid -h

```
<p>
 Tambien podemo revisar si esta corriendo squid en nuestro equipo con el siguiente comando
</p>

```
service squid status

```
 <p>
  Ahora en la linea de comandos de ubuntu, revisamos las carpetas creadas por squid en la ruta
  cd /etc/squid/
  En ella veremos un archivo llamado squid.conf , este archivo es el de configuracion para squid, es por eso que haremos una copia 
  para cualquier situacion poder volver a la configuracion por defecto, eso lo haremos con el siguiente comando
 </p>

```
 sudo cp squid.conf squid.conf.backup

```
<p>
 Ahora bien, el archivo squid.conf, trae ejemplos de uso comentarios etc, que , para esta instalacion no se necesitaran , pero para poder visualizar mejor 
 cada parte del archivo y mejor manejo dentro de el , solo nos quedaremos con las puras configuracion , y para ello no ayudaremo dek editor vim. 
 Para instalarlo es con el siguiente comando
</p>

```
 sudo apt-get install vim

```

<p>
 una vez termianada la instalacion procedemos a abrir el archivo squid.con con vim, con el siguiente comando
</p>

```
sudo vim squid.conf

```
<p>
 una vez dentro , presionamos lo suiente para quitar los comentarios
</p>

```
 :g/^\$*#/d
```

<p>
 Ahora borramos los espcaios restantes con el siguiente comando
</p>

```
:g/^$/d
```

<p>
 Guardamos con :wq
</p>

<p>
 Ahora crearemos nuetras propias reglas, asi que crearemos un archivo que llamaremos reglas
</p>

```
sudo nano /etc/squid/conf.d/reglas.conf
```

<p>
 Una vez dentro del archivo que hemos creado(reglas), crearemos nuestras propias resticciones.
 creamos nuestra acl y buestra red local, en la ip sera la ip que tenga nuestro ubuntu, de preferencia asiganrle una ip fija.
 En mi caso queda asi:
</p>

```
acl miredlocal src 192.168.1.0/24
```
<p>
 Tambien colocamos la siguiente linea que es para nos de accesso a internet, en el anterior paso solo fue la creacion de nuestra red.
</p>

```
http_access allow miredlocal
```
<p>
 Cada que realicemos una configuracion a squid , debemos reiniciarlo para que los cambios surtan efecto.
Para esto tenemos dos opciones, la mejor , para mi es la segunda, que es donde te da un aviso en caso de existir un error.
</p>

```
service squid reload
o
sudo squid -k reconfigure
```

<p>
 ahora, popdemos conectar otro equipo para probar la conexion, en mi caso usare un host con win 11, tambien un mi switch.
 en la computadora win11 no dirirgimos en la barra de tareas en buscar, colocamos proxy (imagen 1), despues ingresamos en configuracion manual de proxy (imagen 2) y activamos y colocamos la ip de nuestro squid y el puerto, que por defecto es el 3128, le damos guardar y ya con esto tendremos la configuracion en nuestros navegadores, comporbamos que nos de salida a interner en el win11.
</p>

![imagen](https://github.com/user-attachments/assets/440ff1ae-2e84-4b7a-bae2-a00b474bac66)

![imagen](https://github.com/user-attachments/assets/d7d7ebd4-7942-4a03-ac08-2eaaa0dfe524)



<p>
 Ahora de regreso a squid, verificamos el registro de acceso del equipo win11. Abrimos una nueva pestania en el cmd y colocamos el siguiente comando
</p>

```
sudo tail -f /var/log/squid/access.log
```
<p>
 si no hay nada, es porque no se ah accedido a internet desde el win11 o no existe un host al cual auditar.
</p>

<p>

 Ahora vamos a el ficehro de nuestras reglas  para colocar crear reglas de acceso de nuestro host, que es la siguiente ruta
</p>

```
sudo nano /etc/squid/conf.d/reglas.conf
```

<p>
 Creamos una regla para negar el acceso a ciertos dominios, de la siguiente manera.
 En este ejemplo solo colocamos facebook , pero podrian colocar lo que sea
</p>

```
acl filtro_rrss dstdomain .facebook.com 
```

<p>
 Ahora denegamos el acceso a la regla creada
</p>


```
http_access deny filtro_rrss

```

<p>
 Reiniciamos squid
</p>

```
service squid reload 
o
sudo squid -k reconfigure
```

<p>
 Verificamos la configuracion en el win11 o el host en su caso.
 Ahora para tener un mejor orden , crearemos un archivo para negar dominios, con el siguiente comando.
 comentamos la de argumentos acl filtro_rrss dstdomain que teniamos en el archivo de reglas.
</p>

```
sudo nano dominios-denegados
```

<p>
 Ahora estando en el archivo nuevo, colocamos los dominios a negar el acceso, guardamos con ctr mas o , enter, y salimos con ctr mas x . 
 recargamos squid.
</p>

```
service squid reload 
o
sudo squid -k reconfigure
```
<p>
 ahora creamos una acl para negar expresiones reguler en reglas.
 para esto tambien crearemos un archivo nuevo, pero primero colocamos la regla y despues de este paso 
 creamos el archivo, verificar bien la ruta y el nombre a crear el archivo.
 colocamos lo siguente:
</p>

```
acl block-exp url_regex “/etc/squid/block-exp”

#denegar navegacion al elemento acl block-exp
http_access deny block-exp
```

<p>
 creamos el archivo block-exp con el siguiente comando y colocamos las palabras a negar en la navegacion , podria ser torren, crack etc, lo que se les ocurra. 
</p>

```
sudo nano block-exp
```

<p>
 Salvamos y reiniciamos squid , ahora en el navegador de win11 o el host, intentamos navegar a algun dominio o alguna pagina con el nombre que negamos.

 Hasta aqui ya esta la configuracion funcional, podemos revidar el log que abrimos en la otra pestania de squid para verificar el acceso y negacion.
</p>





