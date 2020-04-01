---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 27a254b664e2035cc66109f7a473a3e9ef4d730e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73612044"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> De gegevens kunnen worden geschreven naar blobopslag in de [Apache Avro-indeling,](https://avro.apache.org/) de standaardindeling, of JSON (voorbeeld). 
>    
> De mogelijkheid om de JSON-indeling te coderen is in preview in alle regio's waarin IoT Hub beschikbaar is, behalve Oost-VS, West-VS en West-Europa. De coderingsindeling kan alleen worden ingesteld op het moment dat het eindpunt van de blobopslag is geconfigureerd. De indeling kan niet worden gewijzigd voor een eindpunt dat al is ingesteld. Wanneer u JSON-codering gebruikt, moet u de contentType instellen op JSON en de contentEncoding op UTF-8 in de eigenschappen van het berichtsysteem. 
>
> Zie [richtlijnen voor routering naar opslag voor](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage)meer gedetailleerde informatie over het gebruik van een eindpunt voor blobopslag.
>