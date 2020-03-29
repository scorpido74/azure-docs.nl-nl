---
title: Inloggegevens roteren in Azure Stream Analytics-taken
description: In dit artikel wordt beschreven hoe u de referenties van invoer en uitvoersinks in Azure Stream Analytics-taken bijwerken.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 3ae639dd7c5a42fc6880240988f0fb2817b09f43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425974"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Inloggegevens roteren voor invoer en uitvoer van een Stream Analytics-taak

Wanneer u referenties regenereert voor een invoer of uitvoer van een Stream Analytics-taak, moet u de taak bijwerken met nieuwe referenties. U moet de taak stoppen voordat u de referenties bijwerkt, u de referenties niet vervangen terwijl de taak wordt uitgevoerd. Om de vertraging tussen het stoppen en opnieuw opstarten van de taak te verminderen, ondersteunt Stream Analytics het hervatten van een taak vanaf de laatste uitvoer. In dit onderwerp wordt het proces beschreven waarin de inloggegevens worden geroteerd en de taak opnieuw worden gestart met nieuwe referenties.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Nieuwe referenties opnieuw genereren en uw taak bijwerken met de nieuwe referenties 

In deze sectie nemen we u mee door regenererende referenties voor Blob Storage, Event Hubs, SQL Database en Table Storage. 

### <a name="blob-storagetable-storage"></a>Blob-opslag/tabelopslag
1. Meld u aan bij de Azure-portal > door het opslagaccount bladert dat u hebt gebruikt als invoer/uitvoer voor de taak Stream Analytics.    
2. Open **Access-toetsen**in de sectie Instellingen . Kies tussen de twee standaardtoetsen (toets1, toets2) degene die niet door uw taak wordt gebruikt en regenereert deze:  
   ![Sleutels regenereren voor opslagaccount](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. Kopieer de nieuw gegenereerde sleutel.    
4. Blader in de Azure-portal door uw Stream Analytics-taak > selecteer **Stoppen** en wachten tot de taak is gestopt.    
5. Zoek de invoer/uitvoer van de blob/tabelopslag waarvoor u referenties wilt bijwerken.    
6. Zoek het veld **Opslagaccountsleutel** en plak uw nieuw gegenereerde sleutel > klik op **Opslaan**.    
7. Een verbindingstest wordt automatisch gestart wanneer u de wijzigingen opslaat, u deze bekijken op het tabblad Meldingen. Er zijn twee meldingen- een komt overeen met het opslaan van de update en andere komt overeen met het testen van de verbinding:  
   ![Meldingen na het bewerken van de sleutel](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. Ga verder [met het starten van je taak vanaf de laatste gestopte tijdsectie.](#start-your-job-from-the-last-stopped-time)

### <a name="event-hubs"></a>Event Hubs

1. Meld u aan bij de Azure-portal > blader door de gebeurtenishub die u hebt gebruikt als invoer/uitvoer voor de taak Stream Analytics.    
2. Open in de sectie Instellingen **beleid voor gedeelde toegang** en selecteer het vereiste toegangsbeleid. Kies tussen de **primaire sleutel** en **secundaire sleutel**degene die niet door uw taak wordt gebruikt en regenereert deze:  
   ![Toetsen regenereren voor gebeurtenishubs](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. Kopieer de nieuw gegenereerde sleutel.    
4. Blader in de Azure-portal door uw Stream Analytics-taak > selecteer **Stoppen** en wachten tot de taak is gestopt.    
5. Zoek de invoer/uitvoer van gebeurtenishubs waarvoor u referenties wilt bijwerken.    
6. Zoek het veld **Beleidssleutel van gebeurtenishub** en plak de nieuw gegenereerde sleutel > klik op **Opslaan**.    
7. Een verbindingstest wordt automatisch gestart wanneer u uw wijzigingen opslaat, zorg ervoor dat deze is geslaagd.    
8. Ga verder [met het starten van je taak vanaf de laatste gestopte tijdsectie.](#start-your-job-from-the-last-stopped-time)

### <a name="sql-database"></a>SQL Database

U moet verbinding maken met de SQL-database om de inloggegevens van een bestaande gebruiker bij te werken. U referenties bijwerken met behulp van Azure portal of een client-side tool zoals SQL Server Management Studio. In deze sectie wordt het proces van het bijwerken van referenties met behulp van Azure-portal getoond.

1. Meld u aan bij de Azure-portal > blader door de SQL-database die u hebt gebruikt als uitvoer voor de taak Stream Analytics.    
2. Log in/vanuit **Gegevensverkenner**aan > u wilt autorisatietype als **SQL-serververificatie** > uw **inloggegevens** en **wachtwoordgegevens** > **Ok selecteren.**  
   ![Referenties voor SQL-database opnieuw genereren](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. Wijzig op het tabblad query het wachtwoord voor een van uw gebruikers door `<user_name>` de volgende `<new_password>` query uit te voeren (zorg ervoor dat u deze vervangt door uw gebruikersnaam en uw nieuwe wachtwoord):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Noteer het nieuwe wachtwoord.    
5. Blader in de Azure-portal door uw Stream Analytics-taak > selecteer **Stoppen** en wachten tot de taak is gestopt.    
6. Zoek de SQL-databaseuitvoer waarvoor u referenties wilt roteren. Werk het wachtwoord bij en sla wijzigingen op.    
7. Een verbindingstest wordt automatisch gestart wanneer u uw wijzigingen opslaat, zorg ervoor dat deze is geslaagd.    
8. Ga verder [met het starten van je taak vanaf de laatste gestopte tijdsectie.](#start-your-job-from-the-last-stopped-time)

### <a name="power-bi"></a>Power BI
1. Meld u aan bij de Azure-portal > door uw Stream Analytics-taak bladert > selecteer **Stoppen** en wachten tot de taak is gestopt.    
2. Zoek de Power BI-uitvoer waarvoor u referenties wilt vernieuwen > klik op de **autorisatie verlengen** (u ziet een succesbericht) > De wijzigingen **opslaan.**    
3. Een verbindingstest wordt automatisch gestart wanneer u uw wijzigingen opslaat, zorg ervoor dat deze is geslaagd.    
4. Ga verder [met het starten van je taak vanaf de laatste gestopte tijdsectie.](#start-your-job-from-the-last-stopped-time)

## <a name="start-your-job-from-the-last-stopped-time"></a>Je job vanaf de laatste gestopte tijd starten

1. Navigeer naar het **deelvenster Overzicht** van de taak > selecteer **Start** om de taak te starten.    
2. Selecteer **Wanneer het laatst is gestopt** > klik op **Start**. Houd er rekening mee dat de optie 'Wanneer het laatst gestopt' alleen wordt weergegeven als u de taak eerder hebt uitgevoerd en een uitvoer hebt gegenereerd. De taak wordt opnieuw gestart op basis van de tijd van de laatste uitvoerwaarde.
   ![De functie Stream Analytics starten](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)
