---
title: Een virtuele machineaanbieding publiceren in de Azure Marketplace
description: Bevat de stappen die nodig zijn om een bestaande virtuele machineaanbieding op de Azure Marketplace te publiceren.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: dsindona
ms.openlocfilehash: bb875a5c4ab1b898b64fe22140414e5d5b7830b8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273780"
---
# <a name="publish-a-virtual-machine-offer"></a>Een virtuele machineaanbieding publiceren

> [!IMPORTANT]
> Vanaf 13 april 2020 beginnen we met het verplaatsen van het beheer van uw Azure Virtual Machine-aanbiedingen naar partnercentrum. Na de migratie maak en beheer je je aanbiedingen in partnercentrum. Volg de instructies in [Een Azure Virtual Machine-aanbieding maken](https://aka.ms/CreateAzureVMoffer) om uw gemigreerde aanbiedingen te beheren.

 De laatste stap, nadat u de aanbieding in de portal hebt gedefinieerd en de bijbehorende technische activa hebt gemaakt, is het indienen van de aanbieding voor publicatie. In het volgende diagram worden de belangrijkste stappen in het publicatieproces weergegeven om "live te gaan":

![Publicatiestappen voor virtuele machineaanbieding](./media/publishvm_013.png)

In de volgende tabel worden deze stappen beschreven en wordt een maximale tijdschatting gegeven voor de voltooiing ervan:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Publicatiestap**           | **Tijd**    | **Beschrijving**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Vereisten valideren         | 15 min   | Aanbiedingsinformatie en aanbiedingsinstellingen worden gevalideerd.                        |
| Validatie van teststations (optioneel) | 2 uur | Als u hebt geselecteerd om Testdrive in te schakelen, valideert Microsoft de configuratie, de implementatie ervan en de replicatie ervan via de geselecteerde regio's. |
| Certificering                  | 3 dagen | De aanbieding wordt geanalyseerd door het Azure Certification Team. Met deze stap worden scans uitgevoerd op virussen, malware, naleving van de veiligheid en beveiligingsproblemen. Er wordt feedback gegeven als er een probleem wordt gevonden. |
| Inrichten                   | 4 dagen   | VM-aanbod wordt gerepliceerd in marktproductiesystemen.               |
| Registratie van verpakkingen en leadgeneratie | \<1 uur  | De technische assets van de aanbieding zijn verpakt voor gebruik door de klant en de leadsystemen zijn geconfigureerd en ingesteld. |
|  Afmelding van uitgever             |  -        | Definitieve beoordeling en bevestiging van uitgevers voordat de aanbieding live gaat. U uw aanbieding implementeren in de geselecteerde abonnementen (in de stappen voor aanbiedingsinformatie) om te controleren of deze aan al uw vereisten voldoet.  |
| Inrichten                   | 4 dagen | Het afgeronde VM-aanbod wordt gerepliceerd in marktproductiesystemen en -regio's. | 
| Live                           | 4 dagen | VM-aanbieding wordt vrijgegeven, gerepliceerd naar de vereiste regio's en beschikbaar gesteld aan het publiek. |
|  |  |

Geef maximaal 16 dagen de tijd om dit proces te voltooien.  Nadat u deze publicatiestappen hebt doorlopen, wordt uw VM-aanbieding weergegeven in de [Microsoft Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/) 

