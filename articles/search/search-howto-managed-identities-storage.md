---
title: Een verbinding met een opslag account instellen met behulp van een beheerde identiteit (preview)
titleSuffix: Azure Cognitive Search
description: Meer informatie over het instellen van een verbinding met een Indexeer functie met een Azure Storage-account met behulp van een beheerde identiteit (preview)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: ffbc850c580daee5890f9c75021cc518918d098e
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145383"
---
# <a name="set-up-a-connection-to-an-azure-storage-account-using-a-managed-identity-preview"></a>Een verbinding met een Azure Storage account instellen met behulp van een beheerde identiteit (preview)

> [!IMPORTANT] 
> Ondersteuning voor het instellen van een verbinding met een gegevens bron met behulp van een beheerde identiteit bevindt zich momenteel in een open bare preview-versie. Deze previewfunctie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads.
> U kunt toegang tot de preview aanvragen door [dit formulier](https://aka.ms/azure-cognitive-search/mi-preview-request)in te vullen.

Op deze pagina wordt beschreven hoe u een Indexeer functie verbinding kunt instellen met een Azure-opslag account met behulp van een beheerde identiteit in plaats van referenties op te geven in het gegevens bron object connection string.

Voor meer informatie over deze functie kunt u het beste inzicht krijgen in wat een indexer is en hoe u een Indexeer functie instelt voor uw gegevens bron. Meer informatie vindt u op de volgende koppelingen:
* [Overzicht van de indexeerfunctie](search-indexer-overview.md)
* [Indexer van Azure Blob](search-howto-indexing-azure-blob-storage.md)
* [Indexeer functie Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
* [Indexeer functie van Azure-tabel](search-howto-indexing-azure-tables.md)

## <a name="set-up-the-connection"></a>De verbinding instellen

### <a name="1---turn-on-system-assigned-managed-identity"></a>1-door het systeem toegewezen beheerde identiteit inschakelen

Wanneer een door het systeem toegewezen beheerde identiteit is ingeschakeld, maakt Azure een identiteit voor uw zoek service die kan worden gebruikt om te verifiëren bij andere Azure-Services binnen dezelfde Tenant en hetzelfde abonnement. U kunt deze identiteit vervolgens gebruiken in toewijzingen op basis van op rollen gebaseerde toegangs beheer (RBAC) die toegang tot gegevens tijdens het indexeren toestaan.

![Door het systeem toegewezen beheerde identiteit inschakelen](./media/search-managed-identities/turn-on-system-assigned-identity.png "Door het systeem toegewezen beheerde identiteit inschakelen")

Nadat u **Opslaan** hebt geselecteerd, ziet u een object-id die is toegewezen aan uw zoek service.

![Object-id](./media/search-managed-identities/system-assigned-identity-object-id.png "Object-id")
 
### <a name="2---add-a-role-assignment"></a>2-een roltoewijzing toevoegen

In deze stap geeft u uw Azure Cognitive Search-service toestemming om gegevens uit uw opslag account te lezen.

1. Navigeer in het Azure Portal naar het opslag account dat de gegevens bevat die u wilt indexeren.
2. **Toegangs beheer (IAM)** selecteren
3. Selecteer **toevoegen** en vervolgens **functie toewijzing toevoegen**

    ![Roltoewijzing toevoegen](./media/search-managed-identities/add-role-assignment-storage.png "Roltoewijzing toevoegen")

4. Selecteer de gewenste rol (len) op basis van het type opslag account dat u wilt indexeren:
    1. Voor Azure Blob-opslag moet u uw zoek service toevoegen aan de rollen **lezer en gegevens toegang** en **opslag BLOB-gegevens** .
    1. Azure Data Lake Storage Gen2 moet u uw zoek service toevoegen aan de rollen **lezer en gegevens toegang** en **opslag BLOB-gegevens** .
    1. Voor Azure Table-opslag moet u de zoek service alleen toevoegen aan de rol **lezer en gegevens toegang** .
5.  **Wijs toegang toe** als **Azure AD-gebruiker,-groep of-Service-Principal**
6.  Zoek naar uw zoek service, Selecteer deze en selecteer vervolgens **Opslaan** .

    ![Toewijzing van de rol lezer en gegevens toegang toevoegen](./media/search-managed-identities/add-role-assignment-reader-and-data-access.png "Toewijzing van de rol lezer en gegevens toegang toevoegen")

Houd er rekening mee dat wanneer u verbinding maakt met Azure Blob-opslag en Azure Data Lake Storage Gen2, u ook de rol toewijzing van **BLOB-gegevens lezer voor opslag** moet toevoegen.

![Roltoewijzing voor gegevens lezer van opslag-BLOB toevoegen](./media/search-managed-identities/add-role-assignment-storage-blob-data-reader.png "Roltoewijzing voor gegevens lezer van opslag-BLOB toevoegen")

### <a name="3---create-the-data-source"></a>3: de gegevens bron maken

Bij het indexeren van een opslag account moet de gegevens bron de volgende vereiste eigenschappen hebben:

* **naam** is de unieke naam van de gegevens bron in uw zoek service.
* **type**
    * Azure Blob-opslag:`azureblob`
    * Azure-tabel opslag:`azuretable`
    * Azure Data Lake Storage Gen2: het **type** wordt gegeven nadat u zich hebt aangemeld voor het voor beeld met behulp van [dit formulier](https://aka.ms/azure-cognitive-search/mi-preview-request).
* **aanmeldings**
    * Wanneer u een beheerde identiteit gebruikt om te verifiëren, is de notatie van de **referenties** anders dan wanneer u geen beheerde identiteit gebruikt. Hier geeft u een ResourceId op die geen account sleutel of wacht woord heeft. De ResourceId moet de abonnements-ID van het opslag account, de resource groep van het opslag account en de naam van het opslag account bevatten.
    * Indeling beheerde identiteit: 
        * *ResourceId =/Subscriptions/**uw abonnement-id**/resourceGroups/**de naam van uw resource groep**/providers/Microsoft.Storage/storageAccounts/**uw opslag accountnaam**/;*
* **container** bevat een container-of tabel naam in uw opslag account. Standaard kunnen alle blobs in de container worden opgehaald. Als u alleen blobs wilt indexeren in een bepaalde virtuele map, kunt u die directory opgeven met behulp van de optionele **query** parameter.

Voor beeld van het maken van een BLOB-gegevens bron object met behulp van de [rest API](https://docs.microsoft.com/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name/;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}   
```

De Azure Portal en de [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) ondersteunen ook de beheerde identiteiten Connection String. De Azure Portal vereist een functie vlag die aan u wordt gegeven wanneer u zich aanmeldt voor de preview met behulp van de koppeling boven aan deze pagina. 

### <a name="4---create-the-index"></a>4-de index maken

De index specificeert de velden in een document, kenmerken en andere constructies die de zoek ervaring vormen.

Ga als volgt te werk om een index met een doorzoekbaar `content` veld te maken voor het opslaan van de tekst die uit blobs is geëxtraheerd:   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

Zie [index maken](https://docs.microsoft.com/rest/api/searchservice/create-index) voor meer informatie over het maken van indexen

### <a name="5---create-the-indexer"></a>5: de Indexeer functie maken

Een Indexeer functie verbindt een gegevens bron met een doel zoek index en biedt een planning voor het automatiseren van de gegevens vernieuwing.

Zodra de index en gegevens bron zijn gemaakt, kunt u de Indexeer functie maken.

Voor beeld van een indexerings definitie voor een BLOB-Indexer:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Deze Indexeer functie wordt elke twee uur uitgevoerd (schema-interval is ingesteld op "PT2H"). Als u een Indexeer functie elke 30 minuten wilt uitvoeren, stelt u het interval in op ' PT30M '. Het kortste ondersteunde interval is 5 minuten. Het schema is optioneel: als u dit weglaat, wordt een Indexeer functie slechts eenmaal uitgevoerd wanneer deze wordt gemaakt. U kunt echter op elk gewenst moment een Indexeer functie op aanvraag uitvoeren.   

Bekijk [Indexeer functie maken](https://docs.microsoft.com/rest/api/searchservice/create-indexer)voor meer informatie over het maken van Indexeer functie-API.

Zie [Indexeer functies plannen voor Azure Cognitive Search](search-howto-schedule-indexers.md)voor meer informatie over het definiëren van de planningen voor de Indexeer functie.

## <a name="see-also"></a>Zie ook

Meer informatie over Azure Storage Indexeer functies:
* [Indexer van Azure Blob](search-howto-indexing-azure-blob-storage.md)
* [Indexeer functie Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
* [Indexeer functie van Azure-tabel](search-howto-indexing-azure-tables.md)
