---
title: Visual Studio gebruiken op een virtuele Azure-machine
description: Visual Studio gebruiken op een virtuele Azure-machine.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cathysull
manager: cathys
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.date: 12/04/2019
ms.author: cathys
keywords: visualstudio
ms.openlocfilehash: 2977fb6f14468429eb651e4f8a6034c060cdee2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895949"
---
# <a name="visual-studio-images-on-azure"></a>Visual Studio-afbeeldingen op Azure
Het gebruik van Visual Studio in een vooraf geconfigureerde Azure virtual machine (VM) is een snelle, eenvoudige manier om van niets naar een up-and-running ontwikkelomgeving te gaan. Systeemafbeeldingen met verschillende Visual Studio-configuraties zijn beschikbaar in de [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images%3Bmicrosoft%3Bwindows&page=1&subcategories=application-infrastructure)

Bent u nog niet bekend met Azure? [Maak een gratis Azure-account](https://azure.microsoft.com/free)aan .

> [!NOTE]
> Niet alle abonnementen komen in aanmerking voor het implementeren van Windows 10-afbeeldingen. Zie [Windows-client gebruiken in Azure voor dev/testscenario's voor](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) meer informatie

## <a name="what-configurations-and-versions-are-available"></a>Welke configuraties en versies zijn beschikbaar?
Afbeeldingen voor de meest recente grote versies, Visual Studio 2019, Visual Studio 2017 en Visual Studio 2015, zijn te vinden in de Azure Marketplace.  Voor elke uitgebrachte grote versie, zie je de oorspronkelijk "vrijgegeven op het web" (RTW) versie en de nieuwste bijgewerkte versies.  Elk van deze versies biedt de Visual Studio Enterprise en de Visual Studio Community edities.  Deze afbeeldingen worden ten minste elke maand bijgewerkt met de nieuwste Visual Studio- en Windows-updates.  Hoewel de namen van de afbeeldingen hetzelfde blijven, bevat de beschrijving van elke afbeelding de geïnstalleerde productversie en de "vanaf"-datum van de afbeelding.

| Versie van versie vrijgeven                                                                                                                                                | Edities              | Productversie   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------:|:-----------------:|
| [Visual Studio 2019: laatste (versie 16.4)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019latest?tab=Overview) | Onderneming, Gemeenschap | Versie 16.4.0    |
| [Visual Studio 2019: RTW](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019?tab=Overview)                         | Onderneming, Gemeenschap | Versie 16.0.9    |
| [Visual Studio 2017: Laatste versie (versie 15.9)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)           | Onderneming, Gemeenschap | Versie 15.9.17   |
| [Visual Studio 2017: RTW](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)                             | Onderneming, Gemeenschap | Versie 15.0.27  |
| [Visual Studio 2015: laatste (update 3)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)               | Onderneming, Gemeenschap | Versie 14.0.25431.01 |

> [!NOTE]
> In overeenstemming met het servicebeleid van Microsoft is de oorspronkelijk uitgebrachte (RTW)-versie van Visual Studio 2015 verlopen voor onderhoud. Visual Studio 2015 Update 3 is de enige overgebleven versie die wordt aangeboden voor de Visual Studio 2015-productlijn.

Zie het beleid [voor service van de visual studio](https://www.visualstudio.com/productinfo/vs-servicing-vs)voor meer informatie .

## <a name="what-features-are-installed"></a>Welke functies zijn geïnstalleerd?
Elke afbeelding bevat de aanbevolen functieset voor die Visual Studio-editie. Over het algemeen omvat de installatie:

* Alle beschikbare workloads, inclusief de aanbevolen optionele onderdelen van elke workload
* .NET 4.6.2 en .NET 4.7 SDKs, Targeting Packs en Developer Tools
* Beeld F #
* GitHub-extensie voor Visual Studio
* LINQ naar SQL-hulpprogramma's

De opdrachtregel die wordt gebruikt om Visual Studio te installeren bij het bouwen van de afbeeldingen is als volgt:

```
    vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
       add Microsoft.Net.Component.4.7.SDK ^
       add Microsoft.Net.Component.4.7.TargetingPack ^ 
       add Microsoft.Net.Component.4.6.2.SDK ^
       add Microsoft.Net.Component.4.6.2.TargetingPack ^
       add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
       add Microsoft.VisualStudio.Component.FSharp ^
       add Component.GitHub.VisualStudio ^
       add Microsoft.VisualStudio.Component.LinqToSql
```

Als de afbeeldingen geen Visual Studio-functie bevatten die u nodig hebt, geeft u feedback via het feedbackprogramma in de rechterbovenhoek van de pagina.

## <a name="what-size-vm-should-i-choose"></a>Welke maat VM moet ik kiezen?
Azure biedt een volledig scala aan virtuele machineformaten. Omdat Visual Studio een krachtige, multithread-toepassing is, wilt u een VM-formaat met ten minste twee processors en 7 GB geheugen. We raden de volgende VM-formaten aan voor de Visual Studio-afbeeldingen:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
Zie [Grootte voor virtuele Windows-machines in Azure voor](/azure/virtual-machines/windows/sizes)meer informatie over de nieuwste machineformaten.

Met Azure u uw eerste keuze opnieuw in evenwicht brengen door de grootte van de virtuele machine opnieuw te wijzigen. U een nieuwe virtuele machine inrichten met een meer geschikte grootte of het formaat van uw bestaande vm wijzigen in verschillende onderliggende hardware. Zie [Het formaat van een Windows-vm wijzigen](/azure/virtual-machines/windows/resize-vm)voor meer informatie.

## <a name="after-the-vm-is-running-whats-next"></a>Nadat de VM is uitgevoerd, wat is de volgende stap?
Visual Studio volgt het "bring your own license"-model in Azure. Net als bij een installatie op eigen hardware, is een van de eerste stappen het verlenen van licenties voor uw Visual Studio-installatie. Ga als eerste voor het ontgrendelen van Visual Studio als:
- Aanmelden met een Microsoft-account dat is gekoppeld aan een Visual Studio-abonnement 
- Ontgrendel Visual Studio met de productcode die bij uw eerste aankoop is bijgekomen

Zie [Aanmelden bij Visual Studio](/visualstudio/ide/signing-in-to-visual-studio) en Hoe visual studio te [ontgrendelen](/visualstudio/ide/how-to-unlock-visual-studio)voor meer informatie.

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Hoe bewaar ik de ontwikkel-VM voor toekomstig of teamgebruik?

Het spectrum van ontwikkelomgevingen is enorm, en er zijn echte kosten verbonden aan het uitbouwen van de meer complexe omgevingen. Ongeacht de configuratie van uw omgeving, u uw geconfigureerde VM opslaan of vastleggen als een 'basisafbeelding' voor toekomstig gebruik of voor andere leden van uw team. Wanneer u vervolgens een nieuwe virtuele machine opstart, indient u deze vanuit de basisafbeelding in plaats van de Azure Marketplace-afbeelding.

Een korte samenvatting: gebruik het systeemvoorbereidingsgereedschap (Sysprep) en sluit de uitvoerende VM af en leg vervolgens de VM *vast als* afbeelding via de gebruikersinterface in de Azure-portal. Azure slaat `.vhd` het bestand op dat de afbeelding bevat in het opslagaccount van uw keuze. De nieuwe afbeelding wordt vervolgens weergegeven als een afbeeldingsbron in de lijst met bronnen van uw abonnement.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Figuur 1) Maak een afbeelding vast via de gebruikersinterface van de Azure-portal.*</center>

Zie [Een beheerde afbeelding van een gegeneraliseerde vm maken in Azure](/azure/virtual-machines/windows/capture-image-resource)voor meer informatie.

> [!IMPORTANT]
> Vergeet niet om Sysprep te gebruiken om de VM voor te bereiden. Als u deze stap mist, kan Azure geen VM uit de afbeelding inrichten.

> [!NOTE]
> U maakt nog steeds kosten voor de opslag van de afbeeldingen, maar die incrementele kosten kunnen onbeduidend zijn in vergelijking met de overheadkosten om de VM helemaal opnieuw op te bouwen voor elk teamlid dat er een nodig heeft. Het kost bijvoorbeeld een paar dollar om een afbeelding van 127 GB te maken en op te slaan voor een maand die herbruikbaar is voor uw hele team. Deze kosten zijn echter onbeduidend in vergelijking met uren die elke werknemer investeert om een goed geconfigureerde dev-box voor individueel gebruik uit te bouwen en te valideren.

Bovendien hebben uw ontwikkelingstaken of -technologieën mogelijk meer schaal nodig, zoals verschillende ontwikkelingsconfiguraties en meerdere machineconfiguraties. U Azure DevTest Labs gebruiken om _recepten_ te maken die de constructie van uw 'gouden afbeelding' automatiseren. U DevTest Labs ook gebruiken om beleid te beheren voor de vm's waarop uw team draait. [Het gebruik van Azure DevTest Labs voor ontwikkelaars](/azure/devtest-lab/devtest-lab-developer-lab) is de beste bron voor meer informatie over DevTest Labs.

## <a name="next-steps"></a>Volgende stappen
Nu u op de hoogte bent van de vooraf geconfigureerde Visual Studio-afbeeldingen, is de volgende stap het maken van een nieuwe virtuele machine:

* [Een VM maken via de Azure-portal](quick-create-portal.md)
* [Overzicht van Windows Virtual Machines](overview.md)
