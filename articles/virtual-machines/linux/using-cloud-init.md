---
title: Overzicht van Cloud-init-ondersteuning voor virtuele Linux-machines in azure
description: Overzicht van Cloud-init-functies in Microsoft Azure
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
ms.date: 10/11/2019
ms.author: danis
ms.openlocfilehash: 6c522af44be51eb89ee9f64bae2dc4e9e7b24123
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873944"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Cloud-init-ondersteuning voor virtuele machines in azure
In dit artikel wordt de ondersteuning beschreven die bestaat voor [Cloud-init](https://cloudinit.readthedocs.io) voor het configureren van een virtuele machine (VM) of schaal sets voor virtuele machines bij het inrichten van de tijd in Azure. Deze Cloud-init-scripts worden uitgevoerd bij de eerste keer opstarten zodra de resources zijn ingericht door Azure.  

## <a name="cloud-init-overview"></a>Overzicht van cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) is een veelgebruikte benadering voor het aanpassen van een Linux-VM als deze voor de eerste keer wordt opgestart. U kunt cloud-init gebruiken voor het installeren van pakketten en schrijven van bestanden, of om gebruikers en beveiliging te configureren. Omdat cloud-init wordt aangeroepen tijdens het opstartproces, zijn er geen extra stappen of agents vereist om toe te passen van uw configuratie.  Voor meer informatie over het juiste indeling uw `#cloud-config` bestanden, Zie de [cloud-init-documentatiesite](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` bestanden zijn tekstbestanden die zijn gecodeerd in base64.

Cloud-init werkt ook in distributies. U gebruikt bijvoorbeeld niet **apt-get install** of **yum install** om een pakket te installeren. In plaats daarvan kunt u een lijst definiëren met te installeren pakketten. Cloud-init maakt automatisch gebruik van het hulpprogramma voor systeemeigen pakketbeheer voor de distro die u selecteert.

We zijn actief werkt met onze onderschreven Linux-distributie partners om cloud-init ingeschakeld installatiekopieën die beschikbaar zijn in de Azure marketplace. Met deze installatie kopieën kunnen uw Cloud-init-implementaties en-configuraties naadloos werken met Vm's en virtuele-machine schaal sets. De volgende tabel geeft een overzicht van de huidige beschikbaarheid van de installatiekopieën van cloud-init ingeschakeld op het Azure-platform:

| Uitgever | Aanbieding | SKU | Version | cloud-init gereed |
|:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |18.04-LTS |nieuwste |ja | 
|Canonical |UbuntuServer |16.04-LTS |nieuwste |ja | 
|Canonical |UbuntuServer |14.04.5-LTS |nieuwste |ja |
|CoreOS |CoreOS |Stabiel |nieuwste |ja |
|Open Logic 7,7 |CentOS |7-CI |7.7.20190920 |preview |
|Oracle 7,7 |Oracle-Linux |77-CI |7.7.01|preview |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |ja |
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 |preview |
    
Momenteel Azure Stack biedt geen ondersteuning voor het inrichten van RHEL 7. x en CentOS 7. x met behulp van Cloud-init.

* Voor RHEL 7,6, Cloud-init Package, is het ondersteunde pakket: *18.2-1. el7_6.2* 
* Voor RHEL 7,7 (preview), Cloud-init Package, het preview-pakket is: *18.5 -3. EL7*
* Voor CentOS 7,7 (preview), Cloud-init Package, het preview-pakket is: *18.5 -3. EL7. CentOS*
* Voor Oracle 7,7 (preview), Cloud-init pakket, het preview-pakket is: *18.5-3.0.1. EL7*

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Wat is het verschil tussen Cloud-init en de Linux-agent (WALA)?
WALA is een Azure-platformspecifieke agent die wordt gebruikt voor het inrichten en configureren van Vm's en het afhandelen van Azure-extensies. We verbeteren de taak voor het configureren van Vm's voor het gebruik van Cloud-init in plaats van de Linux-agent, zodat bestaande Cloud-init-klanten hun huidige Cloud-init-scripts kunnen gebruiken.  Als u beschikt over bestaande investeringen in Cloud-init-scripts voor het configureren van Linux-systemen, zijn er **geen aanvullende instellingen vereist** om deze in te scha kelen. 

Als u de Azure CLI-`--custom-data` switch niet opneemt tijdens de inrichtings tijd, neemt WALA de minimale VM-inrichtings parameters op die nodig zijn voor het inrichten van de virtuele machine en het volt ooien van de implementatie met de standaard waarden.  Als u verwijst naar de Cloud-init `--custom-data` switch, ongeacht wat is opgenomen in uw aangepaste gegevens (afzonderlijke instellingen of volledig script), worden de standaard waarden van WALA genegeerd. 

WALA configuraties van Vm's zijn beperkt tot het werk binnen de maximale VM-inrichtings tijd.  Cloud-init-configuraties die zijn toegepast op Vm's, hebben geen tijds beperkingen en veroorzaken geen implementaties vanwege een time-out. 

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Een virtuele machine die is ingeschakeld voor de Cloud-init implementeren
Het implementeren van een virtuele machine die is ingeschakeld voor de Cloud, is net zo eenvoudig als verwijzingen naar een distributie met Cloud-init tijdens de implementatie.  De Linux-distributies kunnen ervoor kiezen om Cloud-init in te scha kelen en te integreren in de basis installatie kopieën van Azure. Wanneer u de installatie kopie die u wilt implementeren hebt bevestigd, is Cloud-init ingeschakeld. u kunt de Azure CLI gebruiken om de installatie kopie te implementeren. 

De eerste stap bij het implementeren van deze installatie kopie is het maken van een resource groep met de opdracht [AZ Group Create](/cli/azure/group) . Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
De volgende stap is het maken van een bestand in uw huidige shell met de naam *Cloud-init. txt* en het plakken van de volgende configuratie. In dit voor beeld maakt u het bestand in het Cloud Shell niet op uw lokale computer. U kunt elke editor die u wilt gebruiken. Voer `sensible-editor cloud-init.txt` in voor het maken van het bestand en om een overzicht van beschikbare editors te zien. Kies #1 om de **nano** -editor te gebruiken. Controleer of het hele cloud-init-bestand correct is gekopieerd, met name de eerste regel:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Druk op `ctrl-X` om het bestand af te sluiten, typ `y` om het bestand op te slaan en druk op `enter` om de naam van het bestand te bevestigen bij het afsluiten.

De laatste stap bestaat uit het maken van een virtuele machine met de opdracht [AZ VM Create](/cli/azure/vm) . 

In het volgende voor beeld wordt een VM gemaakt met de naam *centos74* en worden er SSH-sleutels gemaakt, als deze nog niet bestaan op een standaard sleutel locatie. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.  Gebruik de `--custom-data`-parameter om door te geven in uw cloud-init-configuratiebestand. Geef het volledige pad naar *cloud-init.txt* op als u het bestand buiten uw huidige werkmap hebt opgeslagen. In het volgende voor beeld wordt een VM gemaakt met de naam *centos74*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Wanneer de virtuele machine is gemaakt, toont de Azure CLI informatie die specifiek is voor uw implementatie. Noteer het `publicIpAddress`. Dit adres wordt gebruikt voor toegang tot de virtuele machine.  Het duurt enige tijd voordat de virtuele machine is gemaakt, de pakketten die moeten worden geïnstalleerd en de app worden gestart. Er zijn achtergrondtaken die nog worden uitgevoerd nadat u door de Azure CLI bent teruggeleid naar de prompt. U kunt een SSH-verbinding met de virtuele machine uitvoeren en de stappen volgen die worden beschreven in de sectie probleem oplossing om de Cloud-init-logboeken te bekijken. 

## <a name="troubleshooting-cloud-init"></a>Problemen met Cloud-init oplossen
Zodra de VM is ingericht, wordt de Cloud-init uitgevoerd via alle modules en het script dat is gedefinieerd in `--custom-data` om de virtuele machine te configureren.  Als u fouten of omissies van de configuratie moet oplossen, moet u de module naam (`disk_setup` of `runcmd` bijvoorbeeld) zoeken in het logboek voor Cloud-init dat zich bevindt in **/var/log/Cloud-init.log**.

> [!NOTE]
> Niet elke module fout leidt tot een onherstelbare algemene configuratie fout van de Cloud-init. Als u bijvoorbeeld de module `runcmd` gebruikt en de uitvoering van het script mislukt, zal de inrichting van de Cloud nog steeds worden ingericht, omdat de runcmd-module is uitgevoerd.

Raadpleeg de [Cloud-init-documentatie](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) voor meer informatie over de Cloud-init-logboek registratie 

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende documenten voor voor beelden van configuratie wijzigingen in de Cloud-init:
 
- [Een extra Linux-gebruiker toevoegen aan een VM](cloudinit-add-user.md)
- [Een pakket beheer programma uitvoeren om bestaande pakketten bij de eerste keer opstarten bij te werken](cloudinit-update-vm.md)
- [Lokale hostnaam van VM wijzigen](cloudinit-update-vm-hostname.md) 
- [Een toepassings pakket installeren, configuratie bestanden bijwerken en sleutels invoeren](tutorial-automate-vm-deployment.md)
