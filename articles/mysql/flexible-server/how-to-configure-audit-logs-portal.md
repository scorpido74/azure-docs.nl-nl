---
title: Controle logboeken configureren-Azure Portal-Azure Database for MySQL-flexibele server
description: In dit artikel wordt beschreven hoe u de audit Logboeken in Azure Database for MySQL flexibele server kunt configureren en openen vanuit de Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 1d0687e6cdcc8208218183cb873f9620a5525d37
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936662"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Controle logboeken voor Azure Database for MySQL flexibele server configureren en openen met behulp van de Azure Portal

> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

U kunt de Azure Database for MySQL flexibele server [audit logboeken](concepts-audit-logs.md) en diagnostische instellingen van de Azure Portal configureren.

## <a name="prerequisites"></a>Vereisten
Voor de stappen in dit artikel moet u een [flexibele server](quickstart-create-server-portal.md)hebben.

## <a name="configure-audit-logging"></a>Controle logboek registratie configureren

>[!IMPORTANT]
> Het is raadzaam om alleen de gebeurtenis typen en gebruikers die vereist zijn voor controle doeleinden te registreren om ervoor te zorgen dat de prestaties van uw server niet sterk worden beïnvloed.

Controle logboek registratie inschakelen en configureren.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer uw flexibele server.

1. Selecteer in de sectie **instellingen** van de zijbalk **server parameters**.
    <!--:::image type="content" source="./media/howto-configure-audit-logs-portal/server-parameters.png" alt-text="Server parameters":::-->

1. Werk de para meter **audit_log_enabled** bij naar aan.
    <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-enabled.png" alt-text="Enable audit logs":::-->

1. Selecteer de [gebeurtenis typen](concepts-audit-logs.md#configure-audit-logging) die moeten worden geregistreerd door de **audit_log_events** -para meter bij te werken.
    <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-events.png" alt-text="Audit log events":::-->

1. Voeg MySQL-gebruikers toe die moeten worden uitgesloten van logboek registratie door de para meter **audit_log_exclude_users** bij te werken. Geef gebruikers op door hun MySQL-gebruikers naam op te geven.
    <!--:::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="Audit log exclude users":::-->

1. Wanneer u de para meters hebt gewijzigd, kunt u op **Opslaan**klikken. Of u kunt uw wijzigingen **negeren** .
    <!--:::image type="content" source="./media/howto-configure-audit-logs-portal/save-parameters.png" alt-text="Save":::-->

## <a name="set-up-diagnostics"></a>Diagnostische gegevens instellen

Audit logboeken zijn geïntegreerd met Azure Monitor Diagnostische instellingen, zodat u uw logboeken kunt door geven aan Azure Monitor logboeken, Event Hubs of Azure Storage.

1. Selecteer in de sectie **bewaking** van de zijbalk **Diagnostische instellingen**.

1. Klik op "+ diagnostische instelling toevoegen"  <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="Add diagnostic setting":::-->

1. Geef een naam op voor de diagnostische instelling.

1. Geef op welke doelen de audit Logboeken (opslag account, Event Hub en/of Log Analytics werk ruimte) moeten verzenden.

1. Selecteer **MySqlAuditLogs** als het logboek type.
    <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="Configure diagnostic setting"::: -->

1. Zodra u de gegevens sinks hebt geconfigureerd om de controle logboeken te pipeen naar, kunt u op **Opslaan**klikken.
    <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="Save diagnostic setting":::-->

1. Open de audit logboeken door ze te verkennen in de gegevens-sinks die u hebt geconfigureerd. Het kan tot tien minuten duren voordat de logboeken worden weer gegeven.

Als u uw audit Logboeken hebt gepipetteerd naar Azure Monitor-Logboeken (Log Analytics), raadpleegt u enkele [voorbeeld query's](concepts-audit-logs.md#analyze-logs-in-azure-monitor-logs) die u voor analyse kunt gebruiken.  

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [audit logboeken](concepts-audit-logs.md)
- Meer informatie over [Logboeken voor langzame query's](concepts-slow-query-logs.md)
<!-- - Learn how to configure audit logs in the [Azure CLI](howto-configure-audit-logs-cli.md)-->