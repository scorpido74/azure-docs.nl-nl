---
title: Access audit logs - Azure portal - Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u de controlelogboeken in Azure Database voor MariaDB configureert en opent vanuit de Azure-portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: d31b7531e00199ea63c7b057dacff1b67d7cedc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528928"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Controlelogboeken configureren en openen in de Azure-portal

U de [Azure Database voor MariaDB-controlelogboeken](concepts-audit-logs.md) en diagnostische instellingen configureren vanuit de Azure-portal.

> [!IMPORTANT]
> De functionaliteit van het controlelogboek is momenteel in preview.

## <a name="prerequisites"></a>Vereisten

Om deze handleiding door te nemen, hebt u het volgende nodig:

- [Azure-database voor MariaDB-server](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Controlelogboekregistratie configureren

Controlelogboekregistratie inschakelen en configureren.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Selecteer uw Azure-database voor MariaDB-server.

1. Selecteer **serverparameters**onder de sectie **Instellingen** in de zijbalk .
    ![Serverparameters](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Werk de **parameter audit_log_enabled** bij aan AAN.
    ![Controlelogboeken inschakelen](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Selecteer de [gebeurtenistypen](concepts-audit-logs.md#configure-audit-logging) die moeten worden geregistreerd door de **parameter audit_log_events** bij te werken.
    ![Gebeurtenis van het controlelogboek](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Voeg alle MariaDB-gebruikers toe die van logboekregistratie moeten worden uitgesloten door de **parameter audit_log_exclude_users** bij te werken. Geef gebruikers op door hun MariaDB-gebruikersnaam op te geven.
    ![Controlelogboek sluit gebruikers uit](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Zodra u de parameters hebt gewijzigd, u op **Opslaan**klikken. Of u uw wijzigingen **verwijderen.**
    ![Opslaan](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Diagnostische logboeken instellen

1. Selecteer onder de sectie **Controle** in de zijbalk de optie **Diagnostische instellingen**.

1. Klik op '+ Diagnostische ![instelling toevoegen' Diagnostische instelling toevoegen](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Geef een diagnostische instellingsnaam op.

1. Geef op welke gegevens de controlelogboeken moeten worden verzonden (opslagaccount, gebeurtenishub en/of Logboekanalysewerkruimte).

1. Selecteer "MySqlAuditLogs" als het logboektype.
![Diagnostische instelling configureren](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Zodra u de gegevensgootstenen hebt geconfigureerd om de controlelogboeken naar te verzenden, u op **Opslaan**klikken.
![Diagnostische instelling opslaan](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Toegang tot de controlelogboeken door ze te verkennen in de gegevenssinks die u hebt geconfigureerd. Het kan tot 10 minuten duren voordat de logboeken worden weergegeven.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [controlelogboeken](concepts-audit-logs.md) in Azure Database voor MariaDB.