---
title: Vereiste parameters verzamelen
services: cognitive-services
author: IEvangelist
manager: nitinme
description: De parameters voor alle containers van Cognitive Services
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2019
ms.author: dapine
ms.openlocfilehash: bf4e8f06642a4a764794a75e2b49f15958796c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73465932"
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