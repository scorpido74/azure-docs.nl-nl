---
title: Azure Event Hubs - Uitzonderingen op Resource Manager | Microsoft Documenten
description: Lijst met uitzonderingen met Azure Event Hubs die zijn opgedoken door Azure Resource Manager en voorgestelde acties.
services: service-bus-messaging
documentationcenter: na
author: spelluru
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/08/2019
ms.author: spelluru
ms.openlocfilehash: e6ee1137fce97cbe5a64aa5287223f6ba09dcf47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74936085"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Azure Event Hubs - Uitzonderingen op Resource Manager
In dit artikel worden uitzonderingen weergegeven die zijn gegenereerd bij interactie met Azure Event Hubs met Azure Resource Manager - via sjablonen of directe oproepen.

> [!IMPORTANT]
> Dit document wordt regelmatig bijgewerkt. Kijk terug voor updates.

In de volgende secties worden verschillende uitzonderingen/fouten aangebracht die zijn opgedoken via Azure Resource Manager.

## <a name="error-code-conflict"></a>Foutcode: conflict

| Foutcode | Subcode voor fout | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Conflict | 40300 | Het maximum aantal resources van het type EventHub is bereikt of overschreden. Werkelijke: #, Max toegestaan: # | De naamruimte heeft zijn [quotum](event-hubs-quotas.md) bereikt voor het aantal gebeurtenishubs dat het kan bevatten. | Verwijder ongebruikte of externe gebeurtenishubs uit de naamruimte of overweeg een upgrade naar een [speciaal cluster.](event-hubs-dedicated-overview.md) |
| Conflict | geen | Config (Disaster recovery) kan niet worden verwijderd omdat replicatie aan de gang is. De koppeling mislukken of breken voordat u de DR Config probeert te verwijderen. | [GeoDR-replicatie](event-hubs-geo-dr.md) is in volle gang, zodat de config op dit moment niet kan worden verwijderd. | Als u de verwijdering van de config wilt deblokkeren, wacht u tot de replicatie is voltooid, activeert u een failover of breekt u de GeoDR-koppeling. |
| Conflict | geen | De update voor namespace is mislukt met een conflict in backend. | Een andere bewerking wordt momenteel uitgevoerd op deze naamruimte. | Wacht tot de huidige bewerking is voltooid en probeer het opnieuw. |

## <a name="error-code-429"></a>Foutcode: 429

| Foutcode | Subcode voor fout | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | geen | Inrichting voor naamruimte in de overgang | Een andere bewerking wordt momenteel uitgevoerd op deze naamruimte. | Wacht tot de huidige bewerking is voltooid en probeer het opnieuw. |
| 429 | geen | Disaster recovery operatie in uitvoering. | Een [GeoDR-bewerking](event-hubs-geo-dr.md) wordt momenteel uitgevoerd op deze naamruimte of koppeling. | Wacht tot de huidige GeoDR-bewerking is voltooid en probeer het opnieuw. |

## <a name="error-code-badrequest"></a>Foutcode: BadRequest

