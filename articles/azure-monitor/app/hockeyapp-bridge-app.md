---
title: HockeyApp-gegevens verkennen in Azure-toepassing Insights | Microsoft Docs
description: Analyseer het gebruik en de prestaties van uw Azure-app met Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/30/2017
ms.openlocfilehash: b14cd38a1db6804a00883ded0b38511fa46c3a52
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819580"
---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>HockeyApp-gegevens in Application Insights verkennen

> [!NOTE]
> HockeyApp is niet langer beschikbaar voor nieuwe toepassingen. Bestaande HockeyApp-implementaties blijven werken. Visual Studio App Center is nu de aanbevolen service van micro soft voor het bewaken van nieuwe mobiele apps. [Meer informatie over het instellen van uw apps met app Center en Application Insights](../../azure-monitor/learn/mobile-center-quickstart.md).

[HockeyApp](https://azure.microsoft.com/services/hockeyapp/) is een service voor het bewaken van Live bureau blad-en mobiele apps. Vanuit HockeyApp kunt u aangepaste en tracering telemetrie verzenden om het gebruik te bewaken en te helpen bij diagnose (naast het verkrijgen van crash gegevens). Deze stroom van telemetrie kan worden opgevraagd met behulp van de krachtige [analyse](../../azure-monitor/app/analytics.md) functie van [Azure-toepassing Insights](../../azure-monitor/app/app-insights-overview.md). Daarnaast kunt u [de aangepaste en telemetrie tracering exporteren](export-telemetry.md). Als u deze functies wilt inschakelen, stelt u een brug in waarmee HockeyApp aangepaste gegevens worden doorgestuurd naar Application Insights.

## <a name="the-hockeyapp-bridge-app"></a>De HockeyApp Bridge-app
De HockeyApp Bridge-app is de belangrijkste functie waarmee u uw aangepaste HockeyApp-en tracerings-telemetrie kunt openen in Application Insights via de functies voor analyse en doorlopend exporteren. Aangepaste en tracerings gebeurtenissen die door HockeyApp worden verzameld nadat de app HockeyApp Bridge is gemaakt, zijn toegankelijk vanuit deze functies. Laten we eens kijken hoe u een van deze Bridge-apps kunt instellen.

Open in HockeyApp account instellingen, [API-tokens](https://rink.hockeyapp.net/manage/auth_tokens). Maak een nieuw token of hergebruik een bestaande. De mini maal vereiste rechten zijn ' alleen-lezen '. Neem een kopie van het API-token.

![Een HockeyApp-API-Token ophalen](./media/hockeyapp-bridge-app/01.png)

Open de Microsoft Azure-portal en [Maak een Application Insights resource](../../azure-monitor/app/create-new-resource.md ). Stel het toepassings type in op ' HockeyApp Bridge Application ':

![Nieuwe Application Insights resource](./media/hockeyapp-bridge-app/02.png)

U hoeft geen naam in te stellen: deze wordt automatisch ingesteld op basis van de naam van de HockeyApp.

De HockeyApp-brug velden worden weer gegeven. 

![Bridge-velden invoeren](./media/hockeyapp-bridge-app/03.png)

Voer het HockeyApp-token in dat u eerder hebt genoteerd. Met deze actie wordt het vervolg keuzemenu ' HockeyApp Application ' gevuld met al uw HockeyApp-toepassingen. Selecteer het item dat u wilt gebruiken en voltooi de rest van de velden. 

Open de nieuwe resource. 

Houd er rekening mee dat de gegevens even duren om te beginnen met stromen.

![Application Insights resource die wacht op gegevens](./media/hockeyapp-bridge-app/04.png)

Dat is alles. Aangepaste en traceer gegevens die vanaf dit punt zijn verzameld in uw app met HockeyApp-instrumentatie, zijn nu ook beschikbaar in de functies voor analyse en doorlopend exporteren van Application Insights.

Laten we elk van deze functies bekijken die nu voor u beschikbaar zijn.

## <a name="analytics"></a>Analyse
Analytics is een krachtig hulp middel voor het uitvoeren van query's op uw gegevens, zodat u uw telemetrie kunt vaststellen en analyseren en snel hoofd oorzaken en patronen kunt ontdekken.

![Analyse](./media/hockeyapp-bridge-app/05.png)

* [Meer informatie over Analytics](../../azure-monitor/log-query/get-started-portal.md)

## <a name="continuous-export"></a>Continue export
Met doorlopend exporteren kunt u uw gegevens exporteren naar een Azure Blob Storage-container. Dit is handig als u uw gegevens langer wilt bewaren dan de retentie periode die momenteel wordt aangeboden door Application Insights. U kunt de gegevens in Blob Storage bewaren, deze verwerken in een SQL Database of uw voorkeurs oplossing voor gegevens opslag.

[Meer informatie over continue export](export-telemetry.md)

## <a name="next-steps"></a>Volgende stappen
* [Analyses Toep assen op uw gegevens](../../azure-monitor/log-query/get-started-portal.md)

