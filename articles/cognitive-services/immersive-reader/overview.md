---
title: Wat is de Immersive Reader API?
titleSuffix: Azure Cognitive Services
description: De Immersive Reader API is een tool die kan worden gebruikt om mensen met leerverschillen tegemoet te komen of nieuwe lezers en taalstudenten te helpen.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metan
ms.openlocfilehash: 2eab7ab62f6a7a105dab33aa889af9f2be3a3fa3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80052389"
---
# <a name="what-is-immersive-reader"></a>Wat is Insluitende lezer?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

De [Immersive Reader](https://www.onenote.com/learningtools) is een inclusief ontworpen tool die bewezen technieken implementeert om het begrijpen van lezen te verbeteren voor opkomende lezers, taalstudenten en mensen met leerverschillen zoals dyslexie.

U kunt de Insluitende lezer gebruiken in uw webtoepassing door de SDK voor de Insluitende lezer te gebruiken.

## <a name="what-does-immersive-reader-do"></a>Wat doet Immersive Reader?

De Immersive Reader is ontworpen om lezen toegankelijker te maken voor iedereen.

* Toont inhoud in een minimale leesweergave

  ![Insluitende lezer](./media/immersive-reader.png)

* Geeft afbeeldingen weer van veelgebruikte woorden

  ![Afbeeldingswoordenboek](./media/picture-dictionary.png)

* Hoogtepunten zelfstandige naamwoorden, werkwoorden, bijvoeglijke naamwoorden en bijwoorden

  ![Delen van spraak](./media/parts-of-speech.png)

* Leest uw inhoud hardop voor

  ![Voorlezen](./media/read-aloud.png)

* Vertaalt uw inhoud naar een andere taal

  ![Omzetting](./media/translation.png)

* Woorden opsplitsen in lettergrepen

  ![Syllabification](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Hoe werkt Immersive Reader?

De Immersive Reader is een standalone web-app die, wanneer aangeroepen met behulp van de Immersive `iframe`Reader JavaScript SDK, wordt weergegeven op de top van uw bestaande web-app via een . Wanneer u de API aanroept om de immersive reader te starten, geeft u de inhoud op die u wilt weergeven in de immersive reader. Onze SDK verzorgt de creatie `iframe` en styling van de en communicatie met de Immersive Reader backend service, die de inhoud verwerkt voor delen van spraak, tekst naar spraak, vertaling, enzovoort.

## <a name="next-steps"></a>Volgende stappen

Aan de slag met Insluitende lezer:

* Spring in de [quickstart](./quickstart.md)
* Ontdek de [Immersive Reader SDK op GitHub](https://github.com/microsoft/immersive-reader-sdk)
* Lees de [Naslaggids voor de meeslepende lezer SDK](./reference.md)
