# LAB-03 — Configuración de un servidor OpenLDAP

## 1. Objetivo

Instalar y configurar un servidor OpenLDAP en Ubuntu Server 24.04.5 LTS para gestionar un directorio centralizado de usuarios y grupos. Verificar el funcionamiento del servicio mediante consultas LDAP y autenticación con las herramientas de `ldap-utils`.

Este laboratorio forma parte del proyecto **indie-label-infra**, una infraestructura informática simulada para un sello discográfico independiente.

## 2. Entorno de laboratorio

| Elemento | Configuración |
|---|---|
| Máquina virtual | `SRV-LDAP-01` |
| Sistema operativo | Ubuntu Server 24.04.5 LTS |
| Servicio | OpenLDAP (`slapd`) |
| Dominio LDAP | `indielabel.com` |
| Base DN | `dc=indielabel,dc=com` |
| Administrador LDAP | `cn=admin,dc=indielabel,dc=com` |
| Unidad de usuarios | `ou=usuarios` |
| Unidad de grupos | `ou=grupos` |
| Unidad de servicios | `ou=servicios` |
| Usuario de prueba | `mrivera` |
| Grupo de prueba | `produccion` |
| GID del grupo | `2001` |

## 3. Instalación y configuración

Se instaló el servicio OpenLDAP y las utilidades de administración y consulta:

```bash
sudo apt update
sudo apt install slapd ldap-utils
```

Durante la configuración inicial se definió el dominio `indielabel.com` y se estableció la base del directorio:

```text
dc=indielabel,dc=com
```

Se comprobó el estado del servicio:

```bash
sudo systemctl status slapd --no-pager
```

**Resultado:** el servicio aparece como `active (running)`.

## 4. Estructura organizativa

Se creó una estructura básica con tres unidades organizativas:

```text
dc=indielabel,dc=com
├── ou=usuarios
│   └── uid=mrivera
├── ou=grupos
│   └── cn=produccion
└── ou=servicios
```

El archivo `estructura.ldif` define las unidades organizativas mediante la clase `organizationalUnit`.

Se incorporaron al directorio con `ldapadd` y se verificaron mediante `ldapsearch`.

Ejemplo de consulta:

```bash
ldapsearch -x -LLL -H ldap://127.0.0.1 \
-b "dc=indielabel,dc=com" \
"(objectClass=organizationalUnit)" dn ou
```

## 5. Creación y consulta del usuario

Se creó el usuario de directorio `mrivera`, con nombre visible `Marcos Rivera`, apellido `Rivera` y correo `mrivera@indielabel.com`.

Su DN es:

```text
uid=mrivera,ou=usuarios,dc=indielabel,dc=com
```

Consulta de sus atributos:

```bash
ldapsearch -x -LLL -H ldap://127.0.0.1 \
-b "ou=usuarios,dc=indielabel,dc=com" \
"(uid=mrivera)" dn cn sn mail
```

La consulta devolvió el DN, el nombre, el apellido y el correo esperados.

**Nota de seguridad:** los archivos de ejemplo que se publiquen en GitHub no deben incluir contraseñas reales ni hashes de `userPassword`.

## 6. Creación del grupo y asignación del usuario

Se creó el grupo POSIX `produccion` dentro de `ou=grupos`, con GID `2001`.

Su DN es:

```text
cn=produccion,ou=grupos,dc=indielabel,dc=com
```

Se añadió el identificador `mrivera` al atributo `memberUid` mediante una operación `ldapmodify`.

Archivo de modificación utilizado:

```ldif
dn: cn=produccion,ou=grupos,dc=indielabel,dc=com
changetype: modify
add: memberUid
memberUid: mrivera
```

La modificación finalizó correctamente. Se verificó con:

```bash
ldapsearch -x -LLL -H ldap://127.0.0.1 \
-b "cn=produccion,ou=grupos,dc=indielabel,dc=com" \
"(objectClass=posixGroup)" cn gidNumber memberUid
```

Resultado relevante:

```text
cn: produccion
gidNumber: 2001
memberUid: mrivera
```

## 7. Verificación de consultas y autenticación

Se comprobó la identidad del administrador mediante una vinculación LDAP autenticada:

```bash
ldapwhoami -x -H ldap://127.0.0.1 \
-D "cn=admin,dc=indielabel,dc=com" -W
```

También se comprobó la autenticación del usuario `mrivera` con su DN completo y la contraseña establecida durante la creación.

La consulta global final fue:

```bash
ldapsearch -x -LLL -H ldap://127.0.0.1 \
-b "dc=indielabel,dc=com" \
"(objectClass=*)" dn
```

La salida mostró seis entradas:

1. Base `dc=indielabel,dc=com`.
2. Unidad `ou=usuarios`.
3. Unidad `ou=grupos`.
4. Unidad `ou=servicios`.
5. Usuario `uid=mrivera`.
6. Grupo `cn=produccion`.

## 8. Incidencias y resolución

### DN incorrecto

Una prueba de autenticación inicial utilizó un DN con `ou=usuario` en singular. Se corrigió usando el DN real:

```text
uid=mrivera,ou=usuarios,dc=indielabel,dc=com
```

### Atributo obligatorio `sn`

El primer intento de creación del usuario falló porque `inetOrgPerson` requiere el atributo `sn`. Se añadió el apellido `Rivera` y la creación se completó correctamente.

### Operación de modificación incompleta

La primera ejecución de `ldapmodify` indicó que faltaba el tipo de operación. Se creó un archivo independiente con `changetype: modify` y `add: memberUid`, y la modificación posterior se completó correctamente.

## 9. Conclusiones

Se ha desplegado un servidor OpenLDAP operativo en Ubuntu Server 24.04.5 LTS y se ha definido un dominio LDAP personalizado para el entorno simulado del sello discográfico.

Se ha creado una estructura organizativa básica, incorporado un usuario y un grupo POSIX, asociado el usuario al grupo y verificado el directorio mediante consultas LDAP. También se comprobó la autenticación LDAP del usuario durante las pruebas.

El laboratorio permite practicar conceptos de administración de directorios, nombres distinguidos (DN), unidades organizativas, atributos, archivos LDIF y herramientas de consulta y modificación.

**Alcance:** la autenticación comprobada corresponde al directorio LDAP mediante sus herramientas de cliente. No se ha configurado en este laboratorio el inicio de sesión del sistema operativo Ubuntu mediante LDAP/PAM/SSSD. Tampoco se ha documentado aquí la configuración de TLS ni la exposición del servicio a una red de producción.

## 10. Evidencias

Las capturas de las verificaciones realizadas durante el laboratorio
están organizadas por fases en `evidencias/`.

El índice [`evidencias/README.md`](evidencias/README.md) relaciona cada
imagen con el comando o la comprobación correspondiente.

Las evidencias disponibles documentan:
- La versión de Ubuntu Server y el estado de `slapd`.
- La entrada base y las unidades organizativas LDAP.
- El usuario `mrivera` y el grupo POSIX `produccion`.
- La comprobación de identidad mediante `ldapwhoami`.

Estas capturas documentan principalmente verificaciones del estado
configurado; no representan por sí solas todos los pasos de instalación.

Antes de publicar el laboratorio, se revisarán los archivos LDIF y el
resto de los archivos publicables para evitar exponer credenciales,
hashes u otros datos sensibles.
## 11. Estado

**Estado del laboratorio:** configuración principal y verificaciones funcionales completadas. Evidencias organizadas y documentación revisada. El laboratorio está publicado en GitHub; esta actualización completa su cierre documental.
