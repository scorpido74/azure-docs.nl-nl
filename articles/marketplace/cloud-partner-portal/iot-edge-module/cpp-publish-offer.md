---
title: Azure IoT Edge module-aanbieding publiceren | Azure Marketplace
description: Een IoT Edge module-aanbod publiceren.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: ef73956b1b0d3e7bed6e91cde0b92bcc3e432795
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82141844"
---
# <a name="publish-iot-edge-module-offer"></a>Aanbieding voor IoT Edge-module publiceren

>[!Important]
>Vanaf 13 april 2020 gaan we het beheer van uw IoT Edge module-aanbiedingen naar het partner centrum verplaatsen. Na de migratie maakt en beheert u uw aanbiedingen in partner centrum. Volg de instructies in [een IOT Edge-module maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) voor het beheren van uw gemigreerde aanbiedingen.

 Nadat u een nieuwe aanbieding hebt gemaakt door de informatie op de pagina **nieuw aanbod** op te geven, kunt u de aanbieding publiceren. Selecteer **publiceren** om het publicatie proces te starten.

In het volgende diagram ziet u de belangrijkste stappen in het publicatie proces voor een aanbieding naar ' go live '.

![Publicatie stappen voor de aanbieding van IoT Edge module](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Gedetailleerde beschrijving van de publicatie stappen

De volgende tabel beschrijft elke publicatie stap, met een tijd schatting (maximum) voor het volt ooien van elke stap.
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Publicatie stap**           | **Tegelijk**    | **Beschrijving**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Vereisten valideren         | 15 minuten   | De aanbiedings gegevens en aanbiedings instellingen worden gevalideerd.                        |
| Certificering                  | 2 weken | De aanbieding wordt geanalyseerd door het Azure-certificerings team. Met deze stap worden er scans uitgevoerd op virussen, malware, veiligheid en beveiligings problemen. Er wordt ook gecontroleerd of deze IoT Edge module aan alle criteria voldoet (Zie de [vereisten](./cpp-prerequisites.md) en [uw technische activa voorbereiden](./cpp-create-technical-assets.md)). Feedback wordt gegeven als er een probleem is gevonden. |
| Verpakking | 1 uur  | De technische activa van de aanbieding zijn verpakt voor het gebruik van klanten en de lead systemen worden geconfigureerd en ingesteld. |
|  Uitgever afmelden             |  -        | De uiteindelijke controle en bevestiging van de uitgever voordat de aanbieding live gaat. U kunt uw aanbieding in de geselecteerde abonnementen implementeren (in de stappen voor de aanbiedings informatie) om te controleren of deze voldoet aan uw vereisten.  Selecteer **Live gaan** zodat uw aanbieding kan worden verplaatst naar de volgende stap. |
| Verpakking                 | 1 uur | De voltooide aanbieding wordt gerepliceerd in de Marketplace-productie systemen en-regio's. | 
| Live                           | vier dagen |De aanbieding wordt vrijgegeven, gerepliceerd naar de vereiste regio's en beschikbaar gesteld voor het publiek. |

Sta Maxi maal 10 werk dagen toe om het publicatie proces te volt ooien en de aanbieding wordt vrijgegeven. Nadat u klaar bent met het publicatie proces, wordt uw IoT Edge-module aanbod weer gegeven in de [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Volgende stappen

- [Een bestaand IoT Edge module-aanbod bijwerken op Azure Marketplace](./cpp-update-existing-offer.md)
