---
title: Vereiste parameters verzamelen
services: cognitive-services
author: aahill
manager: nitinme
description: De parameters voor alle containers van Cognitive Services
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 85c67384c38376dc6f5701cf26e879d6e1b76c7f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875032"
---
## <a name="gathering-required-parameters"></a>Vereiste parameters verzamelen

Er zijn drie primaire parameters voor alle containers van Cognitive Services die vereist zijn. De licentieovereenkomst voor eindgebruikers (EULA) moet aanwezig `accept`zijn met een waarde van . Daarnaast zijn zowel een Endpoint URL als API Key nodig.

### <a name="endpoint-uri-endpoint_uri"></a>Eindpunt URI`{ENDPOINT_URI}`

De **WAARDE ENDpoint** URI is beschikbaar op de pagina *Overzicht* van azure-portal van de bijbehorende Cognitive Service-bron. Navigeer naar de *pagina Overzicht,* plaats de `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> plaats boven het eindpunt en er wordt een pictogram weergegeven. Kopieer en gebruik waar nodig.

![Verzamel de eindpunturi voor later gebruik](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Sleutels`{API_KEY}`

Deze sleutel wordt gebruikt om de container te starten en is beschikbaar op de pagina Sleutels van de Azure-portal van de bijbehorende Cognitive Service-bron. Navigeer naar de pagina *Sleutels* `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> en klik op het pictogram.

![Een van de twee toetsen ophalen voor later gebruik](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Deze abonnementssleutels worden gebruikt om toegang te krijgen tot uw Cognitive Service API. Deel je sleutels niet. Sla ze veilig op, bijvoorbeeld met Azure Key Vault. We raden ook aan om deze sleutels regelmatig te regenereren. Er is slechts één sleutel nodig om een API-aanroep te maken. Bij het regenereren van de eerste sleutel u de tweede sleutel gebruiken voor verdere toegang tot de service.