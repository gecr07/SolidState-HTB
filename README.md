# SolidState-HTB


## NMAP 

![image](https://github.com/gecr07/SolidState-HTB/assets/63270579/2af3b258-b5fa-4f3d-8c32-34b554d1d35e)

Enumere el servidor apache del puerto 80 y no se encontro nada pero en el 4555.

![image](https://github.com/gecr07/SolidState-HTB/assets/63270579/947e5aa4-82b0-46a3-b31e-fc09db614486)

Buscamos versiones vulnerables. Y encontramos "JAMES Remote Administration Tool 2.3.2" buscamos credenciales por defecto entontramos

```
root:root
searchsploit Apache James Server 2.3.2

```

![image](https://github.com/gecr07/SolidState-HTB/assets/63270579/31772226-8b3b-430b-b37c-bf733edb43be)




## RCE

Podemos listar los usuarios y ¿cambiar los passwords?

![image](https://github.com/gecr07/SolidState-HTB/assets/63270579/4b6c24c5-5a30-4199-b62f-57073072f441)

```
user: james
user: thomas
user: john
user: mindy
user: mailadmin

```

Vamos a cambiar los password de todos los usuarios. Para checar sus correos.Aqui existe un camino alternativo existe un exploit que permite escribiendo un usuario una vez que alguien inicia sesion ejecuta comandos.

![image](https://github.com/gecr07/SolidState-HTB/assets/63270579/71587bc2-b543-4c7f-baee-a390f30f1967)


### POP3 

De todas maneras  se necesita ver los correos porque se encuentra una contraseña para conectarse por SSH...Siempre que tengas el puerto 110(POP3) y usuarios intenta conectarte y ver los correos.


![image](https://github.com/gecr07/SolidState-HTB/assets/63270579/0419a569-ca5f-4588-88ee-c84183459c97)


```
ssh mindy@10.129.52.175
P@55W0rd1!2@

```

## Privilege Escalation

Nos conectamos por ssh ya que encontramos credenciales. El exploit se activa regresando una shell normal mientras que en donde nos conectamos nos regresa una rbash ( restricted bash)

```
ssh mindy@10.129.52.175 bash
### Para escapar de esa rbash

script /dev/null -c bash
CTRL + z

stty -echo raw;fg
              reset xterm
export TERM=xterm
export SHELL=/bin/bash
```

![image](https://github.com/gecr07/SolidState-HTB/assets/63270579/793451bc-8c13-4006-9640-aacc0b6752ef)

![image](https://github.com/gecr07/SolidState-HTB/assets/63270579/288ec71e-3e54-4b88-bde3-cd18c3191469)

Ahora pase el pspy y no vi nada resulta que hay un script que limpia /tmp moraleja pon el linpeas y el pspy en otra carpeta y ten mas opciones como procmon.sh de s4vitar.

procmon.sh

```
#!/bin/bash
#echo "Iniciando procmon..."
#ps -eo command

function ctrl_c(){

        echo -e "\n\n[!] Saliendo...\n"
        tput cnorm;exit 1
}

trap ctrl_c INT

tput civis

old_process="$(ps -eo command)"

while true; do
        new_process="$(ps -eo command)"
        diff <(echo "$old_process") <(echo "$new_process") | grep "[\>\<]" | grep -vE "command|procmon|kworker"
        old_process=$new_process
done


```

Entonces ese script se puede modificiar para que haga lo que queramos.

![image](https://github.com/gecr07/SolidState-HTB/assets/63270579/706f40df-907f-4448-9019-112095241846)

![image](https://github.com/gecr07/SolidState-HTB/assets/63270579/f02152f6-22d3-4421-82ff-1d257f185df6)













































































































