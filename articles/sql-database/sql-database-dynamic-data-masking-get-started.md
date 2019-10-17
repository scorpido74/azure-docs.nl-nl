---
title: Dynamische gegevens maskering voor Azure SQL Database en het Data Warehouse | Micro soft docs
description: Dynamische gegevens maskering beperkt de bloot stelling van gevoelige gegevens door deze te maskeren voor niet-gemachtigde gebruikers voor SQL Database en het Data Warehouse
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
ms.date: 03/04/2019
ms.openlocfilehash: e36e91330232a90ff51cf92ce8dc920b51e2d914
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430115"
---
# <a name="dynamic-data-masking-for-azure-sql-database-and-data-warehouse"></a>Dynamische gegevens maskering voor Azure SQL Database en Data Warehouse

SQL Database dynamische gegevens maskering beperkt de bloot stelling van gevoelige gegevens door deze te maskeren voor niet-bevoegde gebruikers. 

Dynamische gegevensmaskering helpt onbevoegde toegang tot gevoelige gegevens te voorkomen, doordat klanten kunnen aangeven hoeveel van de gevoelige gegevens mag worden vrijgegeven, met minimale gevolgen voor de toepassingslaag. Dit is een beveiligingsfunctie op basis van beleid. De gevoelige gegevens in de resultatenset van een query die is uitgevoerd op toegewezen databasevelden worden verborgen, terwijl de gegevens in de database niet worden gewijzigd.

Een service medewerker in Call Center kan bijvoorbeeld bellers identificeren met verschillende cijfers van hun creditcard nummer, maar deze gegevens items mogen niet volledig worden blootgesteld aan de mede werker van de service. Er kan een maskerings regel worden gedefinieerd waarmee alle, behalve de laatste vier cijfers van een creditcard nummer in de resultatenset van een wille keurige query worden gemaskeerd. Een ander voor beeld is dat er een geschikt gegevens masker kan worden gedefinieerd om PII-gegevens (persoonlijk identificeer bare informatie) te beveiligen, zodat een ontwikkelaar productie omgevingen kan opvragen voor het oplossen van problemen zonder dat nalevings voorschriften worden geschonden.

## <a name="dynamic-data-masking-basics"></a>Basis beginselen van dynamische gegevens maskering

U stelt een beleid voor dynamische gegevens maskering in de Azure Portal in door de bewerking voor dynamische gegevens maskering te selecteren op de Blade SQL Database configuratie of de Blade instellingen.

### <a name="dynamic-data-masking-permissions"></a>Machtigingen voor dynamische gegevens maskering

Dynamische gegevens maskering kan worden geconfigureerd door de rollen Azure SQL Database beheerder, Server beheerder of [SQL Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) .

### <a name="dynamic-data-masking-policy"></a>Beleid voor dynamische gegevens maskering

* **SQL-gebruikers die zijn uitgesloten van maskering** : een set SQL-gebruikers of Aad-identiteiten die niet-gemaskeerde gegevens in de SQL-query resultaten ophalen. Gebruikers met beheerders bevoegdheden zijn altijd uitgesloten van maskering en zien de oorspronkelijke gegevens zonder masker.
* **Maskerings regels** : een set regels waarmee de aangewezen velden worden gedefinieerd die moeten worden gemaskeerd en de maskerings functie die wordt gebruikt. De aangewezen velden kunnen worden gedefinieerd met behulp van de naam van het database schema, de tabel naam en de kolom naam.
* **Masker functies** : een set methoden waarmee de bloot stelling van gegevens voor verschillende scenario's wordt bepaald.

| Maskerings functie | Logica maskeren |
| --- | --- |
| **Standaard** |**Volledige maskering volgens de gegevens typen van de aangewezen velden**<br/><br/>• Gebruik XXXX of minder XS als de grootte van het veld kleiner is dan 4 tekens voor teken reeks gegevens typen (NCHAR, ntext, nvarchar).<br/>• Gebruik de waarde nul voor numerieke gegevens typen (bigint, bit, Decimal, int, Money, numeric, smallint, smallmoney, tinyint, float, Real).<br/>• Gebruik 01-01-1900 voor datum-en tijd gegevens typen (datum, DATETIME2, datetime, date time offset, smalldatetime, time).<br/>• Voor SQL-variant wordt de standaard waarde van het huidige type gebruikt.<br/>• Voor XML wordt het document \<masked/> gebruikt.<br/>• Gebruik een lege waarde voor speciale gegevens typen (Time Stamp-tabel, hierarchyid, GUID, binary, afbeelding, type Spatial). |
| **Credit Card** |**Maskerings methode, waarmee de laatste vier cijfers van de aangewezen velden worden** weer gegeven en een constante teken reeks als voor voegsel wordt toegevoegd in de vorm van een credit card.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-mail** |**Maskerings methode, waarmee de eerste letter wordt weer gegeven en het domein wordt vervangen door xxx.com** met behulp van een constante voor voegsel van een teken reeks in de vorm van een e-mail adres.<br/><br/>aXX@XXXX.com |
| **Wille keurig getal** |**Maskerings methode, waarmee een wille keurig getal wordt gegenereerd** op basis van de geselecteerde grenzen en de werkelijke gegevens typen. Als de aangewezen grenzen gelijk zijn, is de maskerings functie een constant getal.<br/><br/>@no__t deel venster 0Navigation @ no__t-1 |
| **Aangepaste tekst** |**Maskerings methode, waarmee de eerste en laatste tekens worden** weer gegeven en een aangepaste opvullings teken reeks wordt toegevoegd aan het midden. Als de oorspronkelijke teken reeks korter is dan het weer gegeven voor voegsel en achtervoegsel, wordt alleen de opvullings teken reeks gebruikt. <br/>achtervoegsel [opvulling] voor voegsel<br/><br/>@no__t deel venster 0Navigation @ no__t-1 |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Aanbevolen velden om te maskeren

De DDM-engine voor aanbevelingen, markeert bepaalde velden uit uw data base als mogelijk gevoelige velden. Dit kan goede kandidaten zijn voor maskering. In de Blade dynamische gegevens maskering in de portal worden de aanbevolen kolommen voor uw Data Base weer gegeven. U hoeft alleen maar op **masker toevoegen** te klikken voor een of meer kolommen en vervolgens op te **slaan** om een masker voor deze velden toe te passen.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Dynamische gegevens maskering instellen voor uw data base met behulp van Power shell-cmdlets

Zie [Azure SQL database-cmdlets](https://docs.microsoft.com/powershell/module/az.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Dynamische gegevens maskering instellen voor uw data base met behulp van REST API

Zie [bewerkingen voor Azure SQL database](https://docs.microsoft.com/rest/api/sql/).
