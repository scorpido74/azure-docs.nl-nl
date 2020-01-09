---
title: Azure-app configuratie punt-in-time moment opname
description: Een overzicht van hoe point-in-time-snapshot werkt in Azure-appconfiguratie
services: azure-app-configuration
author: yegu-ms
ms.author: yegu
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.openlocfilehash: 4db52ce1897aa5a2b809cb7044b9764baffd0767
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495257"
---
# <a name="point-in-time-snapshot"></a>Momentopname van een bepaald tijdstip

Azure-app configuratie houdt records van de precieze tijden bij het maken van een nieuwe sleutel waarde-paar en vervolgens gewijzigd. Deze records vormen een volledige tijdlijn in sleutel-waardewijzigingen. Een app-configuratie archief kan de geschiedenis van een wille keurige sleutel waarde opnieuw samen stellen en de vorige waarde op elk gewenst moment opnieuw afspelen, al naar gelang. Met deze functie kunt u ' time-reizen ' naar achteren en een oude sleutel waarde ophalen. U kunt bijvoorbeeld de configuratie-instellingen van gisteren ophalen, net vóór de meest recente implementatie, om een vorige configuratie te herstellen en de toepassing terug te draaien.

## <a name="key-value-retrieval"></a>Sleutel/waarde ophalen

Als u de vorige sleutel waarden wilt ophalen, geeft u een tijd op waarop de sleutel waarden moment opname in de HTTP-header van een REST API aanroep. Bijvoorbeeld:

```rest
GET /kv HTTP/1.1
Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT
```

Op dit moment behouden app-configuratie zeven dagen aan wijzigings geschiedenis.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een ASP.NET Core-web-app maken](./quickstart-aspnet-core-app.md)  
