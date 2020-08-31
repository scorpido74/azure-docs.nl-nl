---
title: Query uitvoeren op de tweelinggrafiek
titleSuffix: Azure Digital Twins
description: Zie een query uitvoeren op de Azure Digital Apparaatdubbels dubbele grafiek voor informatie.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: e6236d9ed5ed75b6b5e10914e668de545c48fc2c
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055631"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Query's uitvoeren op de Azure Digital Apparaatdubbels dubbele grafiek

In dit artikel vindt u voor beelden en meer Details voor het gebruik van de [Azure Digital apparaatdubbels-query taal](concepts-query-language.md) om een query uit te zoeken op de [dubbele grafiek](concepts-twins-graph.md) voor informatie. U voert query's uit in de grafiek met behulp van de Azure Digital Apparaatdubbels- [**query-api's**](how-to-use-apis-sdks.md).

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

In de rest van dit artikel vindt u voor beelden van hoe u deze bewerkingen kunt gebruiken.

## <a name="query-syntax"></a>Querysyntaxis

Deze sectie bevat voorbeeld query's die de query taal structuur illustreren en mogelijke query bewerkingen uitvoeren op [digitale apparaatdubbels](concepts-twins-graph.md).

### <a name="select-top-items"></a>Bovenste items selecteren

U kunt de verschillende ' top ' items in een query selecteren met behulp van de- `Select TOP` component.

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE ...
```

### <a name="query-by-property"></a>Query op eigenschap

Digitale apparaatdubbels ophalen op basis van **Eigenschappen** (waaronder id en meta gegevens):
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> De ID van een digitale dubbele query wordt uitgevoerd met behulp van het veld meta gegevens `$dtId` .

U kunt ook apparaatdubbels ophalen op basis van **het feit of een bepaalde eigenschap is gedefinieerd**. Hier volgt een query waarmee apparaatdubbels worden opgehaald die een gedefinieerde *locatie* -eigenschap hebben:

```sql
SELECT *
FROM DIGITALTWINS WHERE IS_DEFINED(Location)
```

Dit kan u helpen apparaatdubbels te verkrijgen op basis van de *code* -eigenschappen, zoals beschreven in [Tags toevoegen aan digitale apparaatdubbels](how-to-use-tags.md). Hier volgt een query waarmee alle apparaatdubbels met *rood*worden opgehaald:

```sql
select * from digitaltwins where is_defined(tags.red) 
```

U kunt ook apparaatdubbels ophalen op basis van het **type van een eigenschap**. Dit is een query die apparaatdubbels ophaalt waarvan de eigenschap *Tempe ratuur* een getal is:

```sql
SELECT * FROM DIGITALTWINS T
WHERE IS_NUMBER(T.Temperature)
```

### <a name="query-by-model"></a>Query op model

De `IS_OF_MODEL` operator kan worden gebruikt om te filteren op basis van het dubbele [**model**](concepts-models.md). Het ondersteunt overname en heeft verschillende overbelasting opties.

Het eenvoudigste gebruik van `IS_OF_MODEL` heeft alleen een `twinTypeName` para meter: `IS_OF_MODEL(twinTypeName)` .
Hier volgt een query voorbeeld waarin een waarde in deze para meter wordt door gegeven:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1')
```

Als u een dubbele verzameling wilt opgeven wanneer er meer dan één moet worden gezocht `JOIN` , voegt u de `twinCollection` para meter: toe als u er meer dan een wilt zoeken `IS_OF_MODEL(twinCollection, twinTypeName)` .
Hier volgt een query voorbeeld waarmee een waarde voor deze para meter wordt toegevoegd:

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1')
```

Als u een exacte overeenkomst wilt, voegt u de `exact` para meter: toe `IS_OF_MODEL(twinTypeName, exact)` .
Hier volgt een query voorbeeld waarmee een waarde voor deze para meter wordt toegevoegd:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1', exact)
```

