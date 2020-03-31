---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70737488"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>FoutconditionHeadersNotSupported van een webtoepassing met Azure-bestanden uit browser

De fout ConditionHeadersNotSupported treedt op wanneer toegang tot inhoud die wordt gehost in Azure-bestanden via een toepassing die gebruik maakt van voorwaardelijke kopteksten, zoals een webbrowser, toegang mislukt. De fout geeft aan dat voorwaardekoppen niet worden ondersteund.

![Fout voorwaardelijke kopteksten van Azure Files](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Oorzaak

Voorwaardelijke kopteksten worden nog niet ondersteund. Toepassingen die ze implementeren, moeten het volledige bestand aanvragen elke keer dat het bestand wordt geopend.

### <a name="workaround"></a>Tijdelijke oplossing

Wanneer een nieuw bestand wordt geüpload, is de eigenschap cachebeheer standaard 'no-cache'. Om de toepassing te dwingen om het bestand elke keer op te vragen, moet de eigenschap cachebeheer van het bestand worden bijgewerkt van "no-cache" naar "no-cache, no-store, must-revalidate". Dit kan worden bereikt met [Azure Storage Explorer.](https://azure.microsoft.com/features/storage-explorer/)

![Wijziging van de cache van de opslagverkenner voor voorwaardelijke kopteksten van Azure Files](media/storage-files-condition-headers/storage-explorer-cache.png)