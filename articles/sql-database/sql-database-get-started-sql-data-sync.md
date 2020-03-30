---
title: Data Sync instellen
description: In deze zelfstudie ziet u hoe u Azure SQL Data Sync instelt
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 01/14/2019
ms.openlocfilehash: 019ddbac1900856666b958d90b4395f25eb5ee84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268962"
---
# <a name="tutorial-set-up-sql-data-sync-between-azure-sql-database-and-sql-server-on-premises"></a>Zelfstudie: SQL-gegevenssynchronisatie instellen tussen Azure SQL Database en SQL Server on-premises

In deze zelfstudie leert u hoe u Azure SQL Data Sync instelt door een synchronisatiegroep te maken die zowel Azure SQL Database- als SQL Server-exemplaren bevat. De synchronisatiegroep is aangepast geconfigureerd en synchroniseert volgens het schema dat u instelt.

De zelfstudie gaat ervan uit dat u ten minste enige eerdere ervaring hebt met SQL Database en SQL Server.

Zie [Gegevens synchroniseren in cloud- en on-premises databases met Azure SQL Data Sync](sql-database-sync-data.md)voor een overzicht van SQL Data Sync.

Zie [Synchroniseren tussen Azure SQL-databases](scripts/sql-database-sync-data-between-sql-databases.md) of [een Azure SQL-database en een on-premises SQL Server-database voor PowerShell-voorbeelden](scripts/sql-database-sync-data-between-azure-onprem.md) over het configureren van SQL-gegevenssynchronisatie.

> [!IMPORTANT]
> Azure SQL Data Sync biedt op dit moment **geen** ondersteuning voor beheerde exemplaren voor Azure SQL Database.

## <a name="create-sync-group"></a>Synchronisatiegroep maken

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw SQL-database te vinden. Zoeken naar **SQL-databases**en selecteer deze.

    ![Zoeken naar SQL-databases, Microsoft Azure-portal](media/sql-database-get-started-sql-data-sync/search-for-sql-databases.png)

1. Selecteer de database die u wilt gebruiken als hubdatabase voor Gegevenssynchronisatie.

    ![Kiezen uit SQL-databaselijst, Microsoft Azure-portal](media/sql-database-get-started-sql-data-sync/select-sql-database.png)

    > [!NOTE]
    > De hubdatabase is het centrale eindpunt van een synchronisatietopologie, waarin een synchronisatiegroep meerdere databaseeindpunten heeft. Alle andere liddatabases met eindpunten in de synchronisatiegroep worden gesynchroniseerd met de hubdatabase.

1. Selecteer **Synchroniseren met andere databases**in het **SQL-databasemenu** voor de geselecteerde database.

    ![Synchroniseren met andere databases, SQL-database, Microsoft Azure-portal](media/sql-database-get-started-sql-data-sync/sync-to-other-databases.png)

1. Selecteer op de pagina **Synchroniseren met andere databases** de optie Nieuwe **synchronisatiegroep**. De pagina **Nieuwe synchronisatiegroep** wordt geopend met **synchronisatiegroep maken (stap 1)** gemarkeerd.

   ![Stap 1-instellingen](media/sql-database-get-started-sql-data-sync/stepone.png)

   Wijzig op de pagina **Groep gegevenssynchronisatie maken** de volgende instellingen:

   | Instelling                        | Beschrijving |
   | ------------------------------ | ------------------------------------------------- |
   | **Groepsnaam synchroniseren** | Voer een naam in voor de nieuwe synchronisatiegroep. Deze naam onderscheidt zich van de naam van de database zelf. |
   | **Metagegevensdatabase synchroniseren** | Kies ervoor om een database te maken (aanbevolen) of om een bestaande database te gebruiken.<br/><br/>Als u **Nieuwe database**kiest, selecteert u Nieuwe database **maken.** Vervolgens op de **pagina SQL Database,** naam en configureren van de nieuwe database en selecteer **OK**.<br/><br/>Als u **Bestaande database gebruiken**kiest, selecteert u de database in de lijst. |
   | **Automatische synchronisatie** | Selecteer **Aan** of **uit**.<br/><br/>Als u **Aan**kiest, voert u een getal in en selecteert u **Seconden**, **Minuten,** **Uren**of **Dagen** in de sectie **Frequentie synchroniseren.** |
   | **Conflictoplossing** | Selecteer **Hub-winst** of **winnen van lid**.<br/><br/>**Hubwin** betekent dat wanneer er conflicten optreden, gegevens in de hubdatabase conflicterende gegevens in de ledendatabase overschrijven.<br/><br/>**Lid winst** betekent dat wanneer conflicten optreden, gegevens in de ledendatabase conflicterende gegevens in de hubdatabase overschrijven. |

   > [!NOTE]
   > Microsoft raadt aan om een nieuwe, lege database te maken voor gebruik als de **database met metagegevens synchroniseren.** Data Sync maakt tabellen in deze database en voert een frequente werkbelasting uit. Deze database wordt gedeeld als de **database met synchronisatiemetagegevens** voor alle synchronisatiegroepen in een geselecteerd gebied en u de database of de naam ervan niet wijzigen zonder alle synchronisatiegroepen en synchronisatieagents in de regio te verwijderen.

   Selecteer **OK** en wacht tot de synchronisatiegroep is gemaakt en geïmplementeerd.

