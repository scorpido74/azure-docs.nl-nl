---
title: Data Sync-agent voor Azure SQL Data Sync
description: Meer informatie over het installeren en uitvoeren van de Data Sync-agent voor Azure SQL Data Sync om gegevens te synchroniseren met on-premises SQL Server-data bases
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 097c8547093a13f73f1ae5facdc0f7e6c75c071d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690795"
---
# <a name="data-sync-agent-for-azure-sql-data-sync"></a>Data Sync-agent voor Azure SQL Data Sync

Synchroniseer gegevens met on-premises SQL Server-data bases door de Data Sync agent voor Azure SQL Data Sync te installeren en configureren. Zie voor meer informatie over SQL Data Sync [gegevens synchroniseren tussen meerdere Cloud-en on-premises data bases met SQL Data Sync](sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync biedt op dit moment **geen** ondersteuning voor beheerde exemplaren voor Azure SQL Database.

## <a name="download-and-install"></a>Downloaden en installeren

Als u de Data Sync-agent wilt downloaden, gaat u naar [SQL Azure Data Sync agent](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="install-silently"></a>Op de achtergrond installeren

Als u de Data Sync-agent op de achtergrond wilt installeren vanaf de opdracht prompt, voert u een opdracht in die vergelijkbaar is met het volgende voor beeld. Controleer de bestands naam van het gedownloade MSI-bestand en geef uw eigen waarden op voor de argumenten **TARGETDIR** en **SERVICEACCOUNT** .

- Als u geen waarde opgeeft voor **TARGETDIR**, is de standaard waarde `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0`.

- Als u `LocalSystem` opgeeft als waarde voor **SERVICEACCOUNT**, gebruikt u SQL Server-verificatie wanneer u de agent configureert om verbinding te maken met de on-premises SQL Server.

- Als u een domein gebruikers account of een lokale gebruikers account opgeeft als waarde voor **SERVICEACCOUNT**, moet u het wacht woord ook opgeven met het argument **SERVICEPASSWORD** . Bijvoorbeeld `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-sql-server-on-premises"></a>Gegevens synchroniseren met SQL Server on-premises

Zie [een on-premises SQL Server-Data Base toevoegen](sql-database-get-started-sql-data-sync.md#add-on-prem)om de Data Sync-agent te configureren, zodat u gegevens kunt synchroniseren met een of meer on-premises SQL server data bases.

## <a name="agent-faq"></a>Veelgestelde vragen over de Data Sync-agent

### <a name="why-do-i-need-a-client-agent"></a>Waarom heb ik een client agent nodig

De SQL Data Sync-service communiceert met SQL Server-data bases via de client agent. Deze beveiligings functie voor komt directe communicatie met data bases achter een firewall. Wanneer de SQL Data Sync-service communiceert met de agent, worden versleutelde verbindingen en een unieke token of *agent sleutel*gebruikt. De SQL Server-data bases verifiëren de agent met behulp van de connection string en de agent sleutel. Dit ontwerp biedt een hoog beveiligings niveau voor uw gegevens.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Hoeveel instanties van de lokale agent-UI kunnen worden uitgevoerd

Er kan slechts één exemplaar van de gebruikers interface worden uitgevoerd.

### <a name="how-can-i-change-my-service-account"></a>Hoe kan ik mijn service account wijzigen

Nadat u een client agent hebt geïnstalleerd, is de enige manier om het service account te wijzigen, het te verwijderen en een nieuwe client agent te installeren met het nieuwe service account.

### <a name="how-do-i-change-my-agent-key"></a>Hoe kan ik mijn agent sleutel wijzigen

Een agent sleutel kan slechts één keer worden gebruikt door een agent. Het kan niet opnieuw worden gebruikt wanneer u een nieuwe agent verwijdert en opnieuw installeert, en kan niet worden gebruikt door meerdere agents. Als u een nieuwe sleutel voor een bestaande agent moet maken, moet u ervoor zorgen dat dezelfde sleutel wordt vastgelegd met de client agent en met de SQL Data Sync-Service.

### <a name="how-do-i-retire-a-client-agent"></a>Een client agent Hoe kan ik buiten gebruik stellen

Als u een agent onmiddellijk ongeldig wilt maken of buiten gebruik wilt stellen, genereert u de sleutel opnieuw in de portal, maar verzendt u deze niet in de gebruikers interface van de agent. Het opnieuw genereren van een sleutel valideert de vorige sleutel, onafhankelijk als de bijbehorende agent online of offline is.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Een client agent Hoe kan ik verplaatsen naar een andere computer

Ga als volgt te werk als u de lokale agent vanaf een andere computer wilt uitvoeren dan momenteel is ingeschakeld:

1. Installeer de agent op de gewenste computer.
2. Meld u aan bij de SQL Data Sync Portal en Genereer opnieuw een agent sleutel voor de nieuwe agent.
3. Gebruik de nieuwe gebruikers interface van de agent om de nieuwe agent sleutel in te dienen.
4. Even geduld. de client agent downloadt de lijst met on-premises data bases die eerder zijn geregistreerd.
5. Geef database referenties op voor alle data bases die als onbereikbaar worden weer gegeven. Deze data bases moeten bereikbaar zijn vanaf de nieuwe computer waarop de agent is geïnstalleerd.

## <a name="agent-tshoot"></a>Problemen met de gegevens synchronisatie agent oplossen

- [De client agent installeren, verwijderen of herstellen mislukt](#agent-install)

- [De client agent werkt niet nadat ik het verwijderen heb geannuleerd](#agent-uninstall)

- [Mijn data base staat niet in de lijst met agents](#agent-list)

- [Client agent wordt niet gestart (fout 1069)](#agent-start)

- [Ik kan de agent sleutel niet verzenden](#agent-key)

- [De client agent kan niet worden verwijderd uit de portal als de gekoppelde on-premises data base onbereikbaar is](#agent-delete)

- [Lokale synchronisatie agent-app kan geen verbinding maken met de lokale synchronisatie service](#agent-connect)

### <a name="agent-install"></a>De client agent installeren, verwijderen of herstellen mislukt

- **Oorzaak**. Veel scenario's kunnen deze fout veroorzaken. Bekijk de logboeken om de specifieke oorzaak van deze fout te bepalen.

- **Oplossing**. Als u de specifieke oorzaak van de fout wilt achterhalen, genereert en bekijkt u de Windows Installer-Logboeken. U kunt logboek registratie inschakelen vanaf een opdracht prompt. Als het gedownloade installatie bestand bijvoorbeeld `SQLDataSyncAgent-2.0-x86-ENU.msi`is, Genereer en onderzoekt u logboek bestanden met behulp van de volgende opdracht regels:

  - Voor installaties: `msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - Voor het verwijderen: `msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    U kunt logboek registratie ook inschakelen voor alle installaties die worden uitgevoerd door Windows Installer. Het micro soft Knowledge Base-artikel [How to enable Windows Installer logging](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) biedt een oplossing met één klik om logboek registratie in te scha kelen voor Windows Installer. Het bevat ook de locatie van de logboeken.

### <a name="agent-uninstall"></a>De client agent werkt niet nadat ik het verwijderen heb geannuleerd

De client agent werkt niet, zelfs nadat u de verwijdering ervan hebt geannuleerd.

- **Oorzaak**. Dit probleem treedt op omdat de SQL Data Sync client agent geen referenties opslaat.

- **Oplossing**. U kunt deze twee oplossingen proberen:

    -   Gebruik Services. msc om de referenties voor de client agent opnieuw in te voeren.
    -   Verwijder deze client agent en installeer vervolgens een nieuwe. Down load en installeer de nieuwste client agent vanuit het [Download centrum](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="agent-list"></a>Mijn data base staat niet in de lijst met agents

Wanneer u een bestaande SQL Server-Data Base aan een synchronisatie groep probeert toe te voegen, wordt de data base niet weer gegeven in de lijst met agents.

Dit probleem kan de volgende oorzaken hebben:

- **Oorzaak**. De client agent en de synchronisatie groep bevinden zich in verschillende data centers.

- **Oplossing**. De client agent en de synchronisatie groep moeten zich in hetzelfde Data Center bedienen. Als u dit wilt instellen, hebt u twee opties:

    -   Maak een nieuwe agent in het Data Center waar de synchronisatie groep zich bevindt. Registreer vervolgens de data base bij die agent.
    -   De huidige synchronisatie groep verwijderen. Vervolgens moet u de synchronisatie groep opnieuw maken in het Data Center waarin de agent zich bevindt.

- **Oorzaak**. De lijst met data bases van de client agent is niet actueel.

- **Oplossing**. Stop de client agent-service en start deze opnieuw.

    De lokale agent downloadt de lijst met gekoppelde data bases alleen bij de eerste verzen ding van de agent sleutel. De lijst met gekoppelde data bases wordt niet gedownload voor volgende agent sleutel inzendingen. Data bases die zijn geregistreerd tijdens het verplaatsen van een agent, worden niet weer gegeven in het oorspronkelijke exemplaar van de agent.

### <a name="agent-start"></a>Client agent wordt niet gestart (fout 1069)

U ontdekt dat de agent niet wordt uitgevoerd op een computer die als host fungeert voor SQL Server. Wanneer u probeert de agent hand matig te starten, ziet u een dialoog venster met het bericht ' fout 1069: de service is niet gestart vanwege een aanmeldings fout '.

![Het dialoog venster gegevens synchronisatie fout 1069](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **Oorzaak**. Een waarschijnlijke oorzaak van deze fout is dat het wacht woord op de lokale server is gewijzigd sinds u de agent en het wacht woord van de agent hebt gemaakt.

- **Oplossing**. Werk het wacht woord van de agent bij naar uw huidige server wachtwoord:

  1. Zoek de service voor de SQL Data Sync-client agent.  
    a. Selecteer **Starten**.  
    b. Typ **Services. msc**in het zoekvak.  
    c. Selecteer in de zoek resultaten **Services**.  
    d. Schuif in het venster **Services** naar de vermelding voor **SQL Data Sync agent**.  
  1. Klik met de rechter muisknop op **SQL Data Sync agent**en selecteer vervolgens **stoppen**.
  1. Klik met de rechter muisknop op **SQL Data Sync agent**en selecteer vervolgens **Eigenschappen**.
  1. Selecteer op **SQL Data Sync eigenschappen van agent**het tabblad **Aanmelden** .
  1. Voer uw wacht woord in het vak **wacht woord** in.
  1. Voer in het vak **wacht woord bevestigen** het wacht woord opnieuw in.
  1. Selecteer **Apply** en vervolgens **OK**.
  1. Klik in het venster **Services** met de rechter muisknop op de service **SQL Data Sync agent** en klik vervolgens op **starten**.
  1. Sluit het venster **Services** .

### <a name="agent-key"></a>Ik kan de agent sleutel niet verzenden

Nadat u een sleutel voor een agent hebt gemaakt of opnieuw hebt gemaakt, probeert u de sleutel via de toepassing SqlAzureDataSyncAgent te verzenden. De verzen ding kan niet worden voltooid.

![Dialoog venster synchronisatie fout-kan de agent sleutel niet verzenden](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **Vereisten**. Voordat u doorgaat, controleert u de volgende vereisten:

  - De SQL Data Sync Windows-service wordt uitgevoerd.

  - Het service account voor SQL Data Sync Windows-service heeft netwerk toegang.

  - De uitgaande 1433-poort is geopend in uw lokale firewall regel.

  - Het lokale IP-adres wordt toegevoegd aan de server-of database firewall regel voor de meta gegevens database.

- **Oorzaak**. De agent sleutel is een unieke identificatie van elke lokale agent. De sleutel moet voldoen aan twee voor waarden:

  -   De client agent sleutel op de SQL Data Sync-server en de lokale computer moet identiek zijn.
  -   De sleutel van de client agent kan slechts één keer worden gebruikt.

- **Oplossing**. Als uw agent niet werkt, komt dit omdat er niet aan een van deze voor waarden wordt voldaan. Om uw agent opnieuw te laten werken:

  1. Genereer een nieuwe sleutel.
  1. Pas de nieuwe sleutel toe op de agent.

  De nieuwe sleutel Toep assen op de agent:

  1. Ga in Verkenner naar de installatiemap van uw agent. De standaard installatie directory is C:\\programma bestanden (x86)\\micro soft SQL Data Sync.
  1. Dubbel klik op de submap van de opslag locatie.
  1. Open de toepassing SqlAzureDataSyncAgent.
  1. Selecteer **agent sleutel verzenden**.
  1. Plak de sleutel van het klem bord in de beschik bare ruimte.
  1. Selecteer **OK**.
  1. Sluit het programma.

### <a name="agent-delete"></a>De client agent kan niet worden verwijderd uit de portal als de gekoppelde on-premises data base onbereikbaar is

Als een lokaal eind punt (dat wil zeggen, een Data Base) die is geregistreerd bij een SQL Data Sync client agent onbereikbaar wordt, kan de client agent niet worden verwijderd.

- **Oorzaak**. De lokale agent kan niet worden verwijderd omdat de data base die niet kan worden bereikt, nog steeds is geregistreerd bij de agent. Wanneer u probeert de agent te verwijderen, probeert het verwijderings proces de data base te bereiken, wat mislukt.

- **Oplossing**. Gebruik Force Delete om de onbereikbare data base te verwijderen.

> [!NOTE]
> Als de synchronisatie van meta gegevens tabellen behouden blijft na een geforceerd verwijderen, gebruikt u `deprovisioningutil.exe` om deze op te schonen.

### <a name="agent-connect"></a>Lokale synchronisatie agent-app kan geen verbinding maken met de lokale synchronisatie service

- **Oplossing**. Voer de volgende stappen uit:

  1. Sluit de app.  
  1. Open het deel venster Component Services.  
    a. Typ **Services. msc**in het zoekvak op de taak balk.  
    b. Dubbel klik in de zoek resultaten op **Services**.  
  1. Stop de **SQL Data Sync** -service.
  1. Start de **SQL Data Sync** -service opnieuw.  
  1. de app opnieuw openen.

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>De Data Sync-agent uitvoeren vanaf de opdracht prompt

U kunt de volgende opdrachten voor de Data Sync-agent uitvoeren vanaf de opdracht prompt:

### <a name="ping-the-service"></a>De service pingen

#### <a name="usage"></a>Gebruik

```cmd
SqlDataSyncAgentCommand.exe -action pingsyncservice
```

#### <a name="example"></a>Voorbeeld

```cmd
SqlDataSyncAgentCommand.exe -action "pingsyncservice"
```

### <a name="display-registered-databases"></a>Geregistreerde data bases weer geven

#### <a name="usage"></a>Gebruik

```cmd
SqlDataSyncAgentCommand.exe -action displayregistereddatabases
```

#### <a name="example"></a>Voorbeeld

```cmd
SqlDataSyncAgentCommand.exe -action "displayregistereddatabases"
```

### <a name="submit-the-agent-key"></a>De agent sleutel verzenden

#### <a name="usage"></a>Gebruik

```cmd
Usage: SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key]  -username [user name] -password [password]
```

#### <a name="example"></a>Voorbeeld

```cmd
SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key generated from portal, PowerShell, or API] -username [user name to sync metadata database] -password [user name to sync metadata database]
```

### <a name="register-a-database"></a>Een Data Base registreren

#### <a name="usage"></a>Gebruik

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Voorbeelden

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>Registratie van een Data Base ongedaan maken

Wanneer u deze opdracht gebruikt om de registratie van een Data Base ongedaan te maken, wordt de data base volledig door het programma ingericht. Als de data base deel uitmaakt van andere synchronisatie groepen, worden de andere synchronisatie groepen met deze bewerking verbroken.

#### <a name="usage"></a>Gebruik

```cmd
SqlDataSyncAgentCommand.exe -action unregisterdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]
```

#### <a name="example"></a>Voorbeeld

```cmd
SqlDataSyncAgentCommand.exe -action "unregisterdatabase" -serverName localhost -databaseName testdb
```

### <a name="update-credentials"></a>Referenties bijwerken

#### <a name="usage"></a>Gebruik

```cmd
SqlDataSyncAgentCommand.exe -action updatecredential -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Voorbeelden

