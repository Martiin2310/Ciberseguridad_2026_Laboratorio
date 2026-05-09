# Ciberseguridad 2026 - Laboratorios Prácticos

Instituto Profesional Santo Tomás Iquique

**Nombre:** Martín Obregón Díaz  
**Carrera:** Ingeniería en Informática  
**Asignatura:** Ciberseguridad  

---

# Descripción

En este repositorio se documentan los laboratorios realizados en la asignatura de Ciberseguridad, utilizando máquinas virtuales con Kali Linux y Ubuntu Server para simular escenarios reales de ataque y defensa.

Los laboratorios tienen como objetivo comprender el funcionamiento de herramientas de criptografía, ataques de credenciales, autenticación multifactor y protección de servicios críticos.

---

# Entorno de Trabajo

- Kali Linux (Máquina atacante)
- Ubuntu Server (Máquina víctima)
- OpenSSL
- Hydra
- Google Authenticator
- SSH

---

# Lab 1 - Criptografía e Integridad de Datos

## Objetivo

Aplicar técnicas de hashing y firma digital para verificar la integridad de archivos y detectar modificaciones no autorizadas.

---

## Creación del archivo

Se creó un archivo llamado `config_bancaria.txt` utilizando la terminal de Kali Linux.

![Creación archivo](Img-Ciberseguridad/01_creacion_archivo_config_bancaria.jpg)

Posteriormente se editó el contenido del archivo utilizando el editor Nano.

![Edición archivo](Img-Ciberseguridad/02_edicion_archivo_config.jpg)

---

## Generación de llave privada

Se generó una llave privada RSA de 2048 bits utilizando OpenSSL.

```bash
openssl genrsa -out privada.pem 2048
```

![Llave privada](Img-Ciberseguridad/03_generacion_llave_privada.jpg)

---

## Generación de llave pública

A partir de la llave privada se generó la llave pública.

```bash
openssl rsa -in privada.pem -pubout -out publica.pem
```

![Llave pública](Img-Ciberseguridad/04_generacion_llave_publica.jpg)

---

## Firma digital del archivo

Se realizó la firma digital del archivo utilizando SHA-256.

```bash
openssl dgst -sha256 -sign privada.pem -out firma.bin config_bancaria.txt
```

![Firma digital](Img-Ciberseguridad/05_firma_digital_archivo.jpg)

---

## Verificación de firma

Se verificó correctamente la firma digital utilizando la llave pública.

```bash
openssl dgst -sha256 -verify publica.pem -signature firma.bin config_bancaria.txt
```

La verificación fue exitosa.

![Verificación correcta](Img-Ciberseguridad/06_verificacion_firma_exitosa.jpg)

---

## Simulación de modificación del archivo

Desde la máquina atacante se modificó el contenido del archivo para alterar su integridad.

![Archivo modificado](Img-Ciberseguridad/07_modificacion_archivo_atacante.jpg)

---

## Verificación fallida

Luego de modificar el archivo, la verificación de la firma digital falló correctamente, demostrando que el archivo había sido alterado.

![Verificación fallida](Img-Ciberseguridad/08_verificacion_firma_fallida.jpg)

---

# Lab 2 - Ataque y Defensa de Credenciales

## Objetivo

Realizar un ataque de diccionario contra el servicio SSH utilizando Hydra y posteriormente implementar autenticación multifactor (MFA) para aumentar la seguridad del sistema.

---

## Ataque con Hydra

Desde Kali Linux se ejecutó un ataque de diccionario contra el servicio SSH del servidor Ubuntu.

```bash
hydra -l usuario -P lista_passwords.txt ssh://IP_SERVIDOR
```

![Ataque Hydra](Img-Ciberseguridad/09_ataque_hydra_ssh.jpg)

---

## Instalación de Google Authenticator

Se instaló el módulo PAM de Google Authenticator en Ubuntu Server.

```bash
sudo apt install libpam-google-authenticator
```

![Instalación Google Authenticator](Img-Ciberseguridad/10_instalacion_google_authenticator.jpg)

---

## Configuración del autenticador

Se ejecutó el comando:

```bash
google-authenticator
```

Luego se configuró el código QR en la aplicación móvil.

![Configuración MFA](Img-Ciberseguridad/11_configuracion_google_authenticator.jpg)

---

## Configuración de PAM para SSH

Se editó el archivo:

```bash
/etc/pam.d/sshd
```

Agregando la siguiente línea:

```bash
auth required pam_google_authenticator.so
```

![Edición PAM](Img-Ciberseguridad/12_edicion_pam_sshd.jpg)

![Configuración PAM](Img-Ciberseguridad/13_habilitacion_pam_google_auth.jpg)

---

## Configuración del servicio SSH

Se modificó el archivo:

```bash
/etc/ssh/sshd_config
```

Configurando los siguientes parámetros:

```bash
ChallengeResponseAuthentication yes
PasswordAuthentication yes
UsePAM yes
```

![Edición SSH](Img-Ciberseguridad/14_edicion_sshd_config.jpg)

![Configuración SSH](Img-Ciberseguridad/15_configuracion_mfa_ssh.jpg)

---

## Reinicio del servicio SSH

Para aplicar los cambios se reinició el servicio SSH.

```bash
sudo service ssh restart
```

![Reinicio SSH](Img-Ciberseguridad/16_reinicio_servicio_ssh.jpg)

---

## Verificación final

Finalmente se volvió a ejecutar Hydra. Aunque la contraseña era correcta, el acceso fue bloqueado debido a la autenticación multifactor.

![Hydra bloqueado](Img-Ciberseguridad/17_hydra_bloqueado_mfa.jpg)

---

# Conclusión

Los laboratorios permitieron comprender la importancia de la criptografía, la integridad de datos y la autenticación multifactor en entornos reales de ciberseguridad.

Además, se comprobó cómo herramientas ofensivas como Hydra pueden ser mitigadas mediante buenas prácticas de seguridad y configuraciones adecuadas en servicios críticos como SSH.
