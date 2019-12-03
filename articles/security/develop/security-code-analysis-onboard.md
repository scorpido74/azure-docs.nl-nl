---
title: Hand leiding voor analyse van micro soft-beveiligings code
description: In dit artikel wordt beschreven hoe u de micro soft security code Analysis extension installeert
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 77be6d8518774a1e2656e23024490dd9c1431865
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707257"
---
# <a name="onboarding-and-installing"></a>Onboarding en installatie

Vereisten om aan de slag te gaan met de analyse van micro soft-beveiligings code:

- Een in aanmerking komend Microsoft Unified Support aanbieding, zoals beschreven in de volgende sectie.
- Een Azure DevOps-organisatie.
- Machtiging voor het installeren van extensies voor de Azure DevOps-organisatie.
- Bron code die kan worden gesynchroniseerd met een door de Cloud gehoste Azure DevOps-pijp lijn.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>De uitbrei ding van de analyse van micro soft-beveiligings code onboarden

- Als u een van de volgende ondersteunings aanbiedingen hebt, neemt u contact op met uw technische account beheerder om bestaande uren te kopen of te wisselen om toegang te krijgen tot de uitbrei ding:
   - Geavanceerde laag voor ondersteuning van Unified support
   - Prestatie niveau Unified support
   - Premier Support voor ontwikkel aars
   - Premier Support voor partners
   - Premier Support voor bedrijven
- Als u een van de volgende ondersteunings services of geen micro soft-ondersteunings abonnement hebt, moet u een upgrade uitvoeren naar een in aanmerking komende ondersteunings aanbieding:
   - Azure-ondersteuning voor partners
   - Azure Basic-ondersteuning
   - Azure Developer Support
   - Azure Standard Support
   - Azure Professional Direct
   - Kern niveau Unified support
- Ga naar de [Start pagina van de ondersteunings services](https://www.microsoft.com/enterprise/services/support)als u een in aanmerking komend ondersteunings aanbieding wilt kopen.
- Wanneer er een ondersteunings contract is opgesteld, neemt u contact op met uw technische account beheerder om u op weg te helpen en om u te helpen alle vereiste gegevens te verzamelen.

>[!NOTE]
> Als u een partner bent die is geregistreerd in het micro soft partners Network, komt u in aanmerking voor het aanschaffen van Premier Support voor partners. Anders moet u een van de hiervoor genoemde ondersteunings aanbiedingen aanschaffen.

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>De uitbrei ding voor de analyse van micro soft-beveiligings code installeren

1. Nadat de extensie is gedeeld met uw Azure DevOps-organisatie, gaat u naar de pagina Azure DevOps-organisatie. Een voor beeld-URL voor een dergelijke pagina is `https://dev.azure.com/contoso`.
1. Selecteer het pictogram winkel Bag in de rechter bovenhoek naast uw naam en selecteer vervolgens **extensies beheren**.
1. Selecteer **gedeeld**.
1. Selecteer de uitbrei ding voor de analyse van micro soft-beveiligings code, selecteer **installeren**.
1. Kies in de vervolg keuzelijst de Azure DevOps-organisatie waarop u de extensie wilt installeren.
1. Selecteer **Installeren**. Nadat de installatie is voltooid, kunt u beginnen met het gebruik van de extensie.

>[!NOTE]
> Ook als u geen toegang hebt voor het installeren van de uitbrei ding, gaat u door met de installatie stappen. U kunt tijdens het installatie proces toegang aanvragen bij de organisatie beheerder van Azure DevOps.

Nadat u de uitbrei ding hebt geïnstalleerd, zijn de taken voor het ontwikkelen van beveiligde ontwikkel aars zichtbaar en beschikbaar voor toevoegen aan uw Azure-pijp lijnen.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Specifieke build-taken toevoegen aan uw Azure DevOps-pijp lijn

1. Open uw team project uit uw Azure DevOps-organisatie.
1. Selecteer **pijp lijnen** > **builds**.
1. Selecteer de pijp lijn waaraan u de extensie taken wilt toevoegen:
   - Nieuwe pijp lijn: Selecteer **Nieuw** en volg de stappen die worden beschreven om een nieuwe pijp lijn te maken.
   - Pijp lijn bewerken: Selecteer een bestaande pijp lijn en selecteer **bewerken** om te beginnen met het bewerken van de pijp lijn.
1. Selecteer **+** en ga naar het deel venster **taken toevoegen** .
1. Zoek in de lijst of via het zoekvak de build-taak die u wilt toevoegen. Selecteer **Toevoegen**.
1. Geef de para meters op die nodig zijn voor de taak.
1. Een nieuwe build in de wachtrij plaatsen.
   >[!NOTE]
   >Paden voor bestanden en mappen zijn relatief ten opzichte van de hoofdmap van de bron opslagplaats. Als u de uitvoer bestanden en-mappen opgeeft als para meters, worden deze vervangen door de algemene locatie die we hebben gedefinieerd in de build-agent.

> [!TIP]
>
> - Als u na uw Build een analyse wilt uitvoeren, plaatst u de taken voor het analyseren van micro soft-beveiligings code na de stap build bouwen artefacten van uw build. Op die manier kan uw build de resultaten volt ooien en plaatsen voordat statische analyse Programma's worden uitgevoerd.
> - Selecteer altijd **door gaan bij fout** voor het maken van beveiligde ontwikkel taken. Zelfs als één hulp programma mislukt, kunnen de andere functies worden uitgevoerd. Er zijn geen onderlinge afhankelijkheden tussen hulpprogram ma's.
> - Micro soft security code Analysis-taken kunnen alleen worden uitgevoerd als het uitvoeren van een hulp programma is mislukt. Maar ze slagen zelfs als een hulp programma problemen in de code identificeert. Met behulp van de taak voor het maken van een post-analyse kunt u instellen dat uw build mislukt wanneer een hulp programma problemen in de code identificeert.
> - Sommige Azure DevOps-build-taken worden niet ondersteund wanneer ze worden uitgevoerd via een release pijplijn. Meer specifiek: Azure DevOps biedt geen ondersteuning voor taken die artefacten publiceren vanuit een release pijplijn.
> - Zie [Azure DevOps build Varia bles](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)(Engelstalig) voor een lijst met vooraf gedefinieerde variabelen in azure DevOps team build die u als para meters kunt opgeven.

## <a name="next-steps"></a>Volgende stappen

Zie onze [configuratie handleiding](security-code-analysis-customize.md)voor meer informatie over het configureren van de build-taken.

Bekijk onze [pagina met veelgestelde vragen](security-code-analysis-faq.md)als u meer vragen hebt over de uitbrei ding en de hulpprogram ma's die worden aangeboden.
