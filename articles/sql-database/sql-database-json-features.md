---
title: Werken met JSON-gegevens
description: Met Azure SQL Database kunt u gegevens in de JavaScript Object Notation (JSON)-notatie parseren, opvragen en opmaken.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687763"
---
# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Aan de slag met JSON-functies in Azure SQL Database
Met Azure SQL Database kunt u gegevens parseren en opvragen die worden weer gegeven in de indeling van JavaScript Object Notation [(JSON)](https://www.json.org/) en uw relationele gegevens exporteren als JSON-tekst. De volgende JSON-scenario's zijn beschikbaar in Azure SQL Database:
- De [opmaak van relationele gegevens in JSON-indeling](#formatting-relational-data-in-json-format) met behulp `FOR JSON` van-component.
- [Werken met JSON-gegevens](#working-with-json-data)
- [JSON-gegevens opvragen](#querying-json-data) met SCALAIRe JSON-functies.
- De [JSON wordt omgezet in een tabel indeling](#transforming-json-into-tabular-format) met behulp van `OPENJSON` de functie.

## <a name="formatting-relational-data-in-json-format"></a>Relationele gegevens in JSON-indeling opmaken
Als u een webservice hebt die gegevens uit de data base-laag haalt, een reactie in JSON-indeling geeft, of Java script-frameworks of-bibliotheken aan de client zijde die gegevens accepteren die zijn ingedeeld als JSON, kunt u de inhoud van uw data base als JSON rechtstreeks in een SQL-query opmaken. U hoeft niet langer een toepassings code te schrijven die de resultaten van Azure SQL Database als JSON opmaakt, of u kunt een JSON-serialisatie bibliotheek bevatten om tabellaire query resultaten te converteren en vervolgens objecten te serialiseren naar JSON-indeling. In plaats daarvan kunt u de component FOR JSON gebruiken om SQL-query resultaten op te maken als JSON in Azure SQL Database en deze rechtstreeks in uw toepassing te gebruiken.

In het volgende voor beeld worden rijen uit de tabel sales. Customer opgemaakt als JSON met behulp van de FOR JSON-component:

```sql
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

Met de component FOR JSON-pad worden de resultaten van de query opgemaakt als JSON-tekst. Kolom namen worden gebruikt als sleutels, terwijl de celwaarden worden gegenereerd als JSON-waarden:

```json
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

De resultatenset wordt opgemaakt als een JSON-matrix waarbij elke rij wordt opgemaakt als een afzonderlijk JSON-object.

PAD geeft aan dat u de uitvoer indeling van het JSON-resultaat kunt aanpassen met behulp van de punt notatie in kolom aliassen. Met de volgende query wordt de naam van de sleutel ' customername ' in de uitvoer JSON-indeling gewijzigd en worden telefoon-en faxnummers in het SubObject ' contact persoon ' geplaatst:

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

In dit voor beeld hebben we één JSON-object geretourneerd in plaats van een matrix door de [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) -optie op te geven. U kunt deze optie gebruiken als u weet dat u een enkel object retourneert als resultaat van een query.

De belangrijkste waarde van de FOR JSON-component is dat u ingewikkelde hiërarchische gegevens uit uw data base kunt ophalen die zijn opgemaakt als geneste JSON-objecten of-matrices. In het volgende voor beeld ziet u hoe u de rijen `Orders` opneemt uit de `Customer` tabel die horen bij de `Orders`as a geneste matrix van:

```sql
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER
```

In plaats van afzonderlijke query's te verzenden om klant gegevens op te halen en vervolgens een lijst met gerelateerde orders op te halen, kunt u alle benodigde gegevens ophalen met één query, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

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
Als u geen strikt gestructureerde gegevens hebt, kunt u, als u complexe subobjecten, matrices of hiërarchische gegevens hebt, of als uw gegevens structuren zich in de loop van de tijd ontwikkelen, de JSON-indeling helpen om een complexe gegevens structuur te vertegenwoordigen.

JSON is een tekstuele indeling die kan worden gebruikt als elk ander type teken reeks in Azure SQL Database. U kunt JSON-gegevens verzenden of opslaan als een standaard-NVARCHAR:

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

De JSON-gegevens die in dit voor beeld worden gebruikt, worden weer gegeven met het type NVARCHAR (MAX). JSON kan worden ingevoegd in deze tabel of als een argument van de opgeslagen procedure met behulp van de standaard Transact-SQL-syntaxis, zoals wordt weer gegeven in het volgende voor beeld:

```sql
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Elke taal of bibliotheek aan de client zijde die werkt met teken reeks gegevens in Azure SQL Database, werkt ook met JSON-gegevens. JSON kan worden opgeslagen in elke tabel die ondersteuning biedt voor het type NVARCHAR, zoals een tabel die is geoptimaliseerd voor geheugen of een tabel met systeem versies. JSON introduceert geen enkele beperking in de code aan de client zijde of in de data base-laag.

## <a name="querying-json-data"></a>JSON-gegevens opvragen
Als u gegevens hebt opgemaakt als JSON die zijn opgeslagen in Azure SQL-tabellen, kunt u deze gegevens in een SQL-query gebruiken met JSON-functies.

Met de JSON-functies die beschikbaar zijn in Azure SQL database, kunt u gegevens als JSON behandelen als elk ander SQL-gegevens type. U kunt eenvoudig waarden uit de JSON-tekst extra heren en JSON-gegevens gebruiken in een wille keurige query:

```sql
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

Met de functie JSON_VALUE wordt een waarde geëxtraheerd uit JSON-tekst die is opgeslagen in de gegevens kolom. Deze functie maakt gebruik van een Java script-soortgelijk pad om te verwijzen naar een waarde in JSON-tekst die moet worden geëxtraheerd. De geëxtraheerde waarde kan worden gebruikt in elk deel van een SQL-query.

De functie JSON_QUERY is vergelijkbaar met JSON_VALUE. In tegens telling tot JSON_VALUE haalt deze functie complexe subobjecten op, zoals matrices of objecten die in JSON-tekst worden geplaatst.

Met de functie JSON_MODIFY kunt u het pad opgeven van de waarde in de JSON-tekst die moet worden bijgewerkt, evenals een nieuwe waarde die de oude overschrijft. Op deze manier kunt u JSON-tekst eenvoudig bijwerken zonder de volledige structuur opnieuw te parseren.

Omdat JSON is opgeslagen in een standaard tekst, is er geen garantie dat de waarden die zijn opgeslagen in tekst kolommen, goed zijn opgemaakt. U kunt controleren of de tekst die in de JSON-kolom is opgeslagen, juist is ingedeeld met behulp van de standaard Azure SQL Database check-beperkingen en de functie ISJSON:

```sql
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Als de invoer tekst de JSON-indeling heeft, retourneert de functie ISJSON de waarde 1. Bij elke invoeg-of update van een JSON-kolom, wordt met deze beperking gecontroleerd of de nieuwe tekst waarde geen ongeldige JSON is.

## <a name="transforming-json-into-tabular-format"></a>JSON transformeren naar tabellaire indeling
Met Azure SQL Database kunt u JSON-verzamelingen ook transformeren in tabellaire indeling en JSON-gegevens laden of opvragen.

Openjson is een functie voor tabel waarden waarmee JSON-tekst wordt geparseerd, een matrix van JSON-objecten wordt gezocht, de elementen van de matrix worden herhaald en een rij wordt geretourneerd in het uitvoer resultaat voor elk element van de matrix.

![In tabel vorm JSON](./media/sql-database-json-features/image_2.png)

In het bovenstaande voor beeld kunnen we opgeven waar de JSON-matrix wordt gezocht die moet worden geopend (in $. Pad naar orders), welke kolommen moeten worden geretourneerd als resultaat en waar de JSON-waarden worden gevonden die als cellen worden geretourneerd.

We kunnen een JSON-matrix in de @orders variabele omzetten in een set rijen, deze resultaatset analyseren of rijen invoegen in een standaard tabel:

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

De verzameling orders die is opgemaakt als een JSON-matrix en die als een para meter voor de opgeslagen procedure wordt gegeven, kan worden geparseerd en ingevoegd in de tabel Orders.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende bronnen voor meer informatie over het integreren van JSON in uw toepassing:

* [TechNet-blog](https://blogs.technet.microsoft.com/dataplatforminsider/20../../json-in-sql-server-2016-part-1-of-4/)
* [MSDN-documentatie](https://msdn.microsoft.com/library/dn921897.aspx)
* [Channel 9-video](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Als u meer wilt weten over de verschillende scenario's voor het integreren van JSON in uw toepassing, raadpleegt u de demo's in deze [kanaal 9-video](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) of zoekt u een scenario dat overeenkomt met uw use-case in [JSON-blog berichten](https://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).

