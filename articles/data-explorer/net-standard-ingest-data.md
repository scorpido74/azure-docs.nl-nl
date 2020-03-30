---
title: Gegevens opnemen met Azure Data Explorer .NET Standard SDK (Preview)
description: In dit artikel leert u hoe u gegevens in neemt (laden) in Azure Data Explorer met behulp van .NET Standard SDK.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 9b6eda60f0b0cb1b697560cccc2cffe719d58536
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251776"
---
# <a name="ingest-data-using-the-azure-data-explorer-net-standard-sdk-preview"></a>Gegevens opnemen met de Azure Data Explorer .NET Standard SDK (Preview)

Azure Data Explorer (ADX) is een snelle en zeer schaalbare service om gegevens in logboeken en telemetrie te verkennen. ADX biedt twee clientbibliotheken voor .NET Standard: een [opnamebibliotheek](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard) en een [gegevensbibliotheek](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard). Met deze bibliotheken kunt u gegevens opnemen (laden) in een cluster en gegevens bevragen vanuit uw code. In dit artikel maakt u eerst een tabel en gegevenstoewijzing in een testcluster. Vervolgens plaatst u een gegevensopname in het cluster in de wachtrij en valideert u de resultaten.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.

* [Een cluster en database voor testdoeleinden](create-cluster-database-portal.md)

## <a name="install-the-ingest-library"></a>De opnamebibliotheek installeren

```
Install-Package Microsoft.Azure.Kusto.Ingest.NETStandard
```

## <a name="authentication"></a>Authentication

Azure Data Explorer maakt gebruik van uw AAD-tenant-id om een toepassing te verifiëren. Om uw tenant-id te vinden, gebruikt u de volgende URL, waarbij u *YourDomain* vervangt door uw domeinnaam.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Dus als uw domein *contoso.com* is, wordt de URL bijvoorbeeld: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Klik op deze URL om de resultaten weer te geven. De eerste regel is als volgt. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

De tenant-id is in dit geval `6babcaad-604b-40ac-a9d7-9fd97c0b779f`.

In dit voorbeeld worden een gebruikersnaam en wachtwoord van ADD gebruikt voor toegang tot het cluster. U kunt ook een AAD-toepassingscertificaat en een AAD-toepassingssleutel gebruiken. Stel de waarden in voor `tenantId`, `user` en `password` voordat u deze code uitvoert.

```csharp
var tenantId = "<TenantId>";
var user = "<User>";
var password = "<Password>";
```

