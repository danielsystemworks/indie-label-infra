# Evidencias — LAB-03-LDAP

## Objetivo

Documentar visualmente la instalación, configuración y verificación
del servicio OpenLDAP en Ubuntu Server 24.04.5 LTS.

Servidor de laboratorio: `SRV-LDAP-01`
Dominio LDAP: `indielabel.com`
Base DN: `dc=indielabel,dc=com`

## 1. Entorno y servicio

| Archivo | Comprobación | Resultado documentado |
|---|---|---|
| `01-entorno-servicio/01-lsb-release.png` | `lsb_release -a` | Identificación de la versión de Ubuntu |
| `01-entorno-servicio/02-systemctl-status-slapd.png` | `sudo systemctl status slapd --no-pager` | Estado del servicio OpenLDAP |

## 2. Estructura LDAP

| Archivo | Comprobación | Resultado documentado |
|---|---|---|
| `02-estructura-ldap/03-ldapsearch-entrada-base.png` | Consulta LDAP de la entrada base | Entrada `dc=indielabel,dc=com` |
| `02-estructura-ldap/04-ldapsearch-unidades-organizativas.png` | Consulta de unidades organizativas | OU `usuarios`, `grupos` y `servicios` |

## 3. Usuarios y grupos

| Archivo | Comprobación | Resultado documentado |
|---|---|---|
| `03-usuarios-grupos/05-ldapsearch-usuario.png` | Consulta del usuario `mrivera` | DN y atributos del usuario |
| `03-usuarios-grupos/06-ldapsearch-grupo-miembro.png` | Consulta del grupo `produccion` | Grupo POSIX, GID 2001 y miembro `mrivera` |

## 4. Autenticación

| Archivo | Comprobación | Resultado documentado |
|---|---|---|
| `04-autenticacion/07-ldapwhoami.png` | `ldapwhoami` | Identidad devuelta por la autenticación LDAP |

## Alcance y limitaciones

Estas capturas documentan las comprobaciones realizadas durante el laboratorio.
Los resultados deben interpretarse según el contenido visible de cada imagen.

La autenticación LDAP documentada no implica que Ubuntu esté configurado
para iniciar sesión en el sistema operativo usando cuentas LDAP.

Este laboratorio no documenta la configuración de TLS ni la integración
de autenticación del sistema mediante PAM o SSSD.
