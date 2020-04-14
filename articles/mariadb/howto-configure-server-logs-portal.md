---
title: Toegang tot slow query logs - Azure portal - Azure Database voor MariaDB
description: In dit artikel wordt beschreven hoe u de logboeken voor trage query's in Azure Database voor MariaDB configureert en opent vanuit de Azure-portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: 1e67637d36eaba55e4c6baaf6f775fc7b79fcd14
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270589"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Langzame querylogboeken configureren en openen vanuit de Azure-portal

U de [Azure Database voor MariaDB slow querylogs](concepts-server-logs.md) configureren, aanbieden en downloaden vanuit de Azure-portal.

## <a name="prerequisites"></a>Vereisten
De stappen in dit artikel vereisen dat u [Azure Database voor MariaDB-server hebt.](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Logboekregistratie configureren
De toegang tot het logboek voor langzame query's configureren. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Selecteer uw Azure-database voor MariaDB-server.

3. Selecteer **Serverlogboeken**onder de sectie **Controle** in de zijbalk . 
   ![Schermafbeelding van serverlogboekenopties](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. Als u de serverparameters wilt bekijken, selecteert u **Klik hier om logboeken in te schakelen en logboekparameters te configureren.**

5. Zet **slow_query_log** **in op AAN**.

6. Selecteer waar u de logboeken wilt uitvoeren naar het gebruik van **log_output**. Als u logboeken wilt verzenden naar zowel lokale opslag als diagnostische logboeken voor Azure-monitor, selecteert u **Bestand**. 

7. Wijzig alle andere parameters die nodig zijn. 

8. Selecteer **Opslaan**. 

   :::image type="content" source="./media/howto-configure-server-logs-portal/3-save-discard.png" alt-text="Schermafbeelding van parameters voor langzaam querylogboek en opslaan.":::

Op de pagina **Serverparameters** u terugkeren naar de lijst met logboeken door de pagina te sluiten.

## <a name="view-list-and-download-logs"></a>Lijst- en downloadlogboeken weergeven
Nadat de logboekregistratie is begonnen, u een lijst met beschikbare logboeken voor langzame query's bekijken en afzonderlijke logboekbestanden downloaden. 

1. Open Azure Portal.

2. Selecteer uw Azure-database voor MariaDB-server.

3. Selecteer **Serverlogboeken**onder de sectie **Controle** in de zijbalk . De pagina toont een lijst met uw logboekbestanden.

   ![Schermafbeelding van de pagina Serverlogboeken, met lijst met logboeken gemarkeerd](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > De naamgevingsconventie van het logboek is **mysql-slow-< uw servernaam>-yyyymmddhh.log**. De datum en tijd die in de bestandsnaam worden gebruikt, is het tijdstip waarop het logboek is uitgegeven. Logbestanden worden elke 24 uur of 7,5 GB gedraaid, wat het eerst komt.

4. Gebruik indien nodig het zoekvak om snel te beperken tot een specifiek logboek, op basis van datum en tijd. De zoekopdracht staat op de naam van het logboek.

5. Als u afzonderlijke logboekbestanden wilt downloaden, selecteert u het pijl-omlaagpictogram naast elk logboekbestand in de tabelrij.

   ![Schermafbeelding van de pagina Serverlogboeken, met pictogram pijl-omlaag gemarkeerd](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Diagnostische logboeken instellen

1. Selecteer onder de sectie **Controle** in de zijbalk de optie **Diagnostische instellingen** > **Diagnostische instelling toevoegen**.

   ![Schermafbeelding van opties voor diagnostische instellingen](./media/howto-configure-server-logs-portal/add-diagnostic-setting.png)

1. Geef een diagnostische instellingsnaam op.

1. Geef op welke gegevens de logboeken voor trage query's (opslagaccount, gebeurtenishub of Log Analytics-werkruimte) moeten verzenden.

1. Selecteer **MySqlSlowLogs** als het logboektype.
![Schermafbeelding van configuratieopties voor diagnostische instellingen](./media/howto-configure-server-logs-portal/configure-diagnostic-setting.png)

1. Nadat u de gegevenssinks hebt geconfigureerd om de logboeken voor langzame query's naar te **pipeeren,** selecteert u Opslaan .
![Schermafbeelding van configuratieopties voor diagnostische instellingen, met Opslaan gemarkeerd](./media/howto-configure-server-logs-portal/save-diagnostic-setting.png)

1. Toegang tot de slow query logs door ze te verkennen in de gegevens sinks die u hebt geconfigureerd. Het kan tot 10 minuten duren voordat de logboeken worden weergegeven.

## <a name="next-steps"></a>Volgende stappen
- Zie [Logboeken voor trage query's openen in CLI](howto-configure-server-logs-cli.md) om te leren hoe u logboeken met langzame query's programmatisch downloaden.
- Meer informatie over [logboeken met langzame query's](concepts-server-logs.md) in Azure Database voor MariaDB.
- Zie de MariaDB-documentatie over [logboeken](https://mariadb.com/kb/en/library/slow-query-log-overview/)voor meer informatie over de parameterdefinities en -logboekregistratie.