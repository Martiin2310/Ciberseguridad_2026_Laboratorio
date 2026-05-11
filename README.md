# Ciberseguridad_2026_Laboratorio

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

- Kali Linux
- Ubuntu Server
- OpenSSL
- Hydra
- Google Authenticator
- Wireshark
- Docker
- SSH

---

# Lab 1 - Criptografía e Integridad de Datos

## Objetivo

Aplicar técnicas de hashing y firma digital para verificar la integridad de archivos y detectar modificaciones no autorizadas.

---

## Creación del archivo

Se creó un archivo llamado `config_bancaria.txt` utilizando la terminal de Kali Linux.

![Creación archivo](Img-Ciberseguridad/01_creacion_archivo_config_bancaria.jpg)

Posteriormente se editó el contenido del archivo utilizando Nano.

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

![Verificación correcta](Img-Ciberseguridad/06_verificacion_firma_exitosa.jpg)

---

## Modificación del archivo

Posteriormente se modificó el contenido del archivo para alterar su integridad.

![Archivo modificado](Img-Ciberseguridad/07_modificacion_archivo_atacante.jpg)

---

## Verificación fallida

Luego de modificar el archivo, la verificación de la firma digital falló correctamente, demostrando que el archivo había sido alterado.

![Verificación fallida](Img-Ciberseguridad/08_verificacion_firma_fallida.jpg)

---

# Lab 2 - Ataque y Defensa de Credenciales

## Objetivo

Realizar un ataque de diccionario contra el servicio SSH utilizando Hydra y posteriormente implementar autenticación multifactor (MFA) para fortalecer la seguridad del sistema.

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

Finalmente se volvió a ejecutar Hydra. Aunque la contraseña fuese correcta, el acceso fue bloqueado debido a la autenticación multifactor.

![Hydra bloqueado](Img-Ciberseguridad/17_hydra_bloqueado_mfa.jpg)

---

# Lab 3 - Protocolos de Comunicación Seguros

## Objetivo

Analizar tráfico inseguro utilizando Wireshark y posteriormente aplicar medidas de protección mediante túneles SSH para proteger la información transmitida en la red.

---

## Captura de tráfico HTTP inseguro

Se realizó una captura de tráfico utilizando Wireshark mientras se enviaban credenciales mediante una conexión HTTP sin cifrado.

El objetivo fue demostrar cómo un atacante puede visualizar información sensible cuando las comunicaciones no utilizan mecanismos de seguridad.

En Wireshark fue posible observar tráfico HTTP y datos transmitidos en texto plano utilizando el filtro:

```bash
http.request.method == "POST"
```

![Wireshark HTTP](Img-Ciberseguridad/18_wireshark_http_texto_plano.jpg)

---

## Obtención de IP del contenedor Docker

Para realizar el túnel SSH primero se obtuvo la dirección IP del contenedor Docker mediante el siguiente comando:

```bash
sudo docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' ID_DEL_CONTENEDOR
```

![IP contenedor Docker](Img-Ciberseguridad/19_ip_contenedor_docker.jpg)

---

## Implementación del túnel SSH

Posteriormente se habilitó el servicio SSH y se creó un túnel local utilizando el siguiente comando:

```bash
ssh -L 8080:172.18.0.2:3000 usuario@localhost
```

Luego se accedió al sitio web mediante la dirección:

```bash
http://localhost:8080
```

Con esto el tráfico pasó a viajar mediante una conexión cifrada.

![Página mediante túnel SSH](Img-Ciberseguridad/20_pagina_tunel_ssh.jpg)

---

## Envío de información mediante conexión segura

Una vez creado el túnel SSH se ingresaron nuevamente los datos en el formulario web.

![Formulario seguro](Img-Ciberseguridad/21_formulario_tunel_seguro.jpg)

---

## Verificación de tráfico cifrado

Finalmente se volvió a analizar el tráfico utilizando Wireshark.

A diferencia de la prueba anterior, ya no fue posible visualizar las credenciales en texto plano, demostrando que el túnel SSH protegió correctamente la información transmitida.

![Tráfico cifrado](Img-Ciberseguridad/22_wireshark_trafico_cifrado.jpg)

---

# Conclusión

Durante los laboratorios se trabajó con distintas herramientas y técnicas de ciberseguridad relacionadas con criptografía, autenticación multifactor y protección de comunicaciones.

En el Lab 1 se aplicaron firmas digitales y verificación de integridad utilizando OpenSSL.

En el Lab 2 se realizaron ataques de diccionario con Hydra y posteriormente se implementó autenticación multifactor (MFA) para fortalecer la seguridad del servicio SSH.

Finalmente, en el Lab 3 se analizó tráfico HTTP inseguro utilizando Wireshark y posteriormente se protegió la comunicación mediante túneles SSH, comprobando el funcionamiento del cifrado en la protección de datos sensibles.

Estos laboratorios permitieron comprender tanto técnicas ofensivas como defensivas utilizadas actualmente en entornos reales de ciberseguridad.