U kunt ook alle drie de argumenten tegelijk door geven: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Hier volgt een query voorbeeld waarin een waarde voor alle drie de para meters wordt opgegeven:

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1', exact)
```

### <a name="query-based-on-relationships"></a>Query op basis van relaties

Bij het uitvoeren van query's op basis van de **relaties**van Digital apparaatdubbels heeft de Azure Digital apparaatdubbels-query taal een speciale syntaxis.

Relaties worden opgehaald in het query bereik in de- `FROM` component. Een belang rijk verschil van ' klassiek ' SQL-type talen is dat elke expressie in deze `FROM` component geen tabel is. in plaats daarvan wordt in de `FROM` component een kruis entiteits relatie door lopen en is deze geschreven met een Azure Digital apparaatdubbels-versie van `JOIN` . 

Voor de mogelijkheden van het Azure Digital Apparaatdubbels [model](concepts-models.md) zijn relaties niet onafhankelijk van apparaatdubbels. Dit betekent dat de Azure Digital Apparaatdubbels-query taal `JOIN` iets anders is dan de algemene SQL `JOIN` , omdat hier geen query's kunnen worden uitgevoerd en moeten ze zijn gekoppeld aan een dubbele.
Om dit verschil op te nemen, wordt het sleutel woord `RELATED` in de- `JOIN` component gebruikt om te verwijzen naar een dubbele set relaties. 

In de volgende sectie vindt u enkele voor beelden van hoe dit eruitziet.

> [!TIP]
> Deze functie imiteert de document gerichte functionaliteit van CosmosDB, waar deze `JOIN` kan worden uitgevoerd op onderliggende objecten in een document. CosmosDB maakt gebruik `IN` van het sleutel woord om aan te geven `JOIN` dat het is bedoeld om matrix elementen in het huidige context document te herhalen.

#### <a name="relationship-based-query-examples"></a>Voor beelden van query's op basis van een relatie

Als u een gegevensset wilt ophalen die relaties bevat, gebruikt u één `FROM` instructie gevolgd door N `JOIN` -instructies, waarbij de `JOIN` instructies Express-relaties hebben met betrekking tot het resultaat van een voor gaande `FROM` of- `JOIN` instructie.

Hier volgt een voor beeld van een op relaties gebaseerde query. Dit code fragment selecteert alle digitale-apparaatdubbels met de *id-* eigenschap ' ABC ' en alle digitale apparaatdubbels met betrekking tot deze digitale apparaatdubbels via een *contains* -relatie. 

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC' 
```

>[!NOTE] 
> De ontwikkelaar hoeft dit niet te correleren `JOIN` met een sleutel waarde in de `WHERE` -component (of geef een sleutel waarde op in de `JOIN` definitie). Deze correlatie wordt automatisch berekend door het systeem, omdat de relatie-eigenschappen zelf de doel entiteit identificeren.

#### <a name="query-the-properties-of-a-relationship"></a>De eigenschappen van een relatie opvragen

Net zoals bij digitale apparaatdubbels eigenschappen beschreven via DTDL, kunnen relaties ook eigenschappen hebben. U kunt apparaatdubbels query's uitvoeren op **basis van de eigenschappen van hun relaties**.
Met de Azure Digital Apparaatdubbels-query taal kunnen relaties worden gefilterd en geprojectied door een alias toe te wijzen aan de relatie binnen de `JOIN` component. 

Denk bijvoorbeeld aan een *servicedBy* -relatie die een *reportedCondition* -eigenschap heeft. In de onderstaande query krijgt deze relatie een alias van ' R ' om te kunnen verwijzen naar de eigenschap.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC' 
AND R.reportedCondition = 'clean'
```

In het bovenstaande voor beeld ziet u hoe *reportedCondition* een eigenschap is van de *servicedBy* -relatie zelf (niet van een digitale dubbele verbinding met een *servicedBy* -relatie).

### <a name="query-with-multiple-joins"></a>Query met meerdere samen voegingen

Momenteel wordt een preview-versie van Maxi maal vijf `JOIN` s ondersteund in één query. Zo kunt u meerdere niveaus van relaties tegelijk door lopen.

Hier volgt een voor beeld van een meervoudige samenvoeg query, waarmee alle gloei lampen in de licht panelen in de ruimten 1 en 2 worden opgehaald.

```sql
SELECT LightBulb 
FROM DIGITALTWINS Room 
JOIN LightPanel RELATED Room.contains 
JOIN LightBulb RELATED LightPanel.contains 
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1') 
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1') 
AND Room.$dtId IN ['room1', 'room2'] 
```

### <a name="other-compound-query-examples"></a>Andere samengestelde query voorbeelden

U kunt elk van de bovenstaande typen query's **combi neren** met behulp van combi Naties om meer details in één query op te halen. Hier volgen enkele aanvullende voor beelden van samengestelde query's waarmee een query wordt uitgevoerd voor meer dan één type dubbele descriptor tegelijk.

| Beschrijving | Query’s uitvoeren |
| --- | --- |
| Van de apparaten die *ruimte 123* heeft, retour neren ze de MxChip-apparaten die de rol van operator gebruiken | `SELECT device`<br>`FROM DigitalTwins space`<br>`JOIN device RELATED space.has`<br>`WHERE space.$dtid = 'Room 123'`<br>`AND device.$metadata.model = 'dtmi:contosocom:DigitalTwins:MxChip:3'`<br>`AND has.role = 'Operator'` |
| Apparaatdubbels ophalen die een relatie met de naam *contains bevat* met een andere dubbele id en een *id1* | `SELECT Room`<br>`FROM DIGITIALTWINS Room`<br>`JOIN Thermostat ON Room.Contains`<br>`WHERE Thermostat.$dtId = 'id1'` |
| Alle kamers van dit room-model ophalen die zijn opgenomen in *floor11* | `SELECT Room`<br>`FROM DIGITALTWINS Floor`<br>`JOIN Room RELATED Floor.Contains`<br>`WHERE Floor.$dtId = 'floor11'`<br>`AND IS_OF_MODEL(Room, 'dtmi:contosocom:DigitalTwins:Room;1')` |

## <a name="run-queries-with-an-api-call"></a>Query's uitvoeren met een API-aanroep

Zodra u een query reeks hebt gekozen, voert u deze uit door een aanroep naar de **query-API**te maken.
Het volgende code fragment illustreert deze aanroep van de client-app:

```csharp
var client = new AzureDigitalTwinsAPIClient(<your-credentials>);
client.BaseUri = new Uri(<your-Azure-Digital-Twins-instance-URL>);

