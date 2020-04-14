---
title: Toegang tot slow query logs - Azure portal - Azure Database voor MySQL
description: In dit artikel wordt beschreven hoe u de langzame logboeken in Azure Database voor MySQL configureert en opent vanuit de Azure-portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: 59faf63312bd7cc657f8b96ca3110707ea997c02
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273598"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Langzame querylogboeken configureren en openen vanuit de Azure-portal

U de [Azure Database for MySQL slow query logs](concepts-server-logs.md) configureren, aanbieden en downloaden vanuit de Azure-portal.

## <a name="prerequisites"></a>Vereisten
De stappen in dit artikel vereisen dat u [Azure Database voor MySQL-server hebt.](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Logboekregistratie configureren
Configureer de toegang tot het Slow Query-logboek van MySQL. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Selecteer uw Azure Database voor MySQL-server.

3. Selecteer **Serverlogboeken**onder de sectie **Controle** in de zijbalk . 
   ![Schermafbeelding van serverlogboekenopties](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Als u de serverparameters wilt bekijken, selecteert u **Klik hier om logboeken in te schakelen en logboekparameters te configureren.**

5. Zet **slow_query_log** **in op AAN**.

6. Selecteer waar u de logboeken wilt uitvoeren naar het gebruik van **log_output**. Als u logboeken wilt verzenden naar zowel lokale opslag als diagnostische logboeken voor Azure-monitor, selecteert u **Bestand**. 

7. Wijzig alle andere parameters die nodig zijn. 

8. Selecteer **Opslaan**. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Schermafbeelding van parameters voor langzaam querylogboek en opslaan.":::

Op de pagina **Serverparameters** u terugkeren naar de lijst met logboeken door de pagina te sluiten.

## <a name="view-list-and-download-logs"></a>Lijst- en downloadlogboeken weergeven
Nadat de logboekregistratie is begonnen, u een lijst met beschikbare logboeken voor langzame query's bekijken en afzonderlijke logboekbestanden downloaden.

1. Open Azure Portal.

2. Selecteer uw Azure Database voor MySQL-server.

3. Selecteer **Serverlogboeken**onder de sectie **Controle** in de zijbalk . De pagina toont een lijst met uw logboekbestanden.

   ![Schermafbeelding van de pagina Serverlogboeken, met lijst met logboeken gemarkeerd](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > De naamgevingsconventie van het logboek is **mysql-slow-< uw servernaam>-yyyymmddhh.log**. De datum en tijd die in de bestandsnaam worden gebruikt, is het tijdstip waarop het logboek is uitgegeven. Logbestanden worden elke 24 uur of 7,5 GB gedraaid, wat het eerst komt. 

4. Gebruik indien nodig het zoekvak om snel te beperken tot een specifiek logboek, op basis van datum en tijd. De zoekopdracht staat op de naam van het logboek.

5. Als u afzonderlijke logboekbestanden wilt downloaden, selecteert u het pijl-omlaagpictogram naast elk logboekbestand in de tabelrij.

   ![Schermafbeelding van de pagina Serverlogboeken, met pictogram pijl-omlaag gemarkeerd](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Diagnostische logboeken instellen

1. Selecteer **diagnostische instellingen** > onder de sectie **Controle** in de zijbalk De optie**Diagnostische instellingen Toevoegen**.

   ![Schermafbeelding van opties voor diagnostische instellingen](./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png)

1. Geef een diagnostische instellingsnaam op.

1. Geef op welke gegevens de logboeken voor trage query's (opslagaccount, gebeurtenishub of Log Analytics-werkruimte) moeten verzenden.

1. Selecteer **MySqlSlowLogs** als het logboektype.
![Schermafbeelding van configuratieopties voor diagnostische instellingen](./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png)

1. Nadat u de gegevenssinks hebt geconfigureerd om de logboeken voor langzame query's naar te **pipeeren,** selecteert u Opslaan .
![Schermafbeelding van configuratieopties voor diagnostische instellingen, met Opslaan gemarkeerd](./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png)

1. Toegang tot de slow query logs door ze te verkennen in de gegevens sinks die u hebt geconfigureerd. Het kan tot 10 minuten duren voordat de logboeken worden weergegeven.

## <a name="next-steps"></a>Volgende stappen
- Zie [Logboeken voor trage query's in CLI openen](howto-configure-server-logs-in-cli.md) om te leren hoe u logboeken met langzame query's programmatisch downloaden.
- Meer informatie over [logboeken met langzame query's](concepts-server-logs.md) in Azure Database voor MySQL.
- Zie de MySQL-documentatie over [logboeken](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)voor meer informatie over de parameterdefinities en MySQL-logboekregistratie.