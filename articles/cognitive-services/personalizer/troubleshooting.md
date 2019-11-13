---
title: Probleem oplossing-persoonlijker
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u antwoorden op veelgestelde vragen over het oplossen van problemen met persoonlijke instellingen.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 5911cba54d7dd0eb1a5621112b41e2fc40fa68eb
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953167"
---
# <a name="personalizer-troubleshooting"></a>Problemen met persoonlijker oplossen

In dit artikel vindt u antwoorden op veelgestelde vragen over het oplossen van problemen met persoonlijke instellingen.

## <a name="learning-loop"></a>Learning-lus

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>De learning-lus lijkt niet te leren. Hoe kan ik dit probleem oplossen?

Voor de learning-lus moeten enkele duizenden belonings aanroepen worden uitgevoerd voordat rang gegeven worden. 

Als u niet zeker weet hoe uw leer proces momenteel zich gedraagt, voert u een [offline-evaluatie](concepts-offline-evaluation.md)uit en past u het gecorrigeerde leer beleid toe. 

## <a name="next-steps"></a>Volgende stappen

[De update frequentie van het model configureren](how-to-settings.md#model-update-frequency)