QuerySpecification spec = new QuerySpecification("SELECT * FROM digitaltwins");
QueryResult result = await client.Query.QueryTwinsAsync(spec);
```

Deze aanroep retourneert query resultaten in de vorm van een QueryResult-object. 

Query aanroepen ondersteunen paginering. Hier volgt een volledig voor beeld met fout afhandeling en paging:

```csharp
string query = "SELECT * FROM digitaltwins";
try
{
    AsyncPageable<string> qresult = client.QueryAsync(query);
    await foreach (string item in qresult) 
    {
        // Do something with each result
    }
}
catch (RequestFailedException e)
{
    Log.Error($"Error {e.Status}: {e.Message}");
    return null;
}
```

## <a name="query-limitations"></a>Query beperkingen

Er kan een vertraging van Maxi maal tien seconden zijn voordat wijzigingen in uw exemplaar worden weer gegeven in query's. Als u bijvoorbeeld een bewerking voltooit zoals het maken of verwijderen van apparaatdubbels met de DigitalTwins-API, wordt het resultaat mogelijk niet direct weer gegeven in de query-API-aanvragen. Het is voldoende om te wachten op een korte periode.

Er zijn extra beperkingen voor het gebruik van `JOIN` tijdens de preview-versie.
* Er worden geen subquery's ondersteund in de `FROM` instructie.
* `OUTER JOIN` semantische waarden worden niet ondersteund, wat betekent dat als de relatie een positie van nul heeft, de hele rij wordt verwijderd uit de uitvoer resultaatset.
* Tijdens de preview-periode is de diepte van de grafiek traverser beperkt tot vijf `JOIN` niveaus per query.
* De bron voor `JOIN` bewerkingen is beperkt: de query moet de apparaatdubbels declareren waar de query wordt gestart.

## <a name="query-best-practices"></a>Aanbevolen procedures voor query’s

Hieronder vindt u enkele tips voor het uitvoeren van query's met Azure Digital Apparaatdubbels.

* Denk na over het query patroon tijdens de model ontwerp fase. Probeer te controleren of relaties die in één query moeten worden beantwoord, zijn gemodelleerd als een relatie met één niveau.
* Ontwerp eigenschappen zodanig dat grote resultaten sets worden voor komen op basis van grafiek navigatie.
* U kunt het aantal query's dat u nodig hebt aanzienlijk beperken door een matrix met apparaatdubbels te bouwen en query's met de operator te maken `IN` . Denk bijvoorbeeld aan een scenario waarin *gebouwen* *vloeren* en *vloeren* *bevatten.* Als u wilt zoeken naar kamers binnen een gebouw dat heet, kunt u het volgende doen:

    1. Vloeren op basis van relatie zoeken in het gebouw `contains`
        ```sql
        SELECT Floor
        FROM DIGITALTWINS Building
        JOIN Floor RELATED Building.contains
        WHERE Building.$dtId = @buildingId
        ``` 
    2. Als u wilt zoeken naar ruimten, in plaats van de vloeren één voor één te overwegen en een `JOIN` query uit te voeren om de kamers voor elke plaats te vinden, kunt u een query uitvoeren op een verzameling van de vloeren in het gebouw (met de naam *vloer* in de onderstaande query).

        In client-app:
        ```csharp
        var floors = "['floor1','floor2', ..'floorn']"; 
        ```
        In query:
        ```sql
        SELECT Room
        FROM DIGITALTWINS Floor
        JOIN Room RELATED Floor.contains
        WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
        AND Room. Temperature > 72
        AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')
        ```
* Eigenschaps namen en-waarden zijn hoofdletter gevoelig, dus let erop dat u de exacte namen gebruikt die in de modellen zijn gedefinieerd. Als de namen van eigenschappen verkeerd zijn gespeld of onjuist zijn, is de resultatenset leeg zonder dat er fouten worden geretourneerd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Azure Digital Apparaatdubbels api's en sdk's](how-to-use-apis-sdks.md), met inbegrip van de query-API die wordt gebruikt om de query's uit dit artikel uit te voeren.
