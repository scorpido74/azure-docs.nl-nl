---
title: Activiteit van serviceprovider bekijken
description: Klanten kunnen geregistreerde activiteiten bekijken om acties te zien die door serviceproviders worden uitgevoerd via azure delegated resource management.
ms.date: 01/15/2020
ms.topic: conceptual
ms.openlocfilehash: a923a57ecc94ac15af207c2b8dc8998708b708d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649633"
---
# <a name="view-service-provider-activity"></a>Activiteit van serviceprovider bekijken

Klanten die abonnementen voor Azure-gedelegeerd bronbeheer hebben gedelegeerd, kunnen [logboekgegevens van Azure Activity bekijken](../../azure-monitor/platform/platform-logs-overview.md) om alle ondernomen acties te bekijken. Dit geeft klanten volledig inzicht in bewerkingen die serviceproviders uitvoeren via Azure delegated resource management, samen met bewerkingen die worden uitgevoerd door gebruikers binnen de eigen Azure Active Directory (Azure AD)-tenant van de klant.

> [!TIP]
> We bieden ook een ingebouwde beleidsdefinitie voor Azure Policy om delegatie van scopes te controleren naar een beheertenant. Zie [Delegaties controleren in uw omgeving](view-manage-service-providers.md#audit-delegations-in-your-environment)voor meer informatie.

## <a name="view-activity-log-data"></a>Gegevens van het activiteitenlogboek weergeven

U [het activiteitenlogboek bekijken](../../azure-monitor/platform/activity-log-view.md) in het menu **Monitor** in de Azure-portal. Als u de resultaten wilt beperken tot een specifiek abonnement, gebruikt u de filters om een specifiek abonnement te selecteren. U ook [programmatisch gebeurtenissen voor activiteitenlogboeken bekijken en ophalen.](../../azure-monitor/platform/activity-log-view.md)

> [!NOTE]
> Gebruikers in de tenant van een serviceprovider kunnen de resultaten van activiteitenlogboeken voor een gedelegeerd abonnement in een klanttenant bekijken als ze de [readerrol](../../role-based-access-control/built-in-roles.md#reader) (of een andere ingebouwde rol met Reader-toegang) hebben gekregen toen dat abonnement is aangesloten voor azure-gedelegeerd resourcebeheer.

In het activiteitenlogboek ziet u de naam van de bewerking en de status ervan, samen met de datum en tijd waarop deze is uitgevoerd. De **gebeurtenis die wordt geïnitieerd door** kolom, geeft aan welke gebruiker de bewerking heeft uitgevoerd, of het nu een gebruiker was in de tenant van een serviceprovider die optreedt via azure gedelegeerd resourcebeheer of een gebruiker in de eigen tenant van de klant. Houd er rekening mee dat de naam van de gebruiker wordt weergegeven, in plaats van de tenant of de rol die de gebruiker voor dat abonnement heeft toegewezen.

Aangemelde activiteit is beschikbaar in de Azure-portal voor de afgelopen 90 dagen. Zie [Azure-activiteitslogboeken verzamelen en analyseren in de werkruimte Log Analytics](../../azure-monitor/platform/activity-log-collect.md)voor meer informatie over het opslaan van deze gegevens.

> [!NOTE]
> Gebruikers van de serviceprovider worden weergegeven in het activiteitenlogboek, maar deze gebruikers en hun roltoewijzingen worden niet weergegeven in **Toegangsbeheer (IAM)** of bij het ophalen van taaktoewijzingsgegevens via API's.

## <a name="set-alerts-for-critical-operations"></a>Waarschuwingen instellen voor kritieke bewerkingen

Om op de hoogte te blijven van kritieke bewerkingen die serviceproviders (of gebruikers in uw eigen tenant) uitvoeren, raden we u aan waarschuwingen voor [activiteitenlogboeken te](../../azure-monitor/platform/activity-log-alerts.md)maken. U bijvoorbeeld alle administratieve acties voor een abonnement bijhouden of een melding krijgen wanneer een virtuele machine in een bepaalde resourcegroep wordt verwijderd. Wanneer u waarschuwingen maakt, worden acties uitgevoerd door gebruikers in de eigen tenant van de klant en in eventuele beheerders van de klant.

Zie [Waarschuwingen voor activiteitenlogboeken maken en beheren](../../azure-monitor/platform/alerts-activity-log.md)voor meer informatie.

## <a name="create-log-queries"></a>Logboekquery's maken

U query's maken om uw aangemelde activiteit te analyseren of u te concentreren op specifieke items. Een audit vereist bijvoorbeeld dat u rapporteert over alle acties op administratief niveau die op een abonnement zijn uitgevoerd. U een query maken om alleen op deze acties te filteren en de resultaten sorteren op gebruiker, datum of andere waarde.

Zie [Overzicht van logboekquery's in Azure Monitor](../../azure-monitor/log-query/log-query-overview.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Monitor](../../azure-monitor/index.yml).
- Meer informatie over het [bekijken en beheren van serviceprovideraanbiedingen](view-manage-service-providers.md) in de Azure-portal.