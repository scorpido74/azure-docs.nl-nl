---
title: Vertaling configureren
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u de verschillende opties voor vertaling kunt configureren.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 0a6ed6ecea216a36cfc9da4ef36a2bddc69cc6a8
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92633141"
---
# <a name="how-to-configure-translation"></a>Omzetting configureren

In dit artikel wordt beschreven hoe u de verschillende opties voor vertaling in de insluitende lezer kunt configureren.

## <a name="configure-translation-language"></a>Vertaal taal configureren

De `options` para meter bevat alle vlaggen die kunnen worden gebruikt voor het configureren van de vertaling. Stel de `language` para meter in op de taal waarnaar u wilt vertalen. Zie [taal ondersteuning](./language-support.md) voor de volledige lijst met ondersteunde talen.

```typescript
const options = {
    translationOptions: {
        language: 'fr-FR'
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="automatically-translate-the-document-on-load"></a>Het document automatisch tijdens het laden vertalen

Instellen `autoEnableDocumentTranslation` op `true` om het hele document automatisch te laten vertalen wanneer de insluitende lezer wordt geladen.

```typescript
const options = {
    translationOptions: {
        autoEnableDocumentTranslation: true
    }
};
```

## <a name="automatically-enable-word-translation"></a>Automatische vertaling van woorden inschakelen

Instellen `autoEnableWordTranslation` op `true` om één woord vertaling in te scha kelen.

```typescript
const options = {
    translationOptions: {
        autoEnableWordTranslation: true
    }
};
```

## <a name="next-steps"></a>Volgende stappen

* Lees de [naslagdocumentatie voor de Immersive Reader-SDK](./reference.md).