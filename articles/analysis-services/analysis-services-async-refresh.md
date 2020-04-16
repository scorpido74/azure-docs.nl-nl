---
title: Asynchrone vernieuwing voor Azure Analysis Services-modellen | Microsoft Documenten
description: Beschrijft hoe u de AZURE Analysis Services REST API gebruiken om asynchrone vernieuwing van modelgegevens te coderen.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c5f6cec8b7fd1169a4f04649fcaf7bb7ada33833
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406290"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Asynchroon vernieuwen met de REST API

Door elke programmeertaal te gebruiken die REST-oproepen ondersteunt, u asynchrone gegevensvernieuwingsbewerkingen uitvoeren op uw tabelmodellen van Azure Analysis Services. Dit omvat synchronisatie van alleen-lezen replica's voor queryscale-out. 

Gegevensvernieuwen kan enige tijd in beslag nemen, afhankelijk van een aantal factoren, waaronder het gegevensvolume, het optimalisatieniveau met behulp van partities, enz. Deze bewerkingen worden traditioneel aangeroepen met bestaande methoden zoals het gebruik van [TOM](https://docs.microsoft.com/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (Tabelvormig objectmodel), [PowerShell-cmdlets](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) of [TMSL](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (Tabelmodelscripttaal). Deze methoden kunnen echter vaak onbetrouwbare, langlopende HTTP-verbindingen vereisen.

Met de REST API voor Azure Analysis Services kunnen bewerkingen voor gegevensvernieuwing asynchroon worden uitgevoerd. Door gebruik te maken van de REST API zijn langlopende HTTP-verbindingen van clienttoepassingen niet nodig. Er zijn ook andere ingebouwde functies voor betrouwbaarheid, zoals automatische retries en batched commits.

## <a name="base-url"></a>Basis-URL

De basis-URL volgt deze indeling:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Overweeg bijvoorbeeld een model met de naam `myserver`AdventureWorks op een server met de naam , gelegen in de West US Azure-regio. De servernaam is:

```
asazure://westus.asazure.windows.net/myserver 
```

De basis-URL voor deze servernaam is:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Door gebruik te maken van de basis-URL kunnen resources en bewerkingen worden toegevoegd op basis van de volgende parameters: 

![Async vernieuwen](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Alles wat eindigt in **s** is een collectie.
- Alles wat eindigt met **()** is een functie.
- Iets anders is een bron / object.

U bijvoorbeeld het werkwoord POST in de collectie Vernieuwen gebruiken om een vernieuwingsbewerking uit te voeren:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Verificatie

Alle aanroepen moeten worden geverifieerd met een geldig Azure Active Directory -token (OAuth 2) in de kopautorisatie en moeten voldoen aan de volgende vereisten:

- Het token moet een gebruikerstoken of een aangever van de toepassingsservice zijn.
- Het token moet de juiste `https://*.asazure.windows.net`doelgroep hebben ingesteld op .
- De gebruiker of toepassing moet over voldoende machtigingen op de server of het model beschikken om de gevraagde oproep te kunnen doen. Het machtigingsniveau wordt bepaald door rollen binnen het model of de beheergroep op de server.

    > [!IMPORTANT]
    > Momenteel zijn **serverbeheerdersfunctiemachtigingen** nodig.

## <a name="post-refreshes"></a>POST /refreshes

Als u een vernieuwingsbewerking wilt uitvoeren, gebruikt u het werkwoord POST in de verzameling /refreshes om een nieuw vernieuwend item aan de verzameling toe te voegen. De koptekst Locatie in het antwoord bevat de vernieuwings-id. De clienttoepassing kan de status later loskoppelen en controleren indien nodig omdat deze asynchroon is.

Voor een model wordt slechts één vernieuwingsbewerking tegelijk geaccepteerd. Als er een huidige bewerking voor het vernieuwen van uitgevoerd is en er een andere is ingediend, wordt de HTTP-statuscode 409 Conflict geretourneerd.

Het lichaam kan op het volgende lijken:

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>Parameters

Het opgeven van parameters is niet vereist. De standaardinstelling wordt toegepast.

| Naam             | Type  | Beschrijving  |Standaard  |
|------------------|-------|--------------|---------|
| `Type`           | Enum  | Het type verwerking dat moet worden uitgevoerd. De typen zijn uitgelijnd met de [vernieuwingstypen van](https://docs.microsoft.com/analysis-services/tmsl/refresh-command-tmsl) TMSL: volledig, clearValues, calculate, dataOnly, automatisch en defragment. Type toevoegen wordt niet ondersteund.      |   automatisch      |
| `CommitMode`     | Enum  | Hiermee bepaalt u of objecten in batches worden vastgelegd of alleen wanneer deze zijn voltooid. Modi zijn: standaard, transactioneel, gedeeltelijkBatch.  |  Transactionele       |
| `MaxParallelism` | Int   | Deze waarde bepaalt het maximum aantal threads waarop verwerkingsopdrachten parallel moeten worden uitgevoerd. Deze waarde is uitgelijnd met de eigenschap MaxParallelisme die kan worden ingesteld in de opdracht TMSL [Sequence](https://docs.microsoft.com/analysis-services/tmsl/sequence-command-tmsl) of met andere methoden.       | 10        |
| `RetryCount`     | Int   | Geeft aan hoe vaak de bewerking opnieuw wordt geprobeerd voordat deze mislukt.      |     0    |
| `Objects`        | Matrix | Een array met objecten die moeten worden verwerkt. Elk object bevat: "tabel" bij het verwerken van de hele tabel of "tabel" en "partitie" bij het verwerken van een partitie. Als er geen objecten zijn opgegeven, wordt het hele model vernieuwd. |   Het hele model verwerken      |

CommitMode is gelijk aan gedeeltelijkBatch. Het wordt gebruikt bij het doen van een eerste belasting van grote gegevenssets die uren kunnen duren. Als de vernieuwingsbewerking mislukt nadat een of meer batches zijn vastgelegd, blijven de met succes vastgelegde batches vastgelegd (deze worden niet met succes vastgelegde batches teruggedraaid).

> [!NOTE]
> Op het moment van schrijven is de batchgrootte de waarde maxparallelïsme, maar deze waarde kan veranderen.

### <a name="status-values"></a>Statuswaarden

|Statuswaarde  |Beschrijving  |
|---------|---------|
|`notStarted`    |   De operatie is nog niet begonnen.      |
|`inProgress`     |   Operatie in volle gang.      |
|`timedOut`     |    Er is een time-out op basis van een time-out van de gebruiker.     |
|`cancelled`     |   Bewerking geannuleerd door gebruiker of systeem.      |
|`failed`     |   De bewerking is mislukt.      |
|`succeeded`      |   De operatie is geslaagd.      |

## <a name="get-refreshesrefreshid"></a>GET /refreshes/\<refreshId>

Als u de status van een vernieuwingsbewerking wilt controleren, gebruikt u het werkwoord GET op de vernieuwings-id. Hier is een voorbeeld van het reactielichaam. Als de bewerking aan `inProgress` de gang is, wordt deze als status geretourneerd.

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>GET /refreshs

Als u een lijst met historische vernieuwingsbewerkingen voor een model wilt ophalen, gebruikt u het werkwoord GET in de verzameling /refreshes. Hier is een voorbeeld van het reactielichaam. 

> [!NOTE]
> Op het moment van schrijven worden de laatste 30 dagen van vernieuwingsbewerkingen opgeslagen en geretourneerd, maar dit aantal kan veranderen.

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-09T01:58:04.76",
        "endTime": "2017-12-09T01:58:12.607",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T02:05:48.32",
        "endTime": "2017-12-07T02:05:54.913",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>Verwijderen /vernieuwen/\<vernieuwenId>

Als u een bewerking voor het vernieuwen in uitvoering wilt annuleren, gebruikt u het werkwoord Verwijderen op de vernieuwings-id.

## <a name="post-sync"></a>POST /sync

Nadat u vernieuwingsbewerkingen hebt uitgevoerd, kan het nodig zijn om de nieuwe gegevens te synchroniseren met replica's voor het uitschalen van query's. Als u een synchronisatiebewerking voor een model wilt uitvoeren, gebruikt u het werkwoord POST op de functie /sync. De koptekst Locatie in het antwoord bevat de synchronisatie-id.

## <a name="get-sync-status"></a>Get /sync-status

Als u de status van een synchronisatiebewerking wilt controleren, gebruikt u het werkwoord GET dat de bewerkings-id als parameter doorgeeft. Hier is een voorbeeld van de reactie body:

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

Waarden `syncstate`voor :

- 0: Repliceren. Databasebestanden worden gerepliceerd naar een doelmap.
- 1: Hydrateren. De database wordt gerehydrateerd op alleen-lezen server-instantie(s).
- 2: Voltooid. De synchronisatiebewerking is voltooid.
- 3: Mislukt. De synchronisatiebewerking is mislukt.
- 4: Afronding. De synchronisatiebewerking is voltooid, maar voert opruimstappen uit.

## <a name="code-sample"></a>Codevoorbeeld

Hier is een C# code voorbeeld om u op weg te helpen, [RestApiSample op GitHub](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Het codevoorbeeld gebruiken

1.    Kloon of download de repo. Open de RestApiSample-oplossing.
2.    Zoek de **lijnclient. BaseAddress = ...** en geef uw [basis-URL](#base-url).

Het codevoorbeeld maakt gebruik van [servicehoofdverificatie.](#service-principal)

### <a name="service-principal"></a>Service-principal

Zie [Serviceprincipal maken - Azure-portal](../active-directory/develop/howto-create-service-principal-portal.md) en [Voeg een serviceprincipal toe aan de serverbeheerderrol](analysis-services-addservprinc-admins.md) voor meer informatie over het instellen van een serviceprincipal en het toewijzen van de benodigde machtigingen in Azure AS. Voer de volgende extra stappen uit nadat u de stappen hebt voltooid:

1.    Zoek in het codevoorbeeld **tekenreeksautoriteit = ...**, vervang **gewoons** met de tenant-id van uw organisatie.
2.    Opmerking/uncomment, zodat de klasse ClientCredential wordt gebruikt om het cred-object te instantiëren. Zorg \<ervoor dat \<de>-waarden van de app-id-> id en app-sleutel op een veilige manier worden geopend of gebruik verificatie op basis van certificaten voor serviceprincipals.
3.    Voet het voorbeeld uit.


## <a name="see-also"></a>Zie ook

[Monsters](analysis-services-samples.md)   
[REST-API](https://docs.microsoft.com/rest/api/analysisservices/servers)   


