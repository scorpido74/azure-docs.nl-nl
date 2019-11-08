---
title: Azure-toepassings aanbieding publiceren | Azure Marketplace
description: Hierin worden het proces en de stappen beschreven voor het publiceren van een Azure-toepassings aanbieding op de Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: pabutler
ms.openlocfilehash: 0005760a16f5109ca3555df5c5c5137facc84c40
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826165"
---
# <a name="publish-azure-application-offer"></a>Aanbieding voor Azure-toepassing maken publiceren

Nadat u een aanbieding hebt gemaakt door de informatie op de pagina **nieuw aanbod** op te geven, kunt u de aanbieding publiceren. Selecteer **publiceren** om het publicatie proces te starten.

In het volgende diagram ziet u de belangrijkste stappen in het publicatie proces voor een aanbieding naar ' go live '.

![Publicatie stappen bieden](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Gedetailleerde beschrijving van de publicatie stappen

In de volgende tabel vindt u een overzicht en een beschrijving van elke stap van de publicatie, en biedt een tijd schatting voor het volt ooien van de stappen.  Times schattingen in dagen worden gedefinieerd als werk dagen, waarbij weekends en feest dagen worden uitgesloten.

|  **Publicatie stap**           | **Tegelijk**    | **Beschrijving**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Vereisten valideren         | < 15 minuten    | De aanbiedings gegevens en aanbiedings instellingen worden gevalideerd.                        |
| Instellingen voor beïnvloede omzet valideren | < 15 minuten  | Toewijzing van Azure-resource gebruik voor de aanbieding is ingeschakeld.             |
| Certificering                  | < 1 dag     | De aanbieding wordt geanalyseerd door het Azure-certificerings team. De aanbieding wordt gescand op virussen, malware, veiligheids naleving en beveiligings problemen. De aanbieding wordt gecontroleerd om te zien of deze voldoet aan alle criteria van geschiktheid. Zie [vereisten](./cpp-prerequisites.md)voor meer informatie. Feedback wordt gegeven als er een probleem is gevonden. |
| Validatie van het test station          | < 2 uur   | Beschrijving Als een test station aanwezig is, valideert micro soft dat het kan worden geïmplementeerd en gerepliceerd.  |
| Registratie van verpakkingen en leads genereren | < 1 uur  | De technische activa van de aanbieding zijn verpakt voor het gebruik van klanten en de lead systemen worden geconfigureerd en geïmplementeerd. |
|  Uitgever afmelden             |  Handmatig    | De uiteindelijke controle en bevestiging van de uitgever voordat de aanbieding live gaat. De aanbieding is nu beschikbaar als preview-versie.  U kunt uw aanbieding in de geselecteerde abonnementen implementeren (in de stappen voor de aanbiedings informatie) om te controleren of deze voldoet aan uw vereisten.  Nadat u de aanbieding hebt gecontroleerd, selecteert u **Live gaan** zodat uw aanbieding naar de volgende stap kan gaan. |
| Micro soft beoordeling                | 7-14 dagen | Micro soft beoordeelt uw Azure-toepassing en stuurt u een e-mail wanneer er problemen zijn gedetecteerd.  De lengte van deze stap is afhankelijk van de complexiteit van de toepassing, de problemen die worden gedetecteerd en hoe u deze vervolgens op de gewenste manier beantwoordt.  |
| Live                           | < 1 dag | De aanbieding wordt vrijgegeven, gerepliceerd naar de opgegeven regio's en beschikbaar gesteld voor het publiek. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

U kunt het publicatie proces bewaken op het tabblad **status** van uw aanbieding in de Cloud Partner-Portal.

![Tabblad status voor een Azure-app-aanbieding](./media/offer-status-tab.png)

Nadat u klaar bent met het publicatie proces, wordt uw aanbieding vermeld in de [categorie Microsoft Azure Marketplace toepassing](https://azuremarketplace.microsoft.com/marketplace/apps/).

>[!Note]
>Opt-in voor Cloud Solution Providers (CSP)-partner kanaal is nu beschikbaar.  Raadpleeg [Cloud Solution Providers](../../cloud-solution-providers.md) voor meer informatie over het marketing gebruik van uw aanbieding via de micro soft CSP-partner kanalen.

## <a name="errors-and-review-feedback"></a>Fouten en feedback bekijken

Naast het weer geven van de publicatie status van uw aanbieding, worden in het tabblad **status** ook fout berichten en feedback weer gegeven uit de publicatie stappen waarbij een probleem wordt aangetroffen.  Als het probleem kritiek is, wordt het publiceren geannuleerd.  U moet de gerapporteerde problemen vervolgens corrigeren en de aanbieding opnieuw publiceren.  Omdat de **micro soft Review** -stap een uitgebreide beoordeling van uw aanbieding en de bijbehorende technische activa (met name de Azure Resource Manager sjabloon) vertegenwoordigt, worden problemen doorgaans gepresenteerd als pull-aanvraag (PR)-koppelingen.  Zie [afhandeling van feedback](./cpp-handling-review-feedback.md)bekijken voor meer informatie over het weer geven en reageren op deze pull.


## <a name="next-steps"></a>Volgende stappen

Als er fouten zijn opgetreden in een of meer van de publicatie stappen, moet u deze corrigeren en uw aanbieding opnieuw publiceren.  Als er kritieke problemen optreden in de stap van **micro soft controle** , moet u [de feedback van de beoordeling afhandelen](./cpp-handling-review-feedback.md) door de Azure DevOps-opslag plaats van het micro soft Review team te openen.

Zodra een Azure-app is gepubliceerd, kunt u [de bestaande aanbieding bijwerken zodat deze](./cpp-update-existing-offer.md) overeenkomt met veranderende zakelijke of technische vereisten. 
