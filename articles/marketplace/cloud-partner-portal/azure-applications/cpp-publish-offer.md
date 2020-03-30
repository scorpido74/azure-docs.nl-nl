---
title: Azure-toepassingsaanbieding publiceren | Azure Marketplace
description: Beschrijft het proces en de stappen voor het publiceren van een Azure-toepassingsaanbieding op de Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: dsindona
ms.openlocfilehash: 9f89e31c2d17ef74971d2057ba58e9572e92184c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280096"
---
# <a name="publish-azure-application-offer"></a>Aanbieding voor Azure-toepassing maken publiceren

Nadat u een aanbieding hebt gemaakt door de informatie op de pagina **Nieuwe aanbieding te** verstrekken, u de aanbieding publiceren. Selecteer **Publiceren** om het publicatieproces te starten.

In het volgende diagram ziet u de belangrijkste stappen in het publicatieproces voor een aanbieding om "live te gaan".

![Publicatiestappen aanbieden](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Gedetailleerde beschrijving van publicatiestappen

In de volgende tabel worden elke publicatiestap weergegeven en beschreven en wordt een tijdsschatting weergegeven om elke stap te voltooien.  Tijdenschattingen in "dagen" worden gedefinieerd als werkdagen, die weekends en feestdagen uitsluiten.

|  **Publicatiestap**           | **Tijd**    | **Beschrijving**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Vereisten valideren         | < 15 min    | Aanbiedingsinformatie en aanbiedingsinstellingen worden gevalideerd.                        |
| Invloed op de inkomsteninstellingen valideren | < 15 min  | De toeschrijving van Azure-bronnen voor de aanbieding wordt gecontroleerd.             |
| Certificering                  | < 1 dag     | De aanbieding wordt geanalyseerd door het Azure Certification Team. De aanbieding wordt gescand op virussen, malware, naleving van de veiligheid en beveiligingsproblemen. De aanbieding wordt gecontroleerd om te zien of deze voldoet aan alle geschiktheidscriteria. Zie [voorwaarden voor](./cpp-prerequisites.md)meer informatie. Er wordt feedback gegeven als er een probleem wordt gevonden. |
| Validatie van teststation          | < 2 uur   | (Optioneel) Als er een teststation aanwezig is, valideert Microsoft dat het kan worden geïmplementeerd en gerepliceerd.  |
| Registratie van verpakkingen en leadgeneratie | < 1 uur  | De technische assets van de aanbieding zijn verpakt voor gebruik door de klant en de leadsystemen worden geconfigureerd en geïmplementeerd. |
|  Afmelden voor uitgevers             |  Handmatig    | Definitieve beoordeling en bevestiging van uitgevers voordat de aanbieding live gaat. De aanbieding is nu beschikbaar voor preview.  U uw aanbieding implementeren in de geselecteerde abonnementen (in de stappen voor aanbiedingsinformatie) om te controleren of deze aan al uw vereisten voldoet.  Nadat u de aanbieding hebt geverifieerd, selecteert u **Live gaan** zodat uw aanbieding naar de volgende stap kan gaan. |
| Microsoft-beoordeling                | 7 - 14 dagen | Microsoft controleert uw Azure-toepassing en e-mailt u als er problemen worden ontdekt.  De duur van deze stap is afhankelijk van de complexiteit van de toepassing, de problemen die aan het licht zijn gekomen en hoe snel u erop reageert.  |
| Live                           | < 1 dag | Aanbieding wordt vrijgegeven, gerepliceerd naar de opgegeven regio's en beschikbaar gesteld aan het publiek. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

U het publicatieproces controleren op het tabblad **Status** voor uw aanbieding in de Cloud Partner Portal.

![Tabblad Status voor een Azure-app-aanbieding](./media/offer-status-tab.png)

Nadat u het publicatieproces hebt voltooid, wordt uw aanbieding weergegeven in de [categorie Microsoft Azure Marketplace-toepassings.](https://azuremarketplace.microsoft.com/marketplace/apps/)

>[!Note]
>Cloud Solution Providers (CSP) partnerkanaal opt-in is nu beschikbaar.  Raadpleeg [Cloud Solution Providers](../../cloud-solution-providers.md) voor meer informatie over het op de markt brengen van uw aanbieding via de Microsoft CSP-partnerkanalen.

## <a name="errors-and-review-feedback"></a>Fouten en feedback bekijken

Het tabblad **Status** geeft niet alleen de publicatiestatus van uw aanbieding weer, maar geeft ook foutmeldingen en feedback weer van alle publicatiestappen waar een probleem is aangetroffen.  Als het probleem kritiek is, wordt publiceren geannuleerd.  U moet vervolgens het gerapporteerde probleem(en) corrigeren en de aanbieding opnieuw publiceren.  Omdat de **microsoft-beoordelingsstap** een uitgebreide beoordeling van uw aanbieding en de bijbehorende technische elementen (met name de Azure Resource Manager-sjabloon) vertegenwoordigt, worden problemen meestal gepresenteerd als koppelingen naar pull request (PR).  Een uitleg over het bekijken en reageren op deze PR's, zie [Feedback over beoordeling afhandelen](./cpp-handling-review-feedback.md).


## <a name="next-steps"></a>Volgende stappen

Als u fouten hebt ondervonden in een of meer van de publicatiestappen, moet u deze corrigeren en uw aanbieding opnieuw publiceren.  Als er kritieke problemen optreden in de **microsoft-beoordelingsstap,** moet u [de koppeling met de beoordeling verwerken](./cpp-handling-review-feedback.md) door toegang te krijgen tot de Azure DevOps-opslagplaats van het Microsoft-beoordelingsteam.

Zodra een Azure-app is gepubliceerd, u [de bestaande aanbieding bijwerken](./cpp-update-existing-offer.md) om rekening te houden met veranderende bedrijfs- of technische vereisten. 
