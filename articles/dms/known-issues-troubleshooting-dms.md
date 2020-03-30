---
title: Veelvoorkomende problemen - Azure Database Migration Service
description: Meer informatie over het oplossen van veelvoorkomende bekende problemen/fouten die zijn gekoppeld aan het gebruik van Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: c5d2ad481124f5ae048d010cdf632ee661bbd6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649104"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Veelvoorkomende problemen en fouten van azure databasemigratieservice oplossen

In dit artikel worden enkele veelvoorkomende problemen en fouten beschreven die gebruikers van Azure Database Migration Service kunnen tegenkomen. Het artikel bevat ook informatie over het oplossen van deze problemen en fouten.

## <a name="migration-activity-in-queued-state"></a>Migratieactiviteit in wachtrijstatus

Wanneer u nieuwe activiteiten maakt in een Azure Database Migration Service-project, blijven de activiteiten in de wachtrijstaan.

| Oorzaak         | Oplossing |
| ------------- | ------------- |
| Dit probleem treedt op wanneer de instantie Azure Database Migration Service de maximale capaciteit heeft bereikt voor lopende taken die gelijktijdig worden uitgevoerd. Elke nieuwe activiteit wordt in de wachtrij geplaatst totdat de capaciteit beschikbaar is. | Het exemplaar Van de gegevensmigratieservice valideren en activiteiten uitvoeren voor projecten. U doorgaan met het maken van nieuwe activiteiten die automatisch worden toegevoegd aan de wachtrij voor uitvoering. Zodra een van de bestaande lopende activiteiten is voltooid, wordt de volgende activiteit in de wachtrij gestart en wordt de status automatisch gewijzigd in de status. U hoeft geen extra actie te ondernemen om de migratie van activiteiten in de wachtrij te starten.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Maximum aantal databases geselecteerd voor migratie

De volgende fout treedt op bij het maken van een activiteit voor een databasemigratieproject voor het verplaatsen naar Azure SQL Database of een Azure SQL Database-beheerde instantie:

* **Fout**: Validatiefout migratie-instellingen", 'errorDetail':"Meer dan maximaal nummer '4' objecten van 'Databases' is geselecteerd voor migratie."

| Oorzaak         | Oplossing |
| ------------- | ------------- |
| Met deze fout wordt weergegeven wanneer u meer dan vier databases hebt geselecteerd voor één migratieactiviteit. Op dit moment is elke migratieactiviteit beperkt tot vier databases. | Selecteer vier of minder databases per migratieactiviteit. Als u meer dan vier databases parallel moet migreren, indient u een ander exemplaar van Azure Database Migration Service. Momenteel ondersteunt elk abonnement maximaal twee Exemplaren van Azure Database Migration Service.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Fouten voor MySQL-migratie naar Azure MySQL met herstelfouten

Wanneer u migreert van MySQL naar Azure Database voor MySQL met Azure Database Migration Service, mislukt de migratieactiviteit met de volgende fout:

* **Fout**: Databasemigratiefout - Taak -taakID is opgeschort vanwege opeenvolgende herstelfouten.

| Oorzaak         | Oplossing |
| ------------- | ------------- |
| Deze fout kan optreden wanneer de gebruiker die de migratie doet, replicatiebeheerdersrol en/of bevoegdheden van replicatieclient, replicatiereplica en SUPER mist (versies eerder dan MySQL 5.6.6).<br><br><br><br><br><br><br><br><br><br><br><br><br> | Zorg ervoor dat de [vereiste bevoegdheden](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) voor het gebruikersaccount nauwkeurig zijn geconfigureerd in de Azure Database voor MySQL-instantie. De volgende stappen kunnen bijvoorbeeld worden gevolgd om een gebruiker met de naam 'migrateuser' met vereiste bevoegdheden te maken:<br>1. MAAK DE GEBRUIKER migrateuser@'%' AANGEDUID MET "geheim"; <br>2. Alle bevoegdheden op db_name toekennen.* aan 'migrateuser'@'%' die door 'geheim' zijn geïdentificeerd; herhaal deze stap om toegang te verlenen aan meer databases <br>3. Subsidie replicatie slaaf op *.* naar "migrateuser'@'%" geïdentificeerd door "geheim";<br>4. Replicatieclient verlenen op *.* naar "migrateuser'@'%" geïdentificeerd door "geheim";<br>5. Doorspoelrechten; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Fout bij het stoppen van Azure Database Migration Service

U ontvangt de volgende fout bij het stoppen van de instantie Azure Database Migration Service:

