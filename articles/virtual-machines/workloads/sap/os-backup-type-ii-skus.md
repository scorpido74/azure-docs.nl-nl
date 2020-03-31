---
title: Back-up en herstel van SAP HANA op Azure (Large Instances) type II SKU's| Microsoft Documenten
description: Back-ups van het besturingssysteem uitvoeren en herstellen voor SAP HANA op Azure (Large Instances) Type II SKU's
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 100e1b974e54d8c0065194bc7beb18f458011434
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616869"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>OS back-up en herstel voor Type II SKU's van Revisie 3 stempels

In dit document worden de stappen beschreven om een back-up en herstel van het besturingssysteembestandniveau uit te voeren voor de **SKU's** van type II van de HANA Large Instances of Revision 3. 

>[!Important]
> **Dit artikel is niet van toepassing op SKU-implementaties van type II in revisie 4 HANA-stempels voor grote instanties.** Boot LUNS van Type II HANA Large Instance-eenheden die zijn geïmplementeerd in revisie 4 HANA Large Instance-stempels kunnen worden geback-upt met opslagmomentopnamen, omdat dit het geval is met SKU's van type I die al in revisie 3-stempels zijn geïmplementeerd


>[!NOTE]
>De back-upscripts van het besturingssysteem maken gebruik van de ReaR-software, die vooraf is geïnstalleerd in de server.  

Nadat de inrichting is voltooid `Service Management` door het Microsoft-team, is de server standaard geconfigureerd met twee back-upschema's om een back-up te maken van het bestandssysteemniveau achter het besturingssysteem. U de schema's van de back-uptaken controleren met behulp van de volgende opdracht:
```
#crontab –l
```
U het back-upschema op elk gewenst moment wijzigen met de volgende opdracht:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Hoe maak je een handmatige back-up?

De back-up van het besturingssysteemsysteem is al gepland met behulp van een **cron-taak.** U echter ook handmatig back-ups van het besturingssysteembestandsniveau uitvoeren. Voer de volgende opdracht uit om een handmatige back-up uit te voeren:

```
#rear -v mkbackup
```
In de volgende schermshow ziet u de voorbeeldhandleiding:

![Hoe](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Hoe herstel je een back-up?

U een volledige back-up of een individueel bestand herstellen vanuit de back-up. Als u wilt herstellen, gebruikt u de volgende opdracht:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Na het herstellen wordt het bestand hersteld in de huidige werkmap.

De volgende opdracht toont het herstellen van een bestand */etc/fstabfrom* de back-up bestand *backup.tar.gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>U moet het bestand naar de gewenste locatie kopiëren nadat het is hersteld vanuit de back-up.

De volgende schermafbeelding toont het herstel van een volledige back-up:

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Hoe installeer je het ReaR-gereedschap en wijzig je de configuratie? 

De Relax-and-Recover (ReaR) pakketten zijn **vooraf geïnstalleerd** in de Type **II SKU's** van HANA Large Instances, en geen actie nodig van u. U direct beginnen met het gebruik van de ReaR voor de back-up van het besturingssysteem.
In de omstandigheden waarin u de pakketten zelf moet installeren, u echter de vermelde stappen volgen om het ReaR-hulpprogramma te installeren en te configureren.

Als u de **ReaR-back-uppakketten** wilt installeren, gebruikt u de volgende opdrachten:

Gebruik voor het besturingssysteem **SLES** de volgende opdracht:
```
#zypper install <rear rpm package>
```
Gebruik voor **het Besturingssysteem RHEL** de volgende opdracht: 
```
#yum install rear -y
```
Als u het rear-gereedschap wilt configureren, moet u parameters **OUTPUT_URL** en **BACKUP_URL** bijwerken in het *bestand /etc/rear/local.conf.*
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

De volgende schermafbeelding toont het herstel ![van een volledige back-up: RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