## <a name="add-sync-members"></a>Synchronisatieleden toevoegen

Nadat de nieuwe synchronisatiegroep is gemaakt en geïmplementeerd, wordt **Synchronisatieleden toevoegen (stap 2)** gemarkeerd op de pagina **Nieuwe synchronisatiegroep.**

Voer in de sectie **Hub Database** bestaande referenties in voor de SQL Database-server waarop de hubdatabase zich bevindt. Voer geen *nieuwe* referenties in deze sectie in.

![Stap 2-instellingen](media/sql-database-get-started-sql-data-sync/steptwo.png)

### <a name="to-add-an-azure-sql-database"></a>Een Azure SQL-database toevoegen

Voeg in de sectie **Member Database** optioneel een Azure SQL Database toe aan de synchronisatiegroep door Een Azure SQL Database **toevoegen te**selecteren. De pagina **Azure SQL Database configureren** wordt geopend.

  ![Stap 2 - database configureren](media/sql-database-get-started-sql-data-sync/steptwo-configure.png)

  Wijzig op de pagina **Azure SQL Database configureren** de volgende instellingen:

  | Instelling                       | Beschrijving |
  | ----------------------------- | ------------------------------------------------- |
  | **Naam van lid synchroniseren** | Geef een naam op voor het nieuwe synchronisatielid. Deze naam onderscheidt zich van de databasenaam zelf. |
  | **Abonnement** | Selecteer het bijbehorende Azure-abonnement voor factureringsdoeleinden. |
  | **Azure SQL Server** | Selecteer de bestaande SQL Database-server. |
  | **Azure SQL Database** | Selecteer de bestaande SQL-database. |
  | **Routebeschrijvingen synchroniseren** | Selecteer **Bidirectionele synchronisatie** **naar de hub**of van **de hub**. |
  | **Gebruikersnaam** en **wachtwoord** | Voer de bestaande referenties in voor de SQL Database-server waarop de ledendatabase zich bevindt. Voer geen *nieuwe* referenties in deze sectie in. |

  Selecteer **OK** en wacht tot het nieuwe synchronisatielid is gemaakt en geïmplementeerd.

<a name="add-on-prem"></a>
### <a name="to-add-an-on-premises-sql-server-database"></a>Een on-premises SQL Server-database toevoegen

Voeg in de sectie **Member Database** optioneel een on-premises SQL Server toe aan de synchronisatiegroep door **Een on-premises database toevoegen te**selecteren. De pagina **On-premises configureren** wordt geopend waar u de volgende dingen doen:

1. Selecteer **De gateway voor synchronisatieagent kiezen**. De pagina **Synchronisatieagent selecteren** wordt geopend.

   ![Een synchronisatieagent maken](media/sql-database-get-started-sql-data-sync/steptwo-agent.png)

