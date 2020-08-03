---
author: baanders
description: include-bestand voor Azure Digital Apparaatdubbels-query bewerkingen
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 70ff1847548c1328a709cf17c02bba3dd25ba213
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87486647"
---
## <a name="query-language-features"></a>Functies voor query taal

Azure Digital Apparaatdubbels biedt uitgebreide query mogelijkheden tegen het dubbele diagram. Query's worden beschreven met behulp van de SQL-achtige syntaxis, in een query taal die vergelijkbaar is met de [IOT hub query taal](../articles/iot-hub/iot-hub-devguide-query-language.md) met veel vergelijk bare functies.

> [!NOTE]
> Alle Azure Digital Apparaatdubbels-query bewerkingen zijn hoofdletter gevoelig.

Hier volgen de bewerkingen die beschikbaar zijn in de Azure Digital Apparaatdubbels query Store-taal.

Digitale apparaatdubbels ophalen op basis van hun...
* model (met behulp van `IS_OF_MODEL` operator)
* eigenschappen (inclusief [Label eigenschappen](../articles/digital-twins/how-to-use-tags.md))
* natie
* relaties
  - eigenschappen van de relaties

U kunt uw query's nog verder verbeteren met de volgende bewerkingen:
* Apparaatdubbels ophalen via meerdere relatie typen ( `JOIN` query's). 
  - Er gelden beperkingen voor het toegestane aantal `JOIN` s (één niveau voor de open bare preview).
* Alleen de bovenste query resultaten selecteren ( `Select TOP` operator)
* Scalaire functies gebruiken: `IS_BOOL` , `IS_DEFINED` , `IS_NULL` , `IS_NUMBER` , `IS_OBJECT` , `IS_PRIMITIVE` , `IS_STRING` , `STARTSWITH` , `ENDSWITH` .
* Vergelijkings operatoren voor query's gebruiken: `IN` / `NIN` , `=` , `!=` , `<` , `>` , `<=` , `>=` .
* Gebruik een combi natie ( `AND` , `OR` , `NOT` operator) van `IS_OF_MODEL` , scalaire functies en vergelijkings operators.
* Voortzetting gebruiken: het query-object wordt geïnstantieerd met een pagina grootte (Maxi maal 100). U kunt de digitale apparaatdubbels één pagina tegelijk ophalen door de vervolg token op te geven in de volgende aanroepen van de API.