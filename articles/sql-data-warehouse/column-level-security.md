---
title: Beveiliging op kolomniveau
description: Met beveiliging op kolom niveau (CLS) kunnen klanten de toegang tot database tabel kolommen beheren op basis van de uitvoerings context of het groepslid maatschap van de gebruiker. CLS vereenvoudigt het ontwerp en de code ring van beveiliging in uw toepassing. Met CLS kunt u beperkingen voor kolom toegang implementeren.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
ms.openlocfilehash: 34172e313b537d59287a424683ea1555cbba9b99
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74049162"
---
# <a name="column-level-security"></a>Beveiliging op kolom niveau
Met beveiliging op kolom niveau (CLS) kunnen klanten de toegang tot database tabel kolommen beheren op basis van de uitvoerings context of het groepslid maatschap van de gebruiker.
Bijwerken naar onderstaande video: omdat deze video is gepost, is de [beveiliging op rijniveau](https://docs.microsoft.com/en-us/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017) ook beschikbaar in SQL Data Warehouse. 
> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]

CLS vereenvoudigt het ontwerp en de code ring van beveiliging in uw toepassing. Met CLS kunt u beperkingen voor kolom toegang implementeren om gevoelige gegevens te beveiligen. Bijvoorbeeld, om ervoor te zorgen dat specifieke gebruikers alleen toegang hebben tot bepaalde kolommen van een tabel die relevant is voor hun afdeling. De logica van de toegangs beperking bevindt zich in de database laag, in plaats van dat de gegevens in een andere toepassingslaag worden verwijderd. De-data base past de toegangs beperkingen toe telkens wanneer de toegang tot de gegevens vanuit een wille keurige laag wordt gestart. Deze beperking zorgt ervoor dat uw beveiligings systeem betrouwbaarder en robuuster is door de surface area van uw algehele beveiligings systeem te verminderen. Daarnaast elimineert CLS de nood zaak van de introductie van weer gaven om kolommen uit te filteren voor het opleggen van toegangs beperkingen voor de gebruikers.

U kunt CLS implementeren met de instructie [Grant](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) T-SQL. Met dit mechanisme worden de authenticatie van SQL en Azure Active Directory (AAD) ondersteund.

![compatibiliteit](./media/column-level-security/cls.png)

## <a name="syntax"></a>Syntaxis

```sql
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
In het volgende voor beeld ziet u hoe u ' test User ' beperkt tot de kolom ' SSN ' in de tabel ' Membership ':

De tabel ' Membership ' maken met de kolom SSN die wordt gebruikt voor het opslaan van sociale-beveiligings nummers:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

' Test User ' toestaan om toegang te krijgen tot alle kolommen, met uitzonde ring van de kolom SSN die gevoelige gegevens bevat:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Query's die worden uitgevoerd als ' test User ' mislukken als ze de kolom SSN bevatten:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Gebruiksvoorbeelden
Hier volgen enkele voor beelden van de manier waarop CLS vandaag wordt gebruikt:
- Met een onderneming met financiële services kunnen alleen account beheerders toegang hebben tot sofi-nummers (Social Security Number) van klanten, telefoon nummers en andere persoons gegevens (PII).
- Een Health Care-provider staat toe dat alleen artsen en verpleegt toegang hebben tot gevoelige medische records zonder dat leden van de facturerings afdeling deze gegevens kunnen bekijken.