1. Kies op de pagina **De synchronisatieagent** kiezen of u een bestaande agent wilt gebruiken of een agent wilt maken.

   Als u **Bestaande agents**kiest, selecteert u de bestaande agent in de lijst.

   Als u **Een nieuwe agent maken**kiest, gaat u als volgt te werk:

   1. Download de gegevenssynchronisatieagent van de meegeleverde koppeling en installeer deze op de computer waar de SQL Server zich bevindt. U de agent ook rechtstreeks downloaden van [SQL Azure Data Sync Agent.](https://www.microsoft.com/download/details.aspx?id=27693)

      > [!IMPORTANT]
      > U moet uitgaande TCP-poort 1433 openen in de firewall om de clientagent met de server te laten communiceren.

   1. Voer een naam in voor de agent.

   1. Selecteer **Sleutel maken en genereren** en kopieer de agentsleutel naar het klembord.

   1. Selecteer **OK** om de pagina **Synchronisatieagent selecteren** te sluiten.

1. Zoek en voer de clientsyncagent-app uit op de SQL Server-computer.

   ![De app voor de clientagent voor gegevenssynchronisatie](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    1. Selecteer **agent-sleutel verzenden**in de app voor synchronisatieagent . Het dialoogvenster **Configuratie van de databaseconfiguratie met metagegevens** synchroniseren wordt geopend.

    1. Plak in het dialoogvenster **Configuratie van metagegevensdatabase configureren** synchroniseren de agentsleutel in die is gekopieerd van de Azure-portal. Geef ook de bestaande referenties op voor de Azure SQL Database-server waarop de metagegevensdatabase zich bevindt. (Als u een metagegevensdatabase hebt gemaakt, bevindt deze database zich op dezelfde server als de hubdatabase.) Selecteer **OK** en wacht tot de configuratie is voltooid.

        ![Voer de agentsleutel en serverreferenties in](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Als er een firewallfout optreedt, maakt u een firewallregel op Azure om binnenkomend verkeer vanaf de SQL Server-computer toe te staan. U de regel handmatig maken in de portal of in SQL Server Management Studio (SSMS). Maak in SSMS verbinding met de hubdatabase op Azure door de naam in te voeren als <hub_database_name>.database.windows.net.

    1. Selecteer **Registreren** om een SQL Server-database bij de agent te registreren. Het dialoogvenster **SQL Server-configuratie** wordt geopend.

        ![Een SQL Server-database toevoegen en configureren](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    1. Kies in het dialoogvenster **SQL Server-configuratie** de optie om verbinding te maken met SQL Server-verificatie of Windows-verificatie. Als u SQL Server-verificatie kiest, voert u de bestaande referenties in. Geef de SQL Server-naam en de naam op van de database die u wilt synchroniseren en selecteer **Testverbinding** om uw instellingen te testen. Selecteer vervolgens **Opslaan** en de geregistreerde database wordt in de lijst weergegeven.

        ![SQL Server-database is nu geregistreerd](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    1. Sluit de clientsyncagent-app.

1. Selecteer in de portal op de pagina **On-premises configureren** de optie **Selecteer de database**.

1. Geef op de pagina **Database selecteren** in het veld Naam van **leden synchroniseren** een naam op voor het nieuwe synchronisatielid. Deze naam onderscheidt zich van de naam van de database zelf. Selecteer de database in de lijst. Selecteer in het veld **Synchronisatierichtingen** de optie **Bidirectionele synchronisatie**, **Naar de hub**of vanuit de **hub**.

    ![De on-premises database selecteren](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

1. Selecteer **OK** om de pagina **Database selecteren** te sluiten. Selecteer **vervolgens OK** om de pagina **On-premises configureren** te sluiten en te wachten tot het nieuwe synchronisatielid is gemaakt en geïmplementeerd. Selecteer tot slot **OK** om de pagina **Synchronisatieleden selecteren** te sluiten.

> [!NOTE]
> Als u verbinding wilt maken met SQL Data Sync en de lokale agent, voegt u uw gebruikersnaam toe aan de rol *DataSync_Executor*. Met Data Sync wordt deze rol gemaakt op de SQL Server-instantie.

## <a name="configure-sync-group"></a>Synchronisatiegroep configureren

Nadat de nieuwe synchronisatiegroepleden zijn gemaakt en geïmplementeerd, wordt **synchronisatiegroep configureren (stap 3)** gemarkeerd op de pagina **Nieuwe synchronisatiegroep.**

![Stap 3-instellingen](media/sql-database-get-started-sql-data-sync/stepthree.png)

1. Selecteer **op** de pagina Tabellen een database in de lijst met leden van de synchronisatiegroep en selecteer **Schema vernieuwen**.

1. Selecteer in de lijst de tabellen die u wilt synchroniseren. Standaard zijn alle kolommen geselecteerd, dus schakel het selectievakje uit voor de kolommen die u niet wilt synchroniseren. Zorg ervoor dat u de primaire sleutelkolom geselecteerd laat.

1. Selecteer **Opslaan**.

1. Standaard worden databases niet gesynchroniseerd totdat ze zijn gepland of handmatig worden uitgevoerd. Als u een handmatige synchronisatie wilt uitvoeren, navigeert u naar uw SQL-database in de Azure-portal, selecteert u **Synchroniseren met andere databases**en selecteert u de synchronisatiegroep. De pagina **Gegevenssynchronisatie** wordt geopend. Selecteer **Synchroniseren**.

    ![Handmatig e-ermee synchroniseren](media/sql-database-get-started-sql-data-sync/datasync-sync.png)

## <a name="faq"></a>Veelgestelde vragen

**Hoe vaak kan Data Sync mijn gegevens synchroniseren?**

De minimale duur tussen synchronisaties is vijf minuten.

**Maakt SQL Data Sync volledig tabellen?**

Als synchronisatieschematabellen ontbreken in de doeldatabase, worden ze in SQL Data Sync gemaakt met de kolommen die u hebt geselecteerd. Dit resulteert echter niet in een volledig getrouw heidsschema om de volgende redenen:

- Alleen kolommen die u selecteert, worden gemaakt in de doeltabel. Kolommen die niet zijn geselecteerd, worden genegeerd.
- Alleen geselecteerde kolomindexen worden gemaakt in de doeltabel. Voor niet geselecteerde kolommen worden deze indexen genegeerd.
- Indexen op XML-typekolommen worden niet gemaakt.
- CHECK-beperkingen worden niet gemaakt.
- Triggers op de brontabellen worden niet gemaakt.
- Weergaven en opgeslagen procedures worden niet gemaakt.

Vanwege deze beperkingen raden we u het volgend aan:

- Maak voor productieomgevingen zelf het full-fidelity schema.
- Wanneer u met de service experimenteert, gebruikt u de functie voor automatisch inrichten.

**Waarom zie ik tabellen die ik niet heb gemaakt?**

Data Sync maakt extra tabellen in de database voor het bijhouden van wijzigingen. Verwijder deze niet of Data Sync werkt niet meer.

**Zijn mijn gegevens convergent na een synchronisatie?**

Niet per se. Neem een synchronisatiegroep met een hub en drie spaken (A, B en C) waarbij synchronisaties hub naar A, Hub naar B en Hub naar C zijn. Als er een wijziging wordt aangebracht in database A *na* de synchronisatie van de hub naar A, wordt deze wijziging pas in database B of database C geschreven tot de volgende synchronisatietaak.

**Hoe krijg ik schemawijzigingen in een synchronisatiegroep?**

Alle schemawijzigingen handmatig wijzigen en doorgeven.

1. Repliceer de schemawijzigingen handmatig naar de hub en naar alle synchronisatieleden.
1. Werk het synchronisatieschema bij.

Ga als bedoeld als het volgende over het toevoegen van nieuwe tabellen en kolommen:

Nieuwe tabellen en kolommen hebben geen invloed op de huidige synchronisatie en Data Sync negeert deze totdat ze zijn toegevoegd aan het synchronisatieschema. Wanneer u nieuwe databaseobjecten toevoegt, volgt u de volgende volgorde:

1. Voeg nieuwe tabellen of kolommen toe aan de hub en aan alle synchronisatieleden.
1. Voeg nieuwe tabellen of kolommen toe aan het synchronisatieschema.
1. Begin met het invoegen van waarden in de nieuwe tabellen en kolommen.

Ga als u het gegevenstype van een kolom wijzigt:

Wanneer u het gegevenstype van een bestaande kolom wijzigt, blijft Gegevenssynchronisatie werken zolang de nieuwe waarden passen bij het oorspronkelijke gegevenstype dat in het synchronisatieschema is gedefinieerd. Als u bijvoorbeeld het type in de brondatabase wijzigt van **int** naar **bigint,** blijft Data Sync werken totdat u een waarde invoegt die te groot is voor het **int-gegevenstype.** Als u de wijziging wilt voltooien, repliceert u de schemawijziging handmatig naar de hub en naar alle synchronisatieleden en werkt u het synchronisatieschema bij.

**Hoe kan ik een database exporteren en importeren met Data Sync?**

Nadat u een database hebt geëxporteerd als een *.bacpac-bestand* en het bestand hebt geïmporteerd om een database te maken, gaat u als volgt te werk om Gegevenssynchronisatie in de nieuwe database te gebruiken:

1. Ruim de gegevenssynchronisatieobjecten en extra tabellen in de nieuwe database op met [dit script](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). Het script verwijdert alle vereiste Gegevenssynchronisatie-objecten uit de database.
1. Maak de synchronisatiegroep opnieuw met de nieuwe database. Als u de oude synchronisatiegroep niet meer nodig hebt, verwijdert u deze.

**Waar kan ik informatie vinden over de cliëntagent?**

Zie [Agent FAQ voor](sql-database-data-sync-agent.md#agent-faq)veelgestelde vragen over de clientagent.

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd! U hebt een synchronisatiegroep gemaakt die zowel een SQL Database-exemplaar als een SQL Server-database bevat.

Zie de volgende onderwerpen voor meer informatie over SQL Data Sync:

- [Gegevenssynchronisatieagent voor Azure SQL-gegevenssynchronisatie](sql-database-data-sync-agent.md)
- [Aanbevolen procedures](sql-database-best-practices-data-sync.md) en [problemen met Azure SQL Data Sync oplossen](sql-database-troubleshoot-data-sync.md)
- [SQL-gegevenssynchronisatie met Azure Monitor-logboeken bewaken](sql-database-sync-monitor-oms.md)
- [Het synchronisatieschema bijwerken met Transact-SQL](sql-database-update-sync-schema.md) of [PowerShell](scripts/sql-database-sync-update-schema.md)

Zie de volgende onderwerpen voor meer informatie over SQL Database:

- [Wat is de Azure SQL Database-service?](sql-database-technical-overview.md)
- [Database Lifecycle Management (DLM)](https://msdn.microsoft.com/library/jj907294.aspx)
