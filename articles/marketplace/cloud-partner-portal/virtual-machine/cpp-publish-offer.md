---
title: Een aanbieding voor een virtuele machine in azure Marketplace publiceren
description: Een lijst met de stappen die nodig zijn voor het publiceren van een bestaande virtuele-machine aanbieding op de Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: dsindona
ms.openlocfilehash: f66ce0c15e976898d5022bf5705a82fe0969ec07
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147658"
---
# <a name="publish-a-virtual-machine-offer"></a>Een aanbieding voor een virtuele machine publiceren

> [!IMPORTANT]
> Vanaf 13 april 2020 gaan we het beheer van uw Azure Virtual Machine-aanbiedingen naar het partner centrum verplaatsen. Na de migratie maakt en beheert u uw aanbiedingen in partner centrum. Volg de instructies in [een Azure virtual machine-aanbieding maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) om uw gemigreerde aanbiedingen te beheren.

 De laatste stap, nadat u de aanbieding in de portal hebt gedefinieerd en de gekoppelde technische activa hebt gemaakt, is het aanbieden van de aanbieding voor publicatie. In het volgende diagram ziet u de belangrijkste stappen in het publicatie proces voor ' Live go ':

![Publicatie stappen voor de aanbieding van de virtuele machine](./media/publishvm_013.png)

In de volgende tabel worden deze stappen beschreven en wordt een maximale tijd schatting voor het volt ooien van het probleem geboden:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Publicatie stap**           | **Tegelijk**    | **Beschrijving**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Vereisten valideren         | 15 minuten   | De aanbiedings gegevens en aanbiedings instellingen worden gevalideerd.                        |
| Validatie van test station (optioneel) | 2 uur | Als u ervoor hebt gekozen om test station in te scha kelen, valideert micro soft de configuratie van het test station, de implementatie ervan en de replicatie via de geselecteerde regio's. |
| Certificering                  | 3 dagen | De aanbieding wordt geanalyseerd door het Azure-certificerings team. Met deze stap worden er scans uitgevoerd op virussen, malware, veiligheid en beveiligings problemen. Feedback wordt gegeven als er een probleem is gevonden. |
| Inrichten                   | vier dagen   | De aanbieding van de virtuele machine wordt gerepliceerd in Marketplace-productie systemen.               |
| Registratie van verpakkingen en leads genereren | \<1 uur  | De technische activa van de aanbieding zijn verpakt voor het gebruik van klanten en de lead systemen worden geconfigureerd en ingesteld. |
|  Publisher-aftekening             |  -        | De uiteindelijke controle en bevestiging van de uitgever voordat de aanbieding live gaat. U kunt uw aanbieding in de geselecteerde abonnementen implementeren (in de stappen voor de aanbiedings informatie) om te controleren of deze voldoet aan uw vereisten.  |
| Inrichten                   | vier dagen | De voltooide VM-aanbieding wordt gerepliceerd in Marketplace-productie systemen en-regio's. | 
| Live                           | vier dagen | De VM-aanbieding wordt vrijgegeven, gerepliceerd naar de vereiste regio's en beschikbaar gesteld voor het publiek. |
|  |  |

Maxi maal 16 dagen toestaan dat dit proces is voltooid.  Nadat u deze publicatie stappen hebt door lopen, wordt uw VM-aanbieding weer gegeven in de [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 

