---
title: Visual Studio gebruiken op een virtuele Azure-machine
description: Visual Studio gebruiken op een virtuele machine van Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: PhilLee-MSFT
manager: cathys
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.date: 09/24/2019
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: 1f89cc55b8b62ca3c28d8d00ee733141179781ed
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74078592"
---
# <a name="visual-studio-images-on-azure"></a>Visual Studio-installatie kopieën op Azure
Het gebruik van Visual Studio in een vooraf geconfigureerde Azure virtual machine (VM) is een snelle en eenvoudige manier om van niets naar een ontwikkel omgeving te gaan. Systeem kopieën met verschillende Visual Studio-configuraties zijn beschikbaar op de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images%3Bmicrosoft%3Bwindows&page=1&subcategories=application-infrastructure).

Nieuw bij Azure? [Maak een gratis Azure-account](https://azure.microsoft.com/free).

> [!NOTE]
> Niet alle abonnementen komen in aanmerking voor het implementeren van Windows 10-installatie kopieën. Zie [Windows-client in azure gebruiken voor ontwikkel-en test scenario's](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) voor meer informatie.

## <a name="what-configurations-and-versions-are-available"></a>Welke configuraties en versies zijn er beschikbaar?
Installatie kopieën voor de meest recente primaire versies, Visual Studio 2019, Visual Studio 2017 en Visual Studio 2015, vindt u op de Azure Marketplace.  Voor elke uitgebrachte primaire versie ziet u de oorspronkelijke versie van ' release to Web ' (RTW) en de meest recente bijgewerkte versies.  Elk van deze versies biedt Visual Studio Enter prise en de edities van de Visual Studio-community.  Deze installatie kopieën worden minstens elke maand bijgewerkt met de nieuwste Visual Studio-en Windows-updates.  Hoewel de namen van de afbeeldingen hetzelfde blijven, bevat de beschrijving van de installatie kopie de geïnstalleerde product versie en de datum van de installatie kopie.

| Release versie                                                                                                                                                | Edities              | Product versie   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------:|:-----------------:|
| [Visual Studio 2019: nieuwste (versie 16,3)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019latest?tab=Overview) | Enter prise, Community | Versie 16.3.0    |
| [Visual Studio 2019: RTW](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019?tab=Overview)                         | Enter prise, Community | Versie 16.0.8    |
| [Visual Studio 2017: nieuwste (versie 15,9)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)           | Enter prise, Community | Versie 15.9.16   |
| [Visual Studio 2017: RTW](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)                             | Enter prise, Community | Versie 15.0.25   |
| [Visual Studio 2015: nieuwste (update 3)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)               | Enter prise, Community | Versie 14.0.25431.01 |

> [!NOTE]
> In overeenstemming met het micro soft-onderhouds beleid is de oorspronkelijk vrijgegeven (RTW) versie van Visual Studio 2015 verlopen voor onderhoud. Visual Studio 2015 update 3 is de enige resterende versie die wordt aangeboden voor de product lijn van Visual Studio 2015.

Zie voor meer informatie het [Visual Studio-onderhouds beleid](https://www.visualstudio.com/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Welke functies zijn geïnstalleerd?
Elke installatie kopie bevat de aanbevolen functieset voor die Visual Studio Edition. Over het algemeen geldt de volgende installatie:

* Alle beschik bare werk belastingen, inclusief de aanbevolen optionele onderdelen van elke werk belasting
* .NET 4.6.2-en .NET 4,7-Sdk's, doel pakketten en Ontwikkelhulpprogramma's
* VBAF#
* GitHub-extensie voor Visual Studio
* LINQ to SQL-Hulpprogram Ma's

De opdracht regel die wordt gebruikt om Visual Studio te installeren tijdens het maken van de installatie kopieën is als volgt:

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

Als de installatie kopieën geen Visual Studio-functie bevatten die u nodig hebt, kunt u feedback geven via het hulp programma feedback in de rechter bovenhoek van de pagina.

## <a name="what-size-vm-should-i-choose"></a>Welke VM moet ik kiezen?
Azure biedt een breed scala aan grootten voor virtuele machines. Omdat Visual Studio een krachtige, multi thread-toepassing is, wilt u een VM-grootte hebben die ten minste twee processors en 7 GB aan geheugen bevat. De volgende VM-grootten worden aanbevolen voor de Visual Studio-installatie kopieën:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
Zie [grootten voor virtuele Windows-machines in azure](/azure/virtual-machines/windows/sizes)voor meer informatie over de meest recente computer grootten.

Met Azure kunt u uw oorspronkelijke keuze herverdelen door de grootte van de virtuele machine te wijzigen. U kunt een nieuwe VM inrichten met een meer geschikte grootte of het formaat van uw bestaande virtuele machine aanpassen aan andere onderliggende hardware. Zie [het formaat van een Windows-VM wijzigen](/azure/virtual-machines/windows/resize-vm)voor meer informatie.

## <a name="after-the-vm-is-running-whats-next"></a>Wat gebeurt er als de VM actief is?
Visual Studio volgt het model ' uw eigen licentie meenemen ' in Azure. Net als bij een installatie op eigen hardware is een van de eerste stappen een licentie voor uw Visual Studio-installatie. Als u Visual Studio wilt ontgrendelen, gaat u als volgt te:
- Meld u aan met een Microsoft-account dat is gekoppeld aan een Visual Studio-abonnement 
- Ontgrendel Visual Studio met de product code die bij uw eerste aankoop werd geleverd

Zie voor meer informatie [Aanmelden bij Visual Studio](/visualstudio/ide/signing-in-to-visual-studio) en [hoe u Visual Studio kunt ontgrendelen](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Hoe kan ik de ontwikkelings-VM opslaan voor toekomstig gebruik of voor een team?

Het spectrum van ontwikkel omgevingen is enorm en er zijn echte kosten verbonden aan het bouwen van de complexere omgevingen. Ongeacht de configuratie van uw omgeving kunt u uw geconfigureerde virtuele machine opslaan of vastleggen als basis installatie kopie voor toekomstig gebruik of voor andere leden van uw team. Wanneer u vervolgens een nieuwe virtuele machine opstart, moet u deze van de basis installatie kopie inrichten in plaats van de Azure Marketplace-installatie kopie.

Een snelle samen vatting: gebruik het hulp programma voor systeem voorbereiding (Sysprep) en sluit de actieve VM af en leg vervolgens *(afbeelding 1)* de virtuele machine vast als een installatie kopie via de gebruikers interface in de Azure Portal. Het `.vhd` bestand dat de installatie kopie bevat in het opslag account van uw keuze, wordt door Azure opgeslagen. De nieuwe installatie kopie wordt vervolgens weer gegeven als een afbeeldings resource in de lijst met resources van uw abonnement.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center> *(Afbeelding 1) Leg een installatie kopie vast via de Azure Portal-gebruikers interface.* </center>

Zie [een beheerde installatie kopie maken van een gegeneraliseerde vm in azure](/azure/virtual-machines/windows/capture-image-resource)voor meer informatie.

> [!IMPORTANT]
> Vergeet niet om Sysprep te gebruiken om de virtuele machine voor te bereiden. Als u deze stap mist, kan Azure geen virtuele machine inrichten van de installatie kopie.

> [!NOTE]
> Er worden nog steeds kosten in rekening gebracht voor de opslag van de installatie kopieën, maar deze incrementele kosten kunnen niet significant zijn vergeleken met de overhead kosten om de virtuele machine opnieuw te bouwen voor elk teamlid dat er een nodig heeft. Het kost bijvoorbeeld een paar dollar om een installatie kopie van 127 GB te maken en op te slaan voor een maand die door uw hele team kan worden gebruikt. Deze kosten zijn echter niet significant in vergelijking met uren die elke werk nemer investeren om een correct geconfigureerde dev box te maken en te valideren voor hun eigen gebruik.

Daarnaast hebben uw ontwikkel taken of technologieën mogelijk meer schaal, zoals de variëteiten van ontwikkel configuraties en configuraties met meerdere computers. U kunt Azure DevTest Labs gebruiken om _recepten_ te maken die de bouw van uw ' gouden installatie kopie ' automatiseren. U kunt ook DevTest Labs gebruiken om beleid te beheren voor de actieve Vm's van uw team. Het [gebruik van Azure DevTest Labs voor ontwikkel aars](/azure/devtest-lab/devtest-lab-developer-lab) is de beste bron voor meer informatie over DevTest Labs.

## <a name="next-steps"></a>Volgende stappen
Nu u bekend bent met de vooraf geconfigureerde Visual Studio-installatie kopieën, is de volgende stap het maken van een nieuwe VM:

* [Een virtuele machine maken via de Azure Portal](quick-create-portal.md)
* [Overzicht van Windows Virtual Machines](overview.md)
