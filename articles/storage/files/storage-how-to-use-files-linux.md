---
title: Azure Files met Linux gebruiken | Microsoft Docs
description: Meer informatie over het koppelen van een Azure-bestands share via SMB op Linux.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9674df346ff0b5c046df26fe85bf7ba0bbb802e6
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900312"
---
# <a name="use-azure-files-with-linux"></a>Azure Files gebruiken met Linux

[Azure Files ](storage-files-introduction.md) is het eenvoudig te gebruiken cloudbestandssysteem van Microsoft. Azure-bestands shares kunnen worden gekoppeld in Linux-distributies met behulp van de [SMB-kernel-client](https://wiki.samba.org/index.php/LinuxCIFS). In dit artikel ziet u twee manieren om een Azure-bestands share te koppelen: op `mount` aanvraag met de opdracht en aan-opstarten door een `/etc/fstab`item in te maken.

> [!NOTE]  
> Om een Azure-bestands share te koppelen buiten de Azure-regio waarin deze wordt gehost, zoals on-premises of in een andere Azure-regio, moet het besturings systeem ondersteuning bieden voor de versleutelings functionaliteit van SMB 3,0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Vereisten voor het koppelen van een Azure-bestands share met Linux en het CIFS-hulppr-pakket
<a id="smb-client-reqs"></a>

* **Een bestaand Azure Storage-account en een bestands share**: U moet een opslag account en bestands share hebben om dit artikel te kunnen volt ooien. Als u nog geen abonnement hebt gemaakt, raadpleegt u een van onze Quick starts voor het onderwerp: [Bestands shares maken-cli](storage-how-to-use-files-cli.md).

* **De naam en sleutel van uw opslag account** U hebt de naam en sleutel van het opslag account nodig om dit artikel te volt ooien. Als u een werk ruimte hebt gemaakt met behulp van de CLI Quick Start, moet u deze al hebben. anders raadpleegt u de CLI-Snelstartgids die eerder was gekoppeld, om te leren hoe u de sleutel van uw opslag account kunt ophalen.

* **Kies een Linux-distributie die aansluit bij uw vereisten voor koppelen.**  
      Azure Files kan worden gekoppeld via SMB 2,1 en SMB 3,0. Voor verbindingen die afkomstig zijn van clients on-premises of in andere Azure-regio's, moet u SMB 3,0 gebruiken. Azure Files zal SMB 2,1 (of SMB 3,0 zonder versleuteling) weigeren. Als u toegang hebt tot de Azure-bestands share vanaf een virtuele machine in dezelfde Azure-regio, kunt u toegang krijgen tot uw bestands share met SMB 2,1, indien en alleen als de *vereiste beveiligde overdracht* is uitgeschakeld voor het opslag account dat als host fungeert voor de Azure-bestands share. We raden altijd aan om veilige overdracht te vereisen en alleen SMB 3,0 met versleuteling te gebruiken.

    Ondersteuning voor SMB 3,0-versleuteling is geïntroduceerd in versie 4,11 van de Linux-kernel en is Backported naar oudere kernel-versies voor populaire Linux-distributies. Op het moment van publicatie van dit document worden de volgende distributies van de Azure Gallery-ondersteuning voor het koppelen van de optie in de tabel headers opgegeven. 

### <a name="minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30"></a>Mini maal aanbevolen versies met de bijbehorende koppelings mogelijkheden (SMB-versie 2,1 vs SMB-versie 3,0)

|   | SMB 2.1 <br>(Koppelt op Vm's binnen dezelfde Azure-regio) | SMB 3.0 <br>(Koppelt van on-premises en kruis regio's) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04 + | 16.04 + |
| RHEL | 7+ | 7.5 + |
| CentOS | 7+ |  7.5 + |
| Debian | 8 + | 9 +<sup>*</sup> |
| openSUSE | 13.2 + | 42.3 + |
| SUSE Linux Enterprise Server | 12 | 12 SP3+ |

<sup>*</sup>Het gebruik van een Backported-kernel is mogelijk vereist. U kunt deze installeren met `sudo apt-get install linux-image-cloud-amd64`.

Als uw Linux-distributie hier niet wordt vermeld, kunt u controleren of de versie van de Linux-kernel wordt weer gegeven met de volgende opdracht:

```bash
uname -r
```

* <a id="install-cifs-utils"></a>**Het CIFS-hulppre-pakket is geïnstalleerd.**  
    Het CIFS-hulppr-pakket kan worden geïnstalleerd met behulp van package manager in de Linux-distributie van uw keuze. 

    Gebruik`apt-get` pakket beheer op **Ubuntu** **-en Debian-gebaseerde** distributies:

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    Gebruik pakket`yum` beheer op RHEL en CentOS:

    ```bash
    sudo yum install cifs-utils
    ```

    Gebruik`zypper` pakket beheer op **openSUSE**:

    ```bash
    sudo zypper install cifs-utils
    ```

    Gebruik op andere distributies de juiste pakket beheer-of [compilatie van de bron](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **Bepaal de machtigingen voor de map of het bestand van de gekoppelde share**: In de onderstaande voor beelden wordt de `0777` machtiging gebruikt om Lees-, schrijf-en uitvoer machtigingen te verlenen aan alle gebruikers. U kunt deze vervangen door andere [CHMOD-machtigingen](https://en.wikipedia.org/wiki/Chmod) , maar dit betekent mogelijk dat u de toegang wilt beperken. Als u andere machtigingen wilt gebruiken, moet u ook rekening houden met UID en GID om de toegang te behouden voor lokale gebruikers en groepen van uw keuze.

> [!NOTE]
> Als u geen machtigingen voor mappen en bestanden expliciet toewijst met dir_mode en file_mode, wordt standaard 0755.

* **Zorg ervoor dat poort 445 open is**: SMB communiceert via TCP-poort 445 - controleer of de TCP-poort 445 van de clientcomputer niet door uw firewall wordt geblokkeerd.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>De Azure-bestands share on-demand koppelen met`mount`

1. **[Installeer het CIFS-hulppr-pakket voor uw Linux-distributie](#install-cifs-utils)** .

1. **Maak een map voor het koppel punt**: U kunt een map voor een koppel punt maken op een wille keurige locatie in het bestands systeem, maar dit is een algemene Conventie voor het maken van deze onder een nieuwe map. Met de volgende opdracht maakt u bijvoorbeeld een nieuwe map, vervangt u **< storage_account_name >** en **< file_share_name >** door de relevante informatie voor uw omgeving:

    ```bash
    mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Gebruik de koppelings opdracht om de Azure-bestands share te koppelen**: Vergeet niet om **< storage_account_name >** , **< share information >** , **< smb_version >** , **< storage_account_key >** en **< mount_point >** te vervangen door de relevante informatie voor uw variabelen. Als uw Linux-distributie SMB 3,0 ondersteunt met versleuteling (Zie [informatie over SMB-client vereisten](#smb-client-reqs) voor meer informatie), gebruikt u **3,0** voor **< smb_version >** . Gebruik voor Linux-distributies die geen ondersteuning bieden voor SMB 3,0 met versleuteling, **2,1** voor **< smb_version >** . Een Azure-bestands share kan alleen worden gekoppeld buiten een Azure-regio (met inbegrip van on-premises of in een andere Azure-regio) met SMB 3,0. Als u wilt, kunt u de map-en bestands machtigingen van de gekoppelde share wijzigen, maar dit betekent dat de toegang wordt beperkt.

    ```bash
    sudo mount -t cifs //<storage_account_name>.file.core.windows.net/<share_name> <mount_point> -o vers=<smb_version>,username=<storage_account_name>,password=<storage_account_key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Wanneer u klaar bent met het gebruik van de Azure-bestands share `sudo umount <mount_point>` , kunt u gebruiken om de share te ontkoppelen.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Een permanent koppel punt maken voor de Azure-bestands share met`/etc/fstab`

1. **[Installeer het CIFS-hulppr-pakket voor uw Linux-distributie](#install-cifs-utils)** .

1. **Maak een map voor het koppel punt**: U kunt een map voor een koppel punt maken op een wille keurige locatie in het bestands systeem, maar dit is een algemene Conventie voor het maken van deze onder een nieuwe map. Wanneer u dit maakt, noteert u het absolute pad van de map. Met de volgende opdracht maakt u bijvoorbeeld een nieuwe map, vervangt u **< storage_account_name >** en **< file_share_name >** door de juiste informatie voor uw omgeving.

    ```bash
    sudo mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Maak een referentie bestand voor het opslaan van de gebruikers naam (de naam van het opslag account) en het wacht woord (de sleutel van het opslag account) voor de bestands share.** Vervang **< storage_account_name >** en **< storage_account_key >** door de relevante informatie voor uw omgeving.

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
    fi
    if [ ! -f "/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred" ]; then
    sudo bash -c 'echo "username=<STORAGE ACCOUNT NAME>" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    sudo bash -c 'echo "password=7wRbLU5ea4mgc<DRIVE LETTER>PIpUCNcuG9gk2W4S2tv7p0cTm62wXTK<DRIVE LETTER>CgJlBJPKYc4VMnwhyQd<DRIVE LETTER>UT<DRIVE LETTER>yR5/RtEHyT/EHtg2Q==" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    fi
    ```

1. **Wijzig de machtigingen voor het referentie bestand zodat alleen het toegangs punt het wachtwoord bestand kan lezen of wijzigen.** Omdat de sleutel van het opslag account in feite een super beheerders wachtwoord voor het opslag account is, is het instellen van de machtigingen voor het bestand, zodat alleen root toegang belang rijk is, zodat gebruikers met een lagere bevoegdheid de sleutel van het opslag account niet kunnen ophalen.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage_account_name>.cred
    ```

1. **Gebruik de volgende opdracht om de volgende regel toe te `/etc/fstab`voegen** : Vergeet niet om **< storage_account_name >** , **< share information >** , **< smb_version >** en **< mount_point >** te vervangen door de relevante informatie voor uw omgeving. Als uw Linux-distributie SMB 3,0 ondersteunt met versleuteling (Zie [informatie over SMB-client vereisten](#smb-client-reqs) voor meer informatie), gebruikt u **3,0** voor **< smb_version >** . Gebruik voor Linux-distributies die geen ondersteuning bieden voor SMB 3,0 met versleuteling, **2,1** voor **< smb_version >** . Een Azure-bestands share kan alleen worden gekoppeld buiten een Azure-regio (met inbegrip van on-premises of in een andere Azure-regio) met SMB 3,0.

    ```bash
    sudo bash -c 'echo "//<STORAGE ACCOUNT NAME>.file.core.windows.net/<FILE SHARE NAME> /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME> cifs _netdev,nofail,vers=3.0,credentials=/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'

    sudo mount /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME>
    ```

> [!Note]  
> U kunt gebruiken `sudo mount -a` om de Azure-bestands share na het `/etc/fstab` bewerken te koppelen in plaats van opnieuw op te starten.

## <a name="feedback"></a>Feedback

Linux-gebruikers willen we graag van u horen.

De groep Azure Files voor Linux-gebruikers biedt een forum waarmee u feedback kunt delen tijdens het evalueren en aannemen van bestands opslag in Linux. E-mail [Azure files Linux-gebruikers](mailto:azurefileslinuxusers@microsoft.com) om lid te worden van de groep gebruikers.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende koppelingen voor meer informatie over Azure Files:

* [Implementatie van Azure Files plannen](storage-files-planning.md)
* [Veelgestelde vragen](../storage-files-faq.md)
* [Problemen oplossen](storage-troubleshoot-linux-file-connection-problems.md)
