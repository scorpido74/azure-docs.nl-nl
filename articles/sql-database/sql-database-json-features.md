---
title: Werken met JSON-gegevens
description: Met Azure SQL Database u gegevens ontleden, opvragen en opmaken in Json-notatie (JavaScript Object Notation).
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 04/19/2020
ms.openlocfilehash: 992c981d49e7c6fbf8b6156570f6554a05caab5d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687763"
---
# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Aan de slag met JSON-functies in Azure SQL Database
Met Azure SQL Database u gegevens ontleden en query's ontleden die worden weergegeven in de [Json-notatie (JavaScript](https://www.json.org/) Object Notation) en uw relationele gegevens exporteren als JSON-tekst. De volgende JSON-scenario's zijn beschikbaar in Azure SQL Database:
- [Relationele gegevens opmaken in JSON-indeling](#formatting-relational-data-in-json-format) met behulp van `FOR JSON` een clausule.
- [Werken met JSON-gegevens](#working-with-json-data)
- [Opvragen van JSON-gegevens](#querying-json-data) met JSON-scalaire functies.
- [Json omzetten in tabelindeling](#transforming-json-into-tabular-format) met behulp van de `OPENJSON` functie.

## <a name="formatting-relational-data-in-json-format"></a>Relationele gegevens opmaken in JSON-indeling
Als u een webservice hebt die gegevens uit de databaselaag haalt en een antwoord biedt in de JSON-indeling, of JavaScript-frameworks aan clientzijde of bibliotheken die gegevens accepteren die zijn opgemaakt als JSON, u uw database-inhoud rechtstreeks in een SQL-query opmaken als JSON. U hoeft niet langer toepassingscode te schrijven die resultaten van Azure SQL Database opmaakt als JSON, of een JSON-serialisatiebibliotheek op te nemen om tabelqueryresultaten te converteren en vervolgens objecten te serialiseren naar de JSON-indeling. In plaats daarvan u de CLAUSULE VOOR JSON gebruiken om SQL-queryresultaten op te maken als JSON in Azure SQL Database en deze rechtstreeks in uw toepassing te gebruiken.

In het volgende voorbeeld worden rijen uit de tabel Sales.Customer opgemaakt als JSON met behulp van de BEDING VOOR JSON:

```sql
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

De clausule FOR JSON PATH maakt de resultaten van de query op als JSON-tekst. Kolomnamen worden gebruikt als sleutels, terwijl de celwaarden worden gegenereerd als JSON-waarden:

```json
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

De resultaatset wordt opgemaakt als een JSON-array waarbij elke rij is opgemaakt als een afzonderlijk JSON-object.

PATH geeft aan dat u de uitvoerindeling van uw JSON-resultaat aanpassen met behulp van dotnotatie in kolomaliassen. In de volgende query wordt de naam van de sleutel "Klantnaam" in de INDELING VOOR uitvoer-JSON gewijzigd en worden telefoon- en faxnummers in het subobject Contact weergegeven:

```sql
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

De uitvoer van deze query ziet er als volgt uit:

```json
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

In dit voorbeeld hebben we één JSON-object geretourneerd in plaats van een array door de [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) optie op te geven. U deze optie gebruiken als u weet dat u één object retoureert als gevolg van query.

De belangrijkste waarde van de FOR JSON-component is dat u hiermee complexe hiërarchische gegevens uit uw database retourneren die zijn opgemaakt als geneste JSON-objecten of -arrays. In het volgende voorbeeld ziet u `Orders` hoe u `Customer` de rijen uit `Orders`de tabel opneemt die behoren tot de als geneste array van :

```sql
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER
```

In plaats van afzonderlijke query's te verzenden om klantgegevens te krijgen en vervolgens een lijst met gerelateerde orders op te halen, u alle benodigde gegevens met één query ophalen, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```json
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
  ]
}
```

## <a name="working-with-json-data"></a>Werken met JSON-gegevens
Als u geen strikt gestructureerde gegevens hebt, als u complexe subobjecten, arrays of hiërarchische gegevens hebt of als uw gegevensstructuren in de loop van de tijd evolueren, kan de JSON-indeling u helpen om een complexe gegevensstructuur weer te geven.

JSON is een tekstuele indeling die kan worden gebruikt zoals elk ander tekenreekstype in Azure SQL Database. U JSON-gegevens verzenden of opslaan als standaard NVARCHAR:

```sql
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

De JSON-gegevens die in dit voorbeeld worden gebruikt, worden weergegeven door het type NVARCHAR (MAX) te gebruiken. JSON kan in deze tabel worden ingevoegd of als argument van de opgeslagen procedure worden geleverd met behulp van de standaard syntaxis Transact-SQL zoals weergegeven in het volgende voorbeeld:

```sql
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Elke taal aan de clientzijde of bibliotheek die werkt met tekenreeksgegevens in Azure SQL Database, werkt ook met JSON-gegevens. JSON kan worden opgeslagen in elke tabel die het NVARCHAR-type ondersteunt, zoals een voor geheugen geoptimaliseerde tabel of een tabel met systeemversies. JSON voert geen beperking in, noch in de client-side code of in de databaselaag.

## <a name="querying-json-data"></a>JSON-gegevens opvragen
Als u gegevens hebt opgemaakt als JSON die zijn opgeslagen in Azure SQL-tabellen, u met JSON-functies deze gegevens gebruiken in een SQL-query.

Met JSON-functies die beschikbaar zijn in Azure SQL-database, u gegevens die zijn opgemaakt als JSON behandelen als elk ander SQL-gegevenstype. U eenvoudig waarden uit de JSON-tekst extraheren en JSON-gegevens in elke query gebruiken:

```sql
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

Met de functie JSON_VALUE wordt een waarde uit JSON-tekst geëxtraheerd die is opgeslagen in de kolom Gegevens. Deze functie maakt gebruik van een JavaScript-achtig pad om te verwijzen naar een waarde in JSON-tekst om uit te pakken. De geëxtraheerde waarde kan worden gebruikt in elk deel van SQL-query.

De JSON_QUERY functie is vergelijkbaar met JSON_VALUE. In tegenstelling tot JSON_VALUE, worden met deze functie complexe subobjecten geëxtraheerd, zoals arrays of objecten die in JSON-tekst zijn geplaatst.

Met de functie JSON_MODIFY u het pad opgeven van de waarde in de JSON-tekst die moet worden bijgewerkt, evenals een nieuwe waarde die de oude waarde overschrijft. Op deze manier u de JSON-tekst eenvoudig bijwerken zonder de hele structuur te herstellen.

Aangezien JSON is opgeslagen in een standaardtekst, zijn er geen garanties dat de waarden die zijn opgeslagen in tekstkolommen correct zijn opgemaakt. U controleren of tekst die is opgeslagen in de kolom JSON correct is opgemaakt met behulp van standaard azure SQL-databasecontrolebeperkingen en de functie ISJSON:

```sql
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Als de invoertekst JSON correct is opgemaakt, geeft de functie ISJSON de waarde 1 als resultaat. Bij elke invoeg- of update van de KOLOM JSON wordt met deze beperking gecontroleerd of de nieuwe tekstwaarde geen misvormde JSON is.

## <a name="transforming-json-into-tabular-format"></a>JSON omzetten in tabelindeling
Met Azure SQL Database u ook JSON-verzamelingen omzetten in tabelindeling en JSON-gegevens laden of query's uitvoeren of query's uitvoeren.

OPENJSON is een tabelwaardefunctie die JSON-tekst onteert, een array met JSON-objecten zoekt, door de elementen van de array heen loopt en één rij in het uitvoerresultaat voor elk element van de array retourneert.

![JSON-tabel](./media/sql-database-json-features/image_2.png)

In het bovenstaande voorbeeld kunnen we opgeven waar de JSON-array moet worden gevonden die moet worden geopend (in de $. Orderspad), welke kolommen als resultaat moeten worden geretourneerd en waar de JSON-waarden moeten worden gevonden die als cellen worden geretourneerd.

We kunnen een JSON-array in de @orders variabele omzetten in een reeks rijen, deze resultaatset analyseren of rijen invoegen in een standaardtabel:

```sql
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    FROM OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )
END
```

Het verzamelen van orders die zijn opgemaakt als een JSON-array en als parameter voor de opgeslagen procedure wordt verstrekt, kan worden ontleed en in de tabel Orders worden ingevoegd.

## <a name="next-steps"></a>Volgende stappen
Ga voor meer informatie over het integreren van JSON in uw toepassing:

* [TechNet Blog](https://blogs.technet.microsoft.com/dataplatforminsider/20../../json-in-sql-server-2016-part-1-of-4/)
* [MSDN-documentatie](https://msdn.microsoft.com/library/dn921897.aspx)
* [Kanaal 9 video](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Zie de demo's in deze [Kanaal 9-video](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) voor meer informatie over verschillende scenario's voor het integreren van JSON in uw toepassing of een scenario dat overeenkomt met uw use case in [JSON-blogberichten.](https://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/)

