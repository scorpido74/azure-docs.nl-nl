---
title: 'Zelfstudie: Insluitende lezer starten met behulp van het Swift iOS-codevoorbeeld'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie gaat u een voorbeeld van een Swift-toepassing configureren en uitvoeren waarmee Insluitende lezer wordt gestart.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: d9bb3a5fb41215f2e839efc7989ea7854f254f16
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537932"
---
# <a name="tutorial-start-the-immersive-reader-using-the-swift-ios-code-sample"></a>Zelfstudie: Insluitende lezer starten met behulp van het Swift iOS-codevoorbeeld

In het [overzicht](./overview.md) hebt u geleerd wat de Insluitende lezer is en hoe het bewezen technieken implementeert om begrijpend lezen te verbeteren voor taalverwervers, beginnende lezers en studenten met leerproblemen. In deze zelfstudie wordt beschreven hoe u een iOS-toepassing maakt waarmee Insluitende lezer wordt gestart. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Swift-app voor iOS configureren en uitvoeren met behulp van een voorbeeldproject.
> * Een toegangstoken verkrijgt.
> * Insluitende lezer starten met voorbeeldinhoud.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Insluitende lezer-resource die is geconfigureerd voor Azure Active Directory-verificatie. Volg [deze instructies](./how-to-create-immersive-reader.md) om deze in te stellen. Enkele waarden die u hier maakt, hebt u nodig bij het configureren van de eigenschappen van de omgeving. Sla de uitvoer van uw sessie op in een tekstbestand voor later gebruik.
* [macOS](https://www.apple.com/macos).
* [Git](https://git-scm.com/).
* [SDK voor Insluitende lezer](https://github.com/microsoft/immersive-reader-sdk).
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="configure-authentication-credentials"></a>Verificatiereferenties configureren

1. Open Xcode en open **immersive-reader-sdk/js/samples/ios/quickstart-swift/quickstart-swift.xcodeproj**.
1. Selecteer in het bovenste menu **Product** > **Scheme** > **Edit Scheme**.
1. Selecteer in de weergave **Run** het tabblad **Arguments**.
1. Voeg in de sectie **Environment Variables** de volgende namen en waarden toe. Geef de waarden op die werden weergegeven bij het maken van de Insluitende lezer-resource.

    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

Zorg ervoor dat u deze wijziging niet doorvoert in broncodebeheer, omdat er geheimen in staan die niet openbaar mogen worden gemaakt.

## <a name="start-the-immersive-reader-with-sample-content"></a>Insluitende lezer starten met voorbeeldinhoud

Selecteer in Xcode **Ctrl+R** om het project uit te voeren.

## <a name="next-steps"></a>Volgende stappen

* Verken de [SDK voor Insluitende lezer](https://github.com/microsoft/immersive-reader-sdk) en de [naslaginformatie voor de SDK voor Insluitende lezer](./reference.md).
* Bekijk codevoorbeelden op [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