```cmd
SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication windows -encryption true
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over SQL Data Sync:

-   Overzicht: [Gegevens synchroniseren tussen meerdere cloud- en on-premises databases met SQL Data Sync](sql-database-sync-data.md)
-   Data Sync instellen
    - In de portal: [Zelfstudie: SQL Data Sync instellen om gegevens te synchroniseren tussen Azure SQL Database en SQL Server on-premises](sql-database-get-started-sql-data-sync.md)
    - Met PowerShell
        -  [PowerShell gebruiken om meerdere Azure SQL-databases te synchroniseren](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [PowerShell gebruiken om te synchroniseren tussen een Azure SQL-database en een on-premises database](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Best practices: [Best practices voor Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   SQL Data Sync controleren [met Azure monitor-logboeken](sql-database-sync-monitor-oms.md)
-   Problemen oplossen: [Problemen met Azure SQL Data Sync oplossen](sql-database-troubleshoot-data-sync.md)
-   Het synchronisatieschema bijwerken
    -   Met Transact-SQL: [De replicatie van schemawijzigingen in Azure SQL Data Sync automatiseren](sql-database-update-sync-schema.md)
    -   Met PowerShell: [PowerShell gebruiken voor het bijwerken van het synchronisatieschema in een bestaande synchronisatiegroep](scripts/sql-database-sync-update-schema.md)
