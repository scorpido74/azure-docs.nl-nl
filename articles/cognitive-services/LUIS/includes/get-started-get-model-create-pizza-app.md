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
ms.openlocfilehash: cffc9af2ef93ec44e2af9363c0040d1f22d945f7
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84416364"
---
Maak de pizza-app.

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

Als er een dialoog venster wordt weer gegeven **waarin wordt uitgelegd hoe u een efficiënte Luis-app maakt**, sluit u het dialoog venster.

## <a name="train-and-publish-the-pizza-app"></a>De pizza-app trainen en publiceren

De pagina **intenties** wordt weer geven met een lijst met de intenties in de pizza-app.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

## <a name="add-an-authoring-resource-to-the-pizza-app"></a>Een ontwerp bron toevoegen aan de pizza-app

1. Selecteer **beheren**.
1. Selecteer **Azure-resources**.
1. Selecteer een **resource**maken.
1. Selecteer **ontwerp resource wijzigen**.

Als u een ontwerp bron hebt, voert u de naam van de **Tenant**, de **naam**van het abonnement en de **Luis-resource naam** van uw ontwerp bron in.

Als u geen bewerkings resource hebt:

1. Selecteer **nieuwe resource maken**.
1. Voer een **Tenant naam**, **resource naam**, **naam van abonnement**en **naam van de Azure-resource groep**in.

Uw pizza-app is nu klaar voor gebruik.

## <a name="record-the-app-id-prediction-key-and-prediction-endpoint-of-your-pizza-app"></a>Registreer de App-ID, de Voorspellings sleutel en het Voorspellings eindpunt van uw pizza-app.

Als u uw nieuwe pizza-app wilt gebruiken, hebt u de App-ID, de ontwerp sleutel en het ontwerp-eind punt van uw pizza-app nodig.

U vindt deze waarden als volgt:

1. Selecteer op de pagina **intenties** **beheren**.
1. Noteer de **App-ID**op de pagina **Toepassings instellingen** .
1. Selecteer **Azure-resources**.
1. Selecteer een **resource**maken.
1. Op het tabblad **Authoring resource** noteert u de **primaire sleutel**. Deze waarde is uw ontwerp sleutel.
1. Noteer de **eind punt-URL**. Deze waarde is uw ontwerp eindpunt.
