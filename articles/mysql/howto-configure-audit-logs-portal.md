---
title: Controle logboeken configureren en openen-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u de audit Logboeken in Azure Database for MySQL kunt configureren en openen vanuit de Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: 2a2d2a697f0e41fb296c61c01909a814678f8277
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350406"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Controle logboeken voor Azure Database for MySQL in het Azure Portal configureren en openen

U kunt de [Azure database for MySQL controle logboeken](concepts-audit-logs.md) en diagnostische instellingen van de Azure Portal configureren.

> [!IMPORTANT]
> De functionaliteit van het controle logboek is momenteel beschikbaar als preview-versie.

## <a name="prerequisites"></a>Vereisten

Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:

- [Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Controle logboek registratie configureren

Controle logboek registratie inschakelen en configureren.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

1. Selecteer uw Azure Database voor MySQL-server.

1. Selecteer in de sectie **instellingen** van de zijbalk **server parameters**.
    ![Serverparameters](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Werk de para meter **audit_log_enabled** bij naar aan.
    controle logboeken ![inschakelen](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Selecteer de [gebeurtenis typen](concepts-audit-logs.md#configure-audit-logging) die moeten worden geregistreerd door de **audit_log_events** -para meter bij te werken.
    ![controle logboek gebeurtenissen](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Voeg MySQL-gebruikers toe die moeten worden uitgesloten van logboek registratie door de para meter **audit_log_exclude_users** bij te werken. Geef gebruikers op door hun MySQL-gebruikers naam op te geven.
    ![controle logboek gebruikers uitsluiten](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Wanneer u de para meters hebt gewijzigd, kunt u op **Opslaan**klikken. Of u kunt uw wijzigingen **negeren** .
    ](./media/howto-configure-audit-logs-portal/save-parameters.png) ![opslaan

## <a name="set-up-diagnostic-logs"></a>Diagnostische logboeken instellen

1. Selecteer in de sectie **bewaking** van de zijbalk **Diagnostische instellingen**.

1. Klik op "+ diagnostische instelling toevoegen" ![diagnostische instelling toevoegen](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Geef een naam op voor de diagnostische instelling.

1. Geef op welke gegevens moeten worden gesinkt om de controle Logboeken (opslag account, Event Hub en/of Log Analytics-werk ruimte) te verzenden.

1. Selecteer ' MySqlAuditLogs ' als het logboek type.
![diagnostische instelling configureren](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Zodra u de gegevens sinks hebt geconfigureerd om de controle logboeken te pipeen naar, kunt u op **Opslaan**klikken.
diagnostische instelling ![opslaan](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Open de audit logboeken door ze te verkennen in de gegevens-sinks die u hebt geconfigureerd. Het kan tot tien minuten duren voordat de logboeken worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [audit logboeken](concepts-audit-logs.md) in azure database for MySQL.