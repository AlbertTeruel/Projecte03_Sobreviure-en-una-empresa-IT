# Fase Pràctica: Diagnosi de Noms (Auditoria amb CLI)

## A. Diagnosi Avançada amb dig (Linux / macOS)

### Comanda 1: Consulta Bàsica de Registre A

**Comanda executada:**
```
dig xtec.cat A
```

(imagen)

**Anàlisi:**
- **IP de resposta:** 83.247.151.214
- **Valor TTL:** 3600 segundos
- **Servidor que ha respost:** 127.0.0.53

---

### Comanda 2: Consulta de Servidors de Noms (NS)

**Comanda executada:**
```
dig tecnocampus.cat NS
```

(imagen)

**Anàlisi:**
- **Servidors de noms autoritatius:**
  - [Servidor 1]
  - [Servidor 2]
  - [Altres servidors si n'hi ha]

---

### Comanda 3: Consulta Detallada SOA

**Comanda executada:**
```
dig escolapia.cat SOA
```

(imagen)

**Anàlisi:**
- **Email de l'administrador:** [Indicar el correu]
- **Número de sèrie del domini:** [Indicar el número]
- **Altres dades SOA importants:** [Serial, Refresh, Retry, Expire, Minimum TTL]

---

### Comanda 4: Consulta de Resolució Inversa

**Comanda executada:**
```
dig -x 147.83.2.135
```

(imagen)

**Anàlisi:**
- **Informació obtinguda:** [Nom de host associat a la IP, altres registres]
- **Tipus de registre:** [PTR o altres]

---

## B. Comprovació de Resolució amb nslookup (Multiplataforma)

### Comanda 1: Consulta Bàsica no Autoritativa

**Comandes executades en mode interactiu:**
```
nslookup
> set type=A
> tecnocampus.cat
```

(imagen)

**Anàlisi:**
- **Per què la resposta és no autoritativa?**
  - La resposta és no autoritativa perquè [explicar que vient d'un resolver intermediari i no del servidor autoritatiu del domini]
  - El servidor que respon no és el servidor oficial responsable de tecnocampus.cat

---

### Comanda 2: Consultes Autoritatives

**Comandes executades en mode interactiu:**
```
> server [IP del primer servidor de noms obtingut en dig]
> set type=A
> tecnocampus.cat
> exit
```

(imagen)

**Anàlisi:**
- **Diferències respecte a la comanda 1:**
  - Ara la resposta apareix com **autoritativa** (Authoritative answer)
  - La resposta ve directament del servidor oficial del domini
  - [Altres diferències observades: TTL, servidor que respon, etc.]

---

## C. Resolucions Locals

### Prova 1: Resolució via /etc/hosts

**Pasos realitzats:**
1. Editar el fitxer `/etc/hosts` (o `C:\Windows\System32\drivers\etc\hosts` a Windows)
2. Afegir una entrada local, per exemple:
```
192.168.1.100  servidor-local.local
```
3. Comprovar que es resol correctament:
```
ping servidor-local.local
```

(imagen)

**Anàlisi:**
- La resolució funciona sense necessitat de servidor DNS
- El sistema consulta primer el fitxer hosts abans de fer consultes DNS

---

### Prova 2: Resolució via mDNS

**Comanda executada:**
```
ping nom-equip.local
```

(imagen)

**Anàlisi:**
- mDNS permet resoldre noms locals sense DNS central
- Els equips a la xarxa local responen als noms acabats en `.local`
- Útil per entorns de xarxa local sense servidor DNS propi

---

## Conclusions de la Fase Pràctica

1. **dig és una eina poderosa** per a consultes detallades de DNS, mostrant informació completa i autoritativa
2. **nslookup permet interactivitat** i facilita consultes múltiples al mateix servidor
3. **Les respostes autoritatives vs. no autoritatives** són importants per entendre on es gestiona realment el domini
4. **La resolució local** (hosts i mDNS) és essencial per a entorns sense DNS centralitzat
5. **El TTL controla l'actualització** dels resultats en cache
