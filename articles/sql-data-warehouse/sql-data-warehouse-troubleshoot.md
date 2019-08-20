---
title: Problemen oplossen Azure SQL Data Warehouse | Microsoft Docs
description: Problemen met Azure SQL Data Warehouse oplossen.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 7/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: a6a6fdf6e63bf8c063f8dd6f23ae380e9ce7b98d
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575505"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Problemen met Azure SQL Data Warehouse oplossen
Dit artikel bevat een lijst met veelvoorkomende problemen met de probleem oplossing.

## <a name="connecting"></a>Verbinding maken
| Probleem                                                        | Oplossing                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| De aanmelding is mislukt voor de gebruiker ' NT AUTHORITY\ANONYMOUS-aanmelding '. (Microsoft SQL Server, Error: 18456) | Deze fout treedt op wanneer een AAD-gebruiker probeert verbinding te maken met de hoofd database, maar geen gebruiker heeft in de master-data base.  U kunt dit probleem oplossen door de SQL Data Warehouse op te geven waarmee u verbinding wilt maken op het moment van de verbinding of door de gebruiker toe te voegen aan de hoofd database.  Zie het artikel over [beveiligings overzicht][Security overview] voor meer informatie. |
| De server-principal ' MyUserName ' heeft geen toegang tot de data base ' Master ' in de huidige beveiligings context. Kan de standaard database van de gebruiker niet openen. De aanmelding is mislukt. De aanmelding is mislukt voor de gebruiker MyUserName. (Microsoft SQL Server, Error: 916) | Deze fout treedt op wanneer een AAD-gebruiker probeert verbinding te maken met de hoofd database, maar geen gebruiker heeft in de master-data base.  U kunt dit probleem oplossen door de SQL Data Warehouse op te geven waarmee u verbinding wilt maken op het moment van de verbinding of door de gebruiker toe te voegen aan de hoofd database.  Zie het artikel over [beveiligings overzicht][Security overview] voor meer informatie. |
| CTAIP-fout                                                  | Deze fout kan optreden als er een aanmelding is gemaakt op de SQL Server-hoofd database, maar niet in de SQL Data Warehouse data base.  Als deze fout optreedt, bekijkt u het artikel overzicht van de [beveiliging][Security overview] .  In dit artikel wordt uitgelegd hoe u een aanmelding en gebruiker op Master maakt en hoe u een gebruiker maakt in de SQL Data Warehouse data base. |
| Geblokkeerd door de firewall                                          | Azure SQL-data bases worden beveiligd door firewalls op server-en database niveau om ervoor te zorgen dat alleen bekende IP-adressen toegang hebben tot een Data Base. De firewalls zijn standaard beveiligd. Dit betekent dat u een expliciete en een IP-adres of bereik van adressen moet inschakelen voordat u verbinding kunt maken.  Als u uw firewall voor toegang wilt configureren, volgt u de stappen in de [Server firewall toegang configureren voor uw client-IP][Configure server firewall access for your client IP] in de instructies voor het [inrichten][Provisioning instructions]. |
| Kan geen verbinding maken met het hulp programma of stuur programma                           | SQL Data Warehouse wordt aangeraden [SSMS][SSMS], [SSDT for Visual Studio][SSDT for Visual Studio]of [Sqlcmd][sqlcmd] te gebruiken om uw gegevens op te vragen. Zie [Stuur Programma's voor Azure SQL Data Warehouse][Drivers for Azure SQL Data Warehouse] en [verbinding maken met Azure SQL Data Warehouse][Connect to Azure SQL Data Warehouse] -artikelen voor meer informatie over Stuur Programma's en het maken van verbinding met SQL Data Warehouse. |

## <a name="tools"></a>Hulpprogramma's
| Probleem                                                        | Oplossing                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Er ontbreken AAD-gebruikers in Visual Studio object Explorer           | Dit is een bekend probleem.  Als tijdelijke oplossing kunt u de gebruikers weer geven in [sys. database_principals][sys.database_principals].  Zie [verificatie voor Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse] voor meer informatie over het gebruik van Azure Active Directory met SQL Data Warehouse. |
| Hand matige scripting, met behulp van de wizard scripting of het maken van verbinding via SSMS is traag, reageert niet of levert fouten op | Zorg ervoor dat gebruikers zijn gemaakt in de hoofd database. In script opties moet u er ook voor zorgen dat de engine Edition is ingesteld als ' Microsoft Azure SQL Data Warehouse Edition ' en het type engine is ' Microsoft Azure SQL Database '. |
| Genereren van scripts mislukt in SSMS                               | Het genereren van een script voor SQL Data Warehouse mislukt als de optie script voor afhankelijke objecten genereren is ingesteld op ' True '. Als tijdelijke oplossing moeten gebruikers hand matig naar Hulpprogram Ma's > Opties-> SQL Server-objectverkenner-> script genereren voor afhankelijke opties en ingesteld op ONWAAR |

## <a name="performance"></a>Prestaties
| Probleem                                                        | Oplossing                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Problemen met query prestaties oplossen                            | Als u probeert een bepaalde query op te lossen, begint u met [leren hoe u uw query's kunt bewaken][Learning how to monitor your queries]. |
| Slechte query prestaties en-plannen zijn vaak het resultaat van ontbrekende statistieken | De meest voorkomende oorzaak van slechte prestaties zijn geen statistieken voor uw tabellen.  Zie beheer van [tabel statistieken][Statistics] voor meer informatie over het maken van statistieken en waarom ze essentieel zijn voor uw prestaties. |
| Weinig gelijktijdigheid/query's in wachtrij                             | Inzicht in het [beheer van werk belastingen][Workload management] is belang rijk om te begrijpen hoe geheugen toewijzing met gelijktijdigheid kan worden gebalanceerd. |
| Aanbevolen procedures implementeren                              | De beste manier om te beginnen met het verbeteren van de query prestaties is [SQL Data Warehouse artikel Best practices][SQL Data Warehouse best practices] . |
| Prestaties verbeteren met schalen                      | Soms is de oplossing voor het verbeteren van de prestaties het eenvoudig om meer reken kracht aan uw query's toe te voegen door [uw SQL Data Warehouse te schalen][Scaling your SQL Data Warehouse]. |
| Slechte query prestaties als gevolg van slechte index kwaliteit     | Sommige keer dat query's kunnen vertragen vanwege [slechte kwaliteit van Column Store-indexen][Poor columnstore index quality].  Raadpleeg dit artikel voor meer informatie en hoe u [indexen opnieuw bouwt om de segment kwaliteit te verbeteren][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>Systeem beheer
| Probleem                                                        | Oplossing                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Kan de bewerking niet uitvoeren omdat de server het toegestane quotum van de database transactie-eenheid 45000 overschrijdt. | Verminder de [DWU][DWU] van de data base die u probeert te maken of [vraag een quotum toename][request a quota increase]aan. |
| Ruimte gebruik onderzoeken                              | Zie [tabel grootten][Table sizes] om inzicht te krijgen in het ruimte gebruik van uw systeem. |
| Hulp bij het beheren van tabellen                                    | Zie het artikel [overzicht][Overview] voor meer informatie over het beheren van tabellen.  Dit artikel bevat ook koppelingen naar gedetailleerde onderwerpen, zoals [tabel gegevens typen][Data types], [het distribueren van een tabel][Distribute], [het indexeren][Index]van een tabel, het partitioneren van [een][Partition]tabel, het [onderhouden van tabel statistieken][Statistics] en [tijdelijke tabellen][Temporary]. |
| De voortgangs balk voor transparent Data Encryption (TDE) wordt niet bijgewerkt in de Azure Portal | U kunt de status van TDE bekijken via [Power shell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |


## <a name="differences-from-sql-database"></a>Verschillen van SQL Database
| Probleem                                 | Oplossing                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Niet-ondersteunde SQL Database functies     | Zie [niet-ondersteunde tabel functies][Unsupported table features]. |
| Niet-ondersteunde SQL Database gegevens typen   | Zie [niet-ondersteunde gegevens typen][Unsupported data types].        |
| Beperkingen voor verwijderen en bijwerken         | Zie [oplossingen bijwerken][UPDATE workarounds], [tijdelijke oplossingen verwijderen][DELETE workarounds] en [CTAS gebruiken om niet-ondersteunde syntaxis voor bijwerken en verwijderen te][Using CTAS to work around unsupported UPDATE and DELETE syntax]omzeilen. |
| De instructie MERGe wordt niet ondersteund      | Zie [tijdelijke oplossingen voor samen voegen][MERGE workarounds].                  |
| Beperkingen voor opgeslagen procedures          | Zie [beperkingen van opgeslagen][Stored procedure limitations] procedures voor een overzicht van de beperkingen van opgeslagen procedure. |
| Udf's bieden geen ondersteuning voor SELECT-instructies | Dit is een huidige beperking van onze Udf's.  Zie [Create function][CREATE FUNCTION] voor de syntaxis die we ondersteunen. |

## <a name="next-steps"></a>Volgende stappen
Als u meer informatie wilt over het vinden van oplossingen voor uw probleem, kunt u het volgende proberen.

* [Blogs]
* [Functieverzoeken]
* [Video's]
* [KAT-team blogs]
* [Ondersteuningsticket maken]
* [MSDN-forum]
* [Stack Overflow-forum]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SSMS]: /sql/ssms/download-sql-server-management-studio-ssms
[SSDT for Visual Studio]: sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Ondersteuningsticket maken]: sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: sql-data-warehouse-manage-compute-overview.md
[DWU]: sql-data-warehouse-overview-what-is.md
[request a quota increase]: sql-data-warehouse-get-started-create-support-ticket.md
[Learning how to monitor your queries]: sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: sql-data-warehouse-get-started-provision.md
[SQL Data Warehouse best practices]: sql-data-warehouse-best-practices.md
[Table sizes]: sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: sql-data-warehouse-tables-overview.md
[Data types]: sql-data-warehouse-tables-data-types.md
[Distribute]: sql-data-warehouse-tables-distribute.md
[Index]: sql-data-warehouse-tables-index.md
[Partition]: sql-data-warehouse-tables-partition.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[Temporary]: sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: resource-classes-for-workload-management.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: sql-data-warehouse-develop-ctas.md
[UPDATE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: sql-data-warehouse-authentication.md


<!--MSDN references-->
[sys.database_principals]: /sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql
[CREATE FUNCTION]: /sql/t-sql/statements/create-function-sql-data-warehouse
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[KAT-team blogs]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Functieverzoeken]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Stack Overflow-forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Video's]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[Databricks]: https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse
