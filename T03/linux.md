# Linux: LVM amb Zorin OS

### Estructura bàsica de LVM

- **Physical Volume (PV)**: Els discos físics o particions reals.  
- **Volume Group (VG)**: Una agrupació de volums físics que actua com un grup d'emmagatzematge comú.  
- **Logical Volume (LV)**: Les particions virtuals creades dins del grup de volums.  

---

## 1. Configuració Inicial: Crear Grup de Volums i Volum Lògic

### Pas 1: Preparar els discos

Primer, hem de crear dues particions en dos discos de 10 GB. En un entorn virtual, aquests discos els veurem com `/dev/sdb` i `/dev/sdc`.

Particio el primer disc
```
sudo fdisk /dev/sdb
```
Opcions: n (nova partició), p (primària), 1, Enter, Enter, t, 8e (LVM)  
Finalment: w (guardar)

El mateix amb el segon disc
```
sudo fdisk /dev/sdc
```

(imagen)

### Pas 2: Crear Physical Volumes

Creem els volumes físics
```
sudo pvcreate /dev/sdb1
sudo pvcreate /dev/sdc1
```

Verifiquem que s'han creat correctament
```
sudo pvdisplay
```

### Pas 3: Crear el Grup de Volums

Creem un grup de volums anomenat "garriga_vg"
```
sudo vgcreate garriga_vg /dev/sdb1 /dev/sdc1
```

Verifiquem
```
sudo vgdisplay garriga_vg
```

### Pas 4: Crear el Volum Lògic

Creem un volum lògic de 15 GB dins del grup de volums
```
sudo lvcreate -L 15G -n lv_dades garriga_vg
```

Verifiquem
```
sudo lvdisplay /dev/garriga_vg/lv_dades
```

### Pas 5: Formatar i Muntar el Volum

Formatem el volum amb ext4
```
sudo mkfs.ext4 /dev/garriga_vg/lv_dades
```

Creem el punt de muntatge
```
sudo mkdir -p /mnt/garriga_data
```

Muntem el volum
```
sudo mount /dev/garriga_vg/lv_dades /mnt/garriga_data
```

Verifiquem
```
df -h /mnt/garriga_data
```

### Pas 6: Muntatge Automàtic amb /etc/fstab

Per assegurar que el volum es munta automàticament cada cop que l'ordinador s'inicia:

Primer, obtenim l'UUID del volum
```
sudo blkid /dev/garriga_vg/lv_dades
```

Editamos el fitxer /etc/fstab
```
sudo nano /etc/fstab
```

