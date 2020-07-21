---
title: Querytaal
titleSuffix: Azure Digital Twins
description: Meer informatie over de basis beginselen van de Azure Digital Apparaatdubbels query Store-taal.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b3ed5d6605097b31dfaa58a2d37e71d3a6702bee
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537486"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Over de query taal voor Azure Digital Apparaatdubbels

Het midden van Azure Digital Apparaatdubbels is het [**dubbele diagram**](concepts-twins-graph.md), dat is geconstrueerd op basis van **digitale apparaatdubbels** en **relaties**. Deze grafiek kan worden opgevraagd om informatie te krijgen over de digitale apparaatdubbels en de relaties hierin. Deze query's worden geschreven in een aangepaste SQL-achtige query taal, genaamd **Azure Digital Apparaatdubbels query Store-taal**.

Als u een query wilt verzenden naar de service vanuit een client-app, gebruikt u de Azure Digital Apparaatdubbels- [**query-API**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview). Hierdoor kunnen ontwikkel aars query's schrijven en filters toep assen om sets van digitale apparaatdubbels te vinden in de dubbele grafiek, en andere informatie over het Azure Digital Apparaatdubbels-scenario.

## <a name="query-language-features"></a>Functies voor query taal

Azure Digital Apparaatdubbels biedt uitgebreide query mogelijkheden tegen het dubbele diagram. Query's worden beschreven met behulp van de SQL-achtige syntaxis, in een query taal die vergelijkbaar is met de [IOT hub query taal](../iot-hub/iot-hub-devguide-query-language.md) met veel vergelijk bare functies.

> [!NOTE]
> Alle Azure Digital Apparaatdubbels-query bewerkingen zijn hoofdletter gevoelig.

Hier volgen de bewerkingen die beschikbaar zijn in de Azure Digital Apparaatdubbels query Store-taal:
* De eigenschappen van apparaatdubbels worden opgehaald via Digital apparaatdubbels.
* Haal apparaatdubbels op door de interfaces van Digital apparaatdubbels.
* Apparaatdubbels op basis van relatie-eigenschappen ophalen.
* Apparaatdubbels ophalen via meerdere relatie typen ( `JOIN` query's). Er gelden beperkingen voor het toegestane aantal `JOIN` s (één niveau voor de open bare preview).
* Gebruik aangepaste functie `IS_OF_MODEL(twinCollection, twinTypeName)` , waarmee filtering op basis van het dubbele [model](concepts-models.md)mogelijk is. Het ondersteunt overname.
* Scalaire functies gebruiken: `IS_BOOL` , `IS_DEFINED` , `IS_NULL` , `IS_NUMBER` , `IS_OBJECT` , `IS_PRIMITIVE` , `IS_STRING` , `STARTS_WITH` , `ENDS_WITH` .
* Vergelijkings operatoren voor query's gebruiken: `IN` / `NIN` , `=` , `!=` , `<` , `>` , `<=` , `>=` .
* Gebruik een combi natie ( `AND` , `OR` , `NOT` operator) van de bovenstaande.
* Voortzetting gebruiken: het query-object wordt geïnstantieerd met een pagina grootte (Maxi maal 100). U kunt de digitale apparaatdubbels één pagina tegelijk ophalen door de vervolg token op te geven in de volgende aanroepen van de API.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het schrijven van query's en het bekijken van voor beelden van client code in [*How-to: Query's uitvoeren op de dubbele grafiek*](how-to-query-graph.md).
