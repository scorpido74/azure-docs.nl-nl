---
title: Logboeken - Azure Database voor PostgreSQL - Single Server
description: Beschrijft logboekconfiguratie, -opslag en -analyse in Azure Database voor PostgreSQL - Single Server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 2636e9a225002148e4cd79bb2176e0883aed623a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280493"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Logboeken in Azure-database voor PostgreSQL - Enkele server
Met Azure Database voor PostgreSQL u de standaardlogboeken van Postgres configureren en openen. De logboeken kunnen worden gebruikt om configuratiefouten en suboptimale prestaties te identificeren, op te lossen en te herstellen. Logboekregistratiegegevens die u configureren en openen, omvatten fouten, querygegevens, automatisch vacuümrecords, verbindingen en controlepunten. (Toegang tot transactielogboeken is niet beschikbaar).

Audit logging wordt beschikbaar gesteld via een Postgres extensie, pgaudit. Ga voor meer informatie naar het artikel [over controleconcepten.](concepts-audit.md)


## <a name="configure-logging"></a>Logboekregistratie configureren 
U de standaardlogboekregistratie van Postgres op uw server configureren met behulp van de parameters van de logboekregistratieserver. Op elke Azure Database voor `log_checkpoints` PostgreSQL-server en `log_connections` standaard ingeschakeld. Er zijn aanvullende parameters die u aanpassen aan uw logboekbehoeften: 

![Azure Database voor PostgreSQL - Logboekregistratieparameters](./media/concepts-server-logs/log-parameters.png)

Ga voor meer informatie over de parameters van het Postgres-logboek naar de secties [Wanneer aanmelden](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) en wat moet [u registreren](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) van de Documentatie Postgres. De meeste, maar niet alle, Postgres-logboekparameters zijn beschikbaar om te configureren in Azure Database voor PostgreSQL.

Zie de [portaldocumentatie](howto-configure-server-parameters-using-portal.md) of de [CLI-documentatie](howto-configure-server-parameters-using-cli.md)voor meer informatie over het configureren van parameters in Azure Database voor PostgreSQL. 

> [!NOTE]
> Het configureren van een groot aantal logboeken, bijvoorbeeld logboekregistratie, kan aanzienlijke prestatieoverhead toevoegen. 

## <a name="access-log-files"></a>Access .log-bestanden
De standaardlogboekindeling in Azure Database voor PostgreSQL is .log. Een voorbeeldregel uit dit logboek ziet eruit als volgt:

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

Azure Database voor PostgreSQL biedt een opslaglocatie op korte termijn voor de .log-bestanden. Een nieuw bestand begint elke 1 uur of 100 MB, wat het eerst komt. Logboeken worden toegevoegd aan het huidige bestand als ze worden uitgezonden door Postgres.  

U de bewaartermijn voor deze korte `log_retention_period` termijn logboekopslag instellen met behulp van de parameter. De standaardwaarde is drie dagen, de maximumwaarde is zeven dagen. De opslaglocatie op korte termijn kan maximaal 1 GB aan logboekbestanden bevatten. Na 1 GB worden de oudste bestanden, ongeacht de bewaartermijn, verwijderd om ruimte te maken voor nieuwe logboeken. 

Voor het bewaren op langere termijn van logboeken en logboekanalyse u de .log-bestanden downloaden en verplaatsen naar een service van derden. U de bestanden downloaden via de [Azure-portal](howto-configure-server-logs-in-portal.md)Azure [CLI](howto-configure-server-logs-using-cli.md). U ook diagnostische instellingen voor Azure Monitor configureren die uw logboeken (in JSON-indeling) automatisch uitzendt naar locaties op langere termijn. Meer informatie over deze optie vind je in het onderstaande gedeelte. 

U stoppen met het genereren `logging_collector` van .log-bestanden door de parameter in te stellen op UIT. Het uitschakelen van .log-bestand wordt aanbevolen als u diagnostische instellingen van Azure Monitor gebruikt. Deze configuratie vermindert de impact op de prestaties van extra logboekregistratie.

## <a name="diagnostic-logs"></a>Diagnostische logboeken
Azure Database voor PostgreSQL is geïntegreerd met diagnostische instellingen voor Azure Monitor. Met diagnostische instellingen u uw Postgres-logboeken in JSON-indeling naar Azure Monitor-logboeken verzenden voor analyses en waarschuwingen, gebeurtenishubs voor streaming en Azure Storage voor archivering. 