* **Fout:** Service kan niet stoppen. Fout: {'error':{'code':'InvalidRequest','message':'Een of meer activiteiten worden momenteel uitgevoerd. Als u de service wilt stoppen, wacht u tot de activiteiten zijn voltooid of stopt u deze activiteiten handmatig en probeert u het opnieuw.'}}

| Oorzaak         | Oplossing |
| ------------- | ------------- |
| Met deze fout wordt weergegeven wanneer de service-instantie die u probeert te stoppen, activiteiten bevat die nog steeds worden uitgevoerd of die aanwezig zijn in migratieprojecten. <br><br><br><br><br><br> | Controleer of er geen activiteiten worden uitgevoerd in het geval van Azure Database Migration Service die u probeert te stoppen. U de activiteiten of projecten ook verwijderen voordat u probeert de service te stoppen. In de volgende stappen u zien hoe u projecten verwijdert om de instantie van de migratieservice op te schonen door alle actieve taken te verwijderen:<br>1. Installatiemodule -Naam AzureRM.DataMigration <br>2. Login-AzurermAccount <br>3. SubName> > Select-AzureRmSubscription -SubscriptionName\<" subNaam>" <br> 4. Remove-AzureRmDataMigrationProject \<-Name projectName> \<-ResourceGroupName \<rgName> -ServiceName serviceName> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Fout bij het starten van Azure Database Migration Service

U ontvangt de volgende fout bij het starten van de instantie Azure Database Migration Service:

* **Fout:** Service kan niet worden gestart. Fout: {'errorDetail':'De service kan niet worden gestart, neem contact op met Microsoft-ondersteuning'}

| Oorzaak         | Oplossing |
| ------------- | ------------- |
| Met deze fout wordt weergegeven wanneer de vorige instantie intern is mislukt. Deze fout komt zelden voor en het technische team is zich ervan bewust. <br> | Verwijder de instantie van de service die u niet starten en vervolgens nieuwe service om deze te vervangen. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Fout bij het herstellen van database tijdens het migreren van SQL naar Azure SQL DB-beheerde instantie

Wanneer u een onlinemigratie uitvoert van SQL Server naar een azure SQL Database-beheerde instantie, mislukt de cutover met de volgende fout:

* **Fout**: Herstelbewerking is mislukt voor de 'operationId'.Error : Restore Operation failed for operation Id 'operationId'. Code 'AuthorizationFailed', Message 'The client 'clientId' with object id 'objectId' heeft geen autorisatie om actie uit te voeren 'Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read' over scope '/subscriptions/subscriptionId'.'.

| Oorzaak         | Oplossing    |
| ------------- | ------------- |
| Deze fout geeft aan dat de hoofdsom van de toepassing die wordt gebruikt voor onlinemigratie van SQL Server naar een azure SQL Database-beheerde instantie geen contribute-toestemming voor het abonnement heeft. Voor bepaalde API-aanroepen met Managed Instance is deze machtiging vereist voor het abonnement voor de herstelbewerking. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Gebruik `Get-AzureADServicePrincipal` de PowerShell-cmdlet met `-ObjectId` beschikbaar in het foutbericht om de weergavenaam van de toepassings-id die wordt gebruikt weer te geven.<br><br> Valideer de machtigingen voor deze toepassing en zorg ervoor dat deze de [rol van bijdrager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) heeft op abonnementsniveau. <br><br> Het Azure Database Migration Service Engineering Team werkt eraan om de vereiste toegang van de huidige contribute-rol bij het abonnement te beperken. Als u een zakelijke vereiste hebt die het gebruik van de contribute-rol niet toestaat, neemt u contact op met Azure-ondersteuning voor extra hulp. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Fout bij het verwijderen van NIC die is gekoppeld aan Azure Database Migration Service

Wanneer u een netwerkinterfacekaart probeert te verwijderen die is gekoppeld aan Azure Database Migration Service, mislukt de verwijderingspoging met deze fout:

* **Fout:** Kan de NIC die is gekoppeld aan Azure Database Migration Service niet verwijderen omdat de DMS-service gebruik maakt van de NIC

| Oorzaak         | Oplossing    |
| ------------- | ------------- |
| Dit probleem treedt op wanneer de instantie Azure Database Migration Service nog steeds aanwezig is en de NIC verbruikt. <br><br><br><br><br><br><br><br> | Als u deze NIC wilt verwijderen, verwijdert u de dms-serviceinstantie die automatisch de NIC verwijdert die door de service wordt gebruikt.<br><br> **Belangrijk:** controleer of de instantie Azure Database Migration Service die wordt verwijderd, geen bedrijfsactiviteiten heeft.<br><br> Nadat alle projecten en activiteiten die zijn gekoppeld aan de instantie Azure Database Migration Service zijn verwijderd, u de service-instantie verwijderen. De NIC die door de service-instantie wordt gebruikt, wordt automatisch gereinigd als onderdeel van het verwijderen van de service. |