| Foutcode | Subcode voor fout | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| BadRequest | 40000 | PartitionCount kan niet worden gewijzigd voor een gebeurtenishub. | De basis- of standaardlaag azure-gebeurtenishubs biedt geen ondersteuning voor het wijzigen van partities. | Maak een nieuwe gebeurtenishub met het gewenste aantal partities in de naamruimte van uw basis- of standaardlaag. Partitiescale-out wordt ondersteund voor [specifieke clusters.](event-hubs-dedicated-overview.md) |
| BadRequest | 40000 | De waarde '#' voor MessageRetentionInDays is niet geldig voor de basislaag. de waarde mag niet hoger zijn dan '1' dag(en). | Naamruimten voor gebeurtenishubs op basisniveau ondersteunt alleen het behoud van berichten van maximaal 1 dag. | Als er meer dan één dag berichtbehoud gewenst is, [maakt u een standaard naamruimte voor gebeurtenishubs](event-hubs-create.md). | 
| BadRequest | geen | De opgegeven naam is niet beschikbaar. | Namen van naamruimte moeten uniek zijn en de opgegeven naam is al gemaakt. | Als u de eigenaar bent van de bestaande naamruimte met de opgegeven naam, u deze verwijderen, wat tot gegevensverlies zal leiden. Probeer het vervolgens opnieuw met dezelfde naam. Als de naamruimte niet veilig is om te verwijderen (of als u niet de eigenaar bent), kiest u een andere naamruimtenaam. |
| BadRequest | geen | Het opgegeven abonnement heeft het quotum van naamruimten bereikt. | Uw abonnement heeft het [quotum](event-hubs-quotas.md) bereikt voor het aantal naamruimten dat het kan bevatten. | Overweeg om ongebruikte naamruimten in dit abonnement te verwijderen, een ander abonnement te maken of een upgrade naar een speciaal cluster te [maken.](event-hubs-dedicated-overview.md) |
| BadRequest | geen | Kan een secundaire naamruimte niet bijwerken | De naamruimte kan niet worden bijgewerkt omdat het de secundaire naamruimte in een [GeoDR-koppeling](event-hubs-geo-dr.md)is. | Breng indien nodig de wijziging aan in de primaire naamruimte in deze koppeling. Anders breek de GeoDR-koppeling om de wijziging aan te brengen. |
| BadRequest | geen | Kan automatisch opblazen niet instellen in basissKU | Automatisch opblazen kan niet worden ingeschakeld op naamruimten van gebeurtenishubs op basisniveau. | Als u Automatisch opblazen op een naamruimte wilt [inschakelen,](event-hubs-auto-inflate.md) controleert u of deze van de standaardlaag is. |
| BadRequest | geen | Er is niet genoeg capaciteit om de naamruimte te maken. Neem contact op met de beheerder van gebeurtenishubs. | Het geselecteerde gebied heeft een capaciteit en er kunnen niet meer naamruimten worden gemaakt. | Selecteer een ander gebied om uw naamruimte te huisvesten. |
| BadRequest | geen | De bewerking kan niet worden uitgevoerd op entiteitstype 'ConsumerGroup' omdat de naamruimte 'naamruimtenaam' de laag 'Basis' gebruikt.  | Naamruimten voor gebeurtenishubs op basisniveau hebben een [quota]((gebeurtenishubs-quota.md#gebeurtenishubs-basisbasis-en-standaard---quota-en-limieten) van één consumentengroep (de standaardinstelling). Het maken van meer consumentengroepen wordt niet ondersteund. | Ga verder met het gebruik van de standaardgroep ($Default) of als er meer nodig is, overweeg dan in plaats daarvan een naamruimte voor standaardgebeurtenishubs te gebruiken. | 
| BadRequest | geen | De naamruimte 'naamruimtenaam' bestaat niet. | De meegeleverde naamruimte kon niet worden gevonden. | Controleer nogmaals of de naamruimtenaam correct is en in uw abonnement te vinden is. Als dit niet het geval is, [maakt u een naamruimte voor gebeurtenishubs](event-hubs-create.md). | 
| BadRequest | geen | De eigenschap locatie van de resource komt niet overeen met de bevattende naamruimte. | Het maken van een gebeurtenishub in een specifieke regio is mislukt omdat deze niet overeenkomt met het gebied van de naamruimte. | Probeer de gebeurtenishub te maken in dezelfde regio als de naamruimte. | 

## <a name="error-code-internal-server-error"></a>Foutcode: interne serverfout

| Foutcode | Subcode voor fout | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Interne serverfout | geen | Interne serverfout. | De service Gebeurtenishubs heeft een interne fout opgelopen. | Probeer de falende bewerking opnieuw. Als de bewerking blijft mislukken, neemt u contact op met de ondersteuning. |