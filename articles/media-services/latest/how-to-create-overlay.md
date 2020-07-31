---
title: Een overlay met Media Encoder Standard maken
description: Meer informatie over het maken van een overlay met Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 0d1a6d5626e081ff50f65b3a4396e223b33f920d
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433589"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Een overlay met Media Encoder Standard maken

Met de Media Encoder Standard kunt u een installatie kopie op een bestaande video bedekken. Momenteel worden de volgende indelingen ondersteund: PNG, JPG, GIF en BMP.

## <a name="prerequisites"></a>Vereisten

* Verzamel de account gegevens die u nodig hebt om de *appsettings.js* in het bestand te configureren in het voor beeld. Als u niet zeker weet hoe u dit moet doen, raadpleegt u [Quick Start: een toepassing registreren bij het micro soft Identity-platform](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). De volgende waarden worden verwacht in de *appsettings.jsvoor* het bestand.

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
* Bekijk het [referentie document transformeert](https://docs.microsoft.com/rest/api/media/transforms).

Wanneer u bekend bent met trans formaties, downloadt u het voor beeld van de overlays.

## <a name="overlays-preset-sample"></a>Voor beeld van overlays vooraf ingesteld

Down load het voor [beeld van Media Services-overlay](https://github.com/Azure-Samples/media-services-overlays) om aan de slag te gaan met overlays.

## <a name="next-steps"></a>Volgende stappen

* [Een video afspelen tijdens het coderen met Media Services-.NET](subclip-video-dotnet-howto.md)