---
title: Omgevingsvariabelen
description: omgevingsvariabelen instellen
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: d63e902a59411a549235c955a39d7dbc4be068ba
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156728"
---
Maak met uw sleutel en eindpunt van de resource die u hebt gemaakt, twee omgevingsvariabelen voor verificatie:

* `FORM_RECOGNIZER_KEY`: de resourcesleutel voor het verifiëren van uw aanvragen.
* `FORM_RECOGNIZER_ENDPOINT`: het resource-eindpunt voor het verzenden van API-aanvragen. Dit ziet er als volgt uit: 
  * `https://<your-custom-subdomain>.cognitiveservices.azure.com`

>[!NOTE]
> De eindpunten voor resources die zijn gemaakt na 1 juli 2019, gebruiken de aangepaste indeling voor subdomeinen die hieronder wordt weergegeven. Zie [Aangepaste subdomeinnamen voor Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains) voor meer informatie en een volledige lijst met regionale eindpunten. 

Gebruik de volgende instructies om omgevingsvariabelen in te stellen op uw besturingssysteem.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FORM_RECOGNIZER_KEY <replace-with-your-form-recognizer-key>
setx FORM_RECOGNIZER_ENDPOINT <replace-with-your-form-recognizer-endpoint>
```

Nadat u de omgevingsvariabelen hebt toegevoegd, sluit u het consolevenster en opent u het opnieuw.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.bashrc` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

#### <a name="macos"></a>[MacOS](#tab/unix)

Bewerk uw `.bash_profile` en voeg de omgevingsvariabele toe:

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Nadat u de omgevingsvariabelen hebt toegevoegd, voert u `source .bash_profile` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.
***
