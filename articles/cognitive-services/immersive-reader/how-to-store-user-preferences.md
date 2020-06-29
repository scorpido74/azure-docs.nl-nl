---
title: Gebruikers voorkeuren opslaan
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u de voor keuren van de gebruiker opslaat.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: beb053551dc1fa28672c488b31dfb29ca3b53651
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/27/2020
ms.locfileid: "85486906"
---
# <a name="how-to-store-user-preferences"></a>Gebruikers voorkeuren opslaan

In dit artikel wordt beschreven hoe u de voor keuren van de gebruiker opslaat. Deze functionaliteit is bedoeld als een alternatieve manier om de voor keuren van de gebruiker op te slaan in het geval dat het gebruik van cookies niet gewenst of haalbaar is.

## <a name="how-to-enable-storing-user-preferences"></a>Het opslaan van gebruikers voorkeuren inschakelen

De `options` para meter bevat de `onPreferencesChanged` retour aanroep. Deze functie wordt aangeroepen wanneer de gebruiker de voor keuren wijzigt (bijvoorbeeld wanneer ze de teken grootte, thema kleur, letter type enzovoort) wijzigen. De `value` para meter bevat een teken reeks die de huidige voor keuren van de gebruiker vertegenwoordigt. Deze teken reeks kan worden opgeslagen met een wille keurig mechanisme dat u wilt.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>Gebruikers voorkeuren laden in de insluitende lezer

Geef de voor keuren van de gebruiker door aan de insluitende lezer met behulp van de `preferences` para meter. Een trivial voor beeld om de voor keuren van de gebruiker op te slaan en te laden, is als volgt:

```typescript
let userPreferences = null;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
    }
};
```

## <a name="next-steps"></a>Volgende stappen

* De referentie voor de [insluitende lezer-SDK](./reference.md) verkennen