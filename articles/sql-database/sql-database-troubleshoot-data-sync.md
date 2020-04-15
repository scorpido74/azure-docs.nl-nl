---
title: Problemen met SQL Data Sync oplossen
description: Meer informatie over het oplossen van veelvoorkomende problemen met Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: d6ea604446cb9d56bb699685d24c81992bcac3a2
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382899"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Problemen met SQL Data Sync oplossen

In dit artikel wordt beschreven hoe bekende problemen met Azure SQL Data Sync kunnen worden opgelost. Als er een oplossing is voor een probleem, wordt deze hier verstrekt.

Zie [Gegevens synchroniseren tussen meerdere cloud- en on-premises databases met SQL Data Sync](sql-database-sync-data.md) voor een overzicht van SQL Data Sync.

> [!IMPORTANT]
> Azure SQL Data Sync biedt op dit moment **geen** ondersteuning voor beheerde exemplaren voor Azure SQL Database.

## <a name="sync-issues"></a>Synchronisatieproblemen

- [Synchronisatie mislukt in de gebruikersinterface van de portal voor on-premises databases die zijn gekoppeld aan de clientagent](#sync-fails)

- [Mijn synchronisatiegroep zit vast in de verwerkingsstatus](#sync-stuck)

- [Ik zie foutieve gegevens in mijn tabellen](#sync-baddata)

- [Ik zie inconsistente primaire sleutelgegevens na een geslaagde synchronisatie](#sync-pkdata)

- [Ik zie een aanzienlijke verslechtering van de prestaties](#sync-perf)

- [Ik zie dit bericht: "Kan de \<waarde NULL niet invoegen in de kolomkolom>. Column staat geen nullen toe." Wat betekent dit en hoe kan ik het oplossen?](#sync-nulls)

- [Hoe gaat Data Sync om met kringverwijzingen? Dat wil zeggen, wanneer dezelfde gegevens worden gesynchroniseerd in meerdere synchronisatiegroepen, en blijft veranderen als gevolg?](#sync-circ)

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a><a name="sync-fails"></a>Synchronisatie mislukt in de gebruikersinterface van de portal voor on-premises databases die zijn gekoppeld aan de clientagent

Synchronisatie mislukt in de gebruikersinterface van de SQL Data Sync-portal voor on-premises databases die zijn gekoppeld aan de clientagent. Op de lokale computer waarop de agent wordt uitgevoerd, ziet u systeem.IO.IOException-fouten in het gebeurtenislogboek. De fouten zeggen dat de schijf onvoldoende ruimte heeft.

- **Oorzaak**. De drive heeft onvoldoende ruimte.

- **Resolutie**. Maak meer ruimte op het station waarop de map %TEMP% zich bevindt.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a><a name="sync-stuck"></a>Mijn synchronisatiegroep zit vast in de verwerkingsstatus

Een synchronisatiegroep in SQL Data Sync is al lange tijd in de verwerkingsstatus. Het reageert niet op de **stopopdracht** en de logboeken tonen geen nieuwe vermeldingen.

Een van de volgende voorwaarden kan ertoe leiden dat een synchronisatiegroep vast komt te zitten in de verwerkingsstatus:

- **Oorzaak**. De clientagent is offline

- **Resolutie**. Zorg ervoor dat de clientagent online is en probeer het vervolgens opnieuw.

- **Oorzaak**. De clientagent is verwijderd of ontbreekt.

- **Resolutie**. Als de clientagent is verwijderd of om een andere reden ontbreekt:

    1. Verwijder het XML-bestand voor de agent uit de SQL Data Sync-installatiemap, als dit bestand bestaat.
    1. Installeer de agent op een on-premises computer. (Dit kan dezelfde computer zijn of een andere.) Verzend vervolgens de agentsleutel die in de portal is gegenereerd voor de agent die offline is.

- **Oorzaak**. De SQL Data Sync-service is gestopt.

- **Resolutie**. Start de SQL Data Sync-service opnieuw.

    1. Zoek in het menu **Start** naar **Services**.
    1. Selecteer **Services**in de zoekresultaten .
    1. Zoek de **SQL Data Sync-service.**
    1. Als de servicestatus is **gestopt,** klikt u met de rechtermuisknop op de servicenaam en selecteert u **Start**.

> [!NOTE]
> Als de voorgaande informatie uw synchronisatiegroep niet uit de verwerkingsstatus haalt, kan Microsoft Support de status van uw synchronisatiegroep opnieuw instellen. Als u de status van uw synchronisatiegroep wilt laten resetten, maakt u in het [Azure SQL Database-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)een bericht. Vermeld in het bericht uw abonnements-ID en de synchronisatiegroep-id voor de groep die opnieuw moet worden ingesteld. Een Microsoft Support-technicus reageert op je bericht en laat je weten wanneer de status is gereset.

### <a name="i-see-erroneous-data-in-my-tables"></a><a name="sync-baddata"></a>Ik zie foutieve gegevens in mijn tabellen

Als tabellen met dezelfde naam, maar die afkomstig zijn van verschillende databaseschema's, zijn opgenomen in een synchronisatie, ziet u foutieve gegevens in de tabellen na de synchronisatie.

- **Oorzaak**. Het SQL Data Sync-inrichtingsproces gebruikt dezelfde trackingtabellen voor tabellen met dezelfde naam, maar die zich in verschillende schema's bevinden. Hierdoor worden wijzigingen van beide tabellen weergegeven in dezelfde trackingtabel. Dit veroorzaakt foutieve gegevenswijzigingen tijdens het synchroniseren.

- **Resolutie**. Zorg ervoor dat de namen van tabellen die betrokken zijn bij een synchronisatie verschillend zijn, zelfs als de tabellen tot verschillende schema's in een database behoren.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a><a name="sync-pkdata"></a>Ik zie inconsistente primaire sleutelgegevens na een geslaagde synchronisatie

Een synchronisatie wordt gerapporteerd als geslaagd en in het logboek worden geen mislukte of overgeslagen rijen weergegeven, maar u merkt dat primaire sleutelgegevens inconsistent zijn tussen de databases in de synchronisatiegroep.

- **Oorzaak**. Dit resultaat is door het ontwerp. Wijzigingen in een primaire sleutelkolom resulteren in inconsistente gegevens in de rijen waar de primaire sleutel is gewijzigd.

- **Resolutie**. Als u dit probleem wilt voorkomen, moet u ervoor zorgen dat er geen gegevens in een primaire sleutelkolom worden gewijzigd. Als u dit probleem wilt oplossen nadat het is opgetreden, verwijdert u de rij met inconsistente gegevens van alle eindpunten in de synchronisatiegroep. Plaats vervolgens de rij opnieuw.

### <a name="i-see-a-significant-degradation-in-performance"></a><a name="sync-perf"></a>Ik zie een aanzienlijke verslechtering van de prestaties

Uw prestaties degradeeren aanzienlijk, mogelijk tot het punt waarop u de Data Sync UI niet eens openen.

- **Oorzaak**. De meest waarschijnlijke oorzaak is een synchronisatielus. Een synchronisatielus treedt op wanneer een synchronisatie door synchronisatiegroep A een synchronisatie activeert door groep B te synchroniseren, die vervolgens een synchronisatie activeert door synchronisatiegroep A. De werkelijke situatie kan complexer zijn en er kunnen meer dan twee synchronisatiegroepen in de lus bij betrokken zijn. Het probleem is dat er een cirkelvormige triggering van synchronisatie is die wordt veroorzaakt door synchronisatiegroepen die elkaar overlappen.

- **Resolutie**. De beste oplossing is preventie. Zorg ervoor dat u geen kringverwijzingen in uw synchronisatiegroepen hebt. Elke rij die wordt gesynchroniseerd door een synchronisatiegroep, kan niet worden gesynchroniseerd door een andere synchronisatiegroep.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a><a name="sync-nulls"></a>Ik zie dit bericht: "Kan de \<waarde NULL niet invoegen in de kolomkolom>. Column staat geen nullen toe." Wat betekent dit en hoe kan ik het oplossen? 
Dit foutbericht geeft aan dat een van de twee volgende problemen is opgetreden:
-  Een tabel heeft geen primaire sleutel. Als u dit probleem wilt oplossen, voegt u een primaire sleutel toe aan alle tabellen die u synchroniseert.
-  Er is een WHERE-clausule in uw CREATE INDEX-instructie. Data Sync verwerkt deze voorwaarde niet. Als u dit probleem wilt oplossen, verwijdert u de WHERE-component of voert u handmatig de wijzigingen aan in alle databases. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a><a name="sync-circ"></a>Hoe gaat Data Sync om met kringverwijzingen? Dat wil zeggen, wanneer dezelfde gegevens worden gesynchroniseerd in meerdere synchronisatiegroepen, en blijft veranderen als gevolg?
Data Sync verwerkt geen kringverwijzingen. Zorg ervoor dat ze te vermijden. 

## <a name="client-agent-issues"></a>Problemen met clientagent

Zie Problemen met [gegevenssynchronisatieagent oplossen](sql-database-data-sync-agent.md#agent-tshoot)als u problemen met de clientagent wilt oplossen.

## <a name="setup-and-maintenance-issues"></a>Problemen met instellen en onderhoud

- [Ik krijg een "schijf uit de ruimte" bericht](#setup-space)

- [Ik kan mijn synchronisatiegroep niet verwijderen](#setup-delete)

- [Ik kan een on-premises SQL Server-database niet uitschrijven](#setup-unreg)

- [Ik heb niet voldoende bevoegdheden om systeemservices te starten](#setup-perms)

- [Een database heeft een 'verouderd'-status](#setup-date)

- [Een synchronisatiegroep heeft een status 'Verouderd'](#setup-date2)

- [Een synchronisatiegroep kan niet worden verwijderd binnen drie minuten nadat de agent is verwijderd of gestopt](#setup-delete2)

- [Wat gebeurt er als ik een verloren of beschadigde database herstel?](#setup-restore)

### <a name="i-get-a-disk-out-of-space-message"></a><a name="setup-space"></a>Ik krijg een "schijf uit de ruimte" bericht

- **Oorzaak**. Het bericht 'schijf buiten de ruimte' kan worden weergegeven als overgebleven bestanden moeten worden verwijderd. Dit kan worden veroorzaakt door antivirussoftware of bestanden worden geopend wanneer verwijderingsbewerkingen worden geprobeerd.

- **Resolutie**. Verwijder handmatig de synchronisatiebestanden die zich in`del \*sync\* /s`de map %temp% bevinden ( ). Verwijder vervolgens de submappen in de map %temp%.

> [!IMPORTANT]
> Verwijder geen bestanden terwijl de synchronisatie aan de gang is.

### <a name="i-cant-delete-my-sync-group"></a><a name="setup-delete"></a>Ik kan mijn synchronisatiegroep niet verwijderen

Uw poging om een synchronisatiegroep te verwijderen mislukt. Een van de volgende scenario's kan ertoe leiden dat een synchronisatiegroep niet wordt verwijderd:

- **Oorzaak**. De clientagent is offline.

- **Resolutie**. Zorg ervoor dat de clientagent online is en probeer het vervolgens opnieuw.

- **Oorzaak**. De clientagent is verwijderd of ontbreekt.

- **Resolutie**. Als de clientagent is verwijderd of om een andere reden ontbreekt:  
    a. Verwijder het XML-bestand voor de agent uit de SQL Data Sync-installatiemap, als dit bestand bestaat.  
    b. Installeer de agent op een on-premises computer. (Dit kan dezelfde computer zijn of een andere.) Verzend vervolgens de agentsleutel die in de portal is gegenereerd voor de agent die offline is.

- **Oorzaak**. Een database is offline.

- **Resolutie**. Zorg ervoor dat uw SQL-databases en SQL Server-databases allemaal online zijn.

- **Oorzaak**. De synchronisatiegroep is in- of synchronisatie.

- **Resolutie**. Wacht tot het inrichtings- of synchronisatieproces is voltooid en probeer vervolgens de synchronisatiegroep opnieuw te verwijderen.

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a><a name="setup-unreg"></a>Ik kan een on-premises SQL Server-database niet uitschrijven

- **Oorzaak**. Waarschijnlijk probeert u het registreren van een database die al is verwijderd, uit te schrijven.

- **Resolutie**. Als u het registreren van een on-premises SQL Server-database wilt uitschrijven, selecteert u de database en selecteert u **Vervolgens Verwijderen forceren**.

  Als deze bewerking de database niet uit de synchronisatiegroep verwijdert:

  1. De hostservice clientagent stoppen en opnieuw starten:  
    a. Selecteer het menu **Start.**  
    b. Voer **services.msc**in het zoekvak in.  
    c. Dubbelklik in het gedeelte **Programma's** van het deelvenster Zoekresultaten op **Services**.  
    d. Klik met de rechtermuisknop op de **SQL Data Sync-service.**  
    e. Als de service wordt uitgevoerd, stop deze dan.  
    f. Klik met de rechtermuisknop op de service en selecteer **Start**.  
    g. Controleer of de database nog steeds is geregistreerd. Als het niet meer geregistreerd is, bent u klaar. Ga anders verder met de volgende stap.
  1. Open de clientagent-app (SqlAzureDataSyncAgent).
  1. Selecteer **Referenties bewerken**en voer de referenties voor de database in.
  1. Ga verder met uitschrijving.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a><a name="setup-perms"></a>Ik heb niet voldoende bevoegdheden om systeemservices te starten

- **Oorzaak**. Deze fout treedt op in twee situaties:
  -   De gebruikersnaam en/of het wachtwoord zijn onjuist.
  -   Het opgegeven gebruikersaccount heeft niet voldoende bevoegdheden om als service aan te melden.

- **Resolutie**. Log-in-as-a-service-referenties toekennen aan het gebruikersaccount:

  1. Ga naar**Configuratiescherm** > **Beheertools** > lokaal**beveiligingsbeleid** > **Lokaal beleid** > voor**gebruikersrechten beheren** **.** > 
  1. Selecteer **Aanmelden als service**.
  1. Voeg in het dialoogvenster **Eigenschappen** het gebruikersaccount toe.
  1. Selecteer **Apply** en vervolgens **OK**.
  1. Sluit alle ramen.

### <a name="a-database-has-an-out-of-date-status"></a><a name="setup-date"></a>Een database heeft een 'verouderd'-status

- **Oorzaak**. SQL Data Sync verwijdert databases die 45 dagen of langer offline zijn geweest van de service (zoals geteld vanaf het moment dat de database offline is gegaan). Als een database 45 dagen of langer offline is en vervolgens weer online komt, is de status **verouderd.**

- **Resolutie**. U een **verouderde** status voorkomen door ervoor te zorgen dat geen van uw databases 45 dagen of langer offline gaat.

  Als de status van een database **verouderd**is:

  1. Verwijder de database met een **verouderde** status uit de synchronisatiegroep.
  1. Voeg de database weer toe aan de synchronisatiegroep.

  > [!WARNING]
  > U verliest alle wijzigingen in deze database terwijl deze offline was.

### <a name="a-sync-group-has-an-out-of-date-status"></a><a name="setup-date2"></a>Een synchronisatiegroep heeft een status 'Verouderd'

- **Oorzaak**. Als een of meer wijzigingen niet van toepassing zijn voor de gehele bewaartermijn van 45 dagen, kan een synchronisatiegroep verouderd raken.

- **Resolutie**. Als u een **verouderde** status voor een synchronisatiegroep wilt voorkomen, bekijkt u regelmatig de resultaten van uw synchronisatietaken in de geschiedenisviewer. Onderzoek en los eventuele wijzigingen op die niet van toepassing zijn.

  Als de status van een synchronisatiegroep **verouderd**is, verwijdert u de synchronisatiegroep en maakt u deze opnieuw.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a><a name="setup-delete2"></a>Een synchronisatiegroep kan niet worden verwijderd binnen drie minuten nadat de agent is verwijderd of gestopt

U een synchronisatiegroep niet binnen drie minuten verwijderen nadat u de bijbehorende SQL Data Sync-clientagent hebt verwijderd of gestopt.

- **Resolutie**.

  1. Een synchronisatiegroep verwijderen terwijl de bijbehorende synchronisatieagents online zijn (aanbevolen).
  1. Als de agent offline is, maar is geïnstalleerd, brengt u deze online op de on-premises computer. Wacht tot de status van de agent wordt weergegeven als **Online** in de SQL Data Sync-portal. Verwijder vervolgens de synchronisatiegroep.
  1. Als de agent offline is omdat deze is verwijderd:  
    a.  Verwijder het XML-bestand voor de agent uit de SQL Data Sync-installatiemap, als dit bestand bestaat.  
    b.  Installeer de agent op een on-premises computer. (Dit kan dezelfde computer zijn of een andere.) Verzend vervolgens de agentsleutel die in de portal is gegenereerd voor de agent die offline is.  
    c. Probeer de synchronisatiegroep te verwijderen.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a><a name="setup-restore"></a>Wat gebeurt er als ik een verloren of beschadigde database herstel?

Als u een verloren of beschadigde database herstelt van een back-up, is er mogelijk sprake van een niet-convergentie van gegevens in de synchronisatiegroepen waartoe de database behoort.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over SQL Data Sync:

-   Overzicht: [Gegevens synchroniseren tussen meerdere cloud- en on-premises databases met SQL Data Sync](sql-database-sync-data.md)
-   Data Sync instellen
    - In de portal: [Zelfstudie: SQL Data Sync instellen om gegevens te synchroniseren tussen Azure SQL Database en SQL Server on-premises](sql-database-get-started-sql-data-sync.md)
    - Met PowerShell
        -  [PowerShell gebruiken om meerdere Azure SQL-databases te synchroniseren](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [PowerShell gebruiken om te synchroniseren tussen een Azure SQL-database en een on-premises database](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Data Sync-agent: [Data Sync-agent voor Azure SQL Data Sync](sql-database-data-sync-agent.md)
-   Best practices: [Best practices voor Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   Monitor - [SQL-gegevenssynchronisatie met Azure Monitor-logboeken controleren](sql-database-sync-monitor-oms.md)
-   Het synchronisatieschema bijwerken
    -   Met Transact-SQL: [De replicatie van schemawijzigingen in Azure SQL Data Sync automatiseren](sql-database-update-sync-schema.md)
    -   Met PowerShell: [PowerShell gebruiken voor het bijwerken van het synchronisatieschema in een bestaande synchronisatiegroep](scripts/sql-database-sync-update-schema.md)

Zie voor meer informatie over SQL Database:

-   [Wat is de Azure SQL Database-service?](sql-database-technical-overview.md)
-   [Database Lifecycle Management (DLM)](https://msdn.microsoft.com/library/jj907294.aspx)
