---
title: Waarschuwingen voor activiteiten logboek in Azure Monitor
description: Ontvang een melding via SMS, webhook, SMS, e-mail en meer wanneer bepaalde gebeurtenissen in het activiteiten logboek optreden.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: b24f24edf2a3a0df8cb8ef9687f205a4a8868537
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71675215"
---
# <a name="alerts-on-activity-log"></a>Waarschuwingen in het activiteiten logboek 

## <a name="overview"></a>Overzicht
Waarschuwingen voor activiteiten logboeken zijn waarschuwingen die worden geactiveerd wanneer een nieuwe [activiteiten logboek gebeurtenis](activity-log-schema.md) optreedt die overeenkomt met de voor waarden die zijn opgegeven in de waarschuwing. Op basis van de volg orde en het volume van de gebeurtenissen die zijn vastgelegd in het [activiteiten logboek van Azure](activity-logs-overview.md), wordt de waarschuwings regel geactiveerd. Waarschuwings regels voor activiteiten logboeken zijn Azure-resources, zodat ze kunnen worden gemaakt met behulp van een Azure Resource Manager sjabloon. Ze kunnen ook worden gemaakt, bijgewerkt of verwijderd in de Azure Portal. In dit artikel worden de concepten geïntroduceerd van waarschuwingen voor activiteiten Logboeken. Zie [waarschuwingen voor activiteiten logboek maken en beheren](alerts-activity-log.md)voor meer informatie over het maken of gebruiken van waarschuwings regels voor activiteiten Logboeken.

> [!NOTE]
> Er **kunnen geen** waarschuwingen worden gemaakt voor gebeurtenissen in de categorie waarschuwing van het activiteiten logboek.

Normaal gesp roken maakt u waarschuwingen voor activiteiten Logboeken om meldingen te ontvangen wanneer:

* Specifieke bewerkingen worden uitgevoerd op resources in uw Azure-abonnement, wat vaak van toepassing is op bepaalde resource groepen of resources. Stel dat u een melding wilt ontvangen wanneer een virtuele machine in myProductionResourceGroup wordt verwijderd. Het kan ook zijn dat u een melding wilt ontvangen als nieuwe rollen zijn toegewezen aan een gebruiker in uw abonnement.
* Er treedt een service Health-gebeurtenis op. Service Health-gebeurtenissen zijn onder andere melding van incidenten en onderhouds gebeurtenissen die van toepassing zijn op resources in uw abonnement.

Een eenvoudige analoge waarde voor de voor waarden waarop waarschuwings regels kunnen worden gemaakt in het activiteiten logboek, is om gebeurtenissen te verkennen of filteren via [activiteiten logboek in azure Portal](activity-log-view.md#azure-portal). In Azure Monitor-activiteiten logboek kan één een vereiste gebeurtenis filteren of vinden en vervolgens een waarschuwing maken met behulp van de knop waarschuwing voor het **activiteiten logboek toevoegen** .

In beide gevallen controleert een activiteiten logboek waarschuwing alleen op gebeurtenissen in het abonnement waarin de waarschuwing is gemaakt.

U kunt een waarschuwing voor een activiteiten logboek configureren op basis van een eigenschap op het hoogste niveau in het JSON-object voor een activiteiten logboek gebeurtenis. Zie [overzicht van het Azure-activiteiten logboek](./activity-logs-overview.md#categories-in-the-activity-log)voor meer informatie. Zie [waarschuwingen voor activiteiten logboeken ontvangen in service meldingen](./alerts-activity-log-service-notifications.md)voor meer informatie over service status gebeurtenissen. 

Waarschuwingen voor activiteiten logboeken bevatten enkele algemene opties:

- **Categorie**: Beheer, Service Health, automatisch schalen, beveiliging, beleid en aanbeveling. 
- **Bereik**: De afzonderlijke resource of set met resources waarvoor de waarschuwing voor het activiteiten logboek is gedefinieerd. Het bereik van een waarschuwing voor een activiteiten logboek kan op verschillende niveaus worden gedefinieerd:
    - Resource niveau: Voor een specifieke virtuele machine bijvoorbeeld
    - Niveau van de resource groep: Bijvoorbeeld alle virtuele machines in een specifieke resource groep
    - Abonnements niveau: Bijvoorbeeld alle virtuele machines in een abonnement (of) alle resources in een abonnement
- **Resourcegroep**: De waarschuwings regel wordt standaard opgeslagen in dezelfde resource groep als die van het doel dat in bereik is gedefinieerd. De gebruiker kan ook de resource groep definiëren waarin de waarschuwings regel moet worden opgeslagen.
- **Resourcetype**: Resource Manager heeft naam ruimte gedefinieerd voor het doel van de waarschuwing.
- **Naam van bewerking**: De naam van de [Azure Resource Manager bewerking](../../role-based-access-control/resource-provider-operations.md) die wordt gebruikt voor op rollen gebaseerd Access Control. Bewerkingen die niet zijn geregistreerd met Azure Resource Manager kunnen niet worden gebruikt in een waarschuwings regel voor het activiteiten logboek.
- **Niveau**: Het Ernst niveau van de gebeurtenis (uitgebreid, informatief, waarschuwing, fout of kritiek).
- **Status**: De status van de gebeurtenis, meestal gestart, mislukt of geslaagd.
- **Gebeurtenis gestart door**: Ook wel bekend als ' aanroeper '. Het e-mail adres of de Azure Active Directory id van de gebruiker die de bewerking heeft uitgevoerd.

> [!NOTE]
> In een abonnement kunnen Maxi maal 100 waarschuwings regels worden gemaakt voor een activiteit van het bereik: een enkele resource, alle resources in de resource groep (of) het volledige abonnements niveau.

Wanneer een waarschuwing voor een activiteiten logboek wordt geactiveerd, wordt een actie groep gebruikt voor het genereren van acties of meldingen. Een actie groep is een herbruikbare set meldings ontvangers, zoals e-mail adressen, webhook-Url's of SMS-telefoon nummers. Er kan vanuit meerdere waarschuwingen naar de ontvangers worden verwezen om uw meldings kanalen te centraliseren en te groeperen. Wanneer u de waarschuwing voor uw activiteiten logboek definieert, hebt u twee opties. U kunt:

* Een bestaande actie groep gebruiken in uw activiteiten logboek waarschuwing.
* Maak een nieuwe actie groep.

Zie voor meer informatie over actie groepen [actie groepen maken en beheren in de Azure Portal](action-groups.md).


## <a name="next-steps"></a>Volgende stappen

- Een [overzicht van waarschuwingen](alerts-overview.md)weer geven.
- Meer informatie over het [maken en wijzigen van waarschuwingen voor activiteiten logboeken](alerts-activity-log.md).
- Controleer het webhook-schema van de waarschuwing voor het [activiteiten logboek](activity-log-alerts-webhook.md).
- Meer informatie over [service status meldingen](service-notifications.md).