Afegim una línia al final (substituir UUID per l'obtingut):
```
UUID=<uuid-obtingut> /mnt/garriga_data ext4 defaults,nofail 0 2
```

Per exemple:
```
UUID=a1b2c3d4-e5f6-7890-abcd-ef1234567890 /mnt/garriga_data ext4 defaults,nofail 0 2
```

Guardem amb Ctrl+O, Enter, Ctrl+X.

Comprovem que la configuració és correcta
```
sudo mount -a
```

(imagen)

---

## 2. Alta Disponibilitat: Configurar LVM Mirror

La funcionalitat de mirall (mirror) protegeix les dades creant una còpia exacta en un altre disc. Si un disc falla, la informació continua disponible.

### Pas 1: Afegir un Tercer Disc al Grup de Volums

Primer particionem un tercer disc
```
sudo fdisk /dev/sdd
```
Mateixa configuració que abans: n, p, 1, Enter, Enter, t, 8e

Creem el PV del tercer disc
```
sudo pvcreate /dev/sdd1
```

L'afegim al grup de volums
```
sudo vgextend garriga_vg /dev/sdd1
```

Verifiquem
```
sudo vgdisplay garriga_vg
```

### Pas 2: Crear un Volum Mirall Nou

Creem un volum lògic amb mirall (1 còpia de backup)  
La flag -m1 significa 1 mirall (2 còpies totals)
```
sudo lvcreate -L 10G -m1 -n lv_mirror garriga_vg /dev/sdb1 /dev/sdc1
```

Verifiquem
```
sudo lvdisplay /dev/garriga_vg/lv_mirror
```

(imagen)

### Pas 3: Testejar el Mirall

Formatem el volum mirall
```
sudo mkfs.ext4 /dev/garriga_vg/lv_mirror
```

Muntem el volum
```
sudo mkdir -p /mnt/garriga_mirror
sudo mount /dev/garriga_vg/lv_mirror /mnt/garriga_mirror
```

Creem arxius de prova
```
sudo touch /mnt/garriga_mirror/test_file_1.txt
sudo touch /mnt/garriga_mirror/test_file_2.txt
```

Si simuléssim una fallada de disc (cosa que no farem en aquest entorn de demostració), el volum mirall seguiria funcionant amb la còpia del disc restant.

---

## 3. Instantànies (Snapshots): Protecció de Dades

Els snapshots són una "fotografia" del estat actual del volum. Si les dades es corrompuen, podem restaurar el snapshot per recuperar l'estat anterior.

### Pas 1: Afegir Discos Addicionals

Afegim dos discos més de 10 GB (`/dev/sde` i `/dev/sdf`)

Particionem els discos
```
sudo fdisk /dev/sde
sudo fdisk /dev/sdf
```
Mateixa configuració anterior

Creem els PV
```
sudo pvcreate /dev/sde1
sudo pvcreate /dev/sdf1
```

Els afegim al grup de volums
```
sudo vgextend garriga_vg /dev/sde1
sudo vgextend garriga_vg /dev/sdf1
```

### Pas 2: Crear Volum per a les Dades

Creem un volum lògic per a dades importants
```
sudo lvcreate -L 8G -n lv_dades_snap garriga_vg
```

Formatem i muntem
```
sudo mkfs.ext4 /dev/garriga_vg/lv_dades_snap
sudo mkdir -p /mnt/garriga_dades
sudo mount /dev/garriga_vg/lv_dades_snap /mnt/garriga_dades
```

### Pas 3: Afegir Dades al Volum

Descarreguem alguns arxius de demostració (per exemple, imatges)
```
cd /mnt/garriga_dades
sudo wget https://example.com/imatge1.jpg
sudo wget https://example.com/imatge2.jpg
```

Podem crear arxius de prova si no tenim accés a internet
```
sudo cp /usr/share/pixmaps/* /mnt/garriga_dades/
```

Llistem el contingut
```
ls -lh /mnt/garriga_dades
```

(imagen)

### Pas 4: Crear Snapshot

Creem un snapshot del volum lv_dades_snap  
Reservem 2GB per als canvis futurs
```
sudo lvcreate -L 2G -s -n lv_snapshot /dev/garriga_vg/lv_dades_snap
```

Verifiquem
```
sudo lvdisplay /dev/garriga_vg/lv_snapshot
```

### Pas 5: Simular Corrupció de Dades i Restauració

Simulem la "corrupció" eliminant tots els arxius del volum original
```
sudo rm -rf /mnt/garriga_dades/*
```

Comprovem que els arxius s'han esborrat
```
ls /mnt/garriga_dades
```
Sense sortida (directori buit)

### Pas 6: Restaurar el Snapshot

Desmuntem el volum original
```
sudo umount /mnt/garriga_dades
```

Desactivem el volum original
```
sudo lvchange -a n /dev/garriga_vg/lv_dades_snap
```

Fusionem el snapshot amb el volum original (restauració)
```
sudo lvconvert --mergesnapshot /dev/garriga_vg/lv_snapshot
```

Reactivem el volum
```
sudo lvchange -a y /dev/garriga_vg/lv_dades_snap
```

Muntem de nou
```
sudo mount /dev/garriga_vg/lv_dades_snap /mnt/garriga_dades
```

Comprovem que les dades han estat restaurades
```
ls -lh /mnt/garriga_dades
```
Veurem que els arxius han reaparesat!

(imagen)

---

## 4. Escalabilitat: Ampliació del Volum

Una de les grans avantatges de LVM és que podem ampliar els volums dinàmicament sense desmontar-los ni perdre dades.

### Pas 1: Verificar Espai Disponible

Mirem quant espai lliure tenim en el grup de volums
```
sudo vgdisplay garriga_vg | grep "Free"
```

Mirem l'espai actual del volum lv_dades
```
sudo lvdisplay /dev/garriga_vg/lv_dades
```

### Pas 2: Ampliar el Volum Lògic

Ampliem el volum en 5 GB més
```
sudo lvextend -L +5G /dev/garriga_vg/lv_dades
```

Verifiquem
```
sudo lvdisplay /dev/garriga_vg/lv_dades
```

### Pas 3: Ampliar el Sistema de Fitxers

Amb ext4, hem d'ampliar també el sistema de fitxers dins del volum

Ampliem el sistema de fitxers
```
sudo resize2fs /dev/garriga_vg/lv_dades
```

Comprovem el nou espai
```
df -h /mnt/garriga_data
```

(imagen)

---

## Verificació Final

Per comprovar que tot funciona correctament

Mirem l'estructura completa de LVM
```
sudo pvdisplay
sudo vgdisplay
sudo lvdisplay
```

Mirem els volums muntats
```
mount | grep garriga
```

Mirem l'espai disponible
```
df -h
```

---
