---
title: Langzame query logboeken voor Azure Database for MySQL in Azure Portal configureren en openen
description: In dit artikel wordt beschreven hoe u de langzame Logboeken in Azure Database for MySQL kunt configureren en openen vanuit de Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: b3986c19ec008437f3230b3674ce60d1dfba2024
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703445"
---
# <a name="configure-and-access-slow-query-logs-in-the-azure-portal"></a>Langzame query logboeken configureren en openen in de Azure Portal

U kunt de [Azure database for MySQL langzame query logboeken](concepts-server-logs.md) configureren, weer geven en downloaden vanuit de Azure Portal.

## <a name="prerequisites"></a>Vereisten
Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:
- [Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Logboek registratie configureren
Configureer de toegang tot het MySQL-logboek voor langzame query's. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Selecteer uw Azure Database voor MySQL-server.

3. Selecteer in de sectie **bewaking** in de zijbalk **Server logboeken**. 
   ![Select-server logboeken, klik om te configureren @ no__t-1

4. Selecteer de kop **Klik hier om Logboeken in te scha kelen en logboek parameters te configureren** om de server parameters weer te geven.

5. Wijzig de para meters die u wilt aanpassen. Alle wijzigingen die u aanbrengt in deze sessie, worden in paars gemarkeerd. 

   Wanneer u de para meters hebt gewijzigd, kunt u op **Opslaan**klikken. Of u kunt uw wijzigingen **negeren** .

   ![Klik op opslaan of negeren](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Ga terug naar de lijst met Logboeken door te klikken op de **knop sluiten** (X pictogram) op de pagina **server parameters** .

## <a name="view-list-and-download-logs"></a>Lijst weer geven en logboeken downloaden
Zodra de logboek registratie is gestart, kunt u een lijst met beschik bare langzame query logboeken bekijken en afzonderlijke logboek bestanden downloaden in het deel venster Server Logboeken.

1. Open Azure Portal.

2. Selecteer uw Azure Database voor MySQL-server.

3. Selecteer in de sectie **bewaking** in de zijbalk **Server logboeken**. Op de pagina ziet u een lijst met uw logboek bestanden, zoals wordt weer gegeven:

   ![Lijst met Logboeken](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > De naam Conventie van het logboek is **mysql-slow < uw server name >-yyyymmddhh. log**. De datum en tijd die in de bestands naam wordt gebruikt, is de tijd waarop het logboek is uitgegeven. Logboek bestanden worden elke 24 uur of 7,5 GB gedraaid, afhankelijk van wat het eerste komt.

4. Gebruik, indien nodig, het **zoekvak** om snel te beperken tot een specifiek logboek op basis van datum/tijd. De zoek opdracht bevindt zich op de naam van het logboek.

5. Down load afzonderlijke logboek bestanden met behulp van de knop **downloaden** (pijl-omlaag) naast elk logboek bestand in de tabelrij, zoals wordt weer gegeven:

   ![Klik op pictogram downloaden](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Diagnostische logboeken instellen

1. Selecteer in de sectie **bewaking** van de zijbalk **Diagnostische instellingen**.

1. Klik op "+ diagnostische instelling toevoegen" ![Add diagnostische instelling @ no__t-1

1. Geef een naam op voor de diagnostische instelling.

1. Geef op welke gegevens worden gesinkt om de langzame query Logboeken (opslag account, Event Hub en/of Log Analytics-werk ruimte) te verzenden.

1. Selecteer ' MySqlSlowLogs ' als het logboek type.
diagnostische instelling van ![Configure @ no__t-1

1. Zodra u de gegevens sinks hebt geconfigureerd om de langzame query logboeken te pipeen naar, kunt u op **Opslaan**klikken.
diagnostische instelling van ![Save @ no__t-1

1. Open de logboeken voor trage query's door ze te verkennen in de gegevens-sinks die u hebt geconfigureerd. Het kan tot tien minuten duren voordat de logboeken worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen
- Zie [langzame query Logboeken openen in cli](howto-configure-server-logs-in-cli.md) voor meer informatie over het downloaden van langzame query logboeken via een programma.
- Meer informatie over [trage query-logboeken](concepts-server-logs.md) in azure database for MySQL.
- Zie de MySQL-documentatie over [Logboeken](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)voor meer informatie over de parameter definities en MySQL-logboek registratie.