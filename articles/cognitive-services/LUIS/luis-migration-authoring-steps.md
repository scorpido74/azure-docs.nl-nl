---
title: Migreren naar een Azure-ontwerp bron
titleSuffix: Azure Cognitive Services
description: Migreren naar een Azure-ontwerp bron.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: 13eb301daadb70456c0c0d7db8991d7a695b1de9
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259682"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>Stappen voor het migreren naar de Azure-ontwerp bron

Migreer vanuit de Language Understanding-Portal (LUIS) alle apps die u hebt aangeschaft voor het gebruik van de Azure-ontwerp bron.

## <a name="prerequisites"></a>Vereisten

* U **kunt ook**een back-up maken van de apps uit de lijst met apps van de Luis-portal door elke app te exporteren of de export- [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)te gebruiken.
* U **kunt desgewenst**de collaborator's-lijst van elke app opslaan. Deze e-mail lijst wordt aangeboden als onderdeel van het migratie proces.
* **Vereist**, u moet een Azure- [abonnement](https://azure.microsoft.com/free/)hebben. Voor een deel van het abonnements proces zijn facturerings gegevens vereist. U kunt echter gratis (F0) prijs categorieën gebruiken wanneer u LUIS gebruikt. Mogelijk hebt u uiteindelijk een betaalde laag nodig, omdat uw gebruik toeneemt. 

Als u geen Azure-abonnement hebt, [meldt u zich](https://azure.microsoft.com/free/)aan. 

## <a name="access-the-migration-process"></a>Toegang tot het migratie proces

U wordt wekelijks gevraagd uw apps te migreren. U kunt dit venster annuleren zonder te migreren. Als u vóór de volgende geplande periode wilt migreren, kunt u beginnen met het migratie proces vanaf het **vergrendelings** pictogram op de bovenste werk balk van de Luis-Portal. 

## <a name="app-owner-begins-the-migration-process"></a>App-eigenaar begint met het migratie proces

Het migratie proces is beschikbaar als u de eigenaar bent van een LUIS-app. 

1. 1. Meld u aan bij de [Luis-Portal](https://www.luis.ai) en ga akkoord met de gebruiks voorwaarden.
1. In het pop-upvenster migratie kunt u de migratie voortzetten of later migreren. Selecteer **nu migreren**. Als u ervoor kiest om later te migreren, hebt u negen maanden om te migreren naar de nieuwe ontwerp sleutel in Azure.

    ![Eerste pop-upvenster in migratie proces selecteert u nu migreren.](./media/migrate-authoring-key/migrate-now.png)

1. Als uw apps deel nemers hebben, wordt u gevraagd **hen een e-mail te sturen** met de informatie over de migratie. Dit is een optionele stap. De standaard-e-mail toepassing wordt geopend met een e-mail met een lichte opmaak. 

    ![Samen werkers e-mail over migratie verzenden](./media/migrate-authoring-key/send-collaborators-email.png)

1. Kies voor het maken van een LUIS-ontwerp bron door starten te selecteren door een bewerkings **resource te maken om uw apps naar te migreren**. 

    ![Ontwerp bron maken](./media/migrate-authoring-key/choose-authoring-resource.png)

1. Voer in het volgende venster de gegevens van de resource sleutel in. Nadat u de gegevens hebt ingevoerd, selecteert u **resource maken**. 

    ![Ontwerp bron maken](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    Wanneer **u een nieuwe ontwerp bron maakt**, geeft u de volgende informatie op: 

    * **Resource naam** : een door u gekozen aangepaste naam, die wordt gebruikt als onderdeel van de URL voor uw ontwerp-en Voorspellings eindpunt query's.
    * **Tenant** : de Tenant waaraan uw Azure-abonnement is gekoppeld. 
    * **Abonnements naam** : het abonnement dat wordt gefactureerd voor de resource.
    * **Resource groep** : de naam van een aangepaste resource groep die u kiest of maakt. Met resource groepen kunt u Azure-resources groeperen voor toegang en beheer. 
    * **Locatie** : de locatie keuze is gebaseerd op de selectie van de **resource groep** .
    * **Prijs categorie** : de prijs categorie bepaalt de maximale trans actie per seconde en maand. 

1. Wanneer de ontwerp bron is gemaakt, wordt het bericht weer gegeven. Selecteer **sluiten** om het pop-upvenster te sluiten.

    ![De ontwerp resource is gemaakt.](./media/migrate-authoring-key/migration-success.png)

    In de lijst **mijn apps** worden de apps weer gegeven die zijn gemigreerd naar de nieuwe ontwerp bron. 

    U hoeft niet de sleutel van de ontwerp bron te weten om uw apps in de LUIS-portal te kunnen bewerken. Als u van plan bent om uw apps programmatisch te bewerken, hebt u de sleutel waarden van de ontwerp functie nodig. Deze waarden worden weer gegeven op de pagina **Manage-> Azure-resources** in de Luis-Portal en zijn ook beschikbaar in de Azure Portal op de pagina **sleutels** van de resource.  

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>Voeg na het migratie proces mede werkers toe aan uw ontwerp bron

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Meer informatie [over het toevoegen van mede](luis-how-to-collaborate.md)werkers. 

## <a name="next-steps"></a>Volgende stappen


* [Begrippen](luis-concept-keys.md) over het ontwerpen en runtime-sleutels bekijken
* Controleren [hoe sleutels worden toegewezen](luis-how-to-azure-subscription.md) en [mede](luis-how-to-collaborate.md) werkers toevoegen