> [!IMPORTANT]
> Deze diagnostische functie voor serverlogboeken is alleen beschikbaar in de [prijzenlagen](concepts-pricing-tiers.md)Algemeen Doel en Geheugengeoptimaliseerd.


### <a name="configure-diagnostic-settings"></a>Diagnostische instellingen configureren
U diagnostische instellingen voor uw Postgres-server inschakelen met behulp van de Azure-portal, CLI, REST API en Powershell. De logboekcategorie die u wilt selecteren, is **PostgreSQLLogs**. (Er zijn andere logboeken die u configureren als u [Query Store](concepts-query-store.md)gebruikt .)

Diagnostische logboeken inschakelen met de Azure-portal:

   1. Ga in de portal naar *Diagnostische instellingen* in het navigatiemenu van uw Postgres-server.
   2. Selecteer *Diagnostische instelling toevoegen*.
   3. Geef deze instelling een naam. 
   4. Selecteer het gewenste eindpunt (opslagaccount, gebeurtenishub, logboekanalyse). 
   5. Selecteer het logboektype **PostgreSQLLogs**.
   7. Sla uw instelling op.

Als u Diagnostische logboeken wilt inschakelen met Powershell, CLI of REST API, gaat u naar het artikel [diagnostische instellingen.](../azure-monitor/platform/diagnostic-settings.md)

### <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

De manier waarop u toegang krijgt tot de logboeken is afhankelijk van welk eindpunt u kiest. Zie het artikel [over logboekenopslagaccount](../azure-monitor/platform/resource-logs-collect-storage.md) voor Azure Storage. Zie het artikel [voor azure-logboeken voor logboeken voor gebeurtenissen.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Voor Azure Monitor Logs worden logboeken verzonden naar de werkruimte die u hebt geselecteerd. De Postgres-logboeken gebruiken de **azurediagnostics-verzamelingsmodus,** zodat ze kunnen worden opgevraagd in de tabel AzureDiagnostics. De velden in de tabel worden hieronder beschreven. Meer informatie over query's en waarschuwingen vindt u in het overzicht van de [query's Azure Monitor Logs.](../azure-monitor/log-query/log-query-overview.md)

De volgende zijn query's die u proberen te proberen aan de slag te gaan. U waarschuwingen configureren op basis van query's.

Zoeken naar alle Postgres logs voor een bepaalde server in de laatste dag
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
```

Zoeken naar alle pogingen voor niet-localhostverbindingen
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
De bovenstaande query toont de resultaten van de afgelopen 6 uur voor elke Postgres-serverdie in deze werkruimte wordt inloggen.

### <a name="log-format"></a>Logboekindeling

In de volgende tabel worden de velden voor het type **PostgreSQLLogs** beschreven. Afhankelijk van het uitvoereindpunt dat u kiest, kunnen de opgenomen velden en de volgorde waarin ze worden weergegeven, variëren. 

|**Veld** | **Beschrijving** |
|---|---|
| TenantId | Uw tenant-id |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Tijdstempel toen het logboek werd opgenomen in UTC |
| Type | Type van het logboek. Altijd`AzureDiagnostics` |
| SubscriptionId | GUID voor het abonnement waartoe de server behoort |
| ResourceGroup | Naam van de brongroep waartoe de server behoort |
| ResourceProvider | Naam van de resourceprovider. Altijd`MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Resource URI |
| Resource | Naam van de server |
| Categorie | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| Errorlevel | Logging niveau, voorbeeld: LOG, FOUT, KENNISGEVING |
| Bericht | Primaire logboekbericht | 
| Domain | Serverversie, voorbeeld: postgres-10 |
| Detail | Secundair logboekbericht (indien van toepassing) |
| ColumnName | Naam van de kolom (indien van toepassing) |
| SchemaName | Naam van het schema (indien van toepassing) |
| DatatypeName | Naam van het gegevenstype (indien van toepassing) |
| LogischeServernaam | Naam van de server | 
| _ResourceId | Resource URI |
| Voorvoegsel | Het voorvoegsel van de logboekregel |


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het openen van logboeken via de [Azure-portal](howto-configure-server-logs-in-portal.md) of [Azure CLI](howto-configure-server-logs-using-cli.md).
- Meer informatie over [azure monitor-prijzen](https://azure.microsoft.com/pricing/details/monitor/).
- Meer informatie over [controlelogboeken](concepts-audit.md)
