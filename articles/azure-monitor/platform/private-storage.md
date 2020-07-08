---
title: Opslag accounts die eigendom zijn van de klant voor logboek opname
description: Gebruik uw eigen opslag account voor opname van logboek gegevens in een Log Analytics-werk ruimte in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/20/2020
ms.openlocfilehash: 0c9982fd4aa6459cdcbd715077f08092075a9776
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84610063"
---
# <a name="customer-owned-storage-accounts-for-log-ingestion-in-azure-monitor"></a>Opslag accounts die eigendom zijn van de klant voor logboek opname in Azure Monitor

Azure Monitor gebruikt opslag accounts in het opname proces van sommige gegevens typen, zoals [aangepaste logboeken](data-sources-custom-logs.md) en bepaalde [Azure-logboeken](azure-storage-iis-table.md). Tijdens het opname proces worden logboeken eerst verzonden naar een opslag account en later opgenomen in Log Analytics of Application Insights. Als u de controle over uw gegevens tijdens opname wilt, kunt u uw eigen opslag accounts gebruiken in plaats van de door de service beheerde opslag. Met uw eigen opslag account hebt u controle over de toegang, inhoud, versleuteling en behoud van de logboeken tijdens opname. We verwijzen naar dit als uw eigen opslag of BYOS. 

Een scenario dat BYOS vereist, is netwerk isolatie via persoonlijke koppelingen. Wanneer u een VNet gebruikt, is netwerk isolatie vaak een vereiste en is toegang tot het open bare Internet beperkt. In dergelijke gevallen is het openen van Azure Monitor-service opslag voor logboek opname volledig geblokkeerd of als een onjuiste procedure beschouwd. In plaats daarvan moeten de logboeken worden opgenomen via een opslag account van de klant in het VNet of eenvoudig toegankelijk zijn.

Een ander scenario is het versleutelen van logboeken met door de klant beheerde sleutels (CMK). Klanten kunnen geregistreerde gegevens versleutelen met behulp van CMK in de clusters waarin de logboeken worden opgeslagen. Dezelfde sleutel kan ook worden gebruikt voor het versleutelen van logboeken tijdens het opname proces.

## <a name="data-types-supported"></a>Ondersteunde gegevens typen

De volgende gegevens typen van een opslag account worden opgenomen. Zie [gegevens verzamelen van Azure Diagnostics-extensie voor Azure monitor-logboeken](azure-storage-iis-table.md) voor meer informatie over de opname van deze typen.

| Type | Tabel gegevens |
|:-----|:------------------|
| IIS-logboeken | Blob: wad-IIS-logboek bestanden|
|Windows-gebeurtenislogboeken | Tabel: WADWindowsEventLogsTable |
| Syslog | Tabel: LinuxsyslogVer2v0 |
| Windows ETW-logboeken | Tabel: WADETWEventTable|
| Service Fabric | Tabel: WADServiceFabricSystemEventTable <br/> WADServiceFabricReliableActorEventTable<br/> WADServiceFabricReliableServicEventTable |
| Aangepaste logboeken | N.v.t. |
| Dump bestanden van Azure Security Center Watson | N.v.t.|  

## <a name="storage-account-requirements"></a>Vereisten voor een opslagaccount 
Het opslag account moet voldoen aan de volgende vereisten:

- Toegankelijk voor bronnen op uw VNet die logboeken naar de opslag schrijven.
- Moet zich in dezelfde regio bevinden als de werk ruimte waaraan deze is gekoppeld.
- Het is expliciet toegestaan Log Analytics logboeken van het opslag account te lezen door *vertrouwde MS-services toegang geven tot dit opslag account*te selecteren.

## <a name="process-to-configure-customer-owned-storage"></a>Proces voor het configureren van opslag in eigendom van de klant
Het basis proces van het gebruik van uw eigen opslag account voor opname is als volgt:

1. Maak een opslag account of selecteer een bestaand account.
2. Koppel het opslag account aan een Log Analytics-werk ruimte.
3. Beheer de opslag door de belasting en retentie te controleren om er zeker van te zijn dat deze werkt zoals verwacht.

De enige beschik bare methode voor het maken en verwijderen van koppelingen vindt u via de REST API. Meer informatie over de specifieke API-aanvraag die is vereist voor elk proces, vindt u in de volgende secties.

## <a name="api-request-values"></a>Waarden van API-aanvragen

#### <a name="datasourcetype"></a>Data Source Type 

- AzureWatson: gebruik deze waarde voor Azure Security Center Azure Watson-dump bestanden.
- CustomLogs: gebruik deze waarde voor de volgende gegevens typen:
  - Aangepaste logboeken
  - IIS-logboeken
  - Gebeurtenissen (Windows)
  - Syslog (Linux)
  - ETW-logboeken
  - Service Fabric gebeurtenissen
  - Beoordelings gegevens  

#### <a name="storage_account_resource_id"></a>storage_account_resource_id
Deze waarde maakt gebruik van de volgende structuur:

```
subscriptions/{subscriptionId}/resourcesGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName1}
```



## <a name="get-current-links"></a>Huidige koppelingen ophalen

### <a name="get-linked-storage-accounts-for-a-specific-data-source-type"></a>Gekoppelde opslag accounts ophalen voor een specifiek gegevens bron type

