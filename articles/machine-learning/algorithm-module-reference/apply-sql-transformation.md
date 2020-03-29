---
title: SQL-transformatie toepassen
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module SQL-transformatie toepassen in Azure Machine Learning om een SQLite-query uit te voeren op invoergegevenssets om de gegevens te transformeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2e44a4861e2522b766aab9c7151d76c471dd2d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314535"
---
# <a name="apply-sql-transformation"></a>SQL-transformatie toepassen

In dit artikel wordt een module van Azure Machine Learning designer (preview) beschreven.

Met de module SQL-transformatie toepassen u het ware doen:
  
-   Maak tabellen voor resultaten en sla de gegevenssets op in een draagbare database.  
  
-   Voer aangepaste transformaties uit op gegevenstypen of maak aggregaten.  
  
-   Sql-queryinstructies uitvoeren om gegevens te filteren of te wijzigen en de queryresultaten als gegevenstabel te retourneren.  

> [!IMPORTANT]
> De SQL-engine die in deze module wordt gebruikt, is **SQLite.** Zie [SQL zoals onderbegrepen door SQLite](https://www.sqlite.org/index.html) voor meer informatie over de syntaxis van SQLite voor meer informatie.  

## <a name="how-to-configure-apply-sql-transformation"></a>SQL-transformatie configureren  

De module kan maximaal drie gegevenssets als ingangen gebruiken. Wanneer u verwijst naar de gegevenssets die zijn `t1`verbonden `t2`met `t3`elke invoerpoort, moet u de namen en . Het tabelnummer geeft de index van de invoerpoort aan.  
  
De resterende parameter is een SQL-query, die de syntaxis van SQLite gebruikt. Wanneer u meerdere regels typt in het SQL Script-tekstvak, gebruikt u een puntkomma om elke instructie te beëindigen. **SQL Script** Anders worden regeleinden omgezet in spaties.  

Deze module ondersteunt alle standaardinstructies van de syntaxis van SQLite. Zie de sectie [Technische notities](#technical-notes) voor een lijst met niet-ondersteunde verklaringen.

##  <a name="technical-notes"></a>Technische notities  

Deze sectie bevat implementatiedetails, tips en antwoorden op veelgestelde vragen.

-   Een ingang is altijd vereist op poort 1.  
  
-   Voor kolom-id's die een spatie of andere speciale tekens bevatten, sluit u altijd de kolom-id in vierkante haakjes of dubbele aanhalingstekens bij verwijzing naar de kolom in de `SELECT` of-clausules. `WHERE`  
  
### <a name="unsupported-statements"></a>Niet-ondersteunde verklaringen  

Hoewel SQLite een groot deel van de ANSI SQL-standaard ondersteunt, bevat het niet veel functies die worden ondersteund door commerciële relationele databasesystemen. Zie [SQL zoals begrepen door SQLite](http://www.sqlite.org/lang.html)voor meer informatie. Houd ook rekening met de volgende beperkingen bij het maken van SQL-instructies:  
  
- SQLite gebruikt dynamisch typen voor waarden, in plaats van een type toe te wijs aan een kolom zoals in de meeste relationele databasesystemen. Het is zwak getypt, en maakt impliciete typeconversie mogelijk.  
  
- `LEFT OUTER JOIN`wordt uitgevoerd, maar `RIGHT OUTER JOIN` `FULL OUTER JOIN`niet of .  

- U kunt `RENAME TABLE` `ADD COLUMN` instructies en `ALTER TABLE` instructies met de opdracht gebruiken, `DROP COLUMN` `ALTER COLUMN`maar `ADD CONSTRAINT`andere clausules worden niet ondersteund, waaronder , en .  
  
- U een weergave binnen SQLite maken, maar daarna worden weergaven alleen-lezen. U `DELETE`een `INSERT`, `UPDATE` of een instructie in een weergave niet uitvoeren. U echter een trigger maken die `DELETE` `INSERT`wordt `UPDATE` geactiveerd bij een poging om, of op een weergave, andere bewerkingen uit te voeren in het hoofd van de trigger.  
  

Naast de lijst van niet-ondersteunde functies op de officiële SQLite-site, biedt de volgende wiki een lijst met andere niet-ondersteunde functies: [SQLite - Niet-ondersteunde SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 
