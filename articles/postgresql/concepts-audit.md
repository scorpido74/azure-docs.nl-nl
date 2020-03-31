---
title: Controlelogboekregistratie - Azure Database voor PostgreSQL - Single Server
description: Concepten voor pgAudit-controlelogboekregistratie in Azure Database voor PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 45490e398abd8b5bd3c10adb95b56e1019d2bb94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842466"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Controlelogboekregistratie in Azure Database voor PostgreSQL - Single Server

Controlelogboekregistratie van databaseactiviteiten in Azure Database voor PostgreSQL - Single Server is beschikbaar via de PostgreSQL Audit Extension: [pgAudit](https://www.pgaudit.org/). pgAudit biedt gedetailleerde sessie- en/of objectauditlogboekregistratie.

> [!NOTE]
> pgAudit is in preview op Azure Database voor PostgreSQL.
> De extensie kan alleen worden ingeschakeld op servers met algemeen gebruik en geheugengeoptimaliseerd.

Zie het [Azure Activity Log](../azure-monitor/platform/platform-logs-overview.md)als u logboeken op Azure-resourceniveau wilt voor bewerkingen zoals compute en storage scaling.

## <a name="usage-considerations"></a>Gebruiksoverwegingen
Standaard worden pgAudit-logboekinstructies samen met uw reguliere logboeken verzonden met behulp van de standaardfunctie voor logboekregistratie van Postgres. In Azure Database for PostgreSQL kunnen deze .log-bestanden worden gedownload via de Azure-portal of de CLI. De maximale opslag voor het verzamelen van bestanden is 1 GB en elk bestand is maximaal zeven dagen beschikbaar (de standaardinstelling is drie dagen). Deze service is een opslagoptie voor korte termijn.

U ook alle logboeken configureren die worden uitgezonden naar de diagnostische logboekservice van Azure Monitor. Als u diagnostische logboekregistratie van Azure Monitor inschakelt, worden uw logboeken automatisch verzonden (in JSON-indeling) naar Azure Storage, Event Hubs en/of Azure Monitor-logboeken, afhankelijk van uw keuze.

Als pgAudit wordt ingeschakeld, wordt er een groot aantal logboekregistraties op een server gegenereerd. Dit heeft gevolgen voor de prestaties en de logboekopslag. U wordt aangeraden de Azure Diagnostic Log-service te gebruiken. Deze biedt opties voor opslag op de langere termijn, evenals analyse- en waarschuwingsfuncties. U wordt aangeraden de standaard logboekregistratie uit te schakelen om de impact van prestaties van extra logboekregistratie te verminderen:

   1. Stel de `logging_collector` parameter in op UIT. 
   2. Start de server opnieuw om deze wijziging toe te passen.

Ga naar het gedeelte diagnostische logboeken van het [artikel serverlogboeken](concepts-server-logs.md)voor het instellen van logboekregistratie voor Azure Storage, Event Hubs of Azure Monitor-logboeken.

## <a name="installing-pgaudit"></a>PgAudit installeren

Als u pgAudit wilt installeren, moet u deze opnemen in de gedeelde preload-bibliotheken van de server. Voor een wijziging in `shared_preload_libraries` de parameter van Postgres moet een serveropnieuw opstarten van kracht worden. U parameters wijzigen met behulp van de [Azure-portal,](howto-configure-server-parameters-using-portal.md) [Azure CLI](howto-configure-server-parameters-using-cli.md)of [REST API.](/rest/api/postgresql/configurations/createorupdate)

De [Azure-portal gebruiken:](https://portal.azure.com)

   1. Selecteer uw Azure Database for PostgreSQL-server.
   2. Selecteer **serverparameters**op de zijbalk .
   3. Zoek naar `shared_preload_libraries` de parameter.
   4. Selecteer **pgaudit**.
   5. Start de server opnieuw om de wijziging toe te passen.

   6. Maak verbinding met uw server via een client (zoals psql) en schakel de pgAudit-extensie in
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Als er een fout wordt weergegeven, bevestigt `shared_preload_libraries`u dat u de server opnieuw hebt opgestart na het opslaan van .

## <a name="pgaudit-settings"></a>pgAudit-instellingen

met pgAudit u sessie- of objectauditlogboekregistratie configureren. [Sessiecontrolelogboekregistratie](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) zendt gedetailleerde logboeken van uitgevoerde instructies uit. [Object audit logging](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) is audit scoped to specific relations. U ervoor kiezen om een of beide typen logboekregistratie in te stellen. 

> [!NOTE]
> pgAudit-instellingen zijn somber opgegeven en kunnen niet worden opgegeven op database- of rolniveau.

Zodra u pgAudit hebt [geïnstalleerd,](#installing-pgaudit)u de parameters configureren om te beginnen met loggen. De [pgAudit documentatie](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) bevat de definitie van elke parameter. Test eerst de parameters en bevestig dat u het verwachte gedrag krijgt.

> [!NOTE]
> Als `pgaudit.log_client` u instelt op ON, worden logboeken omgeleid naar een clientproces (zoals psql) in plaats van naar bestand te worden geschreven. Deze instelling moet over het algemeen uitgeschakeld blijven. <br> <br>
> `pgaudit.log_level`is alleen ingeschakeld `pgaudit.log_client` wanneer deze is ingeschakeld.

> [!NOTE]
> In Azure Database voor `pgaudit.log` PostgreSQL kan `-` niet worden ingesteld met een (min)tekensnelkoppeling zoals beschreven in de pgAudit-documentatie. Alle vereiste instructieklassen (lezen, schrijven, enzovoort) moeten afzonderlijk worden opgegeven.

### <a name="audit-log-format"></a>Auditlogindeling
Elke controle-item `AUDIT:` wordt aangegeven door in de buurt van het begin van de logregel. Het formaat van de rest van de vermelding is beschreven in de [pgAudit documentatie](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Als u andere velden nodig hebt om aan uw `log_line_prefix`controlevereisten te voldoen, gebruikt u de parameter Postgres. `log_line_prefix`is een tekenreeks die wordt uitgevoerd aan het begin van elke Postgres-logboekregel. De volgende `log_line_prefix` instelling bevat bijvoorbeeld tijdstempel, gebruikersnaam, databasenaam en proces-ID:

```
t=%m u=%u db=%d pid=[%p]:
```

Ga voor `log_line_prefix`meer informatie over de [PostgreSQL-documentatie](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Aan de slag
Als u snel `pgaudit.log` aan `WRITE`de slag wilt, u instellen op, en uw logboeken openen om de uitvoer te bekijken. 

## <a name="viewing-audit-logs"></a>Controlelogboeken weergeven
Als u .log-bestanden gebruikt, worden uw controlelogboeken opgenomen in hetzelfde bestand als uw PostgreSQL-foutlogboeken. U logboekbestanden downloaden van de [Azure-portal](howto-configure-server-logs-in-portal.md) of [CLI.](howto-configure-server-logs-using-cli.md) 

Als u Azure-diagnostische logboekregistratie gebruikt, is de manier waarop u toegang krijgt tot de logboeken afhankelijk van welk eindpunt u kiest. Zie het artikel [over logboekenopslagaccount](../azure-monitor/platform/resource-logs-collect-storage.md) voor Azure Storage. Zie het artikel [voor azure-logboeken voor logboeken voor gebeurtenissen.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Voor Azure Monitor Logs worden logboeken verzonden naar de werkruimte die u hebt geselecteerd. De Postgres-logboeken gebruiken de **azurediagnostics-verzamelingsmodus,** zodat ze kunnen worden opgevraagd in de tabel AzureDiagnostics. De velden in de tabel worden hieronder beschreven. Meer informatie over query's en waarschuwingen vindt u in het overzicht van de [query's Azure Monitor Logs.](../azure-monitor/log-query/log-query-overview.md)

U deze query gebruiken om aan de slag te gaan. U waarschuwingen configureren op basis van query's.

Zoeken naar alle Postgres logs voor een bepaalde server in de laatste dag
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Volgende stappen
- [Meer informatie over inloggen in Azure Database voor PostgreSQL](concepts-server-logs.md)
- Meer informatie over het instellen van parameters met behulp van de [Azure-portal,](howto-configure-server-parameters-using-portal.md) [Azure CLI](howto-configure-server-parameters-using-cli.md)of [REST API](/rest/api/postgresql/configurations/createorupdate).
