---
title: Afbeeldingen opslaan en distribueren in Azure DevTest Labs | Microsoft Documenten
description: In dit artikel vindt u de stappen om aangepaste afbeeldingen op te slaan van de reeds gemaakte virtuele machines (VM's) in Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: e5bc8e5041bfe6d95e3ff1a93bb3338ccead5bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759428"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Aangepaste installatiekopieën opslaan en distribueren naar meerdere labs
In dit artikel vindt u de stappen om aangepaste afbeeldingen op te slaan van de reeds gemaakte virtuele machines (VM's). Het behandelt ook hoe deze aangepaste beelden te distribueren naar andere DevTest Labs in de organisatie.

## <a name="prerequisites"></a>Vereisten
De volgende items moeten al op hun plaats zijn:

- Een lab voor de Image Factory in Azure DevTest Labs.
- Een Azure DevOps-project dat wordt gebruikt om de afbeeldingsfabriek te automatiseren.
- Broncodelocatie met de scripts en configuratie (in ons voorbeeld in hetzelfde DevOps-project dat in de vorige stap werd genoemd).
- Bouw de definitie om de Azure Powershell-taken te orkestreren.

Volg indien nodig stappen in de [afbeeldingsfabriek uitvoeren vanuit Azure DevOps](image-factory-set-up-devops-lab.md) om deze items te maken of in te stellen. 

## <a name="save-vms-as-generalized-vhds"></a>VM's opslaan als algemene VHD's
Sla de bestaande VM's op als algemene VHD's.  Er is een voorbeeld van PowerShell-script om de bestaande VM's op te slaan als algemene VHD's. Als u deze wilt gebruiken, voegt u eerst een andere **Azure Powershell-taak** toe aan de builddefinitie zoals weergegeven in de volgende afbeelding:

![Azure PowerShell-stap toevoegen](./media/save-distribute-custom-images/powershell-step.png)

Zodra u de nieuwe taak in de lijst hebt, selecteert u het item, zodat we alle details kunnen invullen zoals in de volgende afbeelding wordt weergegeven: 

![PowerShell-instellingen](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Gegeneraliseerde versus gespecialiseerde aangepaste afbeeldingen
In de [Azure-portal](https://portal.azure.com)u bij het maken van een aangepaste afbeelding vanaf een virtuele machine ervoor kiezen om een algemene of een gespecialiseerde aangepaste afbeelding te hebben.

- **Gespecialiseerde aangepaste afbeelding:** Sysprep/Deprovision werd NIET op de machine uitgevoerd. Het betekent dat de afbeelding een exacte kopie is van de OS-schijf op de bestaande virtuele machine (een momentopname).  Dezelfde bestanden, toepassingen, gebruikersaccounts, computernaam, enzovoort, zijn allemaal aanwezig wanneer we een nieuwe machine maken op basis van deze aangepaste afbeelding.
- **Algemene aangepaste afbeelding:** Sysprep/Deprovision werd uitgevoerd op de machine.  Wanneer dit proces wordt uitgevoerd, verwijdert het gebruikersaccounts, verwijdert de computernaam, verwijdert u de bijenkasten van het gebruikersregister, enz., met als doel het beeld te generaliseren, zodat deze kan worden aangepast bij het maken van een andere virtuele machine.  Wanneer u een virtuele machine generaliseert (door sysprep uit te voeren), vernietigt het proces de huidige virtuele machine - het zal niet langer functioneel zijn.

Het script voor het maken van aangepaste afbeeldingen in de Image Factory slaat VHD's op voor virtuele machines die in de vorige stap zijn gemaakt (geïdentificeerd op basis van een tag op de bron in Azure).

## <a name="update-configuration-for-distributing-images"></a>Configuratie bijwerken voor het distribueren van afbeeldingen
De volgende stap in het proces is om de aangepaste beelden van het laboratorium van de beeldfabriek uit aan om het even welke andere laboratoria te duwen die hen nodig hebben. Het kernonderdeel van dit proces is het **configuratiebestand labs.json.** U vindt dit bestand in de map **Configuratie** in de afbeeldingsfabriek.

Er zijn twee belangrijke dingen die in het configuratiebestand labs.json worden vermeld:

- Uniek een specifiek doellab identificeren met behulp van de abonnements-ID en de labnaam.
- De specifieke set afbeeldingen die naar het lab moeten worden gepusht als relatieve paden naar de configuratiewortel. U ook de volledige map opgeven (om alle afbeeldingen in die map op te halen).

Hier is een voorbeeld labs.json bestand met twee labs vermeld. In dit geval distribueert u afbeeldingen naar twee verschillende laboratoria.

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>Een build-taak maken
Met dezelfde stappen die u eerder in dit artikel hebt gezien, voegt u een extra **Azure Powershell-buildtaak** toe aan de builddefinitie. Vul de details in zoals in de volgende afbeelding wordt weergegeven: 

![Taak maken om afbeeldingen te distribueren](./media/save-distribute-custom-images/second-build-task-powershell.png)

De parameters zijn:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Deze taak neemt alle aangepaste afbeeldingen die aanwezig zijn in de afbeeldingsfabriek en duwt ze naar alle labs gedefinieerd in het Labs.json-bestand.

## <a name="queue-the-build"></a>Wachtrij de build
Zodra de taak voor het bouwen van distributie is voltooid, moet u een nieuwe build in de wachtrij plaatsen om ervoor te zorgen dat alles werkt. Nadat de build is voltooid, worden de nieuwe aangepaste afbeeldingen weergegeven in het doellab dat is ingevoerd in het configuratiebestand Labs.json.

## <a name="next-steps"></a>Volgende stappen
In het volgende artikel in de reeks werkt u de afbeeldingsfabriek bij met een bewaarbeleid en opschoningsstappen: [Stel bewaarbeleid in en voer opschoningsscripts uit.](image-factory-set-retention-policy-cleanup.md)
