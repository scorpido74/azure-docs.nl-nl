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
ms.openlocfilehash: a9259026747102dd65be3bb8da853735098667db
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049320"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-swift-ios-code-sample"></a>Zelfstudie: Insluitende lezer starten met behulp van het Swift iOS-codevoorbeeld

In het [overzicht](./overview.md) hebt u geleerd wat de Insluitende lezer is en hoe het bewezen technieken implementeert om begrijpend lezen te verbeteren voor taalverwervers, beginnende lezers en studenten met leerproblemen. In deze zelfstudie wordt beschreven hoe u een iOS-toepassing maakt waarmee Insluitende lezer wordt gestart. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Swift-app voor iOS configureren en uitvoeren met behulp van een voorbeeldproject
> * Een toegangstoken verkrijgen
> * De Insluitende lezer starten met voorbeeldinhoud

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Insluitende lezer-resource die is geconfigureerd voor Azure Active Directory-verificatie. Volg [deze instructies](./how-to-create-immersive-reader.md) om deze in te stellen. U hebt enkele waarden nodig die u hier hebt gemaakt bij het configureren van de eigenschappen van de omgeving. Sla de uitvoer van uw sessie op in een tekstbestand voor later gebruik.
* [MacOS](https://www.apple.com/macos)
* [Git](https://git-scm.com/)
* [SDK voor Insluitende lezer](https://github.com/microsoft/immersive-reader-sdk)
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)

## <a name="configure-authentication-credentials"></a>Verificatiereferenties configureren

1. Open Xcode en open **immersive-reader-sdk/js/samples/ios/quickstart-swift/quickstart-swift.xcodeproj**.
2. Klik in het bovenste menu op **Product > Scheme > Edit Scheme...**
3. Klik in de weergave **Run** op het tabblad **Arguments**.
4. Voeg de volgende namen en waarden toe in de sectie **Environment Variables**, waarbij u de waarden opgeeft die u hebt gebruikt bij het maken van uw resource voor Insluitende lezer.

```text
TENANT_ID=<YOUR_TENANT_ID>
CLIENT_ID=<YOUR_CLIENT_ID>
CLIENT_SECRET<YOUR_CLIENT_SECRET>
SUBDOMAIN=<YOUR_SUBDOMAIN>
```

Zorg ervoor dat u de bovenstaande wijziging niet doorvoert in broncodebeheer, omdat deze geheimen bevat die niet openbaar moeten worden gemaakt.

## <a name="launch-the-immersive-reader-with-sample-content"></a>De Insluitende lezer starten met voorbeeldinhoud

1. Druk in Xcode op **Ctrl+R** om het project uit te voeren.

## <a name="next-steps"></a>Volgende stappen

* De [SDK voor Insluitende lezer](https://github.com/microsoft/immersive-reader-sdk) en de [naslaginformatie voor de SDK voor Insluitende lezer](./reference.md) verkennen
* Codevoorbeelden op [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/) bekijken
