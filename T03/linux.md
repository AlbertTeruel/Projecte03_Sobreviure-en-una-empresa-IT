## 1. Preparar l’entorn

Primer cal instal·lar els paquets necessaris per gestionar discos i LVM. També crearem dos discos virtuals de 10 GB que seran els que utilitzarem inicialment.

```
sudo apt update
sudo apt install fdisk lvm2
```

Comprovem que Linux detecta els discos correctament:

```
lsblk
```


## 2. Configuració inicial de LVM

Particionem els discos inicials (sdb i sdc) per poder-los utilitzar amb LVM:

```
sudo fdisk /dev/sdb
# n → nova partició
# t → 8e (Linux LVM)
# w → guardar

sudo fdisk /dev/sdc
# mateix procés
sudo partprobe
```

Creem els Physical Volumes (PV):

```
sudo pvcreate /dev/sdb1 /dev/sdc1
```

Creem el Volume Group (VG):

```
sudo vgcreate vg_empresa /dev/sdb1 /dev/sdc1
```

Creem un Logical Volume (LV) inicial de 8 GB:

```
sudo lvcreate -L 8G -n lv_inicial vg_empresa
```

Formategem i muntem el volum:

```
sudo mkfs.ext4 /dev/vg_empresa/lv_inicial
sudo mkdir /mnt/inicial
sudo mount /dev/vg_empresa/lv_inicial /mnt/inicial
```

Perquè es munti automàticament en iniciar, afegim-lo a /etc/fstab i recarreguem la configuració:

```
sudo blkid /dev/vg_empresa/lv_inicial
sudo nano /etc/fstab
# Afegim la línia: UUID=TU_UUID /mnt/inicial ext4 defaults 0 2
sudo systemctl daemon-reload
sudo mount -a
```


## 3. Alta disponibilitat (mirroring)

Creem un LV mirror per protegir la informació en cas de fallada d’un disc:

```
sudo lvcreate -m1 -L 1G -n lvm_mirror vg_empresa
sudo lvdisplay
```


## 4. Instantànies i discos addicionals

Afegim dos discos extra de 10 GB a la VM. Linux els detectarà com sdd i sde. Comprovem amb:

```
lsblk
```

Particionem els discos addicionals:

```
sudo fdisk /dev/sdd
# n → nova partició
# t → 8e
# w → guardar

sudo fdisk /dev/sde
# mateix procés
sudo partprobe
```

Afegim els discos al VG:

```
sudo pvcreate /dev/sdd1 /dev/sde1
sudo vgextend vg_empresa /dev/sdd1 /dev/sde1
```

Creem el LV de dades amb un dels discos addicionals:

```
sudo lvcreate -L 8G -n lvm_dades vg_empresa /dev/sdd1
sudo mkfs.ext4 /dev/vg_empresa/lvm_dades
sudo mkdir /mnt/dades
sudo mount /dev/vg_empresa/lvm_dades /mnt/dades
```


## 5. Arxius de prova

Afegim alguns arxius dins del volum de dades per provar les snapshots:

```
cd /mnt/dades
echo "Archivo de prueba de Carlos" | sudo tee /mnt/dades/carlos.prueba
echo "Archivo de prueba de Martí" | sudo tee /mnt/dades/marti.prueba
ls
```


## 6. Crear snapshot

Creem una instantània del volum de dades:

```
sudo lvcreate -L 2G -s -n lv_snapshot /dev/vg_empresa/lvm_dades
sudo lvs
```

lv_snapshot guarda l’estat actual dels arxius i es pot utilitzar per restaurar-los.

## 7. Simular fallada i restaurar snapshot

Simulem la pèrdua de fitxers:

```
sudo rm /mnt/dades/*.prueba
ls /mnt/dades
```

Desmuntem el volum:

```
sudo umount /mnt/dades
```

Restaurar la snapshot:

```
sudo lvconvert --merge /dev/vg_empresa/lv_snapshot
```

Muntem de nou el volum:

```
sudo mount /dev/vg_empresa/lvm_dades /mnt/dades
ls /mnt/dades
```

Ara els arxius carlos.prueba i marti.prueba tornen a estar disponibles.

## 8. Escalabilitat

Per comprovar que el volum es pot ampliar:

```
vgdisplay vg_empresa
sudo lvextend -L +2G /dev/vg_empresa/lvm_dades
sudo resize2fs /dev/vg_empresa/lvm_dades
df -h
```

Ara lvm_dades té més espai sense interrupcions.
