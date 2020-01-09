---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 12/12/2019
ms.custom: include file
ms.openlocfilehash: 6fa21d78d766ee8e541d6d60f64628b55a84fe41
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469238"
---
> [!NOTE]
> Voor meerdelige aanvragen zijn doorgaans drie onderdelen vereist:
> * Een **Content-type-** header:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * Een **Content-Disposition**:
>   * `form-data; name="metadata"`
> * De bestands inhoud die moet worden geüpload
>
> **Het inhouds type** en de **verplaatsingen van inhoud** variëren afhankelijk van het gebruiks scenario.

Meerdelige aanvragen kunnen via een programma worden gemaakt ( C#via), via een rest-client of hulp programma zoals [postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#make-a-multipart-post-request). REST-client hulpprogramma's kunnen verschillende ondersteunings niveaus voor complexe meerdelige aanvragen hebben. Configuratie-instellingen kunnen ook enigszins afwijken van het hulp programma. Controleer welk hulp programma het meest geschikt is voor uw behoeften.

> [!IMPORTANT]
> Meerdelige aanvragen voor de Azure Digital Apparaatdubbels Management-Api's hebben doorgaans twee delen:
> * BLOB-meta gegevens (zoals een gekoppeld MIME-type) die worden gedeclareerd door **inhouds type** en/of **Content-Disposition**
> * Blob-inhoud die de ongestructureerde inhoud bevat van een bestand dat moet worden geüpload
>
> Geen van beide onderdelen is vereist voor **patch** -aanvragen. Beide zijn vereist voor **post** -of Create-bewerkingen.

De [bron code](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) voor de Quick Start C# van de bezetting bevat volledige voor beelden die laten zien hoe u meerdelige aanvragen kunt maken voor de Azure Digital Apparaatdubbels-beheer-api's.