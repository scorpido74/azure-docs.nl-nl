---
title: Afbeeldingen opslaan en distribueren in Azure DevTest Labs | Microsoft Docs
description: In dit artikel vindt u de stappen voor het opslaan van aangepaste installatie kopieën van de al gemaakte virtuele machines (Vm's) in Azure DevTest Labs.
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
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759428"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Aangepaste installatiekopieën opslaan en distribueren naar meerdere labs
In dit artikel vindt u de stappen voor het opslaan van aangepaste installatie kopieën van de al gemaakte virtuele machines (Vm's). Ook wordt beschreven hoe u deze aangepaste installatie kopieën distribueert naar andere DevTest Labs in de organisatie.

## <a name="prerequisites"></a>Vereisten
De volgende items moeten al aanwezig zijn:

- Een Lab voor de installatie kopie-Factory in Azure DevTest Labs.
- Een Azure DevOps-project dat wordt gebruikt om de installatie kopie-Factory te automatiseren.
- De locatie van de bron code met de scripts en configuratie (in dit voor beeld in hetzelfde DevOps-project dat in de vorige stap wordt vermeld).
- Maak een definitie om de Azure Power shell-taken te organiseren.

Voer, indien nodig, de stappen in de [fabriek een installatie kopie uitvoeren vanuit Azure DevOps](image-factory-set-up-devops-lab.md) om deze items te maken of in te stellen. 

## <a name="save-vms-as-generalized-vhds"></a>Vm's opslaan als gegeneraliseerde Vhd's
Sla de bestaande Vm's op als gegeneraliseerde Vhd's.  Er is een Power shell-voorbeeld script voor het opslaan van de bestaande Vm's als gegeneraliseerde Vhd's. Als u deze wilt gebruiken, moet u eerst een andere **Azure Power shell** -taak toevoegen aan de build-definitie, zoals wordt weer gegeven in de volgende afbeelding:

![Azure PowerShell stap toevoegen](./media/save-distribute-custom-images/powershell-step.png)

Zodra u de nieuwe taak in de lijst hebt, selecteert u het item, zodat we alle details kunnen invullen, zoals wordt weer gegeven in de volgende afbeelding: 

![Power shell-instellingen](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Gegeneraliseerde versus gespecialiseerde aangepaste installatie kopieën
In de [Azure Portal](https://portal.azure.com), wanneer u een aangepaste installatie kopie van een virtuele machine maakt, kunt u ervoor kiezen een gegeneraliseerde of gespecialiseerde aangepaste installatie kopie te maken.

- **Speciale aangepaste installatie kopie:** Sysprep/unprovisioning is niet uitgevoerd op de computer. Dit betekent dat de installatie kopie een exacte kopie van de besturingssysteem schijf op de bestaande virtuele machine (een moment opname) is.  Dezelfde bestanden, toepassingen, gebruikers accounts, computer naam, enzovoort, zijn allemaal aanwezig wanneer we een nieuwe machine maken op basis van deze aangepaste installatie kopie.
- **Gegeneraliseerde aangepaste installatie kopie:** Sysprep/unprovisioning is uitgevoerd op de computer.  Wanneer dit proces wordt uitgevoerd, worden gebruikers accounts verwijderd, wordt de computer naam verwijderd, worden de register onderdelen van de gebruiker, enz., met als doel de installatie kopie te generaliseren zodat deze kan worden aangepast bij het maken van een andere virtuele machine.  Wanneer u een virtuele machine generaliseert (door Sysprep uit te voeren), vernietigt het proces de huidige virtuele machine – deze is niet langer functioneel.

Met het script voor het uitlijnen van aangepaste installatie kopieën in de installatie kopie fabriek worden Vhd's opgeslagen voor virtuele machines die zijn gemaakt in de vorige stap (geïdentificeerd op basis van een label op de resource in Azure).

## <a name="update-configuration-for-distributing-images"></a>Configuratie bijwerken voor het distribueren van installatie kopieën
De volgende stap in het proces is het pushen van de aangepaste installatie kopieën van het image Factory-lab naar alle andere laboratoria die deze nodig hebben. Het kern gedeelte van dit proces is het configuratie bestand **Labs. json** . U kunt dit bestand vinden in de **configuratiemap** die is opgenomen in de installatie kopie-Factory.

Er zijn twee belang rijke dingen die worden vermeld in het configuratie bestand Labs. json:

- Unieke identificatie van een specifiek bestemmings Lab met de abonnements-ID en de naam van het lab.
- De specifieke set installatie kopieën die naar het lab moeten worden gepusht als relatieve paden naar de Configuration root. U kunt ook een volledige map opgeven (om alle installatie kopieën in de map op te halen).

Hier volgt een voor beeld van een Labs. JSON-bestand met twee Labs. In dit geval distribueert u installatie kopieën naar twee verschillende Labs.

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
Aan de hand van de stappen die u eerder in dit artikel hebt gezien, voegt u een extra **Azure Power shell** -taak voor het maken van de definitie toe. Vul de details in, zoals wordt weer gegeven in de volgende afbeelding: 

![Taak voor het distribueren van installatie kopieën maken](./media/save-distribute-custom-images/second-build-task-powershell.png)

De para meters zijn: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Met deze taak worden aangepaste installatie kopieën in de installatie kopie van de fabriek verwerkt en worden deze gepusht naar de Labs die in het bestand Labs. json is gedefinieerd.

## <a name="queue-the-build"></a>De build in de wachtrij plaatsen
Zodra de taak voor het maken van de distributie is voltooid, moet u een nieuwe build in de wachtrij plaatsen om er zeker van te zijn dat alles werkt. Nadat de build is voltooid, worden de nieuwe aangepaste installatie kopieën weer gegeven in het bestemmings Lab dat is ingevoerd in het configuratie bestand Labs. json.

## <a name="next-steps"></a>Volgende stappen
In het volgende artikel in de serie werkt u de installatie kopie-Factory bij met een Bewaar beleid en opschoon stappen: [Bewaar beleid instellen en scripts voor opschonen uitvoeren](image-factory-set-retention-policy-cleanup.md).
