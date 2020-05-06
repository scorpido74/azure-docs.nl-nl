---
author: cynthn
ms.author: cynthn
ms.date: 01/23/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 658910dc4291375c7b2ab22e88c599b970b885af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80419200"
---
Met gestandaardiseerde installatie kopieën van virtuele machines kunnen organisaties migreren naar de Cloud en zorgen voor consistentie in de implementaties. Installatie kopieën bevatten doorgaans vooraf gedefinieerde beveiligings-en configuratie-instellingen en de benodigde software. Voor het instellen van uw eigen Imaging-pijp lijn zijn tijd, infra structuur en configuratie vereist, maar met Azure VM Image Builder kunt u een eenvoudige configuratie bieden waarmee uw installatie kopie wordt beschreven, verzonden naar de service en de installatie kopie wordt gebouwd en gedistribueerd.
 
Met de opbouw functie voor installatie kopieën van Azure VM (Azure Image Builder) kunt u beginnen met een Azure Marketplace-installatie kopie op basis van Windows of Linux, bestaande aangepaste installatie kopieën of Red Hat Enterprise Linux (RHEL) ISO en begint met het toevoegen van uw eigen aanpassingen. Omdat de opbouw functie voor installatie kopieën is gebaseerd op [HashiCorp Packer](https://packer.io/), kunt u ook uw bestaande scripts voor de shell-inrichtings pakket importeren. U kunt ook opgeven waar u wilt dat uw installatie kopieën worden gehost, in de [Galerie met gedeelde installatie kopieën van Azure](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)als een beheerde installatie kopie of een VHD.

> [!IMPORTANT]
> Azure Image Builder is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Preview-functies

Voor de preview worden de volgende functies ondersteund:

- Het maken van gouden Baseline-installatie kopieën, die uw minimale beveiligings-en bedrijfs configuraties bevatten, en die afdelingen in staat stellen om deze verder aan hun behoeften aan te passen.
- Als u bestaande installatie kopieën wilt bijwerken, kunt u met Image Builder voortdurend bestaande aangepaste installatie kopieën bijwerken.
- Verbind de opbouw functie voor installatie kopieën met uw bestaande virtuele netwerken, zodat u verbinding kunt maken met bestaande configuratie servers (DSC, chef, puppet enz.), bestands shares of andere Routeer bare servers/Services.
- Dankzij de integratie met de galerie met gedeelde Azure-afbeeldingen kunt u installatie kopieën globaal distribueren, versies en schalen, en beschikt u over een systeem voor het beheer van installatie kopieën.
- Integratie met bestaande installatie kopieën bouw pijp lijnen, roep de opbouw functie voor installatie kopieën aan vanuit uw pijp lijn, of gebruik de Azure DevOps-taak voor de preview-versie van de voorbeeld weergave van installatie kopieën.
- Migreer een bestaande pijp lijn voor het aanpassen van een installatie kopie naar Azure. Gebruik uw bestaande scripts, opdrachten en processen om installatie kopieën aan te passen.
- Installatie kopieën maken in VHD-indeling.
 

## <a name="regions"></a>Regio's
De Azure Image Builder-service is beschikbaar voor een preview in deze regio's. Installatie kopieën kunnen buiten deze regio's worden gedistribueerd.
- VS - oost
- VS - oost 2
- VS - west-centraal
- VS - west
- VS - west 2
- Europa - noord
- Europa -west

## <a name="os-support"></a>Ondersteuning voor besturings systeem
AIB biedt ondersteuning voor Azure Marketplace Base OS-basis installatie kopieën:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7,6, 7,7
- CentOS 7,6, 7,7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enter prise/Enter prise multi-sessie/Professional
- Windows 2016
- Windows 2019

RHEL Iso's support wordt afgeschaft. Raadpleeg de documentatie van de sjabloon voor meer informatie.

## <a name="how-it-works"></a>Hoe het werkt


![Conceptuele tekening van Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder.png)

De opbouw functie voor installatie kopieën van Azure is een volledig beheerde Azure-service die toegankelijk is voor een Azure-resource provider. Het Azure Image Builder-proces bestaat uit drie hoofd onderdelen: bron, aanpassen en distribueren. deze worden weer gegeven in een sjabloon. In het onderstaande diagram ziet u de onderdelen, met een aantal eigenschappen. 
 


**Image Builder-proces** 

![Conceptuele tekening van het Azure Image Builder-proces](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Maak de afbeeldings sjabloon als een. JSON-bestand. Dit JSON-bestand bevat informatie over de afbeeldings bron, aanpassingen en distributie. Er zijn meerdere voor beelden in de [Azure Image Builder github-opslag plaats](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Verzend het naar de service. Hiermee wordt een afbeeldings sjabloon artefact gemaakt in de resource groep die u opgeeft. Op de achtergrond downloadt de opbouw functie voor afbeeldingen de bron installatie kopie of ISO, en scripts als dat nodig is. Deze worden opgeslagen in een afzonderlijke resource groep die automatisch wordt gemaakt in uw abonnement, in de indeling: IT_\<DestinationResourceGroup>_\<template>. 
1. Zodra de afbeeldings sjabloon is gemaakt, kunt u de installatie kopie maken. In de opbouw functie voor achtergrond gebruiken de sjabloon en de bron bestanden voor het maken van een virtuele machine (standaard grootte: Standard_D1_v2), netwerk, openbaar IP-adres,\<NSG en\<opslag in de IT_ DestinationResourceGroup>_ sjabloon> resource groep.
1. Als onderdeel van het maken van de installatie kopie distribueert Image Builder de installatie kopie op basis van de sjabloon. vervolgens worden de extra resources\<in de\<IT_ DestinationResourceGroup>_ sjabloon naam> resource groep die voor het proces is gemaakt, verwijderd.


## <a name="permissions"></a>Machtigingen

Als u wilt dat Azure VM Image Builder installatie kopieën naar de beheerde installatie kopieën of naar een galerie met gedeelde installatie kopieën kan distribueren, moet u de machtigingen voor Inzender opgeven voor de service ' Azure virtual machine Image Builder ' (App-ID: cf32a0cc-373c-47c9-9156-0db11f6a6dfc) voor de resource groepen. 

Als u een bestaande, aangepaste beheerde installatie kopie of versie van de installatie kopie gebruikt, is voor de Azure Image Builder mini maal de toegang ' lezer ' vereist voor deze resource groepen.

U kunt toegang toewijzen met behulp van de Azure CLI:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

U kunt toegang toewijzen met behulp van de Power shell:

```azurePowerShell-interactive
New-AzRoleAssignment -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 -Scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName> -RoleDefinitionName Contributor
```


Als het service account niet wordt gevonden, kan dit betekenen dat het abonnement waaraan u de roltoewijzing toevoegt, nog niet is geregistreerd voor de resource provider.


## <a name="costs"></a>Kosten
U maakt een aantal reken-, netwerk-en opslag kosten bij het maken, maken en opslaan van installatie kopieën met Azure Image Builder. Deze kosten zijn vergelijkbaar met de kosten die zijn gemaakt bij het hand matig maken van aangepaste installatie kopieën. Voor de resources worden er kosten in rekening gebracht voor uw Azure-tarieven. 

Tijdens het maken van de installatie kopie worden bestanden gedownload en opgeslagen in `IT_<DestinationResourceGroup>_<TemplateName>` de resource groep, waardoor er een kleine opslag kosten in rekening worden gebracht. Als u deze niet wilt blijven gebruiken, verwijdert u de **afbeeldings sjabloon** na het maken van de installatie kopie.
 
Met de opbouw functie voor installatie kopieën wordt een VM gemaakt met behulp van een D1v2 VM-grootte en de opslag en netwerken die nodig zijn voor de virtuele machine. Deze resources duren de duur van het bouw proces en worden verwijderd wanneer de installatie kopie is gemaakt met de opbouw functie voor installatie kopieën. 
 
De installatie kopie wordt door Azure Image Builder gedistribueerd naar de gekozen regio's. Dit kan leiden tot uitstaande netwerk kosten.
 
## <a name="next-steps"></a>Volgende stappen 
 
Zie de artikelen voor het bouwen van [Linux](../articles/virtual-machines/linux/image-builder.md) -of [Windows](../articles/virtual-machines/windows/image-builder.md) -installatie kopieën om de Azure Image Builder uit te proberen.
 
 
