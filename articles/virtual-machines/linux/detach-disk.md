---
title: Een gegevensschijf loskoppelen van een Linux-VM - Azure
description: Leer een gegevensschijf loskoppelen van een virtuele machine in Azure met Azure CLI of de Azure-portal.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: f8a0790169b17ad7755386f9bdd4f9372efc83e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74036371"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Een gegevensschijf loskoppelen van een virtuele Linux-machine

Wanneer u een gegevensschijf die is gekoppeld aan een virtuele machine niet meer nodig hebt, kunt u deze eenvoudig loskoppelen. Hiermee wordt de schijf uit de virtuele machine verwijderd, maar wordt deze niet uit de opslag verwijderd. In dit artikel werken we met een Ubuntu LTS 16.04 distributie. Als u een andere distributie gebruikt, kunnen de instructies voor het demonteren van de schijf anders zijn.

> [!WARNING]
> Als u een schijf losmaakt, wordt deze niet automatisch verwijderd. Als u zich hebt geabonneerd op Premium-opslag, blijft u opslagkosten maken voor de schijf. Zie [Prijzen en facturering bij gebruik van Premium Storage](https://azure.microsoft.com/pricing/details/storage/page-blobs/)voor meer informatie.

Als u de bestaande gegevens op de schijf opnieuw wilt gebruiken, kunt u de schijf opnieuw koppelen aan dezelfde of een andere virtuele machine.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>Verbinding maken met de vm om de schijf los te koppelen

Voordat u de schijf loskoppelen met CLI of de portal, moet u de schijf loskoppelen en verwijzingen naar als uit uw fstab-bestand verwijderen.

Maak verbinding met de VM. In dit voorbeeld is het openbare IP-adres van de VM *10.0.1.4* met de gebruikersnaam *azureuser:* 

```bash
ssh azureuser@10.0.1.4
```

Zoek eerst de gegevensschijf die u wilt loskoppelen. In het volgende voorbeeld wordt dmesg gebruikt om te filteren op SCSI-schijven:

```bash
dmesg | grep SCSI
```

De uitvoer lijkt op die in het volgende voorbeeld:

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

Hier, *sdc* is de schijf die we willen losmaken. Je moet ook pak de UUID van de schijf.

```bash
sudo -i blkid
```

De uitvoer lijkt op het volgende voorbeeld:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


Bewerk het */etc/fstab-bestand* om verwijzingen naar de schijf te verwijderen. 

> [!NOTE]
> Het onjuist bewerken van het **/etc/fstab-bestand** kan resulteren in een niet-opstartbaar systeem. Als u niet zeker weet wat u moet doen, raadpleegt u de documentatie van de distributie over het bewerken van dit bestand. Het wordt ook aanbevolen dat een back-up van het /etc/fstab-bestand wordt gemaakt voordat het wordt bewerkt.

Open het */etc/fstab-bestand* in een teksteditor als volgt:

```bash
sudo vi /etc/fstab
```

In dit voorbeeld moet de volgende regel worden verwijderd uit het */etc/fstab-bestand:*

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

Gebruik `umount` om de schijf los te maken. In het volgende voorbeeld wordt de */dev/sdc1-partitie* losgekoppeld van het mountpoint */datadrive:*

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Een gegevensschijf loskoppelen met Azure CLI 

In dit voorbeeld wordt de *myDataDisk-schijf* losgekoppeld van VM met de naam *myVM* in *myResourceGroup*.

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

De schijf blijft in opslag, maar is niet langer aangesloten op een virtuele machine.


## <a name="detach-a-data-disk-using-the-portal"></a>Een gegevensschijf ontkoppelen via de portal

1. Selecteer **virtuele machines**in het linkermenu .
2. Selecteer de virtuele machine met de gegevensschijf die u wilt loskoppelen en klik op **Stoppen** om de vm af te wijzen.
3. Selecteer **schijven**in het deelvenster virtuele machines .
4. Selecteer Boven aan het deelvenster **Schijven** de optie **Bewerken**.
5. Klik in het deelvenster **Schijven,** helemaal rechts van de gegevensschijf ![die u](./media/detach-disk/detach.png) wilt loskoppelen, op de knop afbeelding losmaken van de knop losmaken.
5. Nadat de schijf is verwijderd, klikt u boven aan het deelvenster op Opslaan.
6. Klik in het deelvenster virtuele machines op **Overzicht** en klik vervolgens op de knop **Start** boven aan het deelvenster om de vm opnieuw te starten.

De schijf blijft in opslag, maar is niet langer aangesloten op een virtuele machine.



## <a name="next-steps"></a>Volgende stappen
Als u de gegevensschijf opnieuw wilt gebruiken, u deze gewoon [koppelen aan een andere VM.](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

