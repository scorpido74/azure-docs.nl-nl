---
title: Activiteit service provider weer geven
description: Klanten kunnen vastgelegde activiteiten weer geven om acties te bekijken die door service providers worden uitgevoerd via het beheer van gedelegeerde resources van Azure.
ms.date: 12/6/2019
ms.topic: conceptual
ms.openlocfilehash: 69517e942aa9f82be16fa3d0e7d6f9252de44d4c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932682"
---
# <a name="view-service-provider-activity"></a>Activiteit service provider weer geven

Klanten met gedelegeerde abonnementen voor Azure delegated resource management kunnen [Azure activiteiten logboek gegevens weer geven](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) om alle acties te zien die worden uitgevoerd. Dit biedt klanten volledige zicht baarheid van de activiteiten die door service providers worden uitgevoerd via het beheer van de gedelegeerde resources van Azure, samen met die van gebruikers in de eigen Azure Active Directory (Azure AD)-Tenant van de klant.

## <a name="view-activity-log-data"></a>Gegevens van activiteiten logboek weer geven

U kunt [het activiteiten logboek bekijken](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview#view-the-activity-log) via het menu **Monitor** in de Azure Portal. Als u de resultaten wilt beperken tot een specifiek abonnement, kunt u de filters gebruiken om een specifiek abonnement te selecteren. U kunt [activiteiten logboek gebeurtenissen ook programmatisch weer geven en ophalen](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) .

> [!NOTE]
> Gebruikers in de Tenant van een service provider kunnen de resultaten van het activiteiten logboek weer geven voor een gedelegeerd abonnement in een Tenant van de klant als ze de rol van [lezer](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) hebben gekregen (of een andere ingebouwde rol die lezers toegang bevat) wanneer dat abonnement is voor bereid voor Azure delegated resource management.

In het activiteiten logboek ziet u de naam van de bewerking en de bijbehorende status, samen met de datum en tijd waarop het is uitgevoerd. In de **gebeurtenis gestart door** de kolom ziet u welke gebruiker de bewerking heeft uitgevoerd, of het een gebruiker in de Tenant van een service provider was die optreedt door middel van het beheer van gedelegeerde resources van Azure of een gebruiker in de eigen Tenant van de klant. Houd er rekening mee dat de naam van de gebruiker wordt weer gegeven in plaats van de Tenant of de rol die aan de gebruiker is toegewezen voor dat abonnement.

Geregistreerde activiteiten is in de afgelopen 90 dagen beschikbaar in de Azure Portal. Zie [Azure-activiteiten logboeken verzamelen en analyseren in log Analytics werk ruimte in azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect) voor meer informatie over hoe u deze gegevens langer dan 90 dagen kunt opslaan.

## <a name="set-alerts-for-critical-operations"></a>Waarschuwingen instellen voor kritieke bewerkingen

We raden u aan [activiteiten logboek waarschuwingen](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-alerts)te maken om te zorgen dat de service providers (of gebruikers in uw eigen Tenant) op de hoogte blijven van kritieke bewerkingen. U kunt bijvoorbeeld alle beheer acties voor een abonnement bijhouden of een melding ontvangen wanneer een virtuele machine in een bepaalde resource groep wordt verwijderd. Wanneer u waarschuwingen maakt, bevatten deze acties die worden uitgevoerd door gebruikers in de eigen Tenant van de klant, evenals bij het beheer van tenants.

Zie [waarschuwingen voor activiteiten logboeken maken en beheren](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)voor meer informatie.

## <a name="create-log-queries"></a>Logboek query's maken

U kunt query's maken om uw vastgelegde activiteiten te analyseren of te focussen op specifieke items. Bijvoorbeeld: een audit vereist dat u rapporteert over alle acties op beheer niveau die zijn uitgevoerd op een abonnement. U kunt een query maken om alleen op deze acties te filteren en de resultaten te sorteren op gebruiker, datum of een andere waarde.

Zie [overzicht van logboek query's in azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/).
- Meer informatie over het [weer geven en beheren van aanbiedingen voor service providers](view-manage-service-providers.md) in de Azure Portal.