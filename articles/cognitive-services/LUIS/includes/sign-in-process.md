---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/05/2020
ms.topic: include
ms.openlocfilehash: fda9df6c7e9651bbd3b0b70ad9d47f23c0c19d01
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91541425"
---
## <a name="sign-in-to-luis-portal"></a>Aanmelden bij de LUIS-portal

Een nieuwe gebruiker van LUIS moet deze procedure volgen:

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai), selecteer uw land/regio en ga akkoord met de gebruiksvoorwaarden. Als u in plaats daarvan **Mijn apps** ziet, bestaat er al een LUIS-resource en kunt u verdergaan met het maken van een app. Voor ondersteunde regio's gaat u naar [Ontwerp- en publicatieregio's en de bijbehorende sleutels](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions).

1. Selecteer **Azure-resource maken** en vervolgens **Een ontwerpresource maken om uw apps naar te migreren**.

    ![Een type Language Understanding-ontwerpresource kiezen](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Vul de details voor de resource in.

    ![Schermopname van het ontwerpdeelvenster Een nieuwe resource maken.](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    Geef bij het **maken van een nieuwe ontwerpresource** de volgende informatie op:

    * **Resourcenaam**: een door u gekozen aangepaste naam die wordt gebruikt als onderdeel van de URL voor uw ontwerp- en voorspellingseindpuntquery's.
    * **Tenant**: de tenant waaraan uw Azure-abonnement is gekoppeld.
    * **Abonnementsnaam**: het abonnement dat wordt gefactureerd voor de resource.
    * **Resourcegroep**: een aangepaste resourcegroepnaam die u kiest of maakt. Met resourcegroepen kunt u Azure-resources groeperen voor toegang en beheer.
    * **Locatie**: de locatiekeuze is gebaseerd op de **resourcegroep**-selectie.
    * **Prijscategorie**: de prijscategorie bepaalt de maximale transactie per seconde en maand.

1. Er wordt een samenvatting weergegeven van de te maken resource. Selecteer **Next**.

    ![Schermopname van de welkomstpagina met de optie om een koppeling naar een Azure-account toe te voegen.](../media/sign-in/sign-in-confirm-key-selection.png)

1. Bevestig door **Doorgaan** te selecteren.

    ![Schermopname van de welkomstpagina nadat u een koppeling naar een Azure-account hebt toegevoegd.](../media/sign-in/sign-in-confirm-continue.png)
