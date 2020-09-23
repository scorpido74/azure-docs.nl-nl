---
title: 'Azure Database for MySQL-flexibele server (preview): gepland onderhoud-Azure Portal'
description: Meer informatie over het configureren van geplande onderhouds instellingen voor een Azure Database for MySQL flexibele server van de Azure Portal.
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 6c3ad5fff6958bce72f876e3ff4701cf081abe60
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936647"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-mysql--flexible-server"></a>Geplande onderhouds instellingen beheren voor Azure Database for MySQL-flexibele server
 
U kunt onderhouds opties opgeven voor elke flexibele server in uw Azure-abonnement. Opties zijn onder andere de onderhouds planning en meldings instellingen voor toekomstige en voltooide onderhouds gebeurtenissen.

> [!IMPORTANT]
> Azure Database for MySQL-flexibele server is in preview.
 
## <a name="prerequisites"></a>Vereisten
U hebt het volgende nodig om deze hand leiding te volt ooien:
- Een [Azure database for MySQL flexibele server](quickstart-create-server-portal.md)
 
## <a name="specify-maintenance-schedule-options"></a>Opties voor onderhouds planning opgeven
 
1. Kies op de pagina MySQL-server onder de kop **instellingen** de optie **onderhoud** om geplande onderhouds opties te openen.
2. Het standaard schema (door systeem beheerd) is een wille keurige dag van de week en een periode van 60 minuten voor het starten van het onderhoud tussen 23:00 uur en de lokale server tijd van 7am. Als u dit schema wilt aanpassen, kiest u **aangepast schema**. U kunt vervolgens een voorkeurs dag van de week en een venster van 60 minuten voor de start tijd van het onderhoud selecteren.
 
## <a name="notifications-about-scheduled-maintenance-events"></a>Meldingen over geplande onderhouds gebeurtenissen
 
U kunt Azure Service Health gebruiken om [meldingen](/azure/service-health/service-notifications.md) over aanstaande en uitgevoerd gepland onderhoud op uw flexibele server weer te geven. U kunt ook waarschuwingen [instellen](/azure/service-health/resource-health-alert-monitor-guide.md) in azure service Health om meldingen over onderhouds gebeurtenissen te ontvangen.
 
## <a name="next-steps"></a>Volgende stappen  
 
* Meer informatie over [gepland onderhoud in azure database for MySQL-flexibele server](concepts-maintenance.md)
* [Azure service Health](/azure/service-health/overview.md) van Lean
