# Configuración de Traefik v3.6 (Local & Producción)

Este repositorio contiene la infraestructura base para desplegar un proxy inverso **Traefik v3** mediante Docker, diseñado para ser utilizado tanto en entorno de desarrollo local como en producción (VPS).

## 1. Requisitos Previos

* Tener instalado `Docker` y `Docker Compose`.
* Crear la red externa que utiliza el proxy:
```bash
docker network create web

```



## 2. Preparación del Archivo de Certificados (SSL)

Traefik requiere un archivo JSON para almacenar los certificados emitidos por Let's Encrypt. Es **obligatorio** establecer permisos estrictos por seguridad:

1. Crea el archivo en la raíz del proyecto:
```bash
touch acme.json

```


2. Asigna los permisos de lectura/escritura únicamente al dueño (requerido por Traefik):
```bash
sudo chmod 600 acme.json

```



## 3. Configuración del Entorno

### Entorno Local

Actualmente el archivo está configurado para desarrollo.

* **Dashboard:** Disponible en `http://localhost:8081`.
* **Puerto Web:** Accesible en `http://localhost:8002`.

### Entorno Producción (VPS)

Para habilitar HTTPS y Let's Encrypt, modifica las secciones comentadas en el `docker-compose.yml`:

1. **Redirección:** Descomenta las líneas bajo `entrypoints.web` para forzar HTTPS.
2. **Certificados:** Descomenta la sección `certificatesresolvers` y **sustituye `tu-email@correo.com**` por tu dirección de correo real.
3. **Puertos:** Ajusta los puertos en la sección `ports` si el VPS ya tiene ocupado el 80 o 443 por otros servicios.

> **Nota Importante:** En producción, asegúrate de que el dominio esté apuntando correctamente mediante registros DNS (tipo A) a la IP de tu VPS antes de levantar el contenedor.

## 4. Cómo desplegar nuevas aplicaciones

Para que Traefik detecte automáticamente tus contenedores, cada nueva aplicación que agregues debe estar en la misma red `web` y usar los siguientes **Labels** en su `docker-compose.yml`:

```yaml
services:
  mi-app:
    networks:
      - web
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.mi-app.rule=Host(`tudominio.com`)"
      - "traefik.http.routers.mi-app.entrypoints=websecure"
      - "traefik.http.routers.mi-app.tls.certresolver=myresolver"

```

---

### Consejos adicionales para tu documentación:

1. **Diagrama de arquitectura:** Incluir un pequeño diagrama visual ayuda muchísimo a entender cómo los paquetes entran desde internet al VPS, pasan por Traefik y se distribuyen a los contenedores.
2. **Seguridad:** Recuerda añadir una advertencia de que `acme.json` **nunca** debe subirse a un repositorio público (GitHub/GitLab) porque contiene datos sensibles de seguridad. Asegúrate de tenerlo en tu `.gitignore`.
3. **Logs:** Añade un pequeño comando útil para depurar si Traefik falla:
```bash
docker logs -f traefik

```



¿Te parece que esta estructura cubre bien las dudas que suelen surgir al configurar esto?