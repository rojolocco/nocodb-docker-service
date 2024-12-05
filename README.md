# Servicio NocoDB

Este directorio contiene la configuración necesaria para levantar un servicio de NocoDB utilizando Docker Compose. Está preparado para entornos de desarrollo y producción, con persistencia de datos y configuración para un proxy inverso con Caddy.

---

## **Estructura de Archivos**

```plaintext
nocodb/
├── .env.dev                     # Variables de entorno para desarrollo
├── .env.prod                    # Variables de entorno para producción
├── docker-compose.dev.yml       # Docker Compose para desarrollo
├── docker-compose.yml           # Docker Compose para producción
├── data/                        # Carpeta para persistencia de datos (creada automáticamente)
```

---

## **Requisitos Previos**

1. Docker y Docker Compose instalados.
2. Una red externa llamada `caddy_network` creada previamente:

   ```bash
   docker network create caddy_network
   ```

---

## **Configuración de Variables de Entorno**

Define las credenciales y ajustes del servicio en los archivos `.env.dev` y `.env.prod`.

### `.env.dev` (Desarrollo)

```plaintext
NC_DB=pg://<host_postgres>:<puerto_postgres>?u=<usuario_postgres>&p=<contraseña_postgres>&d=<base_de_datos>
```

### `.env.prod` (Producción)

```plaintext
NC_DB=pg://<host_postgres>:<puerto_postgres>?u=<usuario_postgres>&p=<contraseña_postgres>&d=<base_de_datos>
```

> **Nota:** Asegúrate de que estos archivos estén protegidos y excluidos del control de versiones mediante `.gitignore`.

---

## **Uso**

### **Levantar el servicio en desarrollo**

```bash
docker-compose -f docker-compose.dev.yml up -d
```

### **Levantar el servicio en producción**

```bash
docker-compose -f docker-compose.yml up -d
```

### **Detener el servicio**

```bash
docker-compose down
```

### **Verificar el estado de los contenedores**

```bash
docker ps
```

---

## **Acceso a NocoDB**

Puedes acceder a la interfaz de NocoDB desde un navegador usando la siguiente URL:

- **Desarrollo**: `http://<tu-ip>:8081`
- **Producción**: `http://nocodb.tudominio.com` (si está configurado con Caddy)

---

## **Persistencia de Datos**

Los datos de NocoDB se almacenan en un volumen llamado `nocodb_data`. Esto asegura que los datos no se pierdan al detener o reiniciar los contenedores.

### **Ubicación del volumen:**

El volumen es gestionado por Docker y no está directamente accesible desde el sistema de archivos.

Para eliminar los datos almacenados:

```bash
docker volume rm nocodb_nocodb_data
```

---

## **Configuración de Proxy Inverso con Caddy**

Para hacer que NocoDB sea accesible a través de un dominio, configura Caddy con un proxy inverso:

```caddyfile
nocodb.tudominio.com {
    reverse_proxy nocodb_prod:8080
}
```

Luego reinicia Caddy:

```bash
sudo systemctl restart caddy
```

---

## **Solución de Problemas**

### Error: `Invalid URL`

- Asegúrate de que la URL de conexión a la base de datos en los archivos `.env` esté correctamente formateada.

### Error: `Database Connection Issues`

- Verifica que la instancia de PostgreSQL sea accesible y que las credenciales en `.env` sean correctas.

---

## **Contribución**

Si deseas mejorar este servicio o agregar nuevas funcionalidades, sigue estos pasos:

1. Haz un fork de este repositorio.
2. Realiza tus cambios en una rama nueva.
3. Envía un pull request con tus mejoras.

---

## **Licencia**

Este proyecto está bajo la licencia MIT. Consulta el archivo `LICENSE` para más detalles.
