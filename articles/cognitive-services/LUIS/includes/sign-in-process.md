---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 155c88ec4766391f70701b17038b915c399d8b0c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77371820"
---
## <a name="sign-in-to-luis-portal"></a>Aanmelden bij de LUIS-Portal

Een nieuwe gebruiker aan LUIS moet deze procedure volgen:

1. Meld u aan bij de [Luis-Portal (preview)](https://preview.luis.ai), selecteer uw land en ga akkoord met de gebruiks voorwaarden. Als u in plaats daarvan **mijn apps** ziet, bestaat er al een Luis-resource en gaat u verder met het maken van een app.

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

1. Er wordt een samen vatting weer gegeven van de resource die moet worden gemaakt. Selecteer **Next**.

    ![Ontwerp bron maken](../media/sign-in/sign-in-confirm-key-selection.png)

1. Bevestig door **door gaan**te selecteren.

    ![Ontwerp bron maken](../media/sign-in/sign-in-confirm-continue.png)