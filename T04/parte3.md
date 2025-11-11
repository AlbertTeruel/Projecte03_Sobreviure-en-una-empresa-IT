# Guia d'Instal·lació del Servidor LDAP amb Ubuntu

## Part 3: Configuració Bàsica d'OpenLDAP

### Pas 6: Configurar el Domini

El domini de la nostra xarxa de prova ha de ser `innovatech22.test`. Aquesta configuració es va a fer automàticament durant la instal·lació.

Per comprovar-la:
```bash
sudo ldapsearch -x -b "dc=innovatech22,dc=test"
```

![](img/4.png)

### Pas 7: Crear les Unitats Organitzatives (OU)

Ara creem els carpetes del directori per guardar usuaris i grups. Creem un fitxer anomenat `ou.ldif`:

```bash
nano ou.ldif
```

Dins del fitxer, escriu el següent:
```
dn: ou=users,dc=innovatech22,dc=test
objectClass: organizationalUnit
objectClass: top
ou: users

dn: ou=groups,dc=innovatech22,dc=test
objectClass: organizationalUnit
objectClass: top
ou: groups
```

![](img/5.png)

Desa el fitxer amb `Ctrl+X`, después `Y` i `Enter`.

### Pas 8: Afegir les OUs al Directori

Executem la comanda per afegir les unitats que vam crear:
```bash
sudo ldapadd -x -D "cn=admin,dc=innovatech22,dc=test" -W -f ou.ldif
```

Et demanarà la contrasenya.

![](img/6.png)

### Pas 9: Verificar les OUs Creades

Per comprovar que les unitats organitzatives s'han creat correctament:
```bash
ldapsearch -xLLL -b "dc=innovatech22,dc=test"
```

---
