---
title: Activiteit van serviceprovider bekijken
description: Klanten kunnen vastgelegde activiteiten weer geven om acties te bekijken die door service providers worden uitgevoerd via het beheer van gedelegeerde resources van Azure.
ms.date: 01/15/2020
ms.topic: conceptual
ms.openlocfilehash: de149bddb6917a63d91b1890c0430f64465cb40c
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76046106"
---
# <a name="view-service-provider-activity"></a>Activiteit van serviceprovider bekijken

Klanten met gedelegeerde abonnementen voor Azure delegated resource management kunnen [Azure activiteiten logboek gegevens weer geven](../../azure-monitor/platform/platform-logs-overview.md) om alle acties te zien die worden uitgevoerd. Dit biedt klanten volledige zicht baarheid van de activiteiten die door service providers worden uitgevoerd via het beheer van de gedelegeerde resources van Azure, samen met die van gebruikers in de eigen Azure Active Directory (Azure AD)-Tenant van de klant.

## <a name="view-activity-log-data"></a>Gegevens van activiteiten logboek weer geven

U kunt [het activiteiten logboek bekijken](../../azure-monitor/platform/activity-log-view.md) via het menu **Monitor** in de Azure Portal. Als u de resultaten wilt beperken tot een specifiek abonnement, kunt u de filters gebruiken om een specifiek abonnement te selecteren. U kunt [activiteiten logboek gebeurtenissen ook programmatisch weer geven en ophalen](../../azure-monitor/platform/activity-log-view.md) .

> [!NOTE]
> Gebruikers in de Tenant van een service provider kunnen de resultaten van het activiteiten logboek weer geven voor een gedelegeerd abonnement in een Tenant van de klant als ze de rol van [lezer](../../role-based-access-control/built-in-roles.md#reader) hebben gekregen (of een andere ingebouwde rol die lezers toegang bevat) wanneer dat abonnement is voor bereid voor Azure delegated resource management.

In het activiteiten logboek ziet u de naam van de bewerking en de bijbehorende status, samen met de datum en tijd waarop het is uitgevoerd. In de **gebeurtenis gestart door** de kolom ziet u welke gebruiker de bewerking heeft uitgevoerd, of het een gebruiker in de Tenant van een service provider was die optreedt door middel van het beheer van gedelegeerde resources van Azure of een gebruiker in de eigen Tenant van de klant. Houd er rekening mee dat de naam van de gebruiker wordt weer gegeven in plaats van de Tenant of de rol die aan de gebruiker is toegewezen voor dat abonnement.

Geregistreerde activiteiten is in de afgelopen 90 dagen beschikbaar in de Azure Portal. Zie [Azure-activiteiten logboeken verzamelen en analyseren in log Analytics werk ruimte in azure monitor](../../azure-monitor/platform/activity-log-collect.md) voor meer informatie over hoe u deze gegevens langer dan 90 dagen kunt opslaan.

> [!NOTE]
> Gebruikers van de service provider worden weer gegeven in het activiteiten logboek, maar deze gebruikers en hun roltoewijzingen worden niet weer gegeven in **Access Control (IAM)** of bij het ophalen van roltoewijzings gegevens via api's.

## <a name="set-alerts-for-critical-operations"></a>Waarschuwingen instellen voor kritieke bewerkingen

We raden u aan [activiteiten logboek waarschuwingen](../../azure-monitor/platform/activity-log-alerts.md)te maken om te zorgen dat de service providers (of gebruikers in uw eigen Tenant) op de hoogte blijven van kritieke bewerkingen. U kunt bijvoorbeeld alle beheer acties voor een abonnement bijhouden of een melding ontvangen wanneer een virtuele machine in een bepaalde resource groep wordt verwijderd. Wanneer u waarschuwingen maakt, bevatten deze acties die worden uitgevoerd door gebruikers in de eigen Tenant van de klant, evenals bij het beheer van tenants.

Zie [waarschuwingen voor activiteiten logboeken maken en beheren](../../azure-monitor/platform/alerts-activity-log.md)voor meer informatie.

## <a name="create-log-queries"></a>Logboek query's maken

U kunt query's maken om uw vastgelegde activiteiten te analyseren of te focussen op specifieke items. Bijvoorbeeld: een audit vereist dat u rapporteert over alle acties op beheer niveau die zijn uitgevoerd op een abonnement. U kunt een query maken om alleen op deze acties te filteren en de resultaten te sorteren op gebruiker, datum of een andere waarde.

Zie [overzicht van logboek query's in azure monitor](../../azure-monitor/log-query/log-query-overview.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure monitor](../../azure-monitor/index.yml).
- Meer informatie over het [weer geven en beheren van aanbiedingen voor service providers](view-manage-service-providers.md) in de Azure Portal.