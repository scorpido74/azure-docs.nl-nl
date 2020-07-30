---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/05/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: da9388a3bd5f4d46ec34ed226e3ee23a96b2f494
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87374727"
---
## <a name="sign-in-to-luis-portal"></a>Aanmelden bij de LUIS-portal

Een nieuwe gebruiker van LUIS moet deze procedure volgen:

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai), selecteer uw land/regio en ga akkoord met de gebruiksvoorwaarden. Als u in plaats daarvan **Mijn apps** ziet, bestaat er al een LUIS-resource en kunt u verdergaan met het maken van een app. Voor ondersteunde regio's gaat u naar [Ontwerp- en publicatieregio's en de bijbehorende sleutels](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions).

1. Selecteer **Azure-resource maken** en vervolgens **Een ontwerpresource maken om uw apps naar te migreren**.

    ![Een type Language Understanding-ontwerpresource kiezen](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Vul de details voor de resource in.

    ![Ontwerpresource maken](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    Geef bij het **maken van een nieuwe ontwerpresource** de volgende informatie op:

    * **Resourcenaam**: een door u gekozen aangepaste naam die wordt gebruikt als onderdeel van de URL voor uw ontwerp- en voorspellingseindpuntquery's.
    * **Tenant**: de tenant waaraan uw Azure-abonnement is gekoppeld.
    * **Abonnementsnaam**: het abonnement dat wordt gefactureerd voor de resource.
    * **Resourcegroep**: een aangepaste resourcegroepnaam die u kiest of maakt. Met resourcegroepen kunt u Azure-resources groeperen voor toegang en beheer.
    * **Locatie**: de locatiekeuze is gebaseerd op de **resourcegroep**-selectie.
    * **Prijscategorie**: de prijscategorie bepaalt de maximale transactie per seconde en maand.

1. Er wordt een samenvatting weergegeven van de te maken resource. Selecteer **Next**.

    ![Ontwerpresource maken](../media/sign-in/sign-in-confirm-key-selection.png)

1. Bevestig door **Doorgaan** te selecteren.

    ![Ontwerpresource maken](../media/sign-in/sign-in-confirm-continue.png)
