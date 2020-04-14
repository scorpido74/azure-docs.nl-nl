---
title: Blijven bestanden in Azure Cloud Shell | Microsoft Documenten
description: Doorloop hoe Azure Cloud Shell bestanden blijft bestaan.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: damaerte
ms.openlocfilehash: 37005a722d4a1962b4f6e1ddb8bb1c7a1229d28a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273287"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Bestanden in Azure Cloud Shell blijven gebruiken
Cloud Shell maakt gebruik van Azure File-opslag om bestanden voor verschillende sessies te blijven gebruiken. Bij de eerste start vraagt Cloud Shell u om een nieuwe of bestaande bestandsshare te koppelen aan het blijven van bestanden in verschillende sessies.

> [!NOTE]
> Bash en PowerShell delen hetzelfde bestandsaandeel. Slechts één bestandsshare kan worden gekoppeld aan automatische montage in Cloud Shell.

> [!NOTE]
> Azure storage firewall wordt niet ondersteund voor cloud shell storage accounts.

## <a name="create-new-storage"></a>Nieuwe opslag maken

Wanneer u basisinstellingen gebruikt en alleen een abonnement selecteert, maakt Cloud Shell namens u drie bronnen in het ondersteunde gebied dat het dichtst bij u ligt:
* Resourcegroep: `cloud-shell-storage-<region>`
* Opslagaccount:`cs<uniqueGuid>`
* Bestandsshare:`cs-<user>-<domain>-com-<uniqueGuid>`

![De instelling Abonnement](media/persisting-shell-storage/basic-storage.png)

Het bestandsaandeel wordt `clouddrive` gemonteerd `$Home` zoals in uw directory. Dit is een eenmalige actie en het delen van bestanden wordt automatisch gestart in volgende sessies. 

Het bestandsaandeel bevat ook een afbeelding van 5 GB die voor `$Home` u is gemaakt en die automatisch gegevens in uw map blijft bestaan. Dit geldt voor zowel Bash als PowerShell.

## <a name="use-existing-resources"></a>Bestaande bronnen gebruiken

Door de geavanceerde optie te gebruiken, u bestaande bronnen koppelen. Wanneer u een Cloud Shell-gebied selecteert, moet u een achtergrondopslagaccount selecteren dat zich in dezelfde regio bevindt. Als uw toegewezen regio bijvoorbeeld West US is, moet u een bestandsshare koppelen dat zich ook in West-US bevindt.

Wanneer de opslaginstellingsprompt wordt weergegeven, selecteert u **Geavanceerde instellingen weergeven** om extra opties weer te geven. Het filter voor de bewoonde opslagopties voor LRS (localredundante opslag), georedundante opslag (GRS) en ZRS-accounts (Zoneredundant storage). 

> [!NOTE]
> Het gebruik van GRS- of ZRS-opslagaccounts wordt aanbevolen voor extra tolerantie voor het delen van back-upbestanden. Welk type redundantie is afhankelijk van uw doelen en prijsvoorkeur. [Meer informatie over replicatieopties voor Azure Storage-accounts](../storage/common/storage-redundancy.md).

![De instelling resourcegroep](media/persisting-shell-storage/advanced-storage.png)

## <a name="securing-storage-access"></a>Toegang tot opslag beveiligen
Voor de beveiliging moet elke gebruiker zijn eigen opslagaccount inrichten.  Voor rbac (role-based access control) moeten gebruikers toegang hebben tot de bijdrager of hoger op het opslagaccountniveau.

Cloud Shell gebruikt een Azure File Share in een opslagaccount, binnen een bepaald abonnement. Vanwege overgenomen machtigingen hebben gebruikers met voldoende toegangsrechten tot het abonnement toegang tot alle opslagaccounts en bestandsshares in het abonnement.

Gebruikers moeten de toegang tot hun bestanden vergrendelen door de machtigingen in te stellen op het opslagaccount of op abonnementsniveau.

