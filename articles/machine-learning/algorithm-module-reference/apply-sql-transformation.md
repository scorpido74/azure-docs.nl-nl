---
title: SQL-trans formatie Toep assen
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de module voor het Toep assen van SQL-trans formatie in Azure Machine Learning-service om een SQLite-query uit te voeren op invoer gegevens sets om de gegevens te transformeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 8eede9d1fbee7d8b70f6b6924b9767b4d3f4e0bf
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72694634"
---
# <a name="apply-sql-transformation"></a>SQL-trans formatie Toep assen

In dit artikel wordt een module van Azure Machine Learning visuele interface beschreven.

Met de module voor het Toep assen van SQL-trans formatie kunt u het volgende doen:
  
-   Maak tabellen voor resultaten en sla de gegevens sets op in een draag bare data base.  
  
-   Aangepaste trans formaties uitvoeren op gegevens typen of aggregaties maken.  
  
-   Voer SQL-query-instructies uit om gegevens te filteren of te wijzigen en de query resultaten te retour neren als een gegevens tabel.  

> [!IMPORTANT]
> De SQL-engine die in deze module wordt gebruikt, is **sqlite**. Voor meer informatie over de syntaxis van SQLite raadpleegt u [SQL zoals geïnterpreteerd door sqlite](https://www.sqlite.org/index.html) voor meer informatie.  

## <a name="how-to-configure-apply-sql-transformation"></a>SQL-trans formatie Toep assen configureren  

De module kan Maxi maal drie gegevens sets als invoer hebben. Wanneer u verwijst naar de gegevens sets die zijn verbonden met elke invoer poort, moet u de namen `t1`, `t2` en `t3` gebruiken. Het tabel nummer geeft de index van de invoer poort aan.  
  
De resterende para meter is een SQL-query die gebruikmaakt van de SQLite-syntaxis. Wanneer u meerdere regels in het tekstvak **SQL script** typt, gebruikt u een punt komma om elke instructie te beëindigen. Anders worden regel einden geconverteerd naar spaties.  

Deze module biedt ondersteuning voor alle standaard instructies van de SQLite-syntaxis. Zie de sectie [technische opmerkingen](#technical-notes) voor een lijst met niet-ondersteunde instructies.

##  <a name="technical-notes"></a>Technische opmerkingen  

Deze sectie bevat implementatie details, tips en antwoorden op veelgestelde vragen.

-   Er is altijd een invoer vereist op poort 1.  
  
-   Voor kolom-id's die spaties of andere speciale tekens bevatten, plaatst u de kolom-id altijd tussen vier Kante haken of dubbele aanhalings teken bij verwijzing naar de kolom in de componenten `SELECT` of `WHERE`.  
  
### <a name="unsupported-statements"></a>Niet-ondersteunde instructies  

Hoewel SQLite veel van de ANSI SQL-standaard ondersteunt, bevat het niet veel functies die worden ondersteund door commerciële relationele database systemen. Zie [SQL as begrepen door sqlite](http://www.sqlite.org/lang.html)voor meer informatie. Houd ook rekening met de volgende beperkingen bij het maken van SQL-instructies:  
  
- SQLite gebruikt dynamische typen voor waarden, in plaats van een type toe te wijzen aan een kolom, zoals in de meeste relationele database systemen. Het is zwak getypt en maakt impliciete type conversie mogelijk.  
  
- `LEFT OUTER JOIN` is geïmplementeerd, maar niet `RIGHT OUTER JOIN` of `FULL OUTER JOIN`.  

- U kunt `RENAME TABLE`-en `ADD COLUMN`-instructies gebruiken met de `ALTER TABLE` opdracht, maar andere componenten worden niet ondersteund, waaronder `DROP COLUMN`, `ALTER COLUMN` en `ADD CONSTRAINT`.  
  
- U kunt een weer gave maken in SQLite, maar daarna zijn de weer gaven alleen-lezen. U kunt een `DELETE`-, `INSERT`-of `UPDATE`-instructie niet uitvoeren op een weer gave. U kunt echter een trigger maken die wordt geactiveerd op een poging om `DELETE`, `INSERT` of `UPDATE` op te geven in een weer gave en andere bewerkingen uit te voeren in de hoofd tekst van de trigger.  
  

Naast de lijst met niet-ondersteunde functies die beschikbaar zijn op de officiële SQLite-site, biedt de volgende wiki een lijst met andere niet-ondersteunde functies: [sqlite-niet-ondersteunde SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 
