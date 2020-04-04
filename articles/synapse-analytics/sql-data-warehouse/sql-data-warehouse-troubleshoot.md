---
title: Problemen oplossen
description: Problemen met Azure Synapse Analytics oplossen.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: b24706943cdf59fba89a8007c4914b628b9e34d5
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632971"
---
# <a name="troubleshooting-sql-analytics-in-azure-synapse"></a>Problemen met SQL Analytics in Azure Synapse oplossen

In dit artikel worden veelvoorkomende probleemoplossingsvragen weergegeven.

## <a name="connecting"></a>Verbinding maken

| Probleem                                                        | Oplossing                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Aanmelding mislukt voor gebruiker 'NT AUTHORITY\ANONYMOUS LOGON'. (Microsoft SQL Server, Fout: 18456) | Deze fout treedt op wanneer een Azure AD-gebruiker verbinding probeert te maken met de hoofddatabase, maar geen gebruiker in master heeft.  Als u dit probleem wilt verhelpen, geeft u de SQL-groep op waarmee u verbinding wilt maken op het moment van verbinding of voegt u de gebruiker toe aan de hoofddatabase.  Zie artikel [over het overzicht sonbeveiliging](sql-data-warehouse-overview-manage-security.md) voor meer informatie. |
| De server-principal 'MijnGebruikersnaam' heeft in de huidige beveiligingscontext geen toegang tot de hoofddatabase. Kan de standaarddatabase van de gebruiker niet openen. Aanmelden mislukt. Aanmelden is mislukt voor gebruiker 'MijnGebruikersnaam'. (Microsoft SQL Server, Fout: 916) | Deze fout treedt op wanneer een Azure AD-gebruiker verbinding probeert te maken met de hoofddatabase, maar geen gebruiker in master heeft.  Als u dit probleem wilt verhelpen, geeft u de SQL-groep op waarmee u verbinding wilt maken op het moment van verbinding of voegt u de gebruiker toe aan de hoofddatabase.  Zie artikel [over het overzicht sonbeveiliging](sql-data-warehouse-overview-manage-security.md) voor meer informatie. |
| CTAIP-fout                                                  | Deze fout kan optreden wanneer een aanmelding is gemaakt in de SQL-servermasterdatabase, maar niet in de SQL-database.  Als u deze fout tegenkomt, neemt u het artikel [beveiligingsoverzicht](sql-data-warehouse-overview-manage-security.md) in.  In dit artikel wordt uitgelegd hoe u een aanmelding en gebruiker op master maakt en hoe u vervolgens een gebruiker in de SQL-database maakt. |
| Geblokkeerd door firewall                                          | SQL-groepen worden beschermd door firewalls om ervoor te zorgen dat alleen bekende IP-adressen toegang hebben tot een database. De firewalls zijn standaard beveiligd, wat betekent dat u expliciet moet inschakelen en IP-adres of bereik van adressen voordat u verbinding maken.  Als u uw firewall wilt configureren voor toegang, voert u de stappen uit in [Serverfirewalltoegang configureren voor uw client-IP](create-data-warehouse-portal.md) in de [inrichtingsinstructies.](create-data-warehouse-portal.md) |
| Kan geen verbinding maken met gereedschap of stuurprogramma                           | Synapse SQL pool raadt aan om [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15), [SSDT voor Visual Studio](sql-data-warehouse-install-visual-studio.md)of [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) te gebruiken om uw gegevens op te vragen. Zie Drivers for Azure Synapse en Verbinding maken met Azure [Synapse](sql-data-warehouse-connect-overview.md) voor meer informatie over stuurprogramma's en verbinding maken met Azure Synapse.For more information on drivers and connecting to Azure Synapse, see Drivers for Azure [Synapse](sql-data-warehouse-connection-strings.md) and Connect to Azure Synapse articles. |

## <a name="tools"></a>Hulpprogramma's

| Probleem                                                        | Oplossing                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Visual Studio-objectexplorer mist Azure AD-gebruikers           | Dit is een bekend probleem.  Bekijk de gebruikers in [sys.database_principals](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=sql-server-ver15)als tijdelijke oplossing.  Zie [Verificatie naar Azure Synapse](sql-data-warehouse-authentication.md) voor meer informatie over het gebruik van Azure Active Directory met Synapse SQL-pool. |
| Handmatig scripten, de wizard scripting gebruiken of verbinding maken via SSMS is traag, reageert niet of produceert geen fouten | Controleer of gebruikers zijn gemaakt in de hoofddatabase. Zorg er bij scriptingopties ook voor dat de engine-editie is ingesteld als "Microsoft Azure SQL Data Warehouse Edition" en het motortype "Microsoft Azure SQL Database". |
| Scripts genereren mislukt in SSMS                               | Het genereren van een script voor Synapse SQL-pool mislukt als de optie 'Script genereren voor afhankelijke objecten' is ingesteld op 'Waar'. Als tijdelijke oplossing moeten gebruikers handmatig naar **Extra->-opties ->SQL Server Object Explorer -> Script genereren voor afhankelijke opties en instellen op false** |

## <a name="performance"></a>Prestaties

