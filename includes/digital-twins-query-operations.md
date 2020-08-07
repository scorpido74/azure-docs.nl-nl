---
author: baanders
description: include-bestand voor Azure Digital Apparaatdubbels-query bewerkingen
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 6dfddd9179569075abdbd94b6c1afa719db9ba1e
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905585"
---
## <a name="query-language-features"></a>Functies voor query taal

Azure Digital Apparaatdubbels biedt uitgebreide query mogelijkheden tegen het dubbele diagram. Query's worden beschreven met behulp van de SQL-achtige syntaxis, in een query taal die vergelijkbaar is met de [IOT hub query taal](../articles/iot-hub/iot-hub-devguide-query-language.md) met veel vergelijk bare functies.

> [!NOTE]
> Alle Azure Digital Apparaatdubbels-query bewerkingen zijn hoofdletter gevoelig.

Hier volgen de bewerkingen die beschikbaar zijn in de Azure Digital Apparaatdubbels-query taal.

Digitale apparaatdubbels ophalen op basis van hun...
* model (met behulp van `IS_OF_MODEL` operator)
* eigenschappen (inclusief [Label eigenschappen](../articles/digital-twins/how-to-use-tags.md))
* natie
* relaties
  - eigenschappen van de relaties

U kunt uw query's nog verder verbeteren met de volgende bewerkingen:
* Apparaatdubbels ophalen via meerdere relatie typen ( `JOIN` query's). 
  - Tijdens de preview zijn Maxi maal vijf niveaus `JOIN` toegestaan.
* Alleen de bovenste query resultaten selecteren ( `Select TOP` operator)
* Scalaire functies gebruiken: `IS_BOOL` , `IS_DEFINED` , `IS_NULL` , `IS_NUMBER` , `IS_OBJECT` , `IS_PRIMITIVE` , `IS_STRING` , `STARTSWITH` , `ENDSWITH` .
* Vergelijkings operatoren voor query's gebruiken: `IN` / `NIN` , `=` , `!=` , `<` , `>` , `<=` , `>=` .
* Gebruik een combi natie ( `AND` , `OR` , `NOT` operator) van `IS_OF_MODEL` , scalaire functies en vergelijkings operators.
* Voortzetting gebruiken: het query-object wordt geïnstantieerd met een pagina grootte (Maxi maal 100). U kunt de digitale apparaatdubbels één pagina tegelijk ophalen door de vervolg token op te geven in de volgende aanroepen van de API.