#### <a name="api-request"></a>API-aanvraag

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

#### <a name="response"></a>Antwoord 

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs",
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
    "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
    "name": "CustomLogs",
    "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```

### <a name="get-all-linked-storage-accounts"></a>Alle gekoppelde opslag accounts ophalen

#### <a name="api-request"></a>API-aanvraag

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts?api-version=2019-08-01-preview  
```

#### <a name="response"></a>Antwoord

```json
{
    [
        {
            "properties":
            {
                "dataSourceType": "CustomLogs",
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_1>",
                    "<storage_account_resource_id_2>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
            "name": "CustomLogs",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        },
        {
            "properties":
            {
                "dataSourceType": " AzureWatson "
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_3>",
                    "<storage_account_resource_id_4>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/AzureWatson",
            "name": "AzureWatson",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        }
    ]
}
```

## <a name="create-or-modify-a-link"></a>Een koppeling maken of wijzigen

Zodra u een opslag account aan een werk ruimte koppelt, wordt Log Analytics gebruikt in plaats van het opslag account dat eigendom is van de service. U kunt op hetzelfde moment een lijst met opslag accounts registreren en u kunt hetzelfde opslag account gebruiken voor meerdere werk ruimten.

Als uw werk ruimte zowel VNet-resources als bronnen buiten een VNet verwerkt, moet u ervoor zorgen dat er geen verkeer wordt afgewezen dat afkomstig is van het internet. Uw opslag moet dezelfde instellingen hebben als uw werk ruimte en beschikbaar worden gesteld aan bronnen buiten uw VNet. 

### <a name="api-request"></a>API-aanvraag

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

### <a name="payload"></a>Nettolading

```json
{
    "properties":
    {
        "storageAccountIds  " : 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    }
}
```

### <a name="response"></a>Antwoord

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs"
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
"id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
"name": "CustomLogs",
"type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```


## <a name="unlink-a-storage-account"></a>Een opslag account ontkoppelen
Als u besluit om een opslag account niet meer te gebruiken voor opname of als u de werk ruimte die u gebruikt, wilt vervangen, moet u de koppeling van de opslag in de werk ruimte ontkoppelen.

Het ontkoppelen van alle opslag accounts van een werk ruimte betekent dat opname probeert te vertrouwen op door service beheerde opslag accounts. Als uw agents worden uitgevoerd op een VNet met beperkte toegang tot internet, wordt de opname naar verwachting mislukt. De werk ruimte moet een gekoppeld opslag account hebben dat bereikbaar is vanuit uw bewaakte resources.

Voordat u een opslag account verwijdert, moet u ervoor zorgen dat alle gegevens erin zijn opgenomen in de werk ruimte. Zorg er als voorzorgsmaatregel voor dat uw opslag account beschikbaar is nadat u een alternatieve opslag hebt gekoppeld. Verwijder het bestand alleen als alle inhoud is opgenomen, en u kunt zien dat er nieuwe gegevens zijn geschreven naar het zojuist verbonden opslag account.


### <a name="api-request"></a>API-aanvraag
```
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

## <a name="replace-a-storage-account"></a>Een opslag account vervangen

Als u een opslag account wilt vervangen dat wordt gebruikt voor opname, maakt u eerst een koppeling voor een nieuw opslag account. De logboek registratie agenten ontvangen de bijgewerkte configuratie en beginnen ook met het verzenden van gegevens naar de nieuwe opslag.

Verwijder de volgende keer de koppeling van het oude opslag account zodat agents niet naar het verwijderde account worden geschreven. Het opname proces bewaart gegevens van dit account totdat het is opgenomen. Verwijder het opslag account pas wanneer u ziet dat alle logboeken zijn opgenomen.

De agent configuratie wordt na een paar minuten vernieuwd en er wordt overgeschakeld naar de nieuwe opslag.

## <a name="manage-storage-account"></a>Opslag account beheren

### <a name="load"></a>Belasting

Opslag accounts kunnen een bepaalde belasting van lees-en schrijf aanvragen afhandelen voordat de aanvragen worden gestart. Beperking is van invloed op de tijd die nodig is om logboeken op te nemen. Dit kan leiden tot verlies van gegevens. Als uw opslag is overbelast, registreert u extra opslag accounts en verspreidt u de belasting ertussen. 

### <a name="related-charges"></a>Gerelateerde kosten

Opslag accounts worden in rekening gebracht op basis van het volume van de opgeslagen gegevens, de typen opslag en het type redundantie. Zie prijzen voor [blok-blobs](https://azure.microsoft.com/pricing/details/storage/blobs/) en [Table Storage prijzen](https://azure.microsoft.com/pricing/details/storage/tables/)voor meer informatie.

Als het geregistreerde opslag account van uw werk ruimte zich in een andere regio bevindt, worden er kosten in rekening gebracht op basis van deze [prijs informatie voor band breedte](https://azure.microsoft.com/pricing/details/bandwidth/).



## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over het instellen van een persoonlijke koppeling [Azure-persoonlijke koppeling gebruiken om een beveiligde verbinding te maken tussen netwerken en Azure monitor](private-link-security.md)
