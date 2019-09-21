---
title: Server Logboeken in Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe Azure Database for PostgreSQL-één-server query-en fout logboeken genereert en hoe de Bewaar periode van het logboek is geconfigureerd.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: b295ab442e70772a86d6699e1063c7a1c728f1a7
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091122"
---
# <a name="server-logs-in-azure-database-for-postgresql---single-server"></a>Server Logboeken in Azure Database for PostgreSQL-één server
Azure Database for PostgreSQL genereert query- en foutenlogboeken. Query-en fout logboeken kunnen worden gebruikt om configuratie fouten en suboptimale prestaties te identificeren, op te lossen en te herstellen. (Toegang tot transactie Logboeken is niet inbegrepen). 

## <a name="configure-logging"></a>Logboek registratie configureren 
U kunt de logboek registratie op uw server configureren met de para meters van de server voor logboek registratie. Op elke nieuwe server **log_checkpoints** en **log_connections** zijn standaard ingeschakeld. Er zijn aanvullende para meters die u kunt aanpassen aan uw logboek registratie vereisten: 

![Azure Database for PostgreSQL-registratie parameters](./media/concepts-server-logs/log-parameters.png)

Zie de documentatie van PostgreSQL voor [fout rapportage en logboek registratie](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) voor meer informatie over deze para meters. Raadpleeg de documentatie van de [Portal](howto-configure-server-parameters-using-portal.md) of de [cli-documentatie](howto-configure-server-parameters-using-cli.md)voor meer informatie over het configureren van Azure database for PostgreSQL para meters.

## <a name="access-server-logs-through-portal-or-cli"></a>Toegang tot de server logboeken via portal of CLI
Als u Logboeken hebt ingeschakeld, kunt u deze openen vanuit de Azure Database for PostgreSQL logboek opslag met behulp van de [Azure Portal](howto-configure-server-logs-in-portal.md), [Azure cli](howto-configure-server-logs-using-cli.md)en Azure rest api's. De logboekbestanden draaien elk uur of bij elke 100 MB, afhankelijk van wat het eerst voorkomt. U kunt de Bewaar periode voor deze logboek opslag instellen met behulp van de para meter voor de  **Bewaar\_\_periode voor logboeken**die aan uw server is gekoppeld. De standaard waarde is 3 dagen; de maximum waarde is 7 dagen. Uw server moet voldoende toegewezen opslag hebben om de logboek bestanden te kunnen bevatten. (Deze Bewaar para meter houdt geen controle uit van Azure Diagnostische logboeken.)


## <a name="diagnostic-logs"></a>Diagnostische logboeken
Azure Database for PostgreSQL is geïntegreerd met Azure Monitor Diagnostische logboeken. Zodra u logboeken op uw PostgreSQL-server hebt ingeschakeld, kunt u ervoor kiezen om deze te laten uitbrengen naar [Azure monitor-logboeken](../azure-monitor/log-query/log-query-overview.md), Event Hubs of Azure Storage. 

> [!IMPORTANT]
> Deze diagnostische functie voor Server Logboeken is alleen beschikbaar in de [prijs categorie](concepts-pricing-tiers.md)algemeen en geoptimaliseerd voor geheugen.

Diagnostische logboeken inschakelen met behulp van de Azure Portal:

   1. Ga in de portal naar *Diagnostische instellingen* in het navigatie menu van uw post gres-server.
   2. Selecteer *Diagnostische instelling toevoegen*.
   3. Geef deze instelling een naam. 
   4. Selecteer uw voorkeurs locatie voor downstream (opslag account, Event Hub, log Analytics). 
   5. Selecteer de gewenste gegevens typen.
   6. Sla de instelling op.

In de volgende tabel wordt beschreven wat zich in elk logboek bevindt. Afhankelijk van het uitvoer eindpunt dat u kiest, worden de opgenomen velden en de volg orde waarin ze worden weer gegeven. 

|**Veld** | **Beschrijving** |
|---|---|
| TenantId | Uw Tenant-ID |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Tijds tempel voor het vastleggen van het logboek in UTC |
| Type | Het type van het logboek. Altijd `AzureDiagnostics` |
| SubscriptionId | GUID voor het abonnement waartoe de server behoort |
| ResourceGroup | Naam van de resource groep waartoe de server behoort |
| ResourceProvider | De naam van de resource provider. Altijd `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Resource-URI |
| Resource | Naam van de server |
| Category | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| Gelijk | Logboek registratie niveau, voor beeld: LOGBOEK, FOUT, KENNISGEVING |
| Message | Primair logboek bericht | 
| Domein | Server versie, voor beeld: post gres-10 |
| Details | Secundair logboek bericht (indien van toepassing) |
| ColumnName | De naam van de kolom (indien van toepassing) |
| SchemaName | De naam van het schema (indien van toepassing) |
| DatatypeName | Naam van het gegevens type (indien van toepassing) |
| LogicalServerName | Naam van de server | 
| _ResourceId | Resource-URI |
| Voorvoegsel | Voor voegsel van logboek regel |



## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het openen van Logboeken vanuit de [Azure Portal](howto-configure-server-logs-in-portal.md) of [Azure cli](howto-configure-server-logs-using-cli.md).
- Meer informatie over [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/).
