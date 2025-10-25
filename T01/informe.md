# Informe Tècnic: Implementació d'un Gestor de Contrasenyes

## Introducció i Justificació

L'incident de seguretat recent a EverPia ha posat de manifest una vulnerabilitat crítica que afecta moltes empreses: l'ús de contrasenyes febles o reutilitzades. Aquest informe analitza per què aquesta pràctica és tan perillosa i com un gestor de contrasenyes pot ajudar a evitar futurs atacs.

### Per què les contrasenyes febles són un risc crític?

Les contrasenyes febles o reutilitzades són la porta d'entrada més fàcil per als ciberdelinqüents. Quan utilitzem la mateixa contrasenya en diferents serveis o contrasenyes massa simples, estem facilitant enormement la feina dels atacants.

**Atac de diccionari**: Aquest tipus d'atac consisteix a provar paraules comunes, noms, dates o combinacions que la gent sol utilitzar com a contrasenyes. Els atacants utilitzen programes automàtics que proven milers de combinacions en pocs segons, començant per les més evidents: "123456", "password", "admin", etc. Si algú a l'empresa té una contrasenya com "EverPia2024", un atac de diccionari la podria descobrir molt ràpidament.

**Credential stuffing**: Aquest atac és especialment perillós perquè aprofita que molta gent reutilitza la mateixa contrasenya en diferents llocs. Els atacants agafen credencials (usuaris i contrasenyes) que han estat robades d'altres llocs web i les proven automàticament en molts altres serveis. Segons estudis, el 81% dels usuaris reutilitzen contrasenyes, i aquests atacs tenen entre un 0,1% i un 4% de taxa d'èxit. Això vol dir que d'un milió de credencials robades, poden aconseguir accés a entre 1.000 i 40.000 comptes.

En el cas d'EverPia, si el compte tècnic compromès utilitzava una contrasenya reutilitzada d'altres serveis, els atacants podrien haver accedit simplement provant credencials filtrades de violacions de dades anteriors.

### La solució: Gestors de contrasenyes

Un gestor de contrasenyes és una eina que guarda totes les nostres contrasenyes de forma segura i xifrada. Només necessitem recordar una contrasenya mestra per accedir a totes les altres. Els avantatges principals són:

- **Contrasenyes úniques i fortes**: El gestor pot generar automàticament contrasenyes complexes i úniques per a cada servei. Ja no cal memoritzar-les totes.
- **Xifratge avançat**: Totes les contrasenyes s'emmagatzemen xifrades, de manera que fins i tot si algú accedeix al nostre ordinador, no podrà llegir-les sense la contrasenya mestra.
- **Emplenat automàtic**: Ens estalvia temps perquè omple automàticament les contrasenyes quan accedim als llocs web o aplicacions.
- **Protecció contra atacs**: Com que cada contrasenya és única, si una es veu compromesa, les altres comptes segueixen segures.

## Comparativa Tècnica


| **Característica** | **Bitwarden (Online/Núvol)** | **KeePassXC (Offline/Local)** |
|-------------------|------------------------------|-------------------------------|
| **Model d'emmagatzematge** | Núvol amb sincronització automàtica | Arxiu local (.kdbx) a l'ordinador |
| **Xifratge** | AES-256 bits amb xifratge end-to-end | AES-256 bits amb xifratge complet |
| **Arquitectura de seguretat** | Zero-knowledge: Bitwarden mai veu les contrasenyes | Dades 100% locals, mai surten del dispositiu |
| **Sincronització entre dispositius** | Automàtica i immediata entre tots els dispositius | Manual: cal copiar l'arxiu .kdbx manualment o usar serveis com Dropbox/Google Drive |
| **Accés des de múltiples dispositius** | Molt fàcil: només cal instal·lar l'aplicació i fer login | Més complicat: cal transferir l'arxiu a cada dispositiu |
| **Connexió a Internet** | Necessària per sincronitzar, però funciona offline després | No necessària en absolut |
| **Extensió de navegador** | Sí, per a tots els navegadors principals | Sí, disponible amb KeePassXC-Browser |
| **Emplenat automàtic** | Automàtic i integrat al navegador | Disponible amb l'extensió del navegador |
| **Preu** | Freemium: versió gratuïta completa, Premium 10$/any | Totalment gratuït i codi obert |
| **Model de llicència** | Codi obert però amb serveis de núvol | Codi obert 100%, llicència GPLv3 |
| **Còpies de seguretat** | Automàtiques al núvol, exportació manual disponible | Responsabilitat de l'usuari: cal fer còpies manualment |
| **Generador de contrasenyes** | Integrat i fàcil d'usar | Integrat amb múltiples opcions |
| **Autenticació de dos factors** | Suport complet (versió gratuïta i premium) | Suport TOTP integrat |
| **Corba d'aprenentatge** | Baixa: interfície intuïtiva | Mitjana: més opcions però més complexa |
| **Millor per a** | Equips que necessiten accedir des de molts dispositius | Usuaris que prioritzen control total i privacitat |
| **Continuïtat del negoci** | Alta: sempre accessible des de qualsevol lloc amb Internet | Depèn de les còpies de seguretat i sincronització manual |

