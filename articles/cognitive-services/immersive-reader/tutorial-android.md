---
title: 'Zelfstudie: Insluitende lezer starten met de Android-codevoorbeelden'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie gaat u een voorbeeld van een Android-toepassing configureren en uitvoeren waarmee Insluitende lezer wordt gestart.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.custom: devx-track-java
ms.openlocfilehash: f8420a42b87481b5e51b383ace1ba8a510d2a6a7
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87366735"
---
# <a name="tutorial-start-the-immersive-reader-using-the-android-java-code-sample"></a>Zelfstudie: Insluitende lezer starten met behulp van het Android Java-codevoorbeeld

In het [overzicht](./overview.md) hebt u geleerd wat de Insluitende lezer is en hoe het bewezen technieken implementeert om begrijpend lezen te verbeteren voor taalverwervers, beginnende lezers en studenten met leerproblemen. In deze zelfstudie wordt beschreven hoe u een Android-toepassing maakt waarmee Insluitende lezer wordt gestart. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een app voor Android configureren en uitvoeren met behulp van een voorbeeldproject
> * Een toegangstoken verkrijgt.
> * Insluitende lezer starten met voorbeeldinhoud.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Insluitende lezer-resource die is geconfigureerd voor Azure Active Directory-verificatie. Volg [deze instructies](./how-to-create-immersive-reader.md) om deze in te stellen. Enkele waarden die u hier maakt, hebt u nodig bij het configureren van de eigenschappen van de omgeving. Sla de uitvoer van uw sessie op in een tekstbestand voor later gebruik.
* [Git](https://git-scm.com/).
* [SDK voor Insluitende lezer](https://github.com/microsoft/immersive-reader-sdk).
* [Android Studio](https://developer.android.com/studio).

## <a name="configure-authentication-credentials"></a>Verificatiereferenties configureren

1. Start Android Studio en open het project in de map **immersive-reader-sdk/js/samples/quickstart-java-android** (Java) of de map **immersive-reader-sdk/js/samples/quickstart-kotlin** (Kotlin).

1. Maak een bestand met de naam **env** in de map **/assets**. Voeg de volgende namen en waarden toe en geef waar nodig waarden op. Zorg ervoor dat u dit bestand niet doorvoert in broncodebeheer, omdat er geheimen in staan die niet openbaar mogen worden gemaakt.
    
    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET=<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

## <a name="start-the-immersive-reader-with-sample-content"></a>Insluitende lezer starten met voorbeeldinhoud

Kies een apparaat-emulator in AVD Manager en voer het project uit.

## <a name="next-steps"></a>Volgende stappen

* Verken de [SDK voor Insluitende lezer](https://github.com/microsoft/immersive-reader-sdk) en de [naslaginformatie voor de SDK voor Insluitende lezer](./reference.md).
* Bekijk codevoorbeelden op [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).