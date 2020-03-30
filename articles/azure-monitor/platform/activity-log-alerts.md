---
title: Waarschuwingen voor activiteitenlogboeken in Azure Monitor
description: Laat u hiervan op de hoogte stellen via SMS, webhook, SMS, e-mail en meer wanneer bepaalde gebeurtenissen zich voordoen in het activiteitenlogboek.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 26ecfdb33b92c91010af63ec14089dd148d6bad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669010"
---
# <a name="alerts-on-activity-log"></a>Waarschuwingen op activiteitenlogboek

## <a name="overview"></a>Overzicht

Waarschuwingen voor activiteitenlogboeken zijn waarschuwingen die worden geactiveerd wanneer een nieuwe [activiteitslogboekgebeurtenis](activity-log-schema.md) plaatsvindt die overeenkomt met de voorwaarden die in de waarschuwing zijn opgegeven. Op basis van de volgorde en het volume van de gebeurtenissen die zijn geregistreerd in [het Azure-activiteitenlogboek,](platform-logs-overview.md)wordt de waarschuwingsregel gestart. Waarschuwingsregels voor activiteitenlogboeken zijn Azure-bronnen, zodat ze kunnen worden gemaakt met behulp van een Azure Resource Manager-sjabloon. Ze kunnen ook worden gemaakt, bijgewerkt of verwijderd in de Azure-portal. In dit artikel worden de concepten achter waarschuwingen voor activiteitenlogboeken geïntroduceerd. Zie Waarschuwingen voor [activiteitenlogboeken maken en beheren](alerts-activity-log.md)voor meer informatie over het maken of gebruiken van waarschuwingsregels voor activiteitenlogboeken.

> [!NOTE]
> Waarschuwingen **kunnen niet** worden gemaakt voor gebeurtenissen in de categorie Waarschuwing van het activiteitenlogboek.

Meestal maakt u meldingen van activiteitenlogboeken om meldingen te ontvangen wanneer:

* Specifieke bewerkingen vinden plaats op resources in uw Azure-abonnement, vaak beperkt tot bepaalde resourcegroepen of resources. U bijvoorbeeld een melding ontvangen wanneer een virtuele machine in myProductionResourceGroup wordt verwijderd. U ook een melding ontvangen als er nieuwe rollen zijn toegewezen aan een gebruiker in uw abonnement.
* Er vindt een servicestatusgebeurtenis plaats. Servicestatusgebeurtenissen omvatten meldingen van incidenten en onderhoudsgebeurtenissen die van toepassing zijn op resources in uw abonnement.

Een eenvoudige analogie voor het begrijpen van voorwaarden waarop waarschuwingsregels kunnen worden gemaakt op het activiteitenlogboek, is het verkennen of filteren van gebeurtenissen via [activity log in Azure portal.](activity-log-view.md#azure-portal) In Azure Monitor - Activiteitenlogboek kan men de benodigde gebeurtenis filteren of vinden en vervolgens een waarschuwing maken met de knop **Waarschuwing voor activiteitenlogboek toevoegen.**

In beide gevallen wordt een waarschuwing voor een activiteitenlogboek alleen bewaakt voor gebeurtenissen in het abonnement waarin de waarschuwing wordt gemaakt.

U een waarschuwing voor het activiteitenlogboek configureren op basis van een eigenschap op het hoogste niveau in het JSON-object voor een gebeurtenis voor een activiteitslogboek. Zie [Categorieën in het activiteitenlogboek](activity-log-view.md#categories-in-the-activity-log)voor meer informatie . Zie [Meldingen van activiteitenlogboeken ontvangen op servicemeldingen](alerts-activity-log-service-notifications.md)voor meer informatie over gebeurtenissen in de servicestatus. 

Waarschuwingen voor activiteitenlogboeken hebben een aantal veelvoorkomende opties:

- **Categorie**: Administratief, Servicestatus, Autoscale, Beveiliging, Beleid en Aanbeveling. 
- **Bereik:** de afzonderlijke resource of set resource(s) waarvoor het waarschuwingslogboek op activiteit is gedefinieerd. De ruimte voor een waarschuwing voor het activiteitenlogboek kan op verschillende niveaus worden gedefinieerd:
    - Resourceniveau: bijvoorbeeld voor een specifieke virtuele machine
    - Resourcegroepniveau: bijvoorbeeld alle virtuele machines in een specifieke resourcegroep
    - Abonnementsniveau: bijvoorbeeld alle virtuele machines in een abonnement (of) alle bronnen in een abonnement
- **Resourcegroep**: Standaard wordt de waarschuwingsregel opgeslagen in dezelfde resourcegroep als die van het doel dat is gedefinieerd in Bereik. De gebruiker kan ook de resourcegroep definiëren waar de waarschuwingsregel moet worden opgeslagen.
- **Resourcetype:** Resourcemanager definieert naamruimte voor het doel van de waarschuwing.
- **Bedrijfsnaam:** de naam van de [Azure Resource Manager-bewerking](../../role-based-access-control/resource-provider-operations.md) die wordt gebruikt voor op rollen gebaseerd toegangsbeheer . Bewerkingen die niet zijn geregistreerd met Azure Resource Manager, kunnen niet worden gebruikt in een waarschuwingsregel voor activiteitenlogboeken.
- **Niveau:** het ernstniveau van de gebeurtenis (Verbose, Informatief, Waarschuwing, Fout of Kritiek).
- **Status:** de status van de gebeurtenis, meestal Gestart, mislukt of geslaagd.
- **Gebeurtenis geïnitieerd door**: Ook wel bekend als de "beller." Het e-mailadres of de Azure Active Directory-id van de gebruiker die de bewerking heeft uitgevoerd.

> [!NOTE]
> In een abonnement kunnen maximaal 100 waarschuwingsregels worden gemaakt voor een activiteit met bereik op één resource, alle resources in resourcegroep (of) volledig abonnementsniveau.

Wanneer een waarschuwing voor een activiteitenlogboek wordt geactiveerd, wordt een actiegroep gebruikt om acties of meldingen te genereren. Een actiegroep is een herbruikbare set meldingsontvangers, zoals e-mailadressen, webhook-URL's of sms-telefoonnummers. De ontvangers kunnen worden verwezen van meerdere waarschuwingen te centraliseren en groeperen van uw melding kanalen. Wanneer u uw waarschuwing voor het activiteitenlogboek definieert, hebt u twee opties. U kunt:

* Gebruik een bestaande actiegroep in uw waarschuwing voor het activiteitenlogboek.
* Maak een nieuwe actiegroep.

Zie [Actiegroepen maken en beheren in de Azure-portal](action-groups.md)voor meer informatie over actiegroepen.


## <a name="next-steps"></a>Volgende stappen

- Krijg een [overzicht van waarschuwingen.](alerts-overview.md)
- Meer informatie over [het maken en wijzigen van waarschuwingen voor activiteitenlogboeken](alerts-activity-log.md).
- Bekijk het [webhookschema voor de waarschuwing voor activiteitenlogboeken](activity-log-alerts-webhook.md).
- Meer informatie over [meldingen over servicestatus](service-notifications.md).
