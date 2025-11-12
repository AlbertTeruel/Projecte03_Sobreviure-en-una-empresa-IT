# Producte 04 - Servidor DNS Intern (BIND9)

Aquest projecte recull la configuració d'un servidor DNS intern creat com a prova de concepte per al client Digicore. L'objectiu principal era substituir l'ús directe d'adreces IP per noms de domini fàcils de recordar, millorant la usabilitat, la gestió de la xarxa i la professionalitat dels serveis interns.

El projecte inclou la configuració completa del servidor BIND9 amb:

- **named.conf.options**: Configuració general del servidor. Aquí s'ha activat la resolució recursiva només per a la xarxa local i s'ha configurat el reenviador a Google DNS (8.8.8.8), per poder resoldre noms externs quan calgui.
- **named.conf.local**: Definició de les zones del domini. S'han creat tant la **zona directa** (`digicore-XX.test`) com la **zona inversa**, permetent la resolució de noms a IP i d’IP a noms. També s'ha configurat la transferència de zones per compartir-les amb els companys de l’equip.
- **Carpeta zones/**: Conté els arxius de zona amb els registres definitius:
  - SOA i NS correctament configurats.
  - Registres A per a `server` (servidor) i `dbserver` (client).
  - Registre CNAME `data` apuntant a `dbserver`.
  - Registres PTR per a la resolució inversa de les IPs.

Durant la pràctica, hem comprovat que el servidor funciona correctament des d’un client de la xarxa: es poden resoldre noms de domini interns, així com fer consultes inverses, i també es pot accedir a Internet utilitzant el reenviador configurat. A més, s’ha provat la transferència de zones amb un company, garantint que les zones secundàries es sincronitzen correctament.

