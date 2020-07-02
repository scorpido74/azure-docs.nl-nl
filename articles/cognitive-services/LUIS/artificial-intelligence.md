---
title: Kunstmatige intelligentie
description: LUIS maakt gebruik van kunst matige intelligentie om inzicht te krijgen in de taal van uw gegevens op basis van het schema dat u hebt gedefinieerd.
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: 4f145585e097a3cf6a2338dbab879f6fce07f71d
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85802651"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>Kunst matige intelligentie in Language Understanding (LUIS)

LUIS maakt gebruik van kunst matige intelligentie om inzicht te bieden in natuurlijke taal (NLU) aan uw gegevens op basis van het schema dat u hebt gedefinieerd.

## <a name="natural-language-processing"></a>Natuurlijke taalverwerking

Natuurlijke Language Understanding (NLU) is een specifiek subonderwerp van natuurlijke taal verwerking (NLP).

De verwerking van natuurlijke taal is een breder concept dat elke vorm van verwerking van tekstuele gegevens verwerkt. Dit omvat zaken als:

* Token
* Onderdeel van spraak code ring (POS)
* Segmentatie
* Morfologische analyse
* Semantische gelijkenis
* Uitschakeling
* Omzetting

## <a name="natural-language-processing-in-luis"></a>Natuurlijke taal verwerking in LUIS

De verwerking van natuurlijke taal is op de volgende manieren beschikbaar voor uw LUIS-app:
* [Natuurlijk taal memorandum](#natural-language-understanding) (Luis)
* Configureer bare NLP-aspecten in LUIS:
    * [Token](luis-language-support.md#tokenization)
    * Morphology via diakritische tekens, interpunctie en Word Forms [API-instellingen](luis-reference-application-settings.md)
* Vóór of na het verwerken van de query utterance van andere [Cognitive Services](../Welcome.md) , zoals:
    * [Rekening](../translator/translator-info-overview.md)

## <a name="natural-language-understanding"></a>Natuurlijk taal memorandum

NLU is de mogelijkheid om een taal kundige verklaring te _Transformeren_ naar een representatie waarmee u uw gebruikers op natuurlijke wijze kunt begrijpen. In natuurlijke taal is er een probleem met de meeste problemen en wordt gedefinieerd als een _AI-hard_ probleem.

LUIS is bedoeld om zich te richten op beoogd en extractie, waaronder het identificeren van:
* Wat de gebruiker wil
* Waar ze over zijn.

LUIS heeft weinig of geen kennis van de bredere _NLP_ -aspecten, zoals semantische gelijkenis, zonder expliciete identificatie in voor beelden. De volgende tokens (woorden) zijn bijvoorbeeld drie verschillende dingen tot ze worden gebruikt in vergelijk bare contexten in de voor beelden:

* ze
* koopt
* hebben

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [ontwikkelings levenscyclus](luis-concept-app-iteration.md) voor een Luis-app