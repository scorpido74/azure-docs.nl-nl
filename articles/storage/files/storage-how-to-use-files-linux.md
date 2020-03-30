---
title: Azure-bestanden gebruiken met Linux | Microsoft Documenten
description: Meer informatie over het monteren van een Azure-bestandsshare via SMB op Linux.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2dc78c25c2cf63a510b9451c8d694795cd8a91eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060950"
---
# <a name="use-azure-files-with-linux"></a>Azure Files gebruiken met Linux
[Azure Files ](storage-files-introduction.md) is het eenvoudig te gebruiken cloudbestandssysteem van Microsoft. Azure-bestandsshares kunnen worden gemonteerd in Linux-distributies met behulp van de [SMB-kernelclient.](https://wiki.samba.org/index.php/LinuxCIFS) In dit artikel worden twee manieren weergegeven om een `mount` Azure-bestandsshare te monteren: `/etc/fstab`on-demand met de opdracht en op start starten door een item te maken in .

De aanbevolen manier om een Azure-bestandsshare op Linux te monteren, is smb 3.0 te gebruiken. Azure Files vereist standaard versleuteling tijdens het transport, die alleen wordt ondersteund door SMB 3.0. Azure Files ondersteunt ook SMB 2.1, dat geen versleuteling tijdens het transport ondersteunt, maar u mag azure-bestandsshares niet monteren met SMB 2.1 uit een andere Azure-regio of on-premises om veiligheidsredenen. Tenzij uw toepassing specifiek SMB 2.1 vereist, is er weinig reden om het te gebruiken aangezien de populairste, onlangs vrijgegeven distributies Linux SMB 3.0 steunen:  

| | SMB 2.1 <br>(Monteert vm's binnen hetzelfde Azure-gebied) | SMB 3.0 <br>(Mounts van on-premises en cross-region) |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04+ |
| Red Hat Enterprise Linux (RHEL) | 7+ | 7,5+ |
| CentOS | 7+ |  7,5+ |
| Debian | 8+ | 10+ |
| openSUSE | 13,2+ | 42,3+ |
| SUSE Linux Enterprise Server | 12+ | 12 SP3+ |

Als u een Linux-distributie gebruikt die niet in de bovenstaande tabel staat, u controleren of uw Linux-distributie SMB 3.0 ondersteunt met versleuteling door de Linux-kernelversie te controleren. SMB 3.0 met encryptie is toegevoegd aan Linux kernel versie 4.11. De `uname` opdracht retourneert de versie van de Linux-kernel in gebruik:

```bash
uname -r
```

## <a name="prerequisites"></a>Vereisten
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Zorg ervoor dat het cifs-utils-pakket is geïnstalleerd.**  
    Het cifs-utils pakket kan worden geïnstalleerd met behulp van de package manager op de Linux distributie van uw keuze. 

    Gebruik **op Ubuntu-** en **Debian-gebaseerde** distributies de `apt` package manager:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    Op **Fedora**, **Red Hat Enterprise Linux 8+** en **CentOS 8 +**, gebruik maken van de `dnf` package manager:

    ```bash
    sudo dnf install cifs-utils
    ```

    Op oudere versies van **Red Hat Enterprise** `yum` Linux en **CentOS,** gebruik maken van de package manager:

    ```bash
    sudo yum install cifs-utils 
    ```

    Gebruik **bij openSUSE**de `zypper` package manager:

    ```bash
    sudo zypper install cifs-utils
    ```

    Gebruik bij andere distributies de juiste package manager of [compileer uit de bron](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **De meest recente versie van de Azure Command Line Interface (CLI).** Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) en uw besturingssysteem selecteren voor meer informatie over het installeren van de Azure CLI. Als u de Azure PowerShell-module liever gebruikt in PowerShell 6+, u echter de onderstaande instructies voor de Azure CLI wel weergeven.

* **Controleer of poort 445 open is**: SMB communiceert via TCP-poort 445 - controleer of uw firewall TCP-poorten 445 niet blokkeert vanaf de clientmachine.  Vervang **<>van uw brongroep** en<uw **>**
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    Als de verbinding is geslaagd, ziet u iets dat lijkt op de volgende uitvoer:

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Als u poort 445 niet openen op uw bedrijfsnetwerk of niet door een INTERNETPROVIDER wordt geblokkeerd, u een VPN-verbinding of ExpressRoute gebruiken om rond poort 445 te werken. Zie [Netwerkoverwegingen voor directe azure-toegang tot bestandsdelen](storage-files-networking-overview.md)voor meer informatie ..

## <a name="mounting-azure-file-share"></a>Azure-bestandsshare monteren
Als u een Azure-bestandsshare wilt gebruiken met uw Linux-distributie, moet u een map maken om te dienen als het bevestigingspunt voor het Azure-bestandsaandeel. Een mount point kan overal op uw Linux-systeem worden gemaakt, maar het is gemeenschappelijk verdrag om dit te maken onder / mnt. Na het bevestigingspunt gebruikt `mount` u de opdracht om toegang te krijgen tot de Azure-bestandsshare.

U desgewenst dezelfde Azure-bestandsshare op meerdere bevestigingspunten monteren.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Het Azure-bestandsshare on-demand monteren met`mount`
1. **Een map maken voor het bevestigingspunt:** Vervang `<your-resource-group>`, `<your-storage-account>`en `<your-file-share>` met de juiste informatie voor uw omgeving:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Gebruik de opdracht Monteren om de Azure-bestandsshare te monteren.** In het onderstaande voorbeeld, de lokale Linux-bestand en map machtigingen standaard 0755, wat betekent lezen, schrijven en uitvoeren voor de eigenaar (op basis van het bestand / directory Linux eigenaar), lezen en uitvoeren voor gebruikers in de eigenaar groep, en lezen en uitvoeren voor anderen op het systeem. U `uid` de `gid` opties en de montage gebruiken om de gebruikersnaam en groeps-id voor de houder in te stellen. U `dir_mode` ook `file_mode` aangepaste machtigingen naar wens gebruiken en instellen. Zie [UNIX numerieke notatie](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) op Wikipedia voor meer informatie over het instellen van machtigingen. 

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
    ```

    > [!Note]  
    > De bovenstaande mount commando mounts met SMB 3.0. Als uw Linux-distributie smb 3.0 niet ondersteunt met versleuteling of als deze alleen SMB 2.1 ondersteunt, u alleen vanuit een Azure VM worden gemonteerd in dezelfde regio als het opslagaccount. Als u uw Azure-bestandsshare wilt monteren op een Linux-distributie die SMB 3.0 niet ondersteunt met versleuteling, moet u [versleuteling tijdens het transport uitschakelen voor het opslagaccount.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

Wanneer u klaar bent met het gebruik `sudo umount $mntPath` van de Azure-bestandsshare, u het delen de monteren.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Een blijvend bevestigingspunt maken voor de Azure-bestandsshare met`/etc/fstab`
1. **Een map maken voor het bevestigingspunt:** een map voor een bevestigingspunt kan overal op het bestandssysteem worden gemaakt, maar het is gebruikelijk om dit onder /mnt te maken. Met de volgende opdracht wordt bijvoorbeeld `<your-resource-group>`een `<your-storage-account>`nieuwe `<your-file-share>` map gemaakt, vervangt u en met de juiste informatie voor uw omgeving:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Maak een referentiebestand om de gebruikersnaam (de naam van het opslagaccount) en het wachtwoord (de opslagaccountsleutel) voor de bestandsshare op te slaan.** 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir "/etc/smbcredentials"
    fi

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')
    
    smbCredentialFile="/etc/smbcredentials/$storageAccountName.cred"
    if [ ! -f $smbCredentialFile ]; then
        echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
        echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
    else 
        echo "The credential file $smbCredentialFile already exists, and was not modified."
    fi
    ```

1. **Machtigingen voor het referentiebestand wijzigen, zodat alleen root het wachtwoordbestand kan lezen of wijzigen.** Aangezien de opslagaccountsleutel in wezen een superbeheerderswachtwoord voor het opslagaccount is, is het belangrijk om de machtigingen voor het bestand zodanig in te stellen dat alleen root toegang heeft, zodat gebruikers met een lager privilege de opslagaccountsleutel niet kunnen ophalen.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **Gebruik de volgende opdracht om de `/etc/fstab`volgende regel toe te voegen aan **: In het onderstaande voorbeeld, de lokale Linux-bestand en map machtigingen standaard 0755, wat betekent lezen, schrijven en uitvoeren voor de eigenaar (op basis van het bestand / directory Linux eigenaar), lezen en uitvoeren voor gebruikers in de eigenaar groep, en lezen en uitvoeren voor anderen op het systeem. U `uid` de `gid` opties en de montage gebruiken om de gebruikersnaam en groeps-id voor de houder in te stellen. U `dir_mode` ook `file_mode` aangepaste machtigingen naar wens gebruiken en instellen. Zie [UNIX numerieke notatie](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) op Wikipedia voor meer informatie over het instellen van machtigingen.

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
        echo "$smbPath $mntPath cifs nofail,vers=3.0,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
    else
        echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
    fi

    sudo mount -a
    ```
    
    > [!Note]  
    > De bovenstaande mount commando mounts met SMB 3.0. Als uw Linux-distributie smb 3.0 niet ondersteunt met versleuteling of als deze alleen SMB 2.1 ondersteunt, u alleen vanuit een Azure VM worden gemonteerd in dezelfde regio als het opslagaccount. Als u uw Azure-bestandsshare wilt monteren op een Linux-distributie die SMB 3.0 niet ondersteunt met versleuteling, moet u [versleuteling tijdens het transport uitschakelen voor het opslagaccount.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="securing-linux"></a>Linux beveiligen
Om een Azure-bestandsshare op Linux te kunnen monteren, moet poort 445 toegankelijk zijn. Veel organisaties blokkeren poort 445 vanwege de beveiligingsrisico's die bij SMB 1 horen. SMB 1, ook bekend als CIFS (Common Internet File System), is een legacy bestandssysteem protocol opgenomen met veel Linux-distributies. SMB 1 is een verouderd, inefficiënt en bovenal onveilig protocol. Het goede nieuws is dat Azure Files geen SMB 1 ondersteunt, en te beginnen met Linux-kernelversie 4.18 maakt Linux het mogelijk om SMB 1 uit te schakelen. We [raden je altijd ten zeerste aan](https://aka.ms/stopusingsmb1) om de SMB 1 uit te schakelen op uw Linux-clients voordat u smb-bestandsshares in productie gebruikt.

Beginnend met Linux kernel 4.18, de `cifs` SMB kernel module, opgeroepen om legacy redenen, onthult een `disable_legacy_dialects`nieuwe module parameter (vaak aangeduid als *parm* door verschillende externe documentatie), genaamd . Hoewel geïntroduceerd in Linux kernel 4.18, sommige leveranciers hebben backported deze verandering naar oudere kernels die zij ondersteunen. Voor het gemak, de volgende tabel details van de beschikbaarheid van deze module parameter op gemeenschappelijke Linux-distributies.

| Distributie | Kan SMB 1 uitschakelen |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | Nee |
| Ubuntu 18.04 | Ja |
| Ubuntu 19.04+ | Ja |
| Debian 8-9 | Nee |
| Debian 10+ | Ja |
| Fedora 29+ | Ja |
| CentOS 7 | Nee | 
| CentOS 8+ | Ja |
| Red Hat Enterprise Linux 6.x-7.x | Nee |
| Red Hat Enterprise Linux 8+ | Ja |
| openSUSE Leap 15.0 | Nee |
| openSUSE Leap 15.1+ | Ja |
| openSUSE Tumbleweed | Ja |
| SUSE Linux Enterprise 11.x-12.x | Nee |
| SUSE Linux Enterprise 15 | Nee |
| SUSE Linux Enterprise 15.1 | Nee |

U controleren of uw Linux-distributie de `disable_legacy_dialects` parameter module ondersteunt via de volgende opdracht.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Met deze opdracht moet het volgende bericht worden uitgevoerd:

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Voordat u SMB 1 uitschakelt, moet u controleren of de SMB-module momenteel niet op uw systeem is geladen (dit gebeurt automatisch als u een SMB-aandeel hebt gemonteerd). U dit doen met de volgende opdracht, die niets moet uitvoeren als SMB niet wordt geladen:

```bash
lsmod | grep cifs
```

Als u de module wilt uitladen, ontkoppelt u eerst alle SMB-shares (met behulp van de `umount` opdracht zoals hierboven beschreven). U alle gemonteerde SMB-shares op uw systeem identificeren met de volgende opdracht:

```bash
mount | grep cifs
```

Zodra u alle smb-bestandsshares hebt losgeontkoppeld, is het veilig om de module te ontladen. U kunt dit doen met de opdracht `modprobe`:

```bash
sudo modprobe -r cifs
```

U de module handmatig laden met SMB 1 gelost met de `modprobe` opdracht:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Ten slotte u controleren of de SMB-module is geladen met `/sys/module/cifs/parameters`de parameter door te kijken naar de geladen parameters in:

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Als u SMB 1 op Ubuntu- en Debian-gebaseerde distributies voortdurend wilt uitschakelen, moet u een `/etc/modprobe.d/local.conf` nieuw bestand maken (als u nog geen aangepaste opties hebt voor andere modules) dat met de instelling wordt aangeroepen. U dit doen met de volgende opdracht:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

U controleren of dit heeft gewerkt door de SMB-module te laden:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="feedback"></a>Feedback
Linux gebruikers, we willen graag van je horen!

De groep Azure Files for Linux-gebruikers biedt een forum voor feedback terwijl u bestandsopslag op Linux evalueert en aanneemt. E-mail [Azure Files Linux-gebruikers](mailto:azurefiles@microsoft.com) om lid te worden van de gebruikersgroep.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende koppelingen voor meer informatie over Azure Files:

* [Implementatie van Azure Files plannen](storage-files-planning.md)
* [Veelgestelde vragen](../storage-files-faq.md)
* [Probleemoplossing](storage-troubleshoot-linux-file-connection-problems.md)
