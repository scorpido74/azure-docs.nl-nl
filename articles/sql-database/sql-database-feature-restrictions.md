---
title: Functie beperkingen Azure SQL Database
description: Dankzij de beperkingen van Azure SQL Database-functies wordt uw database beveiliging verbeterd door functies in uw data base te beperken die aanvallers kunnen gebruiken om toegang te krijgen tot informatie.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 03/22/2019
ms.openlocfilehash: e9518065b2240d72698ed75f2fa8a7aed343b7bf
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690058"
---
# <a name="azure-sql-database-feature-restrictions"></a>Functie beperkingen Azure SQL Database

Een veelvoorkomende bron van SQL Server aanvallen is via webtoepassingen die toegang hebben tot de data base waar verschillende vormen van SQL-injectie aanvallen worden gebruikt om informatie over de data base te beschikken.  In het ideale geval wordt de toepassings code ontwikkeld zodat SQL-injectie niet is toegestaan.  In grote code bases die verouderde en externe code bevatten, kan er echter nooit zeker van zijn dat alle cases zijn geadresseerd, zodat SQL-injecties veel tijd in beslag nemen.  Het doel van functie beperkingen is om te voor komen dat sommige vormen van SQL-injecties gegevens over de data base lekken, zelfs wanneer de SQL-injectie is geslaagd.

## <a name="enabling-feature-restrictions"></a>Functie beperkingen inschakelen

Het inschakelen van functie beperkingen wordt als volgt uitgevoerd met behulp van de `sp_add_feature_restriction` opgeslagen procedure:

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

De volgende functies kunnen worden beperkt:

| Functie          | Beschrijving |
|------------------|-------------|
| N'ErrorMessages' | Wanneer dit is beperkt, worden alle gebruikers gegevens in het fout bericht gemaskeerd. Zie [fout berichten functie beperking](#error-messages-feature-restriction) |
| N'Waitfor'       | Wanneer dit is beperkt, wordt de opdracht direct zonder vertraging geretourneerd. Zie [waitfor-functie beperking](#waitfor-feature-restriction) |

De waarde van `object_class` kan `N'User'` of `N'Role'` zijn om aan te geven of `object_name` een gebruikers naam of een rolnaam in de data base is.

In het volgende voor beeld worden alle fout berichten voor gebruikers `MyUser` gemaskeerd:

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>Functie beperkingen uitschakelen

Het uitschakelen van de functie beperkingen wordt als volgt uitgevoerd met behulp van de `sp_drop_feature_restriction` opgeslagen procedure:

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

In het volgende voor beeld wordt fout bericht maskering uitgeschakeld voor gebruikers `MyUser`:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>Functie beperkingen weer geven

De weer gave `sys.sql_feature_restrictions` geeft alle momenteel gedefinieerde functie beperkingen voor de data base. Het bevat de volgende kolommen:

| Kolom naam | Gegevenstype | Beschrijving |
|-------------|-----------|-------------|
| Klasse       | nvarchar (128) | Klasse van object waarop de beperking van toepassing is |
| object      | nvarchar (256) | De naam van het object waarop de beperking van toepassing is |
| Hulp     | nvarchar (128) | Functie die is beperkt |

## <a name="feature-restrictions"></a>Functie beperkingen

### <a name="error-messages-feature-restriction"></a>Functie beperking voor fout berichten

Een algemene aanvals methode voor SQL-injectie is het invoegen van code die een fout veroorzaakt.  Als u het fout bericht bekijkt, kan een aanvaller informatie over het systeem achterhalen, zodat er meer gerichte aanvallen mogelijk zijn.  Deze aanval kan vooral nuttig zijn als de toepassing de resultaten van een query niet weergeeft, maar er fout berichten worden weer gegeven.

Overweeg een webtoepassing met een aanvraag in de vorm van:

```html
http://www.contoso.com/employee.php?id=1
```

die de volgende database query uitvoert:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Als de waarde die wordt door gegeven als de `id` para meter voor de aanvraag van de webtoepassing is gekopieerd om $EmpId te vervangen in de database query, kan een aanvaller de volgende aanvraag indienen:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

En de volgende fout wordt geretourneerd, waardoor de aanvaller de naam van de data base kan achterhalen:

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

Na het inschakelen van de functie beperking voor fout berichten voor de toepassings gebruiker in de data base, wordt het geretourneerde fout bericht gemaskeerd zodat er geen interne informatie over de data base wordt gelekt:

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

Op dezelfde manier kan de aanvaller de volgende aanvraag doen:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

En de volgende fout wordt geretourneerd, waardoor de aanvaller het salaris van de werk nemer kan weten:

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

Als u de functie voor fout berichten gebruikt, retourneert de data base het volgende:

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>WAITFOR-functie beperking

Een blinde SQL-injectie is wanneer een toepassing geen aanvaller met de resultaten van de geïnjecteerde SQL of met een fout bericht biedt, maar de aanvaller gegevens uit de data base kan afleiden door een voorwaardelijke query te maken waarin de twee voorwaardelijke vertakkingen Voer een andere hoeveelheid tijd in om uit te voeren. Door de reactie tijd te vergelijken, kan de aanvaller weten welke vertakking werd uitgevoerd en wordt er informatie over het systeem meer in rekening gebracht. De eenvoudigste variant van deze aanval gebruikt de `WAITFOR`-instructie om de vertraging aan te brengen.

Overweeg een webtoepassing met een aanvraag in de vorm van:

```html
http://www.contoso.com/employee.php?id=1
```

die de volgende database query uitvoert:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Als de waarde die wordt door gegeven als de id-para meter voor de aanvragen van de webtoepassing, wordt gekopieerd om $EmpId te vervangen in de database query, kan een aanvaller de volgende aanvraag doen:

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

En de query duurt vijf seconden als het `sa`-account werd gebruikt. Als `WAITFOR` functie beperking is uitgeschakeld in de data base, wordt de instructie `WAITFOR` genegeerd en wordt er geen informatie gelekt met behulp van deze aanval.