## <a name="construct-the-connection-string"></a>De verbindingsreeks samenstellen
Nu maakt u de verbindingsreeks. U maakt de doeltabel en toewijzing in een latere stap.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var database = "<DatabaseName>";

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };
```

## <a name="set-source-file-information"></a>Gegevens van bronbestand instellen

Stel het pad in voor het bronbestand. In dit voorbeeld wordt een voorbeeldbestand gebruikt dat wordt gehost in Azure Blob Storage. De **StormEvents-gegevensset** bevat weergerelateerde gegevens van de [National Centers for Environmental Information.](https://www.ncdc.noaa.gov/stormevents/)

```csharp
var blobPath = "https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
```

## <a name="create-a-table-on-your-test-cluster"></a>Een tabel maken in het testcluster
Maak een tabel met de naam `StormEvents` die overeenkomt met het schema van de gegevens in het bestand `StormEvents.csv`.

```csharp
var table = "StormEvents";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("StartTime", "System.DateTime"),
                Tuple.Create("EndTime", "System.DateTime"),
                Tuple.Create("EpisodeId", "System.Int32"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("State", "System.String"),
                Tuple.Create("EventType", "System.String"),
                Tuple.Create("InjuriesDirect", "System.Int32"),
                Tuple.Create("InjuriesIndirect", "System.Int32"),
                Tuple.Create("DeathsDirect", "System.Int32"),
                Tuple.Create("DeathsIndirect", "System.Int32"),
                Tuple.Create("DamageProperty", "System.Int32"),
                Tuple.Create("DamageCrops", "System.Int32"),
                Tuple.Create("Source", "System.String"),
                Tuple.Create("BeginLocation", "System.String"),
                Tuple.Create("EndLocation", "System.String"),
                Tuple.Create("BeginLat", "System.Double"),
                Tuple.Create("BeginLon", "System.Double"),
                Tuple.Create("EndLat", "System.Double"),
                Tuple.Create("EndLon", "System.Double"),
                Tuple.Create("EpisodeNarrative", "System.String"),
                Tuple.Create("EventNarrative", "System.String"),
                Tuple.Create("StormSummary", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="define-ingestion-mapping"></a>Toewijzing van opname definiëren

Wijs de binnenkomende CSV-gegevens toe aan de kolomnamen die zijn gebruikt bij het maken van de tabel.
Richt een [CSV-kolomtoewijzingsobject](/azure/kusto/management/create-ingestion-mapping-command) in voor deze tabel

```csharp
var tableMapping = "StormEvents_CSV_Mapping";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCsvMappingCreateCommand(
            table,
            tableMapping,
            new[]
            {
                new CsvColumnMapping { ColumnName = "StartTime", Ordinal = 0 },
                new CsvColumnMapping { ColumnName = "EndTime", Ordinal = 1 },
                new CsvColumnMapping { ColumnName = "EpisodeId", Ordinal = 2 },
                new CsvColumnMapping { ColumnName = "EventId", Ordinal = 3 },
                new CsvColumnMapping { ColumnName = "State", Ordinal = 4 },
                new CsvColumnMapping { ColumnName = "EventType", Ordinal = 5 },
                new CsvColumnMapping { ColumnName = "InjuriesDirect", Ordinal = 6 },
                new CsvColumnMapping { ColumnName = "InjuriesIndirect", Ordinal = 7 },
                new CsvColumnMapping { ColumnName = "DeathsDirect", Ordinal = 8 },
                new CsvColumnMapping { ColumnName = "DeathsIndirect", Ordinal = 9 },
                new CsvColumnMapping { ColumnName = "DamageProperty", Ordinal = 10 },
                new CsvColumnMapping { ColumnName = "DamageCrops", Ordinal = 11 },
                new CsvColumnMapping { ColumnName = "Source", Ordinal = 12 },
                new CsvColumnMapping { ColumnName = "BeginLocation", Ordinal = 13 },
                new CsvColumnMapping { ColumnName = "EndLocation", Ordinal = 14 },
                new CsvColumnMapping { ColumnName = "BeginLat", Ordinal = 15 },
                new CsvColumnMapping { ColumnName = "BeginLon", Ordinal = 16 },
                new CsvColumnMapping { ColumnName = "EndLat", Ordinal = 17 },
                new CsvColumnMapping { ColumnName = "EndLon", Ordinal = 18 },
                new CsvColumnMapping { ColumnName = "EpisodeNarrative", Ordinal = 19 },
                new CsvColumnMapping { ColumnName = "EventNarrative", Ordinal = 20 },
                new CsvColumnMapping { ColumnName = "StormSummary", Ordinal = 21 },
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="queue-a-message-for-ingestion"></a>Bericht in wachtrij zetten voor opname

Zet een bericht in de wachtrij om gegevens op te halen uit blob-opslag en deze gegevens op te nemen in ADX.

```csharp
var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
var ingestConnectionStringBuilder =
    new KustoConnectionStringBuilder(ingestUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };

using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder))
{
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.csv,
            CSVMappingReference = tableMapping,
            IgnoreFirstRecord = true
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
}
```

## <a name="validate-data-was-ingested-into-the-table"></a>Controleren of gegevens zijn opgenomen in de tabel

Wacht vijf tot tien minuten totdat de opname in de wachtrij de opname heeft gepland en de gegevens zijn geladen in ADX. Voer vervolgens de volgende code uit om het aantal records in de tabel `StormEvents` te bepalen.

```csharp
using (var cslQueryProvider = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
{
    var query = $"{table} | count";

    var results = cslQueryProvider.ExecuteQuery<long>(query);
    Console.WriteLine(results.Single());
}
```

## <a name="run-troubleshooting-queries"></a>Query's voor probleemoplossing uitvoeren

Meld u [https://dataexplorer.azure.com](https://dataexplorer.azure.com) aan en maak verbinding met uw cluster. Voer de volgende opdracht uit in uw database om te zien of er in de afgelopen vier uur fouten zijn opgetreden tijdens het opnemen van gegevens. Vervang de naam van de database voordat u de opdracht uitvoert.

```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Voer de volgende opdracht uit om de status op te vragen van alle bewerkingen voor het opnemen van gegevens van de afgelopen vier uur. Vervang de naam van de database voordat u de opdracht uitvoert.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent onze andere artikelen te volgen, behoudt u de resources die u hebt gemaakt. Voer anders de volgende opdracht uit in uw database om de tabel `StormEvents` op te schonen.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Volgende stappen

* [Query's schrijven](write-queries.md)
