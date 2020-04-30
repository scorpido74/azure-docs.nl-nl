---
title: Wat is beveiliging op kolom niveau voor Azure Synapse?
description: Met beveiliging op kolom niveau kunnen klanten de toegang tot database tabel kolommen beheren op basis van de uitvoerings context of het groepslid maatschap van de gebruiker, het ontwerp en de code ring van de beveiliging in uw toepassing vereenvoudigen, en kunt u beperkingen voor kolom toegang implementeren.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687925"
---
# <a name="column-level-security"></a>Beveiliging op kolom niveau

Met beveiliging op kolom niveau kunnen klanten de toegang tot tabel kolommen beheren op basis van de uitvoerings context of het groepslid maatschap van de gebruiker.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Omdat deze video is gepost, is [beveiliging op rijniveau](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) beschikbaar geworden voor Azure Synapse.

Beveiliging op kolom niveau vereenvoudigt het ontwerp en de code ring van beveiliging in uw toepassing, zodat u kolom toegang kunt beperken voor het beveiligen van gevoelige gegevens. Bijvoorbeeld, om ervoor te zorgen dat specifieke gebruikers alleen toegang hebben tot bepaalde kolommen van een tabel die relevant is voor hun afdeling. De logica van de toegangs beperking bevindt zich in de database laag, in plaats van dat de gegevens in een andere toepassingslaag worden verwijderd. De-data base past de toegangs beperkingen toe telkens wanneer gegevens toegang vanuit een wille keurige laag wordt uitgevoerd. Deze beperking zorgt ervoor dat uw beveiliging betrouwbaarder en robuuster wordt door de surface area van uw algehele beveiligings systeem te verminderen. Daarnaast elimineert beveiliging op kolom niveau ook de nood zaak van de introductie van weer gaven om kolommen uit te filteren voor het opleggen van toegangs beperkingen voor de gebruikers.

U kunt beveiliging op kolom niveau implementeren met de instructie [Grant](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL. Met dit mechanisme worden de authenticatie van SQL en Azure Active Directory (AAD) ondersteund.

![compatibiliteit](./media/column-level-security/cls.png)

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

In het volgende voor beeld ziet u `TestUser` hoe u de `SSN` toegang tot de `Membership` kolom van de tabel beperkt:

Tabel `Membership` maken met SSN-kolom die wordt gebruikt voor het opslaan van sociale-beveiligings nummers:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Toegang `TestUser` tot alle kolommen toestaan, behalve de kolom SSN, die de gevoelige gegevens bevat:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Query's die worden `TestUser` uitgevoerd als, mislukken als ze de kolom SSN bevatten:

```sql
SELECT * FROM Membership;

-- Msg 230, Level 14, State 1, Line 12
-- The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Use cases

Hier volgen enkele voor beelden van de manier waarop beveiliging op kolom niveau momenteel wordt gebruikt:

- Met een onderneming met financiële services kunnen alleen account beheerders toegang hebben tot sofi-nummers (Social Security Number) van klanten, telefoon nummers en andere persoons gegevens (PII).
- Een Health Care-provider staat toe dat alleen artsen en verpleegt toegang hebben tot gevoelige medische records en dat leden van de facturerings afdeling deze gegevens niet kunnen bekijken.
