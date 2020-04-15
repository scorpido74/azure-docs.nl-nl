---
title: Gegevenssynchronisatieagent voor SQL-gegevenssynchronisatie
description: Meer informatie over het installeren en uitvoeren van de Data Sync Agent voor Azure SQL Data Sync om gegevens te synchroniseren met on-premises SQL Server-databases
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 39471ebded6280e7d394ee69c2d732b779c9ea50
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380915"
---
# <a name="data-sync-agent-for-azure-sql-data-sync"></a>Gegevenssynchronisatieagent voor Azure SQL-gegevenssynchronisatie

Synchroniseer gegevens met on-premises SQL Server-databases door de Data Sync Agent voor Azure SQL Data Sync te installeren en configureren. Zie [Gegevens synchroniseren in meerdere cloud- en on-premises databases met SQL Data Sync](sql-database-sync-data.md)voor meer informatie over SQL Data Sync.

> [!IMPORTANT]
> Azure SQL Data Sync biedt op dit moment **geen** ondersteuning voor beheerde exemplaren voor Azure SQL Database.

## <a name="download-and-install"></a>Downloaden en installeren

Als u de gegevenssynchronisatieagent wilt downloaden, gaat u naar [SQL Azure Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="install-silently"></a>Stil installeren

Als u de gegevenssynchronisatieagent in stilte wilt installeren vanuit de opdrachtprompt, voert u een opdracht in die vergelijkbaar is met het volgende voorbeeld. Controleer de bestandsnaam van het gedownloade .msi-bestand en geef uw eigen waarden op voor de argumenten **TARGETDIR** en **SERVICEACCOUNT.**

- Als u geen waarde voor **TARGETDIR**opgeeft, `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0`is de standaardwaarde .

- Als u `LocalSystem` de waarde van **SERVICEACCOUNT aangeeft,** gebruikt u SQL Server-verificatie wanneer u de agent configureert om verbinding te maken met de on-premises SQL Server.

- Als u een domeingebruikersaccount of een lokaal gebruikersaccount als waarde van **SERVICEACCOUNT opgeeft,** moet u het wachtwoord ook het argument **SERVICEPASSWORD** opgeven. Bijvoorbeeld `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-sql-server-on-premises"></a>Gegevens synchroniseren met on-premises SQL Server

Zie [Een on-premises SQL Server-database toevoegen](sql-database-get-started-sql-data-sync.md#add-on-prem)als u de gegevenssynchronisatieagent wilt configureren, zodat u gegevens synchroniseren met een of meer on-premises SQL Server-databases.

## <a name="data-sync-agent-faq"></a><a name="agent-faq"></a>Veelgestelde vragen over gegevenssynchronisatieagent

### <a name="why-do-i-need-a-client-agent"></a>Waarom heb ik een cliëntagent nodig?

De SQL Data Sync-service communiceert met SQL Server-databases via de clientagent. Deze beveiligingsfunctie voorkomt directe communicatie met databases achter een firewall. Wanneer de SQL Data Sync-service communiceert met de agent, gebruikt deze dit met behulp van versleutelde verbindingen en een unieke token- of *agentsleutel.* De SQL Server-databases verifiëren de agent met behulp van de verbindingstekenreeks en de agentsleutel. Dit ontwerp biedt een hoog beveiligingsniveau voor uw gegevens.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Hoeveel exemplaren van de gebruikersinterface van de lokale agent kunnen worden uitgevoerd

Er kan slechts één exemplaar van de gebruikersinterface worden uitgevoerd.

### <a name="how-can-i-change-my-service-account"></a>Hoe kan ik mijn serviceaccount wijzigen?

Nadat u een clientagent hebt geïnstalleerd, u het serviceaccount alleen verwijderen en een nieuwe clientagent installeren met het nieuwe serviceaccount.

### <a name="how-do-i-change-my-agent-key"></a>Hoe wijzig ik mijn agentsleutel

Een agentsleutel kan slechts één keer door een agent worden gebruikt. Het kan niet opnieuw worden gebruikt wanneer u verwijdert en vervolgens een nieuwe agent opnieuw installeert, noch kan het door meerdere agents worden gebruikt. Als u een nieuwe sleutel voor een bestaande agent moet maken, moet u er zeker van zijn dat dezelfde sleutel is opgenomen met de clientagent en met de SQL Data Sync-service.

### <a name="how-do-i-retire-a-client-agent"></a>Hoe ga ik met pensioen een cliënt agent

Als u een agent onmiddellijk wilt ongeldig maken of met pensioen wilt laten gaan, regenereert u de sleutel in het portaal, maar dient u deze niet in de agentui. Als u een sleutel regenereert, wordt de vorige sleutel ongeldig gemaakt, ongeacht of de bijbehorende agent online of offline is.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Hoe verplaats ik een clientagent naar een andere computer

Als u de lokale agent vanaf een andere computer wilt uitvoeren dan op dit moment, gaat u als volgt te werk:

1. Installeer de agent op de gewenste computer.
2. Log in op de SQL Data Sync-portal en regenereerde een agentsleutel voor de nieuwe agent.
3. Gebruik de gebruikersinterface van de nieuwe agent om de nieuwe agentsleutel in te dienen.
4. Wacht terwijl de clientagent de lijst downloadt met on-premises databases die eerder zijn geregistreerd.
5. Geef databasereferenties op voor alle databases die als onbereikbaar worden weergegeven. Deze databases moeten bereikbaar zijn vanaf de nieuwe computer waarop de agent is geïnstalleerd.

## <a name="troubleshoot-data-sync-agent-issues"></a><a name="agent-tshoot"></a>Problemen met gegevenssynchronisatieagent oplossen

- [De clientagent die de installatie, installatie of reparatie van de clientagent installeert, verwijdert of herstelt, mislukt](#agent-install)

- [De clientagent werkt niet nadat ik de verwijderen annuleer](#agent-uninstall)

- [Mijn database staat niet in de lijst met agenten](#agent-list)

- [Clientagent start niet (Fout 1069)](#agent-start)

- [Ik kan de agentsleutel niet indienen.](#agent-key)

- [De clientagent kan niet van de portal worden verwijderd als de bijbehorende on-premises database onbereikbaar is](#agent-delete)

- [Lokale Sync Agent-app kan geen verbinding maken met de lokale synchronisatieservice](#agent-connect)

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a><a name="agent-install"></a>De clientagent die de installatie, installatie of reparatie van de clientagent installeert, verwijdert of herstelt, mislukt

- **Oorzaak**. Veel scenario's kunnen deze fout veroorzaken. Als u de specifieke oorzaak van deze fout wilt bepalen, bekijkt u de logboeken.

- **Resolutie**. Als u de specifieke oorzaak van de fout wilt vinden, genereert en bekijkt u de logboeken van Windows Installer. U logboekregistratie inschakelen bij een opdrachtprompt. Als het gedownloade installatiebestand `SQLDataSyncAgent-2.0-x86-ENU.msi`bijvoorbeeld logboekbestanden genereert en onderzoekt met behulp van de volgende opdrachtregels:

  - Voor installaties:`msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - Voor het verwijderen:`msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    U logboekregistratie ook inschakelen voor alle installaties die worden uitgevoerd door Windows Installer. Het Microsoft Knowledge Base-artikel [Hoe u Logboekregistratie van Windows Installer inschakelt,](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) biedt u een oplossing met één klik om logboekregistratie voor Windows Installer in te schakelen. Het biedt ook de locatie van de logs.

### <a name="the-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a><a name="agent-uninstall"></a>De clientagent werkt niet nadat ik de verwijderen annuleer

De clientagent werkt niet, zelfs nadat u de installatie hebt geannuleerd.

- **Oorzaak**. Dit gebeurt omdat de SQL Data Sync-clientagent geen referenties opslaat.

- **Resolutie**. U deze twee oplossingen uitproberen:

    -   Gebruik services.msc om de referenties voor de clientagent opnieuw in te voeren.
    -   Verwijder deze clientagent en installeer vervolgens een nieuwe. Download en installeer de nieuwste clientagent van [Download Center.](https://www.microsoft.com/download/details.aspx?id=27693)

### <a name="my-database-isnt-listed-in-the-agent-list"></a><a name="agent-list"></a>Mijn database staat niet in de lijst met agenten

Wanneer u een bestaande SQL Server-database aan een synchronisatiegroep probeert toe te voegen, wordt de database niet weergegeven in de lijst met agents.

Deze scenario's kunnen dit probleem veroorzaken:

- **Oorzaak**. De clientagent en synchronisatiegroep bevinden zich in verschillende datacenters.

- **Resolutie**. De clientagent en de synchronisatiegroep moeten zich in hetzelfde datacenter bevinden. Om dit in te stellen, hebt u twee opties:

    -   Maak een nieuwe agent in het datacenter waar de synchronisatiegroep zich bevindt. Registreer vervolgens de database bij die agent.
    -   Verwijder de huidige synchronisatiegroep. Maak vervolgens de synchronisatiegroep opnieuw in het datacenter waar de agent zich bevindt.

- **Oorzaak**. De lijst met databases van de clientagent is niet actueel.

- **Resolutie**. Stop en start vervolgens de clientagentservice opnieuw.

    De lokale agent downloadt de lijst met bijbehorende databases alleen bij de eerste indiening van de agentsleutel. Het downloadt niet de lijst van bijbehorende databases op volgende agent belangrijkste inzendingen. Databases die zijn geregistreerd tijdens een agentverplaatsing, worden niet weergegeven in de oorspronkelijke agentinstantie.

### <a name="client-agent-doesnt-start-error-1069"></a><a name="agent-start"></a>Clientagent start niet (Fout 1069)

U ontdekt dat de agent niet wordt uitgevoerd op een computer die SQL Server host. Wanneer u de agent handmatig probeert te starten, ziet u een dialoogvenster met het bericht 'Fout 1069: de service is niet gestart vanwege een aanmeldingsfout'.

![Dialoogvenster Gegevenssynchronisatiefout 1069](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **Oorzaak**. Een waarschijnlijke oorzaak van deze fout is dat het wachtwoord op de lokale server is gewijzigd sinds u het wachtwoord van de agent en de agent hebt gemaakt.

- **Resolutie**. Werk het wachtwoord van de agent bij naar uw huidige serverwachtwoord:

  1. Zoek de SQL Data Sync-clientagentservice.  
    a. Selecteer **Starten**.  
    b. Voer **services.msc**in het zoekvak in.  
    c. Selecteer **Services**in de zoekresultaten .  
    d. Schuif in het venster **Services** naar de vermelding voor **SQL Data Sync Agent**.  
  1. Klik met de rechtermuisknop op **SQL Data Sync Agent**en selecteer **Stoppen**.
  1. Klik met de rechtermuisknop op **SQL Data Sync Agent**en selecteer **Eigenschappen**.
  1. Selecteer op **SQL Data Sync Agent Properties**het tabblad **Aanmelden.**
  1. Voer **in** het vak Wachtwoord uw wachtwoord in.
  1. Voer uw wachtwoord opnieuw in het vak **Wachtwoord bevestigen.**
  1. Selecteer **Apply** en vervolgens **OK**.
  1. Klik in het venster **Services** met de rechtermuisknop op de **SQL Data Sync Agent-service** en klik vervolgens op **Start**.
  1. Sluit het venster **Services.**

### <a name="i-cant-submit-the-agent-key"></a><a name="agent-key"></a>Ik kan de agentsleutel niet indienen.

Nadat u een sleutel voor een agent hebt gemaakt of opnieuw hebt gemaakt, probeert u de sleutel in te dienen via de SqlAzureDataSyncAgent-toepassing. De indiening niet te voltooien.

![Dialoogvenster Synchronisatiefout - Kan agentsleutel niet verzenden](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **Voorwaarden**. Controleer voordat u verder gaat de volgende voorwaarden:

  - De SQL Data Sync Windows-service wordt uitgevoerd.

  - Het serviceaccount voor SQL Data Sync Windows-service heeft netwerktoegang.

  - De uitgaande 1433-poort is geopend in uw lokale firewallregel.

  - Het lokale ip-adres wordt toegevoegd aan de server- of databasefirewallregel voor de database met synchronisatiemetagegevens.

- **Oorzaak**. De agentsleutel identificeert op unieke wijze elke lokale agent. De sleutel moet aan twee voorwaarden voldoen:

  -   De clientagentsleutel op de SQL Data Sync-server en de lokale computer moeten identiek zijn.
  -   De client agent sleutel kan slechts één keer worden gebruikt.

- **Resolutie**. Als uw agent niet werkt, is dit omdat niet aan een of beide voorwaarden wordt voldaan. Ga als u uw agent weer aan het werk zetten:

  1. Genereer een nieuwe sleutel.
  1. Pas de nieuwe sleutel toe op de agent.

  Ga als volgende over de nieuwe sleutel naar de agent:

  1. Ga in Verkenner naar de installatiemap van uw agent. De standaardinstallatiemap is\\C: Program Files\\(x86) Microsoft SQL Data Sync.
  1. Dubbelklik op de submap van de opslaglocatie.
  1. Open de SqlAzureDataSyncAgent-toepassing.
  1. Selecteer **Agent-sleutel verzenden**.
  1. Plak in de meegeleverde ruimte de toets van uw klembord.
  1. Selecteer **OK**.
  1. Sluit het programma.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a><a name="agent-delete"></a>De clientagent kan niet van de portal worden verwijderd als de bijbehorende on-premises database onbereikbaar is

Als een lokaal eindpunt (dat wil zeggen een database) dat is geregistreerd bij een SQL Data Sync-clientagent onbereikbaar wordt, kan de clientagent niet worden verwijderd.

- **Oorzaak**. De lokale agent kan niet worden verwijderd omdat de onbereikbare database nog steeds is geregistreerd bij de agent. Wanneer u de agent probeert te verwijderen, probeert het verwijderingsproces de database te bereiken, wat mislukt.

- **Resolutie**. Gebruik 'force delete' om de onbereikbare database te verwijderen.

> [!NOTE]
> Als synchronisatiemetagegevenstabellen blijven bestaan `deprovisioningutil.exe` na een 'force delete', gebruikt u deze om ze op te schonen.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a><a name="agent-connect"></a>Lokale Sync Agent-app kan geen verbinding maken met de lokale synchronisatieservice

- **Resolutie**. Voer de volgende stappen uit:

  1. Sluit de app af.  
  1. Open het deelvenster Componentservices.  
    a. Voer **services.msc**in het zoekvak op de taakbalk in.  
    b. Dubbelklik in de zoekresultaten op **Services**.  
  1. Stop de **SQL Data Sync-service.**
  1. Start de **SQL Data Sync-service opnieuw.**  
  1. de app opnieuw openen.

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>De gegevenssynchronisatieagent uitvoeren vanuit de opdrachtprompt

U de volgende opdrachten voor gegevenssynchronisatieagent uitvoeren via de opdrachtprompt:

### <a name="ping-the-service"></a>Ping de service

#### <a name="usage"></a>Gebruik

```cmd
SqlDataSyncAgentCommand.exe -action pingsyncservice
```

#### <a name="example"></a>Voorbeeld

```cmd
SqlDataSyncAgentCommand.exe -action "pingsyncservice"
```

### <a name="display-registered-databases"></a>Geregistreerde databases weergeven

#### <a name="usage"></a>Gebruik

```cmd
SqlDataSyncAgentCommand.exe -action displayregistereddatabases
```

#### <a name="example"></a>Voorbeeld

```cmd
SqlDataSyncAgentCommand.exe -action "displayregistereddatabases"
```

### <a name="submit-the-agent-key"></a>De agentsleutel verzenden

#### <a name="usage"></a>Gebruik

```cmd
Usage: SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key]  -username [user name] -password [password]
```

#### <a name="example"></a>Voorbeeld

```cmd
SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key generated from portal, PowerShell, or API] -username [user name to sync metadata database] -password [user name to sync metadata database]
```

### <a name="register-a-database"></a>Een database registreren

#### <a name="usage"></a>Gebruik

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Voorbeelden

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>Een database uitschrijven

Wanneer u deze opdracht gebruikt om een database uit te schrijven, wordt de database volledig gedeprovisiond. Als de database deelneemt aan andere synchronisatiegroepen, wordt de andere synchronisatiegroepen met deze bewerking afgebroken.

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

Zie de volgende artikelen voor meer informatie over SQL Data Sync:

-   Overzicht: [Gegevens synchroniseren tussen meerdere cloud- en on-premises databases met SQL Data Sync](sql-database-sync-data.md)
-   Data Sync instellen
    - In de portal: [Zelfstudie: SQL Data Sync instellen om gegevens te synchroniseren tussen Azure SQL Database en SQL Server on-premises](sql-database-get-started-sql-data-sync.md)
    - Met PowerShell
        -  [PowerShell gebruiken om meerdere Azure SQL-databases te synchroniseren](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [PowerShell gebruiken om te synchroniseren tussen een Azure SQL-database en een on-premises database](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Best practices: [Best practices voor Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   Monitor - [SQL-gegevenssynchronisatie met Azure Monitor-logboeken controleren](sql-database-sync-monitor-oms.md)
-   Problemen oplossen: [Problemen met Azure SQL Data Sync oplossen](sql-database-troubleshoot-data-sync.md)
-   Het synchronisatieschema bijwerken
    -   Met Transact-SQL: [De replicatie van schemawijzigingen in Azure SQL Data Sync automatiseren](sql-database-update-sync-schema.md)
    -   Met PowerShell: [PowerShell gebruiken voor het bijwerken van het synchronisatieschema in een bestaande synchronisatiegroep](scripts/sql-database-sync-update-schema.md)