## Avantatges i Inconvenients

### Bitwarden (Model Online/Núvol)

**Avantatges:**

- **Sincronització perfecta**: Tots els dispositius estan sempre actualitzats sense fer res
- **Accés universal**: Es pot accedir des de qualsevol lloc i dispositiu amb Internet
- **Manteniment mínim**: Les còpies de seguretat es fan automàticament al núvol
- **Facilitat d'ús**: Molt intuïtiu, ideal per a tots els nivells tècnics
- **Col·laboració**: Fàcil compartir contrasenyes de forma segura entre l'equip
- **Versió gratuïta completa**: Inclou totes les funcions bàsiques sense cost

**Inconvenients:**

- **Dependència d'Internet**: Cal connexió per sincronitzar canvis
- **Servidor extern**: Les dades xifrades estan al núvol de Bitwarden
- **Superfície d'atac major**: Com que està al núvol, hi ha més punts potencials d'atac
- **Menys control total**: Confiem en Bitwarden per mantenir els servidors segurs

### KeePassXC (Model Offline/Local)

**Avantatges:**

- **Control total**: Totes les dades estan només al nostre ordinador
- **Privacitat màxima**: Cap empresa externa té accés a res
- **Funciona sempre offline**: No cal connexió a Internet mai
- **Superfície d'atac reduïda**: Només el nostre dispositiu és vulnerable
- **Gratuït i codi obert 100%**: Sense subscripcions ni serveis de pagament
- **Portabilitat**: L'arxiu .kdbx es pot copiar i moure fàcilment

**Inconvenients:**

- **Sincronització manual**: Cal copiar l'arxiu .kdbx manualment entre dispositius
- **Risc de pèrdua de dades**: Si es perd l'ordinador sense còpia de seguretat, es perd tot
- **Menys convenient**: Més passos per accedir des de diferents llocs
- **Responsabilitat de l'usuari**: Cal gestionar les còpies de seguretat manualment
- **Més complex d'usar**: Corba d'aprenentatge més pronunciada
- **Dificultat per a equips**: Compartir contrasenyes entre diversos membres és més complicat

## Recomanació

**Per a l'equip tècnic d'EverPia, recomano implementar Bitwarden com a solució principal.**

**Justificació:**

1. **Sincronització automàtica**: En un entorn empresarial on els tècnics treballen des de diferents dispositius i ubicacions, tenir les contrasenyes sempre sincronitzades és essencial per a la productivitat.

2. **Facilitat d'adopció**: Bitwarden té una interfície molt intuïtiva que facilita que tot el personal tècnic l'adopti ràpidament, reduint la resistència al canvi.

3. **Continuïtat del negoci**: Si un membre de l'equip perd el seu ordinador o té un problema tècnic, pot accedir immediatament a totes les contrasenyes des de qualsevol altre dispositiu. Amb KeePassXC, això seria problemàtic si no hi ha còpia de seguretat actualitzada.

4. **Col·laboració segura**: Bitwarden permet compartir contrasenyes entre membres de l'equip de forma segura, essencial per a la gestió d'infraestructures compartides.

5. **Seguretat robusta**: Malgrat estar al núvol, Bitwarden utilitza xifratge end-to-end i arquitectura zero-knowledge. Fins i tot si els servidors de Bitwarden fossin compromesos, les dades seguirien xifrades i inaccessibles.

6. **Cost zero**: La versió gratuïta de Bitwarden inclou totes les funcions essencials que necessita un equip tècnic.

7. **Còpies de seguretat automàtiques**: Redueix el risc d'error humà en la gestió de backups.

Encara que KeePassXC ofereix màxim control i privacitat, la seva sincronització manual i major complexitat el fan menys pràctic per a un equip tècnic que necessita agilitat i col·laboració. En el context d'una empresa que acaba de patir un incident de seguretat per contrasenyes febles, la prioritat és garantir una adopció ràpida i efectiva d'un gestor de contrasenyes, i Bitwarden compleix millor aquest objectiu.


[Tornar](README.md) 