## <a name="supported-storage-regions"></a>Ondersteunde opslaggebieden
Als u uw huidige `env` regio wilt vinden, `ACC_LOCATION`u in `$env:ACC_LOCATION`Bash uitvoeren en de variabele of van PowerShell-run lokaliseren. Bestandsshares ontvangen een afbeelding van 5 GB `$Home` die is gemaakt om uw directory te blijven behouden.

Cloud Shell-machines bestaan in de volgende regio's:

|Onderwerp|Regio|
|---|---|
|Noord- en Zuid-Amerika|Oost-VS, South Central US, West US|
|Europa|Europa - noord, Europa - west|
|Azië en Stille Oceaan|India Centraal, Zuidoost-Azië|

Klanten moeten een primaire regio kiezen, tenzij ze de eis hebben dat hun gegevens in rust in een bepaalde regio worden opgeslagen. Als zij een dergelijke eis hebben, moet een secundair opslaggebied worden gebruikt.

### <a name="secondary-storage-regions"></a>Secundaire opslaggebieden
Als een secundair opslaggebied wordt gebruikt, bevindt het bijbehorende Azure-opslagaccount zich in een andere regio als de Cloud Shell-machine waarop u ze monteert. Jane kan bijvoorbeeld instellen dat haar opslagaccount zich in Canada East bevindt, een secundaire regio, maar de machine waaraan ze is gemonteerd, bevindt zich nog steeds in een primaire regio. Haar gegevens in rust is gevestigd in Canada, maar het wordt verwerkt in de Verenigde Staten.

> [!NOTE]
> Als een secundaire regio wordt gebruikt, kan de bestandstoegang en de opstarttijd voor Cloud Shell trager verlopen.

Een gebruiker `(Get-CloudDrive | Get-AzStorageAccount).Location` kan in PowerShell worden uitgevoerd om de locatie van zijn bestandsshare te zien.

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Het maken van resources beperken met een Azure-resourcebeleid
Opslagaccounts die u in Cloud `ms-resource-usage:azure-cloud-shell`Shell maakt, worden getagd met . Als u gebruikers wilt verbieden opslagaccounts te maken in Cloud Shell, maakt u een [Azure-bronbeleid voor tags](../azure-policy/json-samples.md) die worden geactiveerd door deze specifieke tag.

## <a name="how-cloud-shell-storage-works"></a>Hoe Cloud Shell-opslag werkt 
Cloud Shell blijft bestanden via beide volgende methoden gebruiken: 
* Een schijfafbeelding van `$Home` uw map maken om alle inhoud in de map te blijven bestaan. De schijfafbeelding wordt opgeslagen in `acc_<User>.img` de `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`opgegeven bestandsshare zoals bij , en hiermee worden wijzigingen automatisch gesynchroniseerd. 
* Het monteren van `clouddrive` uw `$Home` opgegeven bestandsshare zoals in uw directory voor directe interactie met bestandsshare. `/Home/<User>/clouddrive`is toegewezen `fileshare.storage.windows.net/fileshare`aan .
 
> [!NOTE]
> Alle bestanden `$Home` in uw map, zoals SSH-sleutels, blijven bestaan in de afbeelding van uw gebruikersschijf, die is opgeslagen in uw gemonteerde bestandsshare. Pas aanbevolen procedures toe wanneer `$Home` u informatie in uw map en het gemonteerde bestandsshare blijft gebruiken.

## <a name="clouddrive-commands"></a>clouddrive-opdrachten

### <a name="use-the-clouddrive-command"></a>De `clouddrive` opdracht gebruiken
In Cloud Shell u `clouddrive`een opdracht uitvoeren met de naam, waarmee u het bestandsaandeel dat is gemonteerd op Cloud Shell handmatig bijwerken.

