# Plex sobre Docker en Raspberry Pi

Con este repo podes crear tu propio server que descarga tus series y peliculas automáticamente.

Incluye un server samba para compartir los archivos por red.

## Requerimientos iniciales

Agregar tu usuario (cambiar `[$user]` con tu nombre de usuario)

```
sudo useradd [$user] -G sudo
```
Agregar esta linea a `sshd_config` para que sólo tu usuario pueda hacer ssh
```
echo "AllowUsers [$user]" | sudo tee -a /etc/ssh/sshd_config
sudo systemctl enable ssh && sudo systemctl start ssh
```

Instalar paquetes básicos

```
sudo apt-get update && sudo apt-get install -y \
     apt-transport-https \
     ca-certificates \
     curl \
     gnupg2 \
     software-properties-common \
     vim \
     fail2ban \
     ntfs-3g
```

Modificá tu docker config para que guarde los temps en el disco:
```
sudo vim /etc/default/docker
# Agregar esta linea al final con la ruta de tu disco externo montado
export DOCKER_TMPDIR="/mnt/storage/docker-tmp"
```
Montar el disco (es necesario ntfs-3g si es que tenes tu disco en NTFS)
```
# usamos la terminal como root porque vamos a ejecutar algunos comandos que necesitan ese modo de ejecución
sudo su
# buscamos el disco que querramos montar (por ejemplo la partición sdb1 del disco sdb)
fdisk -l
# pueden usar el siguiente comando para obtener el UUID
ls -l /dev/disk/by-uuid/
# y simplemente montamos el disco en el archivo /etc/fstab (pueden hacerlo por el editor que les guste o por consola)
echo UUID="{nombre del disco o UUID que es único por cada disco}" {directorio donde queremos montarlo} (por ejemplo /mnt/storage) ntfs-3g defaults,auto 0 0 | \
     sudo tee -a /etc/fstab
# por último para que lea el archivo fstab
mount -a (o reiniciar)
```

## Cómo correrlo

Simplemente bajate este repo y modificá las rutas de tus archivos en el archivo (oculto) .env, y después corré:

`docker-compose up -d`

### Creditos Pelado Nerd [link](https://www.youtube.com/@PeladoNerd).