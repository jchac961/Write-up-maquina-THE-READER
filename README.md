<img width="666" height="268" alt="Captura de pantalla 2026-06-01 233835" src="https://github.com/user-attachments/assets/dd1f2f29-b04e-49dc-8ccf-f75c9561caa9" />


# Write-up-maquina-THE-READER

Maquina de la plataforma www.whoami-labs.com

Iniciamos nuestra maquina vulnerable

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-01 230436" src="https://github.com/user-attachments/assets/483993a2-25f2-479f-9e08-f0d31b7e3d96" />

# RECONOCIMIENTO

Una vez iniciada la maquina realizamos un escaneo de puertos utilizando la herramienta nmap

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-01 230555" src="https://github.com/user-attachments/assets/071dd8fb-6ae6-418a-bb0b-98ff23f37b2a" />

Como podemos observar en la imagen anterior tenemos los siguientes puertos abiertos

✳️ #22 corriendo un servicio ssh en una version *OpenSSH 10.0p2 Debian 7+deb13u4 (protocol 2.0)*

✳️ #80 corriendo un servicio http en una version *Apache httpd 2.4.67 ((Debian))*

Ya teniendo informacion sobre estos dos puertos y servicios procedi a verificar en un nvegador a ver si encontraba información importante

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-01 230606" src="https://github.com/user-attachments/assets/db3c4ee1-f5f9-4968-88bc-3096aea4d8e3" />

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-01 234713" src="https://github.com/user-attachments/assets/bb0ac2a4-f044-47c4-85e7-f56e1e21f267" />

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-01 234720" src="https://github.com/user-attachments/assets/9cc115a1-e779-49fc-8281-8a0301e01e58" />

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-01 234724" src="https://github.com/user-attachments/assets/fc720897-df3e-49af-8422-e271a3706106" />

Como podemos ver en las imagenes anteriores es un sitio bastante sencillo, asi que procedi a verificar el codigoo a ver que podia encontrar, dentro del codigo encontre un script que lo que hacia era que cada vez que clickaba en uno de los archivos me invocaba a una api la cual me abria un documento

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-01 231416" src="https://github.com/user-attachments/assets/97dd9df6-03f0-406e-978b-50156800db1a" />

Procedi a hacer un fuzzing en busqueda de algo mas y encontre algunos directorios

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-01 230737" src="https://github.com/user-attachments/assets/86716258-299c-44a9-9be3-2baea8d8929f" />

En vista de que no encontraba mas nada a la vista procedi a abrir la herramienta burpsuite para interceptar el trafico a ver si podia obtener acceso a la maquina

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-01 232353" src="https://github.com/user-attachments/assets/369f7c8d-f593-4d01-8370-ad56e00fcc75" />

Como observamos en la imagen anterior en efecto cada vez que clickaba los vinculos la api llamaba un documento en este caso con nombre article1.txt, esta peticion la enviamos al repeater para luego proceder con la modificación a ver si lograbamos el acceso a la maquina

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-01 232403" src="https://github.com/user-attachments/assets/bf873701-2ebe-416c-b542-f8ea51720c75" />

# EXPLOTACION

Ya dentro del repeater lo que hicimos fue solicitar a la api que me permitiera visualizar el archivo etc/passw/ el cual contiene los nombres de usuarios que tienen acceso a la maquina, dentro de la cual encontramos un usuario llamado "reader"

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-01 232521" src="https://github.com/user-attachments/assets/765c8df4-6531-4727-8f93-faf3abaa1b77" />

Es importante resaltar que esta vulnerabilidad encontrada es llamada "Local File Inclusion" ya que nos permite ingresar comandos los cuales nos permite visualizar información sensible que por obvias razones no deberia estar expuesta

Luego procedimos a buscar una id_rsa para el usuario reader a ver si la misma se encontraba expuesta y obtuvimos una respuesta positiva

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-01 232736" src="https://github.com/user-attachments/assets/08893a07-c869-49d5-8bf5-2b0a6f21df50" />

Ya teniendo esta llave lo que hice fue crear un archivo llamado id_rsa y copie la llave encontrada, y luego le di los permisos a la misma para proceder a tratar de ingresar por ssh, de esta manera obteniendo el acceso a la maquina

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-01 233201" src="https://github.com/user-attachments/assets/d1758f02-1e39-4fef-b7e0-613073733a78" />

Busque la flag de usuario

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-01 233221" src="https://github.com/user-attachments/assets/f8845c88-4f72-46a1-880b-242f75944318" />

# ESCALADA DE PRIVILEGIOS

Luego busque los archivos o binarios que el usuario reader podia ejecutar como sudo y encontramos uno

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-01 233354" src="https://github.com/user-attachments/assets/5257b1a5-af72-4ad8-b920-53965b80b422" />

Ingresmosa este archivo y dentro tuvimos que utilizar el comando !sh, el cual nos permitiria abrir una shell como root

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-01 233547" src="https://github.com/user-attachments/assets/64ea87e3-2c28-4237-af8e-05beebadc17c" />

Obtuvimos acceso como root y buscamos de paso la flag

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-01 233623" src="https://github.com/user-attachments/assets/bc8aad35-b8c8-4458-8f67-7b2b7d9e8e74" />

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-01 233632" src="https://github.com/user-attachments/assets/08c7036f-5ef7-4469-ba2c-9ea9a2f6f429" />

# POWNED

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-01 233645" src="https://github.com/user-attachments/assets/fa73056a-c7dc-42c5-affd-4028c71f3b69" />

