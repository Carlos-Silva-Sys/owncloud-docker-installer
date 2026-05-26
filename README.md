# 🐳 owncloud-docker-installer

Instalación de OwnCloud con Docker en 1 minuto. Incluye volumen persistente, variables de entorno y mapeo de puertos.

---

## ⚠️ ANTES DE EMPEZAR

Necesitas:
- **Docker instalado** en tu servidor (Ubuntu 22.04 o Debian 12)
- Un usuario con permisos para ejecutar `docker`

Si no tienes Docker, instálalo con:  
👉 [docker-installer](https://github.com/Carlos-Silva-Sys/docker-installer) (Ubuntu 22.04 / Debian 12)

---

## 🚀 INSTALACIÓN EN 1 COMANDO

```bash
docker run -d \
  --name owncloud \
  -p 80:8080 \
  -e OWNCLOUD_ADMIN_USERNAME=admin \
  -e OWNCLOUD_ADMIN_PASSWORD=TuClaveSegura123 \
  -e OWNCLOUD_TRUSTED_DOMAINS=192.168.1.111 \
  -v owncloud-data:/mnt/data \
  owncloud/server:latest
```

**¿Qué hace este comando?**

| Parte | Significado |
|-------|-------------|
| `docker run -d` | Corre el contenedor en segundo plano |
| `--name owncloud` | Le da un nombre (para no usar el ID) |
| `-p 80:8080` | Puerto 80 de tu servidor → puerto 8080 del contenedor |
| `-e OWNCLOUD_ADMIN_USERNAME` | Usuario administrador de OwnCloud |
| `-e OWNCLOUD_ADMIN_PASSWORD` | Contraseña del administrador |
| `-e OWNCLOUD_TRUSTED_DOMAINS` | IP o dominio desde donde accederás |
| `-v owncloud-data:/mnt/data` | **Volumen persistente** (tus archivos no se pierden) |
| `owncloud/server:latest` | Imagen oficial de OwnCloud |

---

## 🔧 DESPUÉS DE EJECUTAR

### 1. Acceder a OwnCloud

Abre tu navegador y ve a:

```
http://192.168.1.111
```

(Si cambiaste el puerto, usa `http://IP:PUERTO`)

### 2. Iniciar sesión

- **Usuario:** `admin`
- **Contraseña:** la que definiste en `OWNCLOUD_ADMIN_PASSWORD`

---

## 💾 ¿POR QUÉ USAR UN VOLUMEN?

**El problema:** Si instalas OwnCloud sin volumen, los archivos se guardan **dentro del contenedor**. Si borras el contenedor, **pierdes todos tus datos**.

**La solución:** El volumen `owncloud-data` guarda tus archivos **FUERA del contenedor**, en tu servidor.

| Lo que pasa | Sin volumen | Con volumen |
|-------------|-------------|-------------|
| Borras el contenedor | ❌ Pierdes TODO | ✅ Los datos siguen ahí |
| Actualizas OwnCloud | ❌ Riesgo de pérdida | ✅ Creas nuevo contenedor con el mismo volumen |
| Migras a otro servidor | ❌ Complejo | ✅ Copias el volumen y listo |

**¿Dónde está físicamente el volumen?**

```bash
docker volume inspect owncloud-data
```

Suele estar en `/var/lib/docker/volumes/owncloud-data/_data`

---

## 📊 ESPACIO DEL VOLUMEN

**¿Cuánto puedo guardar?**

El volumen usa **el mismo espacio de tu disco duro**. Si tu servidor tiene 200 GB, el volumen puede crecer hasta 200 GB.

```bash
# Ver espacio libre de tu disco
df -h

# Ver cuánto ocupa el volumen actualmente
du -sh /var/lib/docker/volumes/owncloud-data/_data
```

---

## 🛠️ COMANDOS ÚTILES

```bash
# Ver contenedores activos
docker ps

# Ver logs del contenedor
docker logs owncloud

# Entrar al contenedor (para explorar)
docker exec -it owncloud bash

# Detener el contenedor
docker stop owncloud

# Eliminar el contenedor (los datos NO se pierden, están en el volumen)
docker rm owncloud

# Eliminar también el volumen (CUIDADO: borra todos los archivos)
docker volume rm owncloud-data
```

---

## 🔁 ACTUALIZAR OWNCLOUD

```bash
# 1. Detener y eliminar el contenedor actual
docker stop owncloud
docker rm owncloud

# 2. Actualizar la imagen
docker pull owncloud/server:latest

# 3. Crear un nuevo contenedor con el mismo volumen
docker run -d \
  --name owncloud \
  -p 80:8080 \
  -e OWNCLOUD_ADMIN_USERNAME=admin \
  -e OWNCLOUD_ADMIN_PASSWORD=TuClaveSegura123 \
  -e OWNCLOUD_TRUSTED_DOMAINS=192.168.1.111 \
  -v owncloud-data:/mnt/data \
  owncloud/server:latest
```

✅ **Tus archivos siguen ahí.** El volumen persiste.

---

## 📁 ESTRUCTURA DEL PROYECTO

```
owncloud-docker-installer/
└── README.md
```

---

## 🔗 PROYECTOS RELACIONADOS

- [docker-installer](https://github.com/Carlos-Silva-Sys/docker-installer) - Instalación de Docker en Ubuntu 22.04 / Debian 12
- [owncloud-ubuntu22-installer](https://github.com/Carlos-Silva-Sys/owncloud-ubuntu22-installer) - OwnCloud con script nativo (aprendizaje)

---

## 📝 AUTOR

Carlos Silva  
GitHub: [@Carlos-Silva-Sys](https://github.com/Carlos-Silva-Sys)

---

## 📌 NOTA DE SEGURIDAD

Cambia las contraseñas y dominios por los de tu entorno. No uses valores por defecto en producción.
