---
title: Overzicht van cloud-init-ondersteuning voor Linux VM's in Azure
description: Overzicht van cloud-init-mogelijkheden om een VM te configureren op inrichtingstijd in Azure.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 01/23/2019
ms.author: danis
ms.openlocfilehash: 1f0395956fa6977be5d1d6f4f4faf06b84c094d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79465036"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>cloud-init-ondersteuning voor virtuele machines in Azure
In dit artikel wordt de ondersteuning uitgelegd die bestaat voor [cloud-init](https://cloudinit.readthedocs.io) om een virtuele machine (VM) of virtuele machineschaalsets te configureren op inrichtingstijd in Azure. Deze cloud-init-configuraties worden uitgevoerd op de eerste boot zodra de resources zijn ingericht door Azure.  

VM Provisioning is het proces waarbij de Azure uw VM Create parameterwaarden, zoals hostname, gebruikersnaam, wachtwoord enz., doorgeeft en beschikbaar maakt voor de VM terwijl deze wordt opgestart. Een 'inrichtingsagent' verbruikt deze waarden, configureert de VM en rapporteert wanneer deze is voltooid. 

Azure ondersteunt twee inrichtingsagents [cloud-init](https://cloudinit.readthedocs.io)en de [Azure Linux Agent (WALA).](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)

## <a name="cloud-init-overview"></a>cloud-init-overzicht
[cloud-init](https://cloudinit.readthedocs.io) is een veel gebruikte aanpak om een Linux VM aan te passen terwijl deze voor het eerst wordt opgestart. U kunt cloud-init gebruiken voor het installeren van pakketten en schrijven van bestanden, of om gebruikers en beveiliging te configureren. Omdat cloud-init wordt aangeroepen tijdens het eerste opstartproces, zijn er geen extra stappen of vereiste agents om uw configuratie toe te passen.  Zie de `#cloud-config` [documentatiesite voor cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)voor meer informatie over het correct inmaken van uw bestanden of andere ingangen.  `#cloud-config`bestanden zijn tekstbestanden gecodeerd in base64.

cloud-init werkt ook voor distributies. U gebruikt bijvoorbeeld niet **apt-get install** of **yum install** om een pakket te installeren. In plaats daarvan kunt u een lijst definiëren met te installeren pakketten. cloud-init gebruikt automatisch de native package management tool voor de distro die u selecteert.

We werken actief samen met onze goedgekeurde Linux distro-partners om cloud-init-afbeeldingen beschikbaar te hebben in de Azure-marktplaats. Deze afbeeldingen zorgen ervoor dat uw cloud-init-implementaties en configuraties naadloos werken met VM's en virtuele machineschaalsets. In eerste instantie werken we samen met de goedgekeurde Linux distro-partners en upstream om cloud-init-functies te garanderen met het besturingssysteem op Azure, vervolgens worden de pakketten bijgewerkt en openbaar gemaakt in de distro-pakketrepositories. 

Er zijn twee fasen om cloud-init beschikbaar te maken voor de goedgekeurde Linux distro OS's op Azure, pakketondersteuning en vervolgens ondersteuning voor afbeeldingen:
* 'cloud-init package support on Azure' documenten die cloud-init pakketten verder worden ondersteund of in preview, zodat u deze pakketten gebruiken met het besturingssysteem in een aangepaste afbeelding.
* 'image cloud-init ready' documenten als de afbeelding al is geconfigureerd om cloud-init te gebruiken.


### <a name="canonical"></a>Canonical
| Uitgever / versie| Aanbieding | SKU | Versie | afbeelding cloud-init klaar | ondersteuning voor cloud-init-pakketten op Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canoniek 18.04 |UbuntuServer |18.04-LTS |meest recente |ja | ja |
|Canoniek 16.04|UbuntuServer |16.04-LTS |meest recente |ja | ja |
|Canoniek 14.04|UbuntuServer |14.04.5-LTS |meest recente |ja | ja |

### <a name="rhel"></a>RHEL
| Uitgever / versie | Aanbieding | SKU | Versie | afbeelding cloud-init klaar | ondersteuning voor cloud-init-pakketten op Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |ja | Ja - ondersteuning van pakketversie: *18.2-1.el7_6.2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | Ja (let op dit is een preview-afbeelding, en zodra alle RHEL 7.7-afbeeldingen cloud-init ondersteunen, wordt dit medio 2020 verwijderd, bericht zal worden gegeven) | Ja - ondersteuning van pakketversie: *18.5-3.el7*|
|RedHat 7.7 |RHEL |7-RAW | N.v.t.| no - image updates tot eind april 2020| Ja - ondersteuning van pakketversie: *18.5-3.el7*|
|RedHat 7.7 |RHEL |7-LVM | N.v.t.| nee - afbeeldingsupdates tot eind april| Ja - ondersteuning van pakketversie: *18.5-3.el7*|
|RedHat 7.7 |RHEL |7.7 | N.v.t.| nee - afbeeldingsupdates tot eind april | Ja - ondersteuning van pakketversie: *18.5-3.el7*|
|RedHat 7.7 |rhel-byos | rhel-lvm77 | N.v.t.|nee - afbeeldingsupdates tot eind april  | Ja - ondersteuning van pakketversie: *18.5-3.el7*|

### <a name="centos"></a>CentOS

| Uitgever / versie | Aanbieding | SKU | Versie | afbeelding cloud-init klaar | ondersteuning voor cloud-init-pakketten op Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7.7 |CentOS |7-CI |7.7.20190920 |Ja (let op dit is een preview-afbeelding, en zodra alle CentOS 7.7-afbeeldingen cloud-init ondersteunen, wordt dit medio 2020 verwijderd, bericht zal worden gegeven) | Ja - ondersteuning van pakketversie: *18,5-3.el7.centos*|

* CentOS 7.7-afbeeldingen die cloud-init-enabled zijn, worden hier in maart 2020 bijgewerkt 

### <a name="oracle"></a>Oracle

| Uitgever / versie | Aanbieding | SKU | Versie | afbeelding cloud-init klaar | ondersteuning voor cloud-init-pakketten op Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7.7 |Oracle-Linux |77-ci |7.7.01| preview afbeelding (let op dit is een preview afbeelding, en het zodra alle Oracle 7.7 beelden cloud-init ondersteunen, zal dit worden verwijderd medio 2020, bericht zal worden gegeven) | nee, in preview, pakket is: *18.5-3.0.1.el7*

### <a name="debian--suse-sles"></a>Debian & SuSE SLES
We zijn momenteel bezig om ondersteuning te bekijken, verwachten updates in februari en maart 2020.

Momenteel ondersteunt Azure Stack de inrichting van afbeeldingen met cloud-init.


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Wat is het verschil tussen cloud-init en de Linux Agent (WALA)?
WALA is een Azure-platformspecifieke agent die wordt gebruikt voor het inrichten en configureren van VM's en het verwerken van [Azure-extensies.](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux) 

We verbeteren de taak van het configureren van VM's om cloud-init te gebruiken in plaats van de Linux Agent om bestaande cloud-init-klanten in staat te stellen hun huidige cloud-init-scripts te gebruiken, of nieuwe klanten om te profiteren van de rijke cloud-init-configuratiefunctionaliteit. Als u bestaande investeringen hebt in cloud-init-scripts voor het configureren van Linux-systemen, zijn er **geen extra instellingen nodig** om het cloud-init-proces in te schakelen. 

cloud-init kan Azure-extensies niet verwerken, dus WALA is nog steeds vereist in de afbeelding om extensies te verwerken, maar moet de inrichtingscode hebben uitgeschakeld, voor goedgekeurde Linux-distro's-afbeeldingen die worden geconverteerd naar voorziening door cloud-init, ze hebben WALA geïnstalleerd en correct instellen.

Wanneer u een VM maakt, neemt `--custom-data` cloud-init of WALA bij het maken van een VM de minimale VM-inrichtingsparameters die nodig zijn om de VM in te richten en de implementatie met de standaardinstellingen te voltooien.  Als u met de switch verwijst `--custom-data` naar de cloud-init-configuratie, is alles wat in uw aangepaste gegevens is opgenomen, beschikbaar voor cloud-init wanneer de VM wordt opgestart.

cloud-init-configuraties die op VM's worden toegepast, hebben geen tijdsbeperkingen en veroorzaken niet dat een implementatie mislukt door timing uit. Dit geldt niet voor WALA, als u de WALA-standaard wijzigt om aangepaste gegevens te verwerken, kan deze niet hoger zijn dan de totale vm-inprovisioning-tijdsvergoeding van 40 minuten, als dat het geval is, mislukt de VM Create.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Implementatie van een cloud-init enabled Virtual Machine
Het implementeren van een virtuele machine met cloudinit is net zo eenvoudig als verwijzen naar een cloud-init-distributie tijdens de implementatie.  Linux-distributiebeheerders moeten ervoor kiezen om cloud-init in te schakelen en te integreren in hun basis Azure-gepubliceerde afbeeldingen. Zodra u hebt bevestigd dat de afbeelding die u wilt implementeren cloud-init is ingeschakeld, u de Azure CLI gebruiken om de afbeelding te implementeren. 

De eerste stap bij het implementeren van deze afbeelding is het maken van een resourcegroep met de opdracht [Az-groep maken.](/cli/azure/group) Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
De volgende stap is het maken van een bestand in uw huidige shell, genaamd *cloud-init.txt* en plak de volgende configuratie. Maak in dit voorbeeld het bestand in de Cloud Shell niet op uw lokale machine. U kunt elke editor die u wilt gebruiken. Voer `sensible-editor cloud-init.txt` in voor het maken van het bestand en om een overzicht van beschikbare editors te zien. Kies #1 om de **nano-editor** te gebruiken. Controleer of het hele cloud-init-bestand correct is gekopieerd, met name de eerste regel:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Druk `ctrl-X` om het bestand `y` af te sluiten, typ om het bestand op te slaan en druk op `enter` om de bestandsnaam bij het afsluiten te bevestigen.

De laatste stap is het maken van een VM met de [opdracht AZ VM create.](/cli/azure/vm) 

In het volgende voorbeeld wordt een VM met de naam *centos74* gemaakt en worden SSH-sleutels gemaakt als deze nog niet bestaan op een standaardsleutellocatie. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.  Gebruik de `--custom-data`-parameter om door te geven in uw cloud-init-configuratiebestand. Geef het volledige pad naar *cloud-init.txt* op als u het bestand buiten uw huidige werkmap hebt opgeslagen. In het volgende voorbeeld wordt een VM met de naam *centos74 geaamiend:*

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Wanneer de VM is gemaakt, worden in de Azure CLI informatie weergegeven die specifiek is voor uw implementatie. Noteer het `publicIpAddress`. Dit adres wordt gebruikt voor toegang tot de virtuele machine.  Het duurt enige tijd voordat de VM wordt gemaakt, de pakketten worden geïnstalleerd en de app wordt gestart. Er zijn achtergrondtaken die nog worden uitgevoerd nadat u door de Azure CLI bent teruggeleid naar de prompt. U SSH in de VM gebruiken en de stappen gebruiken die in de sectie Probleemoplossing worden beschreven om de cloud-init-logboeken weer te geven. 

## <a name="troubleshooting-cloud-init"></a>Problemen met de cloud-init oplossen
Zodra de VM is ingericht, loopt de cloud-init door alle `--custom-data` modules en scripten die zijn gedefinieerd om de VM te configureren.  Als u fouten of omissies uit de configuratie moet oplossen, moet`disk_setup` `runcmd` u zoeken naar de naam van de module (of bijvoorbeeld) in het cloud-init-logboek - in **/var/log/cloud-init.log**.

> [!NOTE]
> Niet elke modulefout resulteert in een fatale algehele configuratiefout in de cloud-init. Als het `runcmd` script bijvoorbeeld mislukt, rapporteert cloud-init nog steeds dat de inrichting is geslaagd omdat de runcmd-module is uitgevoerd.

Zie voor meer informatie over cloud-init-logging de [cloud-init-documentatie](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende documenten voor voorbeelden van configuratiewijzigingen in de cloud init:
 
- [Een extra Linux-gebruiker toevoegen aan een vm](cloudinit-add-user.md)
- [Een package manager uitvoeren om bestaande pakketten bij te werken bij het eerste opstarten](cloudinit-update-vm.md)
- [De lokale hostnaam vm wijzigen](cloudinit-update-vm-hostname.md) 
- [Een toepassingspakket installeren, configuratiebestanden bijwerken en sleutels injecteren](tutorial-automate-vm-deployment.md)
 
