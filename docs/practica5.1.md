# practica-iaw-5.1

# HTTPS con Let's Encrypt, Docker y Docker Compose

## Script en docker-compose

Este script define el escenario de Docker Compose para desplegar un sitio web PrestaShop con HTTPS habilitado mediante Let's Encrypt, utilizando la imagen [HTTPS-PORTAL](https://hub.docker.com/r/steveltn/https-portal).

---

### 1. Archivo `docker-compose.yml`

```yaml
version: '3.4'

services:
  mysql:
    image: mysql:9.1
    ports: 
      - 3306:3306
    environment: 
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
      - MYSQL_DATABASE=${MYSQL_DATABASE}
      - MYSQL_USER=${MYSQL_USER}
      - MYSQL_PASSWORD=${MYSQL_PASSWORD}
    volumes: 
      - mysql_data:/var/lib/mysql
    networks: 
      - backend-network
    restart: always
  
  phpmyadmin:
    image: phpmyadmin:5.2.1
    ports:
      - 8080:80
    environment: 
      - PMA_ARBITRARY=1
    networks: 
      - backend-network
      - frontend-network
    restart: always
    depends_on: 
      - mysql

  prestashop:
    image: prestashop/prestashop:8
    environment: 
      - DB_SERVER=mysql
    volumes:
      - prestashop_data:/var/www/html
    networks: 
      - backend-network
      - frontend-network
    restart: always
    depends_on: 
      - mysql

  https-portal:
    image: steveltn/https-portal:1
    ports:
      - 80:80
      - 443:443
    restart: always
    environment:
      DOMAINS: "${DOMAIN} -> http://prestashop:80"
      STAGE: 'production' # No utilizar production hasta probar staging
      # FORCE_RENEW: 'true'
    networks:
      - frontend-network

volumes:
  mysql_data:
  prestashop_data:

networks: 
  backend-network:
  frontend-network:
```

---

### 2. Archivo `.env`

```env
MYSQL_ROOT_PASSWORD=root
MYSQL_DATABASE=prestashop
MYSQL_USER=ps_user
MYSQL_PASSWORD=ps_password
DOMAIN=jmsr-iawpractica51.ddns.net
```

---

### 3. Despliegue y configuración

1. **Clonar el repositorio:**

   ```bash
   git clone https://github.com/jsegrod1512/practica-iaw-5.1.git
   cd practica-iaw-5.1
   ```

2. **Crear el archivo `.env`:**  
   Colocamos en la raíz del proyecto el archivo `.env` con las variables de entorno.

3. **Levantar los servicios:**

   ```bash
   docker-compose up -d
   ```

4. **Verificar el despliegue:**  
   - Accedemos a `https://jmsr-iawpractica51.ddns.net` para visualizar el sitio PrestaShop con HTTPS habilitado.  

---

**Capturas de pantalla:**

- ![Despliegue sin errores](./capturas/5.1/captura1.png)

- ![Servicios corriendo en Docker](./capturas/5.1/captura2.png)

- ![Acceso a PrestaShop vía HTTPS](./capturas/5.1/captura3.png)

- ![Prestashop funcionando](./capturas/5.1/captura4.png)