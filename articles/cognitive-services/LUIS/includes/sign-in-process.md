---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 10/30/2020
ms.topic: include
ms.openlocfilehash: e592c11062e81d48014a90895a0e42b460d4b77c
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128168"
---
## <a name="sign-in-to-luis-portal"></a>Aanmelden bij de LUIS-portal

Een nieuwe gebruiker van LUIS moet deze procedure volgen:

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai), selecteer uw land/regio en ga akkoord met de gebruiksvoorwaarden. Als u in plaats daarvan **Mijn apps** ziet, bestaat er al een LUIS-resource en kunt u verdergaan met het maken van een app. U hebt twee opties bij het aanmelden:

    * Met een Azure-resource (aanbevolen): Hiermee kunt u uw LUIS-account koppelen aan een nieuwe of bestaande Azure-creatieresource. Dit is gelijk aan aanmelden als u al bent gemigreerd. U hoeft later niet het [migratieproces](../luis-migration-authoring.md#what-is-migration) te doorlopen.

    * Een proefversiesleutel gebruiken. Zo kunt u zich bij LUIS aanmelden met een proefversieresource die u niet hoeft in te stellen. Als u deze optie kiest, moet u uiteindelijk [uw account migreren](../luis-migration-authoring.md#migration-steps) en uw toepassingen koppelen aan een creatieresource.

1. Zoek in het venster **Een creatie kiezen** dat verschijnt uw Azure-abonnement en LUIS-creatieresource. Als u geen resource hebt, kunt u een nieuwe maken.

    :::image type="content" source="../media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="Kies een type Language Understanding-creatieresource.":::
    
    Geef bij het maken van een nieuwe ontwerpresource de volgende informatie op:
    * **Tenantnaam** : de tenant waaraan uw Azure-abonnement is gekoppeld.
    * **Azure-abonnementsnaam** : het abonnement dat wordt gefactureerd voor de resource.
    * **Azure-resourcegroepsnaam** : een aangepaste resourcegroepnaam die u kiest of maakt. Met resourcegroepen kunt u Azure-resources groeperen voor toegang en beheer.
    * **Azure-resourcenaam** : een door u gekozen aangepaste naam die wordt gebruikt als onderdeel van de URL voor uw ontwerp- en voorspellingseindpuntquery's.
    * **Prijscategorie** : de prijscategorie bepaalt de maximale transactie per seconde en maand.


