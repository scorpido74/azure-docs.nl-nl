---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8c5c0c8f649e7cbab2c16688717de1aaabfb93c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75477146"
---
Deze verwijzing bevat schaalbaarheid en prestatiedoelen voor Azure Storage. De schaalbaarheid s- en prestatiedoelen die hier worden genoemd, zijn high-end doelen, maar zijn haalbaar. In alle gevallen zijn de aanvraagsnelheid en bandbreedte die door uw opslagaccount worden bereikt, afhankelijk van de grootte van de opgeslagen objecten, de gebruikte toegangspatronen en het type werkbelasting dat uw toepassing uitvoert.

Zorg ervoor dat u uw service test om te bepalen of de prestaties aan uw vereisten voldoen. Vermijd indien mogelijk plotselinge pieken in het verkeerstempo en zorg ervoor dat het verkeer goed verdeeld is over partities.

Wanneer uw toepassing de limiet bereikt van wat een partitie voor uw werkbelasting kan verwerken, begint Azure Storage foutcode 503 (Server bezet) of foutcode 500 (Operation Timeout)-antwoorden te retourneren. Als er 503 fouten optreden, u overwegen uw toepassing te wijzigen om een exponentieel back-offbeleid te gebruiken voor nieuwe pogingen. De exponentiële backoff maakt het mogelijk de belasting op de partitie te verminderen, en te verlichten pieken in het verkeer naar die partitie.
