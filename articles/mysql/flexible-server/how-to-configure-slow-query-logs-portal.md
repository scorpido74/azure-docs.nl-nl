---
title: Langzame query logboeken configureren-Azure Portal-Azure Database for MySQL-flexibele server
description: In dit artikel wordt beschreven hoe u de langzame Logboeken in Azure Database for MySQL flexibele server kunt configureren en openen vanuit de Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 1416efaa325ced623b5c7514f2f9953dc0bd2781
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936653"
---
# <a name="configure-and-access-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Langzame query logboeken voor Azure Database for MySQL-flexibele server configureren en openen met behulp van de Azure Portal

> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

U kunt de Azure Database for MySQL flexibele server [langzame query logboeken](concepts-slow-query-logs.md) van de Azure Portal configureren, weer geven en downloaden.

## <a name="prerequisites"></a>Vereisten
Voor de stappen in dit artikel moet u een [flexibele server](quickstart-create-server-portal.md)hebben.

## <a name="configure-logging"></a>Logboek registratie configureren
Configureer de toegang tot het MySQL-logboek voor langzame query's. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer uw flexibele server.

1. Selecteer in de sectie **instellingen** van de zijbalk **server parameters**.
   <!-- :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="Screenshot of Server logs options":::-->

1. Werk de **slow_query_log** para meter slow_query_log **bij naar aan**.

1. Andere benodigde para meters wijzigen (bijvoorbeeld `long_query_time`, `log_slow_admin_statements`). Raadpleeg de [langzame query logs](./concepts-slow-query-logs.md#configure-slow-query-logging) documenten voor meer para meters.  

1. Selecteer **Opslaan**. 

   <!-- :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Screenshot of slow query log parameters and save."::: -->

Op de pagina **server parameters** kunt u teruggaan naar de lijst met Logboeken door de pagina te sluiten.

## <a name="set-up-diagnostics"></a>Diagnostische gegevens instellen

Langzame query logboeken zijn geïntegreerd met Azure Monitor Diagnostische instellingen, zodat u uw logboeken kunt door geven aan Azure Monitor logboeken, Event Hubs of Azure Storage.

1. Selecteer in de sectie **bewaking** van de zijbalk **Diagnostische instellingen**  >  **Diagnostische instellingen toevoegen**.

   <!--:::image type="content" source="./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png" alt-text="Screenshot of Diagnostic settings options":::-->

1. Geef een naam op voor de diagnostische instelling.

1. Geef op welke doelen de langzame query Logboeken (opslag account, Event Hub of Log Analytics-werk ruimte) moeten verzenden.

1. Selecteer **MySqlSlowLogs** als het logboek type.
    <!--:::image type="content" source="./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png" alt-text="Screenshot of Diagnostic settings configuration options":::-->

1. Nadat u de gegevens sinks hebt geconfigureerd om de langzame query logboeken te pipeen, selecteert u **Opslaan**.
    <!--:::image type="content" source="./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png" alt-text="Screenshot of Diagnostic settings configuration options, with Save highlighted":::-->

1. Open de logboeken voor trage query's door ze te verkennen in de gegevens-sinks die u hebt geconfigureerd. Het kan tot tien minuten duren voordat de logboeken worden weer gegeven.

Als u uw audit Logboeken hebt gepipetteerd naar Azure Monitor-Logboeken (Log Analytics), raadpleegt u enkele [voorbeeld query's](concepts-audit-logs.md#analyze-logs-in-azure-monitor-logs) die u voor analyse kunt gebruiken. 

## <a name="next-steps"></a>Volgende stappen
<!-- - See [Access slow query Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download slow query logs programmatically.-->
- Meer informatie over [Logboeken voor langzame query's](concepts-slow-query-logs.md)
- Zie de MySQL-documentatie over [Logboeken](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)voor meer informatie over de parameter definities en MySQL-logboek registratie.
- Meer informatie over [audit logboeken](concepts-audit-logs.md)