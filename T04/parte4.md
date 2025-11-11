# Guia d'Instal·lació del Servidor LDAP amb Ubuntu

## Part 4: Instal·lació de LAM (Gestor d'Usuaris LDAP)

LAM és una eina web que facilita la gestió d'usuaris i grups sense haver de fer comandes.

### Pas 10: Instal·lar LAM

```bash
sudo apt install ldap-account-manager
```


### Pas 11: Accedir a LAM des de l'Ordinador Principal

Obres el navegador web de l'ordinador principal i vas a:
```
http://192.168.56.101/lam
```

![](img/7.png)

### Pas 12: Configurar LAM per Defecte

Dins de LAM, configura els paràmetres per defecte perquè:
cn=admin,dc=innovatech,dc=test

![](img/8.png)

---

## Part 5: Creació de Grups i Usuaris

### Pas 13: Crear els grups

Dins de LAM, creem dos grups:
1. **tech**: Un grup per als tècnics
2. **manager**: Un grup per als gestors

![](img/9.png)

### Pas 14: Crear els usuaris

Creem dos usuaris:
1. **tech01**: Pertany al grup `tech`
2. **manager01**: Pertany al grup `manager`

![](img/10.png)

---

## Part 6: Configuració del Client Ubuntu Desktop

Ara configurem una màquina client que es connectarà al servidor LDAP.

### Pas 15: Instal·lar Ubuntu Desktop al Client

Instal·la una nova màquina virtual amb Ubuntu Desktop. Assegura't que la interfície de xarxa es connecta a la mateixa xarxa Host-Only que el servidor.

### Pas 16: Configurar la Resolució de Noms

Dins de la màquina client, obrim l'arxiu d'hosts:
```bash
sudo nano /etc/hosts
```

Afegim aquesta línia:
```
192.168.56.101    server.innovatech22.test
```


![](img/11.png)


### Pas 17: Verificar la Connectivitat amb el Servidor

Instal·lem el paquet ldap-utils

![](img/12.png)

Executem una comanda des del client per verificar que es pot connectar al servidor LDAP:
```bash
ldapsearch -x -H ldap://server.innovatech22.test -b "dc=innovatech22,dc=test"
```

![](img/13.png)

### Pas 18: Instal·lar els Mòduls d'Autenticació

Instal·lem els paquets necessaris per permetre que els usuaris del LDAP es puguin connectar localment:
```bash
sudo apt install libnss-ldap libpam-ldap nscd
```
Configuracio:

![](img/14.png)
![](img/15.png)
![](img/16.png)
![](img/17.png)
![](img/18.png)
![](img/19.png)
![](img/20.png)

Verificació:

![](img/21.png)

### Pas 19: Configurar la Autenticació LDAP

Modifiquem l'arxiu `/etc/nsswitch.conf` per afegir LDAP a la consulta d'usuaris:

```bash
sudo nano /etc/nsswitch.conf
```

Busca les línies que digan `passwd:`, `group:` i `shadow:` i afegeix `ldap` al final:

```
passwd:         files systemd ldap
group:          files systemd ldap
shadow:         files ldap
```

![](img/22.png)

### Pas 20: Configuracions

Modifiquem el fitxer `/etc/pam.d/common-password`:

```bash
sudo nano /etc/pam.d/common-password
```

Elimineu aquesta part d'una línia:
```
use_authtok
```

![](img/23.png)

Ara editem l’arxiu /etc/pam.d/common-session i afegim la línia:

```
session optional       pam_mkhomedir.so skel=/etc/skel umask=077
```

![](img/24.png)


### Pas 21: Verificar que els Usuaris Són Visibles

Reinicia els serveis:
```bash
sudo systemctl restart nscd
```
![](img/25.png)

Després verifica que els usuaris del LDAP són visibles:
```bash
getent passwd | tail
```

![](img/26.png)

### Pas 22: Prova d'Accés Final

Reinicia la màquina client

Quan es reinicia, intenta connectar-te amb l'usuari `tech01` i la contrasenya que vam configurar. Si tot està bé, l'usuari hauria de poder connectar-se i crear automàticament la carpeta personal.

![](img/27.png)

![](img/28.png)


---

[Tornar](README.md) 
