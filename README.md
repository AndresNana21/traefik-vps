# Arquitectura vps traefik

esta arquitectua te permite levantar un traefik en local y esta preparado para que descomentes las secciones para un entorno de produccion.

tambien contamos con una guia de como armar los archivos docker necesarios para poder desplegar las aplicaciones con otras tecnologias

necesitas crear un archivo acme con el siguente comadno, recuerda q el archivo necesita estar en la misma carpeta con el archivo docker-compose.yml

touch acme.json


comando para darle permisos

sudo chown 600:600 acme.json