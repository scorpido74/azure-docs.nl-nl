---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: ff7ba04271c150018f2c55b62e40542a686608cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176545"
---
## <a name="create-a-client-connection"></a><a name="create-client"></a>Een clientverbinding maken
Maak een clientverbinding door een `WindowsAzure.MobileServiceClient`-object te maken.  Vervang `appUrl` door de URL van uw mobiele app.

```javascript
var client = WindowsAzure.MobileServiceClient(appUrl);
```

## <a name="work-with-tables"></a><a name="table-reference"></a>Werken met tabellen
Maak een verwijzing naar de back-endtabel als u gegevens wilt bekijken of bijwerken. Vervang `tableName` door de naam van uw tabel

```javascript
var table = client.getTable(tableName);
```

Wanneer u een tabelverwijzing hebt, kunt u verder werken aan uw tabel:

* [Een query uitvoeren op een tabel](#querying)
  * [Gegevens filteren](#table-filter)
  * [Door gegevens bladeren](#table-paging)
  * [Gegevens sorteren](#sorting-data)
* [Gegevens invoegen](#inserting)
* [Gegevens wijzigen](#modifying)
* [Gegevens verwijderen](#deleting)

### <a name="how-to-query-a-table-reference"></a><a name="querying"></a>Procedure: Een query uitvoeren op een tabelverwijzing
Als u een tabelverwijzing hebt, kunt u deze gebruiken om een query uit te voeren op gegevens op de server.  Query's worden gemaakt in een LINQ-achtige taal.
Gebruik de volgende code om alle gegevens uit de tabel op te halen:

```javascript
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

De functie voor geslaagde pogingen wordt aangeroepen met de resultaten.  Gebruik `for (var i in results)` niet in de functie voor geslaagde pogingen, aangezien dit wordt herhaald voor informatie die is opgenomen in de resultaten wanneer andere queryfuncties (zoals `.includeTotalCount()`) worden gebruikt.

Zie de [Documentatie over queryobjecten] voor meer informatie over de querysyntaxis.

#### <a name="filtering-data-on-the-server"></a><a name="table-filter"></a>Gegevens filteren op de server
U kunt in de tabelverwijzing een `where`-clausule gebruiken:

```javascript
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

U kunt ook een functie gebruiken die het object filtert.  In dit geval wordt de `this`-variabele toegewezen aan het huidige object dat wordt gefilterd.  De volgende code is functioneel equivalent aan het vorige voorbeeld:

```javascript
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

#### <a name="paging-through-data"></a><a name="table-paging"></a>Doorgegevens heen gaan
Gebruik de methode `take()` en `skip()`.  Bijvoorbeeld als u de tabel wilt splitsen in records met 100 rijen:

```javascript
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

De methode `.includeTotalCount()` wordt gebruikt om een totalCount-veld toe te voegen aan het resultaatobject.  Het veld totalCount wordt gevuld met het totale aantal records dat zou worden geretourneerd als er geen paginering zou zijn gebruikt.

U kunt vervolgens de paginavariabelen en sommige UI-knoppen gebruiken om een paginalijst op te geven; gebruik `loadPage()` om de nieuwe records voor elke pagina te laden.  Implementeer caching voor snellere toegang tot de records die al zijn geladen.

#### <a name="how-to-return-sorted-data"></a><a name="sorting-data"></a>Procedure: Gesorteerde gegevens retourneren
Gebruik de querymethode `.orderBy()` of `.orderByDescending()`:

```javascript
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Zie de [Documentatie over queryobjecten] voor meer informatie over het queryobject.

### <a name="how-to-insert-data"></a><a name="inserting"></a>Procedure: Gegevens invoegen
Maak een JavaScript-object met de juiste datum en roep `table.insert()` asynchroon aan:

```javascript
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

Wanneer het invoegen is geslaagd, wordt het ingevoegde item geretourneerd met de aanvullende velden die vereist zijn voor synchronisatiebewerkingen.  Werk uw eigen cache bij met deze gegevens voor latere updates.

De Azure Mobile Apps Node.js Server SDK biedt ondersteuning voor dynamische schema's voor ontwikkelingsdoeleinden.  Met een dynamisch schema kunt u kolommen toevoegen aan de tabel door deze op te geven in een Insert- of Update-bewerking.  We raden u aan het dynamische schema uit te schakelen voordat u uw toepassing in productie neemt.

### <a name="how-to-modify-data"></a><a name="modifying"></a>Procedure: Gegevens wijzigen
Net als bij de methode `.insert()` moet u een Update-object maken en vervolgens `.update()` aanroepen.  Het Update-object moet de id van het bij te werken record bevatten: de id wordt verkregen tijdens het lezen van het record of het aanroepen van `.insert()`.

```javascript
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

### <a name="how-to-delete-data"></a><a name="deleting"></a>Procedure: Gegevens verwijderen
Roep de methode `.del()` aan als u een record wilt verwijderen.  Geef de id door in een objectverwijzing:

```javascript
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```
