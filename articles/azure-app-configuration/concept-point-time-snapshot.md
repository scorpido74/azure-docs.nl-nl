---
title: Point-in-time-snapshot voor Azure-appconfiguratie | Microsoft Docs
description: Een overzicht van hoe point-in-time-snapshot werkt in Azure-appconfiguratie
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: fdbe4846700c690261dbc734063f4420478666a8
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185203"
---
# <a name="point-in-time-snapshot"></a>Momentopname van een bepaald tijdstip

Azure-app configuratie houdt records van de precieze tijden bij het maken van een nieuwe sleutel waarde-paar en vervolgens gewijzigd. Deze records vormen een volledige tijdlijn in sleutel-waardewijzigingen. Een app-configuratie archief kan de geschiedenis van een wille keurige sleutel waarde opnieuw samen stellen en de vorige waarde op elk gewenst moment opnieuw afspelen, al naar gelang. Met deze functie kunt u ' time-reizen ' naar achteren en een oude sleutel waarde ophalen. U kunt bijvoorbeeld de configuratie-instellingen van gisteren ophalen, net vóór de meest recente implementatie, om een vorige configuratie te herstellen en de toepassing terug te draaien.

## <a name="key-value-retrieval"></a>Sleutel/waarde ophalen

Als u de vorige sleutel waarden wilt ophalen, geeft u een tijd op waarop de sleutel waarden moment opname in de HTTP-header van een REST API aanroep. Bijvoorbeeld:

        GET /kv HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

Op dit moment behouden app-configuratie zeven dagen aan wijzigings geschiedenis.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een ASP.NET Core-web-app maken](./quickstart-aspnet-core-app.md)  
