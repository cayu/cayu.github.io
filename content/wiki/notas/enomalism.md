---
title: "Configuraciones a mano"
date: 2009-07-04T01:41:24-03:00
---

[Enomalism](http://www.enomalism.com/)

Para agregar una imagen bajada o hecha en casa

```
slapadd  -l mi-imagen.ldif
```

o mas recomendado

```
ldapadd -D 'cn=Manager,dc=enomalism,dc=com' -w secret -x -f debian.ldif
```

mi-imagen.ldif

```
dn: cn={mi-imagen},ou=Images,dc=enomalism,dc=com
cn: {mi-imagen}
objectClass: top
objectClass: device
objectClass: enomalism-domu-image
enomalism-dom-adminusers: uid=admin,ou=users,dc=enomalism,dc=com
url: /opt/enomalism/vmimages/{mi-imagen}.xvm
```

En <http://www.vmcasting.org/fileadmin/vmimages/> hay imagenes de ejemplo para bajar

Ademas /opt/enomalism/data/xvmmake/ hay un esqueleto armando a modo de helper para crear las imagenes virtuales.

La imagenes-templates se guardan almacenadas en un tar sin comprimir .xvm en /opt/enomalism/vmimages y el enomalism al crear las maquinas las descomprime en /xen

En el archivo init de enomalism hay que cambiar esta linea

```
            ${SLAPDPATH}slapd -f "$SLAPCFG" -h "ldap:// ldaps://"
```

```
            /usr/sbin/slapd -f "/opt/enomalism/openldap/etc/openldap/slapd_debian.conf" -h "ldap:// ldaps://"
```

Y el archivo slapd_debian se crea en base al slapd_ubuntu

# Crear una VM a mano

Ejemplo tengo un archivo ldif con los datos de la maquina a usar

```
dn: cn={mi_vm},ou=DomU,ou=XenDomains,dc=enomalism,dc=com
enomalism-parent-dom0: cn=localhost,ou=dom0,ou=XenDomains,dc=enomalism,dc=com
ipHostNumber: 0.0.0.0
enomalism-hdimage-uuid: {mi_uuid}
cn: {mi_vm}
enomalism-dom-adminusers: cn=Manager,dc=enomalism,dc=com
enomalism-dom-adminusers: uid=fedora,ou=users,dc=enomalism,dc=com
objectClass: top
objectClass: ipHost
objectClass: enomalism-domU
```

para introducir los datos

```
ldapadd -D 'cn=Manager,dc=enomalism,dc=com' -v -w secret -x -f mi_VM.ldif
```

para borrarlos

```
ldapdelete -D 'cn=Manager,dc=enomalism,dc=com' -v -w secret -x cn=imagen_inventada,ou=DomU,ou=XenDomains,dc=enomalism,dc=com
```

\* secret es el passwd default

luego en /xen/ hay que crear un directorio que se llame

```
{mi_uuid}_{mi_VM}
```

ejemplo hay una maquina que aparecer en enomalism como fedora6-no pero que se llama para el xm como f52cf5d5d42739d9758c943db1b76c04_fedora6-eno, y hay que crear un /xen/f52cf5d5d42739d9758c943db1b76c04_fedora6-eno y alli dentro esta xen.cfg y si usa una imagen disco virtual tambien esta ej sda1.img

el archivo xen.cfg

```
import os, re
arch = os.uname()[4]
if re.search('64', arch):
    arch_libdir = 'lib64'
else:
    arch_libdir = 'lib'
device_model = '/usr/' + arch_libdir + '/xen/bin/qemu-dm'

kernel = "/boot/vmlinuz-2.6-xenU"
vif = ['']
# ej name = "f52cf5d5d42739d9758c943db1b76c04_fedora6-eno"
name = "{mi_uuid}_{mi_VM}"
usbdevice = "tablet"
on_reboot = "restart"
builder = "linux"
on_crash = "restart"
device_model = "/usr/lib64/xen/bin/qemu-dm"
vcpus = "1"
memory = "128"
serial = "pty"
disk = ['tap:aio:/xen/f52cf5d5d42739d9758c943db1b76c04_fedora6-eno/sda1.img,ioemu:sda1,w']
root = "/dev/sda1 ro"
usb = "1"
```

# Sobre los UUID

FIXME! ejemplo un uuid seria asi :

```
enomalism-hdimage-uuid: f52cf5d5d42739d9758c943db1b76c05
```

pero se le puede inventar cualquiera, no hace falta que sea algun caracter en particular, pero seria bueno ver de donde lo inventa el enomalism, aunque me parece aleatorio. \* Podria tal vez ir ahi el ID cliente del que habiamos hablado al crear un VM ?

Encontrado :)

Aca esta el codigo de generacion del UUID en /opt/enomalism/enomalism/ProvisionTools.py

```
    def generateUUID(self):
        try:
            fp=open('/dev/urandom','r')
        except:
            try:
                fp=open('/dev/random','r')
            except:
                return False
        if fp.__class__.__name__!='file':
            return False
        temp=fp.read(128);
        m=md5.new()
        m.update(temp)
        return m.hexdigest()
```

Efectivamente era un codigo aleatorio

# Ejemplo de VM

La VM se llama maquina_inventada

/xen/f52cf5d5d42739d9758c943db1b76c05f_imagen_inventad

el ldif

```
dn: cn=imagen_inventada,ou=DomU,ou=XenDomains,dc=enomalism,dc=com
enomalism-parent-dom0: cn=localhost,ou=dom0,ou=XenDomains,dc=enomalism,dc=com
ipHostNumber: 0.0.0.0
enomalism-hdimage-uuid: f52cf5d5d42739d9758c943db1b76c05f
cn: imagen_inventada
enomalism-dom-adminusers: cn=Manager,dc=enomalism,dc=com
enomalism-dom-adminusers: uid=fedora,ou=users,dc=enomalism,dc=com
objectClass: top
objectClass: ipHost
objectClass: enomalism-domU
```

el xen.cfg

```
kernel = "/boot/vmlinuz-2.6-xenU"
vnc = "0"
vif = ['']
name = "f52cf5d5d42739d9758c943db1b76c05f_imagen_inventada"
usbdevice = "tablet"
on_reboot = "restart"
builder = "linux"
on_crash = "restart"
vcpus = "1"
memory = "128"
root = "/dev/hda1 ro"
arch_libdir = "lib"
disk    = [ 'phy:/dev/storage/debian64-test1-disk,hda1,w', 'phy:/dev/storage/debian64-test1-swap,hda2,w' ]
arch = "x86_64"
serial = ""
usb = "0"
```