## <a name="connection-error-when-using-expressroute"></a>Verbindingsfout bij het gebruik van ExpressRoute

Als u verbinding wilt maken met de bron in de wizard Azure Database Migration-serviceproject, mislukt de verbinding na een langere time-out, als de bron gebruikmaakt van ExpressRoute voor connectiviteit.

| Oorzaak         | Oplossing    |
| ------------- | ------------- |
| Wanneer u [ExpressRoute](https://azure.microsoft.com/services/expressroute/)gebruikt, [vereist](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) Azure Database Migration Service het inrichten van drie serviceeindpunten in het subnet Van het Virtuele Netwerk dat aan de service is gekoppeld:<br> -- Eindpunt servicebus<br> -- Eindpunt opslag<br> -- Doeldatabaseeindpunt (bijvoorbeeld SQL-eindpunt, Cosmos DB-eindpunt)<br><br><br><br><br> | [Schakel](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) de vereiste serviceeindpunten in voor ExpressRoute-connectiviteit tussen bron en Azure Database Migration Service. <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>Fout bij het vergrendelen van een time-out bij het migreren van een MySQL-database naar Azure DB voor MySQL vergrendelen

Wanneer u een MySQL-database migreert naar een Azure Database voor MySQL-instantie via Azure Database Migration Service, mislukt de migratie met een time-outfout voor het volgende vergrendelen:

* **Fout**: Databasemigratiefout - Kan bestand niet laden - Kan het laadproces voor bestand 'n' RetCode niet starten: SQL_ERROR SqlState: HY000 NativeError: 1205 Message: [MySQL][ODBC Driver][mysqld] Lock wait timeout exceeded; probeer de transactie opnieuw te starten

| Oorzaak         | Oplossing    |
| ------------- | ------------- |
| Deze fout treedt op wanneer migratie mislukt vanwege de time-out voor het vergrendelen van de vergrendeling tijdens de migratie. | Overweeg de waarde van serverparameter **'innodb_lock_wait_timeout' te**verhogen. De hoogst toegestane waarde is 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Fout verbinding maken met bron SQL Server bij gebruik dynamische poort of benoemde instantie

Wanneer u Azure Database Migration Service probeert te verbinden met SQL Server-bron die wordt uitgevoerd op een benoemde instantie of een dynamische poort, mislukt de verbinding met deze fout:

* **Fout**: -1 - SQL-verbinding is mislukt. Een netwerkgerelateerde of exemplaarspecifieke fout is opgetreden bij het maken van een verbinding met SQL Server. De server wordt niet gevonden of toegang tot de server is niet mogelijk. Controleer of de instantienaam correct is en of SQL Server is geconfigureerd om externe verbindingen toe te staan. (provider: SQL Network Interfaces, fout: 26 - Error Locating Server/Instance Specified)

| Oorzaak         | Oplossing    |
| ------------- | ------------- |
| Dit probleem treedt op wanneer de bron SQL Server-instantie waarmee Azure Database Migration Service verbinding probeert te maken, een dynamische poort heeft of een benoemde instantie gebruikt. De SQL Server Browser-service luistert naar UDP-poort 1434 voor binnenkomende verbindingen met een benoemde instantie of wanneer u een dynamische poort gebruikt. De dynamische poort kan elke keer dat sql server-service opnieuw wordt opgestart, veranderen. U de dynamische poort die aan een instantie is toegewezen via netwerkconfiguratie controleren in SQL Server Configuration Manager.<br><br><br> |Controleer of Azure Database Migration Service verbinding kan maken met de bronSQL Server Browser-service op UDP-poort 1434 en de SQL Server-instantie via de dynamisch toegewezen TCP-poort als van toepassing. |

## <a name="additional-known-issues"></a>Aanvullende bekende problemen

* [Bekende problemen/migratiebeperkingen met online migraties naar Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Bekende problemen/migratiebeperkingen met online migraties naar Azure Database voor MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Bekende problemen/migratiebeperkingen met online migraties naar Azure Database voor PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Volgende stappen

* Bekijk het artikel [Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Bekijk het artikel [Serverparameters configureren in Azure Database voor MySQL met behulp van de Azure-portal.](https://docs.microsoft.com/azure/mysql/howto-server-parameters)
* Bekijk het [artikelOverzicht van vereisten voor het gebruik van Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs).
* Zie de [veelgestelde vragen over het gebruik van Azure Database Migration Service](https://docs.microsoft.com/azure/dms/faq).
