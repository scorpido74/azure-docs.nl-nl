---
title: Onboarding-handleiding voor microsoft-beveiligingscodeanalyse
description: In dit artikel wordt beschreven dat de microsoft Security Code Analysis-extensie wordt geïnstalleerd
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/14/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 3ef111817b6351277f975b9b7e454f9a89982451
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460202"
---
# <a name="onboarding-and-installing"></a>Onboarding en installeren

Vereisten om aan de slag te gaan met Microsoft Security Code Analysis:

- Een in aanmerking komend Microsoft Unified Support-aanbod, zoals beschreven in de volgende sectie.
- Een Azure DevOps-organisatie.
- Toestemming om extensies te installeren voor de Azure DevOps-organisatie.
- Broncode die kan worden gesynchroniseerd met een Azure DevOps-pijplijn met cloudgehoste azure.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>Onboarding van de Microsoft Security Code Analysis-extensie

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>Geïnteresseerd in de aankoop van de Microsoft Security Code Analysis extensie?

Als je een van de volgende ondersteuningsaanbiedingen hebt, neem je contact op met je technische accountmanager om bestaande uren te kopen of te ruilen om toegang te krijgen tot de extensie:

- Unified Support Advanced-laag
- Unified Support-prestatielaag
- Premier Support voor ontwikkelaars
- Premier Support voor partners
- Premier Support voor Enterprise

Als u niet beschikt over een van de bovengenoemde ondersteuningsovereenkomsten, u de uitbreiding kopen bij een van onze partners.

**Volgende stappen:**

Neem contact op met een partner in de onderstaande lijst en vraag om de microsoft Security Code Analysis-extensie aan te schaffen.

>**Partners:**

- Zones - Contactgegevens:cloudsupport@zones.com
- Wortell – Contactgegevens:info@wortell.nl

### <a name="become-a-partner"></a>Word partner

Het Microsoft Security Code Analysis-team is op zoek naar partners aan boord met een Premier Support for Partners-overeenkomst. Partners zullen Azure DevOps-klanten in staat stellen zich veiliger te ontwikkelen door de extensie te verkopen aan klanten die deze willen kopen, maar geen Enterprise Support-overeenkomst met Microsoft hebben. Geïnteresseerde partners kunnen zich [hier](http://www.microsoftpartnersupport.com/msrd/opin)inschrijven.

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>De extensie Microsoft Security Code Analysis installeren

1. Nadat de extensie is gedeeld met uw Azure DevOps-organisatie, gaat u naar uw azure DevOps-organisatiepagina. Een voorbeeld-URL voor `https://dev.azure.com/contoso`een dergelijke pagina is .
1. Selecteer het pictogram van de boodschappentas in de rechterbovenhoek naast uw naam en selecteer **Extensies beheren.**
1. Selecteer **Gedeeld**.
1. Selecteer de microsoft security code analysis extensie, selecteer **installeren**.
1. Kies in de vervolgkeuzelijst de Azure DevOps-organisatie om de extensie op te installeren.
1. Selecteer **Installeren**. Nadat de installatie is voltooid, u beginnen met het gebruik van de extensie.

>[!NOTE]
> Zelfs als u geen toegang hebt voor het installeren van de extensie, gaat u verder met de installatiestappen. U tijdens het installatieproces toegang aanvragen van uw Azure DevOps-organisatiebeheerder.

Nadat u de extensie hebt geïnstalleerd, zijn de taken voor het bouwen van beveiligde ontwikkeling zichtbaar en beschikbaar om toe te voegen aan uw Azure-pijplijnen.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Specifieke buildtaken toevoegen aan uw Azure DevOps-pijplijn

1. Open uw teamproject vanuit uw Azure DevOps-organisatie.
1. Selecteer **Pijplijnen** > **builds**.
1. Selecteer de pijplijn waarin u de uitbreidingstaken wilt toevoegen:
   - Nieuwe pijplijn: Selecteer **Nieuw** en volg de stappen die zijn beschreven om een nieuwe pijplijn te maken.
   - Pijplijn bewerken: selecteer een bestaande pijplijn en selecteer **Bewerken** om te beginnen met het bewerken van de pijplijn.
1. Selecteer **+** en ga naar het deelvenster **Taken toevoegen.**
1. Zoek in de lijst of met het zoekvak de buildtaak die u wilt toevoegen. Selecteer **Toevoegen**.
1. Geef de parameters op die nodig zijn voor de taak.
1. Zet een nieuwe build in de wachtrij.
   >[!NOTE]
   >Bestands- en mappaden zijn relatief ten opzichte van de hoofdmap van uw bronopslagplaats. Als u de uitvoerbestanden en mappen als parameters opgeeft, worden deze vervangen door de algemene locatie die we op de buildagent hebben gedefinieerd.

> [!TIP]
>
> - Als u na uw build een analyse wilt uitvoeren, plaatst u de buildtaken van Microsoft Security Code Analysis na de stap Artefacten publiceren van uw build. Op die manier kan uw build resultaten voltooien en posten voordat statische analysetools worden uitgevoerd.
> - Selecteer **Continue on Error** altijd voor taken voor het bouwen van veilige ontwikkeling. Zelfs als een tool uitvalt, kunnen de anderen draaien. Er zijn geen onderlinge afhankelijkheden tussen instrumenten.
> - Buildtaken van Microsoft Security Code Analysis mislukken alleen als een hulpprogramma niet wordt uitgevoerd. Maar ze slagen zelfs als een tool problemen in de code identificeert. Met de buildtaak na analyse u configureren dat uw build mislukt wanneer een hulpprogramma problemen in de code identificeert.
> - Sommige Azure DevOps-buildtaken worden niet ondersteund wanneer ze worden uitgevoerd via een releasepijplijn. Meer specifiek ondersteunt Azure DevOps geen taken die artefacten publiceren vanuit een releasepijplijn.
> - Zie [Azure DevOps Build Variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)voor een lijst met vooraf gedefinieerde variabelen in Azure DevOps Team Build die u als parameters opgeven.

## <a name="next-steps"></a>Volgende stappen

Zie onze [configuratiehandleiding](security-code-analysis-customize.md) of [YAML-configuratiehandleiding](yaml-configuration.md)voor meer informatie over het configureren van de buildtaken.

Als u meer vragen heeft over de extensie en de aangeboden tools, kijk dan op onze [FAQ-pagina.](security-code-analysis-faq.md)
