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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73612044"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> De gegevens kunnen naar Blob-opslag worden geschreven in de [Apache Avro](https://avro.apache.org/) -indeling, de standaard instelling of JSON (preview). 
>    
> De mogelijkheid om de JSON-indeling te coderen, is in de preview-fase van alle regio's waar IoT Hub beschikbaar is, met uitzonde ring van VS-Oost, VS-West en Europa-west. De coderings indeling kan alleen worden ingesteld op het moment dat het eind punt van de Blob-opslag is geconfigureerd. De indeling kan niet worden gewijzigd voor een eind punt dat al is ingesteld. Wanneer u JSON-code ring gebruikt, moet u het content type instellen op JSON en de contentEncoding naar UTF-8 in de eigenschappen van het bericht systeem. 
>
> Voor meer informatie over het gebruik van een Blob Storage-eind punt raadpleegt [u de richt lijnen voor route ring naar opslag](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage).
>