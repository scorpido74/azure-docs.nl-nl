---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: roy-har
manager: diberry
ms.service: cognitive-services
ms.date: 06/03/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: roy-har
ms.openlocfilehash: 8e67a6d0c98a3839922a79e9b452465087da1b69
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84418011"
---
1. Selecteer [pizza-app-for-Luis-V6. json](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json) om de GitHub-pagina voor het bestand weer te geven `pizza-app-for-luis.json` .
1. Klik met de rechter muisknop of op de **onbewerkte** knop en selecteer **Koppeling opslaan als** om de `pizza-app-for-luis.json` op uw computer op te slaan.
1. Meld u aan bij de [Luis-Portal](https://www.luis.ai).
1. Selecteer [mijn apps](https://www.luis.ai/applications).
1. Selecteer op de pagina **mijn apps** **+ nieuwe app voor gesprek**.
1. Selecteer **importeren als JSON**.
1. Selecteer in het dialoog venster **nieuwe app importeren** de knop **bestand kiezen** .
1. Selecteer het `pizza-app-for-luis.json` bestand dat u hebt gedownload en selecteer vervolgens **openen**.
1. Voer in het veld de **naam** van het dialoog venster **nieuwe app importeren** een naam in voor uw pizza-app en selecteer vervolgens de knop **gereed** .

De app wordt geïmporteerd.

Als u het dialoog venster ziet **hoe u een efficiënte Luis-app maakt**, sluit u het dialoog venster.

## <a name="train-and-publish-the-pizza-app"></a>De pizza-app trainen en publiceren

De pagina **intenties** wordt weer geven met een lijst met de intenties in de pizza-app.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

Uw pizza-app is nu klaar voor gebruik.

## <a name="record-the-app-id-prediction-key-and-prediction-endpoint-of-your-pizza-app"></a>Registreer de App-ID, de Voorspellings sleutel en het Voorspellings eindpunt van uw pizza-app.

Als u uw nieuwe pizza-app wilt gebruiken, hebt u de App-ID, de Voorspellings sleutel en het Voorspellings eindpunt van uw pizza-app nodig.

U vindt deze waarden als volgt:

1. Selecteer op de pagina **intenties** **beheren**.
1. Noteer de **App-ID**op de pagina **Toepassings instellingen** .
1. Selecteer **Azure-resources**.
1. Noteer de **primaire sleutel**op de pagina **Azure-resources** . Deze waarde is uw Voorspellings sleutel.
1. Noteer de **eind punt-URL**. Deze waarde is uw Voorspellings eindpunt.
