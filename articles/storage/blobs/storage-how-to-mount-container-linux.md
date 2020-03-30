---
title: Azure Blob-opslag monteren als bestandssysteem op Linux | Microsoft Documenten
description: Een Azure Blob-opslagcontainer met FUSE op Linux monteren
author: rishabpoh
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 2/1/2019
ms.author: ripohane
ms.reviewer: dineshm
ms.openlocfilehash: a0a03df59bc6ecffcb4f0a701616297f2da78fdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061429"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Blob-opslag monteren als een bestandssysteem met blobfuse

## <a name="overview"></a>Overzicht
[Blobfuse](https://github.com/Azure/azure-storage-fuse) is een stuurprogramma voor het virtuele bestandssysteem voor Azure Blob Storage. Met Blobfuse u via het Linux-bestandssysteem toegang krijgen tot uw bestaande block blob-gegevens in uw opslagaccount. Blobfuse gebruikt het virtuele mapschema met de forward-slash '/' als scheidingsteken.  

In deze handleiding ziet u hoe u blobfuse gebruiken en een Blob-opslagcontainer op Linux monteren en toegang hebt tot gegevens. Lees de details in [de blobfuse-opslagplaats](https://github.com/Azure/azure-storage-fuse)voor meer informatie over blobfuse.

> [!WARNING]
> Blobfuse garandeert geen 100% POSIX-naleving omdat het eenvoudigaanvragen vertaalt naar [Blob REST API's.](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) De naam van bewerkingen worden bijvoorbeeld atoom in POSIX, maar niet in blobfuse.
> Ga naar [de broncoderepository blobfuse](https://github.com/azure/azure-storage-fuse)voor een volledige lijst met verschillen tussen een native bestandssysteem en blobfuse.
> 

## <a name="install-blobfuse-on-linux"></a>Blobfuse installeren op Linux
Blobfuse binaries zijn beschikbaar op [de Microsoft software repositories voor Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) voor Ubuntu en RHEL distributies. Als u blobfuse op deze distributies wilt installeren, configureert u een van de opslagplaatsen uit de lijst. U de binaire bestanden ook bouwen op de broncode volgens de [installatiestappen azure storage](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) als er geen binaire bestanden beschikbaar zijn voor uw distributie.

Blobfuse ondersteunt de installatie op Ubuntu 14.04, 16.04 en 18.04. Voer deze opdracht uit om ervoor te zorgen dat een van deze versies is geïmplementeerd:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>De Microsoft-pakketopslagplaats configureren
Configureer de [Linux Package Repository voor Microsoft-producten.](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software)

Als voorbeeld, op een Enterprise Linux 6 distributie:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Wijzig ook de URL `.../rhel/7/...` om naar een Enterprise Linux 7-distributie te wijzen.

Een ander voorbeeld op een Ubuntu 14.04 distributie:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Wijzig ook de URL `.../ubuntu/16.04/...` `.../ubuntu/18.04/...` in of om naar een andere Ubuntu-versie te verwijzen.

### <a name="install-blobfuse"></a>Blobfuse installeren

Op een Ubuntu/Debian distributie:
```bash
sudo apt-get install blobfuse
```

Op een Enterprise Linux-distributie:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Bereid je voor op montage
Blobfuse biedt native-achtige prestaties door een tijdelijk pad in het bestandssysteem te vereisen om geopende bestanden te bufferen en in de cache te plaatsen. Kies voor dit tijdelijke pad de meest performante schijf of gebruik een ramdisk voor de beste prestaties. 

> [!NOTE]
> Blobfuse slaat alle geopende bestandsinhoud op in het tijdelijke pad. Zorg ervoor dat u voldoende ruimte hebt om alle geopende bestanden te kunnen openen. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Optioneel) Een ramdisk gebruiken voor het tijdelijke pad
In het volgende voorbeeld wordt een ramdisk van 16 GB en een map voor blobfuse gemaakt. Kies de grootte op basis van uw behoeften. Met deze ramdisk kan blobfuse bestanden tot 16 GB groot openen. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>Een SSD als tijdelijk pad gebruiken
In Azure u de efemere schijven (SSD) die beschikbaar zijn op uw VM's gebruiken om een buffer met lage latentie voor blobfuse te bieden. In Ubuntu-distributies is deze kortstondige schijf gemonteerd op '/mnt'. In Red Hat- en CentOS-distributies wordt de schijf gemonteerd op '/mnt/resource/'.

Controleer of uw gebruiker toegang heeft tot het tijdelijke pad:
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Uw opslagaccountreferenties configureren
Blobfuse vereist dat uw referenties worden opgeslagen in een tekstbestand in de volgende indeling: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
Het `accountName` is het voorvoegsel voor uw opslagaccount - niet de volledige URL.

Maak dit bestand met:

```
touch ~/fuse_connection.cfg
```

Zodra u dit bestand hebt gemaakt en bewerkt, moet u de toegang beperken zodat andere gebruikers het bestand niet meer kunnen lezen.
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Als u het configuratiebestand op Windows hebt `dos2unix` gemaakt, moet u het bestand ontsmetten en converteren naar de Unix-indeling. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Een lege map maken voor montage
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Koppelen

> [!NOTE]
> Voor een volledige lijst met bevestigingsopties controleert u [de blobfuse-opslagplaats](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Als u blobfuse wilt monteren, voert u de volgende opdracht uit met uw gebruiker. Met deze opdracht monteert de container die is opgegeven in '/pad/naar/fuse_connection.cfg' op de locatie '/mycontainer'.

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

U moet nu toegang hebben tot uw blokblobs via de reguliere API's voor bestandssystemen. De gebruiker die de map monteert, is de enige persoon die er standaard toegang toe heeft, waardoor de toegang wordt beveiligd. Om toegang te geven aan alle gebruikers, u monteren via de optie. ```-o allow_other``` 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Volgende stappen

* [Startpagina van Blobfuse](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Blobfuse-problemen melden](https://github.com/Azure/azure-storage-fuse/issues) 

