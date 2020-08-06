---
title: Een overlay met Media Encoder Standard maken
description: Meer informatie over het maken van een overlay met Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 85b1b6c56221deaa03057a7ccb658b4bf67124eb
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830577"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Een overlay met Media Encoder Standard maken

Met de Media Encoder Standard kunt u een installatie kopie op een bestaande video bedekken. Momenteel worden de volgende indelingen ondersteund: PNG, JPG, GIF en BMP.

## <a name="prerequisites"></a>Vereisten

* Verzamel de account gegevens die u nodig hebt om de *appsettings.js* in het bestand te configureren in het voor beeld. Als u niet zeker weet hoe u dit moet doen, raadpleegt u [Quick Start: een toepassing registreren bij het micro soft Identity-platform](../../active-directory/develop/quickstart-register-app.md). De volgende waarden worden verwacht in de *appsettings.jsvoor* het bestand.

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Region": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

Als u nog niet bekend bent met trans formaties, is het raadzaam om de volgende activiteiten uit te voeren:

* [Video en audio coderen met behulp van Media Services](encoding-concept.md)
* Lees [hoe u codeert met een aangepaste trans formatie-.net](customize-encoder-presets-how-to.md). Volg de stappen in dit artikel om de .NET in te stellen die nodig zijn om met trans formaties te werken en ga vervolgens hier terug om een voor beeld van een overlay voor te bereiden.
* Bekijk het [referentie document transformeert](/rest/api/media/transforms).

Wanneer u bekend bent met trans formaties, downloadt u het voor beeld van de overlays.

## <a name="overlays-preset-sample"></a>Voor beeld van overlays vooraf ingesteld

Down load het voor [beeld van Media Services-overlay](https://github.com/Azure-Samples/media-services-overlays) om aan de slag te gaan met overlays.

## <a name="next-steps"></a>Volgende stappen

* [Een video afspelen tijdens het coderen met Media Services-.NET](subclip-video-dotnet-howto.md)