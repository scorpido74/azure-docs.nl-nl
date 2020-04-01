---
author: cynthn
ms.author: cynthn
ms.date: 01/23/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 658910dc4291375c7b2ab22e88c599b970b885af
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419200"
---
Met vm-afbeeldingen (Virtual Machine) kunnen organisaties migreren naar de cloud en zorgen voor consistentie in de implementaties. Afbeeldingen bevatten meestal vooraf gedefinieerde beveiligings- en configuratie-instellingen en de benodigde software. Het instellen van uw eigen imaging-pijplijn vergt tijd, infrastructuur en installatie, maar met Azure VM Image Builder biedt u gewoon een eenvoudige configuratie die uw afbeelding beschrijft, deze aan de service voorlegt en de afbeelding wordt gebouwd en gedistribueerd.
 
Met azure VM Image Builder (Azure Image Builder) u beginnen met een Windows- of Linux-gebaseerde Azure Marketplace-afbeelding, bestaande aangepaste afbeeldingen of Red Hat Enterprise Linux (RHEL) ISO en beginnen met het toevoegen van uw eigen aanpassingen. Omdat de Image Builder is gebouwd op [HashiCorp Packer,](https://packer.io/)u ook uw bestaande Packer shell provisioner scripts importeren. U ook opgeven waar u wilt dat uw afbeeldingen worden gehost, in de [Azure Shared Image Gallery,](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)als een beheerde afbeelding of een VHD.

> [!IMPORTANT]
> Azure Image Builder bevindt zich momenteel in een openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

## <a name="preview-features"></a>Preview-functies

Voor de preview worden deze functies ondersteund:

- Het maken van gouden basislijnafbeeldingen, die uw minimale beveiliging en bedrijfsconfiguraties omvatten, en afdelingen in staat stellen deze verder aan te passen aan hun behoeften.
- Met het patchen van bestaande afbeeldingen u met Image Builder voortdurend bestaande aangepaste afbeeldingen patchen.
- Verbind imagebuilder met uw bestaande virtuele netwerken, zodat u verbinding maken met bestaande configuratieservers (DSC, Chef, Puppet enz.), bestandsshares of andere routeerbare servers/services.
- Integratie met de Azure Shared Image Gallery, stelt u in staat om afbeeldingen wereldwijd te distribueren, te gebruiken en te schalen en geeft u een imagemanagementsysteem.
- Integratie met bestaande pijplijnen voor imagebuilds, bel Image Builder vanuit uw pijplijn of gebruik de eenvoudige Preview Image Builder Azure DevOps-taak.
- Migreer een bestaande pijplijn voor het aanpassen van afbeeldingen naar Azure. Gebruik uw bestaande scripts, opdrachten en processen om afbeeldingen aan te passen.
- Het maken van afbeeldingen in VHD-formaat.
 

## <a name="regions"></a>Regio's
De Azure Image Builder-service is beschikbaar voor een voorbeeld in deze regio's. Afbeeldingen kunnen buiten deze regio's worden verspreid.
- VS - oost
- VS - oost 2
- VS - west-centraal
- VS - west
- VS - west 2
- Europa - noord
- Europa -west

## <a name="os-support"></a>Ondersteuning voor besturingssysteem
AIB ondersteunt Azure Marketplace-basis-OS-afbeeldingen:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6.
- CentOS 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise multisessie/Professional
- Windows 2016
- Windows 2019

RHEL ISOs-ondersteuning wordt afgeschaft, bekijk de sjabloondocumentatie voor meer informatie.

## <a name="how-it-works"></a>Hoe werkt het?


![Conceptuele tekening van Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder.png)

De Azure Image Builder is een volledig beheerde Azure-service die toegankelijk is voor een Azure-bronprovider. Het Azure Image Builder-proces heeft drie hoofdonderdelen: bron, aanpassen en distribueren, deze worden weergegeven in een sjabloon. Het onderstaande diagram toont de componenten, met een aantal van hun eigenschappen. 
 


**Image Builder-proces** 

![Conceptuele tekening van het Azure Image Builder-proces](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Maak de afbeeldingssjabloon als een JSon-bestand. Dit JSON-bestand bevat informatie over de afbeeldingsbron, aanpassingen en distributie. Er zijn meerdere voorbeelden in de [GitHub-repository azure image builder.](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)
1. Als u deze naar de service verzendt, wordt er een artefact voor afbeeldingssjablonen gemaakt in de door u opgegeven brongroep. Op de achtergrond downloadt Image Builder de bronafbeelding of ISO en scripts indien nodig. Deze worden opgeslagen in een afzonderlijke brongroep die automatisch wordt gemaakt\<in uw\<abonnement, in de indeling: IT_ DestinationResourceGroup>_ TemplateName>. 
1. Zodra de afbeeldingssjabloon is gemaakt, u de afbeelding opnieuw maken. Op de achtergrond gebruikt Image Builder de sjabloon en bronbestanden om een VM (standaardgrootte: Standard_D1_v2),\<netwerk,\<openbaar IP, NSG en opslag te maken in de IT_ DestinationResourceGroup>_ TemplateName> brongroep.
1. Als onderdeel van het maken van afbeeldingen verdeelt Image Builder de afbeelding volgens de\<sjabloon en\<verwijdert vervolgens de extra bronnen in de IT_ DestinationResourceGroup>_ TemplateName> resourcegroep die voor het proces is gemaakt.


## <a name="permissions"></a>Machtigingen

Als u Azure VM Image Builder toestaat afbeeldingen te distribueren naar de beheerde afbeeldingen of naar een gedeelde imagegalerie, moet u 'Inzendermachtigingen' geven voor de service 'Azure Virtual Machine Image Builder' (app-id: cf32a0cc-373c-47c9-9156-0db11f6a6dfc) op de brongroepen. 

Als u een bestaande aangepaste beheerde afbeelding of afbeeldingsversie gebruikt, heeft de Azure Image Builder een minimum aan 'Reader'-toegang tot die brongroepen nodig.

U toegang toewijzen via azure cli:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

U toegang toewijzen via de PowerShell:

```azurePowerShell-interactive
New-AzRoleAssignment -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 -Scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName> -RoleDefinitionName Contributor
```


Als het serviceaccount niet wordt gevonden, kan dit betekenen dat het abonnement waarbij u de roltoewijzing toevoegt, nog niet is geregistreerd voor de resourceprovider.


## <a name="costs"></a>Kosten
U maakt een aantal reken-, netwerk- en opslagkosten bij het maken, bouwen en opslaan van afbeeldingen met Azure Image Builder. Deze kosten zijn vergelijkbaar met de kosten die worden gemaakt voor het handmatig maken van aangepaste afbeeldingen. Voor de resources worden kosten in rekening gebracht tegen uw Azure-tarieven. 

Tijdens het proces voor het maken van `IT_<DestinationResourceGroup>_<TemplateName>` afbeeldingen worden bestanden gedownload en opgeslagen in de resourcegroep, wat een kleine opslagkosten met zich meebrengt. Als u deze niet wilt bewaren, verwijdert u de **afbeeldingssjabloon** na de afbeeldingsopbouw.
 
Image Builder maakt een VM met een D1v2 VM-grootte en de opslag en netwerken die nodig zijn voor de VM. Deze resources gaan mee voor de duur van het bouwproces en worden verwijderd zodra Image Builder klaar is met het maken van de afbeelding. 
 
Azure Image Builder distribueert de afbeelding naar de door u gekozen regio's, waardoor netwerkuitwetrederkosten kunnen worden gemaakt.
 
## <a name="next-steps"></a>Volgende stappen 
 
Zie de artikelen voor het bouwen van [Linux-](../articles/virtual-machines/linux/image-builder.md) of [Windows-afbeeldingen](../articles/virtual-machines/windows/image-builder.md) om de Azure Image Builder uit te proberen.
 
 
