---
title: Wat is beveiliging op kolomniveau voor Azure Synapse?
description: Met beveiliging op kolomniveau kunnen klanten de toegang tot databasetabelkolommen beheren op basis van de uitvoeringscontext of het groepslidmaatschap van de gebruiker, waardoor het ontwerp en de codering van beveiliging in uw toepassing worden vereenvoudigd en u beperkingen op kolomtoegang implementeren.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/19/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: b0a783ad5db86ca783ff1cebceec8d77ab528047
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687925"
---
# <a name="column-level-security"></a>Beveiliging op kolomniveau

Met beveiliging op kolomniveau kunnen klanten de toegang tot tabelkolommen beheren op basis van de uitvoeringscontext of groepslidmaatschap van de gebruiker.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Sinds deze video is [geplaatst,](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) is Row level Security beschikbaar voor Azure Synapse.

Beveiliging op kolomniveau vereenvoudigt het ontwerp en de codering van beveiliging in uw toepassing, zodat u de toegang tot kolommen beperken om gevoelige gegevens te beschermen. Bijvoorbeeld ervoor zorgen dat specifieke gebruikers alleen toegang hebben tot bepaalde kolommen van een tabel die relevant zijn voor hun afdeling. De logica voor toegangsbeperking bevindt zich in de databaselaag in plaats van in plaats van in de buurt van de gegevens in een andere toepassingslaag. De database past de toegangsbeperkingen toe telkens wanneer gegevenstoegang vanaf elke laag wordt geprobeerd. Deze beperking maakt uw beveiliging betrouwbaarder en robuuster door het verkleinen van de oppervlakte van uw algehele beveiligingssysteem. Bovendien elimineert beveiliging op kolomniveau ook de noodzaak voor het introduceren van weergaven om kolommen uit te filteren voor het opleggen van toegangsbeperkingen aan de gebruikers.

U beveiliging op kolomniveau implementeren met de [GRANT](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL-instructie. Met dit mechanisme worden zowel SQL- als Azure Active Directory -verificatie (AAD) ondersteund.

![Cls](./media/column-level-security/cls.png)

## <a name="syntax"></a>Syntaxis

```syntaxsql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>Voorbeeld

In het volgende voorbeeld `TestUser` ziet u `SSN` hoe `Membership` u de toegang tot de kolom van de tabel beperken:

Tabel `Membership` maken met de Kolom SSN die wordt gebruikt om burgerservicenummers op te slaan:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Toegang `TestUser` tot alle kolommen, behalve de Kolom SSN, die de gevoelige gegevens bevat:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Query's die `TestUser` worden uitgevoerd als ze mislukken als ze de kolom SSN bevatten:

```sql
SELECT * FROM Membership;

-- Msg 230, Level 14, State 1, Line 12
-- The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Use Cases

Enkele voorbeelden van hoe beveiliging op kolomniveau vandaag wordt gebruikt:

- Een financiële dienstverlener staat alleen accountmanagers toe om toegang te hebben tot burgerservicenummers (SSN), telefoonnummers en andere persoonlijk identificeerbare informatie (PII).
- Een zorgverlener staat alleen artsen en verpleegkundigen om toegang te hebben tot gevoelige medische dossiers, terwijl het voorkomen van leden van de facturering afdeling van het bekijken van deze gegevens.
