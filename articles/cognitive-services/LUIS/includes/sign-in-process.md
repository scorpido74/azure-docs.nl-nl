---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/05/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: ef7208596ead8f7d3903bb614ccb980df782e581
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837904"
---
## <a name="sign-in-to-luis-portal"></a>Aanmelden bij de LUIS-Portal

Een nieuwe gebruiker aan LUIS moet deze procedure volgen:

1. Meld u aan bij de [Luis-Portal](https://www.luis.ai), selecteer uw land/regio en ga akkoord met de gebruiks voorwaarden. Als u in plaats daarvan **mijn apps** ziet, bestaat er al een Luis-resource en gaat u verder met het maken van een app. Voor ondersteunde regio's gaat u naar [ontwerp-en publicatie regio's en de bijbehorende sleutels](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions).

1. Selecteer **Azure-resource maken** en selecteer vervolgens **een ontwerp bron maken om uw apps naar te migreren.**

    ![Een type Language Understanding-ontwerp bron kiezen](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Vul de Details voor de resource in.

    ![Ontwerp bron maken](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    Wanneer **u een nieuwe ontwerp bron maakt**, geeft u de volgende informatie op:

    * **Resource naam** : een door u gekozen aangepaste naam, die wordt gebruikt als onderdeel van de URL voor uw ontwerp-en Voorspellings eindpunt query's.
    * **Tenant** : de Tenant waaraan uw Azure-abonnement is gekoppeld.
    * **Abonnements naam** : het abonnement dat wordt gefactureerd voor de resource.
    * **Resource groep** : de naam van een aangepaste resource groep die u kiest of maakt. Met resource groepen kunt u Azure-resources groeperen voor toegang en beheer.
    * **Locatie** : de locatie keuze is gebaseerd op de selectie van de **resource groep** .
    * **Prijs categorie** : de prijs categorie bepaalt de maximale trans actie per seconde en maand.

1. Er wordt een samen vatting weer gegeven van de resource die moet worden gemaakt. Selecteer **Volgende**.

    ![Ontwerp bron maken](../media/sign-in/sign-in-confirm-key-selection.png)

1. Bevestig door **door gaan**te selecteren.

    ![Ontwerp bron maken](../media/sign-in/sign-in-confirm-continue.png)
