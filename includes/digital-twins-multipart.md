---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
ms.custom: include file
ms.openlocfilehash: 0e7cb7e4aaa9862a2b4af51593c29793ea54dd14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111202"
---
> [!NOTE]
> Aanvragen voor meerdere delen vereisen doorgaans drie stukken:
> * Een **koptekst van Inhoudstype:**
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * Een **content-disposition:**
>   * `form-data; name="metadata"`
> * De bestandsinhoud die moet worden geüpload
>
> **Content-Type** en **Content-Disposition** zijn afhankelijk van het gebruiksscenario.

Meerdelige aanvragen kunnen programmatisch worden gedaan (via C#), via een REST-client of tool zoals [Postman.](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#make-a-multipart-post-request) REST-clienttools kunnen verschillende ondersteuningsniveaus hebben voor complexe meerdelige aanvragen. Configuratie-instellingen kunnen ook enigszins variëren van gereedschap tot gereedschap. Controleer welke tool het meest geschikt is voor uw behoeften.

> [!IMPORTANT]
> Aanvragen voor meerdere delen die worden ingediend bij de Azure Digital Twins Management API's hebben doorgaans twee delen:
> * Blob-metagegevens (zoals een gekoppeld MIME-type) die is gedeclareerd door **Content-Type** en/of **Inhoudsgezindheid**
> * Blob-inhoud die de ongestructureerde inhoud van een bestand bevat dat moet worden geüpload
>
> Geen van beide delen is **PATCH** vereist voor PATCH-aanvragen. Beide zijn vereist voor **POST** of het maken van bewerkingen.

De [broncode van Occupancy Quickstart](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) bevat volledige C#-voorbeelden die laten zien hoe u meerdelige aanvragen indienen tegen de Azure Digital Twins Management API's.