| Probleem                                                        | Oplossing                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Problemen met queryprestaties oplossen                            | Als u een bepaalde query probeert op te lossen, begint u met [Leren hoe u uw query's controleren.](sql-data-warehouse-manage-monitor.md#monitor-query-execution) |
| TempDB ruimteproblemen | [Monitor](sql-data-warehouse-manage-monitor.md#monitor-tempdb) tempdb-ruimtegebruik.  Veelvoorkomende oorzaken voor het opraken van TempDB ruimte zijn:<br>- Niet genoeg resources toegewezen aan de query waardoor gegevens te morsen naar TempDB.  Zie [Werkbelastingbeheer](resource-classes-for-workload-management.md) <br>- Statistieken ontbreken of verouderd zijn en veroorzaken overmatige gegevensverplaatsing.  Zie [Tabelstatistieken bijhouden](sql-data-warehouse-tables-statistics.md) voor meer informatie over het maken van statistieken<br>- TempDB-ruimte wordt per serviceniveau toegewezen.  [Als u uw SQL-groep opeenschaalt](sql-data-warehouse-manage-compute-overview.md#scaling-compute) naar een hogere DWU-instelling, wordt meer TempDB-ruimte toegewezen.|
| Slechte queryprestaties en -plannen zijn vaak het gevolg van ontbrekende statistieken | De meest voorkomende oorzaak van slechte prestaties is het ontbreken van statistieken op uw tabellen.  Zie [Tabelstatistieken bijhouden](sql-data-warehouse-tables-statistics.md) voor meer informatie over het maken van statistieken en waarom deze essentieel zijn voor uw prestaties. |
| Lage gelijktijdigheid / query's in de wachtrij                             | Inzicht [in Workload management](resource-classes-for-workload-management.md) is belangrijk om te begrijpen hoe geheugen toewijzing in evenwicht te brengen met gelijktijdigheid. |
| Best practices implementeren                              | De beste plek om manieren te leren om de queryprestaties te verbeteren, is het best [practices-artikel van Synapse SQL-pool.](sql-data-warehouse-best-practices.md) |
| Hoe de prestaties te verbeteren met schalen                      | Soms is de oplossing voor het verbeteren van de prestaties is om gewoon meer rekenkracht toe te voegen aan uw query's door [het schalen van uw SQL-pool.](sql-data-warehouse-manage-compute-overview.md) |
| Slechte queryprestaties als gevolg van slechte indexkwaliteit     | Soms kunnen query's vertragen vanwege [de slechte kolomarchiefindexkwaliteit](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality).  Zie dit artikel voor meer informatie en hoe [u indexen herstellen om de kwaliteit van het segment te verbeteren.](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality) |

## <a name="system-management"></a>Systeembeheer

| Probleem                                                        | Oplossing                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Kon de bewerking niet uitvoeren omdat de server het toegestane databasetransactie-eenheidsquotum van 45000 zou overschrijden. | Verklein de [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) van de database die u probeert te maken of [vraag een quotumverhoging aan.](sql-data-warehouse-get-started-create-support-ticket.md) |
| Ruimtegebruik onderzoeken                              | Zie [Tabelgroottes](sql-data-warehouse-tables-overview.md#table-size-queries) om inzicht te krijgen in het ruimtegebruik van uw systeem. |
| Hulp bij het beheren van tabellen                                    | Zie het artikel [tabeloverzicht](sql-data-warehouse-tables-overview.md) voor hulp bij het beheren van uw tabellen.  Dit artikel bevat ook koppelingen naar meer gedetailleerde onderwerpen zoals [tabelgegevenstypen](sql-data-warehouse-tables-data-types.md), [Het distribueren van een tabel](sql-data-warehouse-tables-distribute.md), Het [indexeren van een tabel](sql-data-warehouse-tables-index.md), het [partitioneren van een tabel](sql-data-warehouse-tables-partition.md), Het [bijhouden van tabelstatistieken](sql-data-warehouse-tables-statistics.md) en [tijdelijke tabellen](sql-data-warehouse-tables-temporary.md). |
| De voortgangsbalk voor transparante gegevensversleuteling (TDE) wordt niet bijgewerkt in de Azure-portal | U de toestand van TDE bekijken via [powershell.](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |

## <a name="differences-from-sql-database"></a>Verschillen met SQL-database

| Probleem                                 | Oplossing                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Niet-ondersteunde SQL-databasefuncties     | Zie [Niet-ondersteunde tabelfuncties](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Niet-ondersteunde SQL-database-gegevenstypen   | Zie [Niet-ondersteunde gegevenstypen](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| Beperkingen verwijderen en bijwerken         | Zie [Tijdelijke oplossingen bijwerken,](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements)Tijdelijke oplossingen verwijderen en [CTAS gebruiken om de syntaxis van niet-ondersteunde UPDATE en Delete te](sql-data-warehouse-develop-ctas.md) [omzeilen](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements) . |
| De instructie MERGE wordt niet ondersteund      | Zie [Tijdelijke oplossingen SAMENVOEGEN](sql-data-warehouse-develop-ctas.md#replace-merge-statements).                  |
| Beperkte opgeslagen procedure          | Zie [Beperkingen van de opgeslagen procedure](sql-data-warehouse-develop-stored-procedures.md#limitations) om enkele van de beperkingen van opgeslagen procedures te begrijpen. |
| UDF's ondersteunen SELECT-instructies niet | Dit is een huidige beperking van onze UDF's.  Zie [FUNCTIE MAKEN](https://docs.microsoft.com/sql/t-sql/statements/create-function-sql-data-warehouse?view=aps-pdw-2016-au7) voor de syntaxis die wij ondersteunen. |

## <a name="next-steps"></a>Volgende stappen

Voor meer hulp bij het vinden van een oplossing voor uw probleem, hier zijn enkele andere bronnen die u proberen.

* [Blogs](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Functieverzoeken](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Video's](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Ondersteuningsticket maken](sql-data-warehouse-get-started-create-support-ticket.md)
* [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse)
* [Stack Overflow-forum](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