![De opdracht 'clouddrive' uitvoeren](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Lijst`clouddrive`
Voer de `df` opdracht uit om `clouddrive`te ontdekken welke bestandsshare is gemonteerd als , als uitgevoerd. 

Het bestandspad naar clouddrive toont de naam en bestandsshare van uw opslagaccount in de URL. Bijvoorbeeld: `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

### <a name="mount-a-new-clouddrive"></a>Een nieuwe clouddrive monteren

#### <a name="prerequisites-for-manual-mounting"></a>Voorwaarden voor handmatige montage
U de bestandsshare die is gekoppeld aan `clouddrive mount` Cloud Shell bijwerken met behulp van de opdracht.

Als u een bestaand bestandsaandeel monteert, moeten de opslagaccounts zich in het geselecteerde Cloud Shell-gebied bevinden. Haal de locatie `env` op `ACC_LOCATION`door de .

#### <a name="the-clouddrive-mount-command"></a>De `clouddrive mount` opdracht

> [!NOTE]
> Als u een nieuwe bestandsshare monteert, wordt er `$Home` een nieuwe gebruikersafbeelding gemaakt voor uw directory. Uw `$Home` vorige afbeelding wordt bewaard in uw vorige bestandsshare.

Voer `clouddrive mount` de opdracht uit met de volgende parameters:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Om meer details `clouddrive mount -h`te bekijken, uit te voeren, zoals hier weergegeven:

![De opdracht 'clouddrive mount' uitvoeren](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Clouddrive loskoppelen
U een bestandsshare dat op elk gewenst moment is gemonteerd op Cloud Shell, demonteren. Aangezien Cloud Shell een gemonteerde bestandsshare vereist om te worden gebruikt, wordt u gevraagd om een ander bestandsaandeel te maken en te monteren op de volgende sessie.

1. Voer `clouddrive unmount` uit.
2. Bevestig en bevestig aanwijzingen.

Uw bestandsshare blijft bestaan, tenzij u deze handmatig verwijdert. Cloud Shell zal niet langer zoeken naar dit bestandsaandeel bij volgende sessies. Om meer details `clouddrive unmount -h`te bekijken, uit te voeren, zoals hier weergegeven:

![De opdracht 'clouddrive unmount' uitvoeren](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Hoewel het uitvoeren van deze opdracht geen resources verwijdert, verwijdert u handmatig een brongroep, opslagaccount of `$Home` bestandsshare die is toegewezen aan Cloud Shell, uw directoryschijfafbeelding en alle bestanden in uw bestandsshare. Deze actie kan niet ongedaan worden gemaakt.
## <a name="powershell-specific-commands"></a>PowerShell-specifieke opdrachten

### <a name="list-clouddrive-azure-file-shares"></a>Azure-bestandsshares `clouddrive` aanbieden
De `Get-CloudDrive` cmdlet haalt de Azure-gegevens voor `clouddrive` het delen van bestanden op die momenteel zijn gemonteerd door de cloudshell. <br>
![Get-CloudDrive uitvoeren](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Ontkoppelen`clouddrive`
U een Azure-bestandsshare dat op elk gewenst moment is gemonteerd op Cloud Shell, ongedaan maken. Als de Azure-bestandsshare is verwijderd, wordt u gevraagd om bij de volgende sessie een nieuwe Azure-bestandsshare te maken en te monteren.

De `Dismount-CloudDrive` cmdlet ontkoppelt een Azure-bestandsshare van het huidige opslagaccount. Het demonteren `clouddrive` van de huidige sessie wordt beëindigd. De gebruiker wordt gevraagd om tijdens de volgende sessie een nieuwe Azure-bestandsshare te maken en te monteren.
![Deismount-CloudDrive uitvoeren](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Opmerking: Als u een functie in een bestand moet definiëren en deze moet aanroepen vanaf de PowerShell-cmdlets, moet de puntoperator worden opgenomen. Bijvoorbeeld: . .\MyFunctions.ps1

## <a name="next-steps"></a>Volgende stappen
[Cloud Shell Snelstart](quickstart.md) <br>
[Meer informatie over Microsoft Azure Files-opslag](../storage/files/storage-files-introduction.md) <br>
[Meer informatie over opslagtags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
