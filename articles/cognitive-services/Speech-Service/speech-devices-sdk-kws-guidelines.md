---
title: Richt lijnen voor naamgeving van tref woorden-spraak service
titleSuffix: Azure Cognitive Services
description: Het maken van een effectief sleutel woord is essentieel om ervoor te zorgen dat uw apparaat consistent en nauw keurig reageert.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 3c3943f7269fa2d0ed25acf2215549635b5f16ac
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219564"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>Richt lijnen voor het maken van een effectief tref woord

Het maken van een effectief sleutel woord is essentieel om ervoor te zorgen dat uw apparaat consistent en nauw keurig reageert. Het aanpassen van uw tref woord is een efficiënte manier om uw apparaat te onderscheiden en uw huis stijl te versterken. In dit artikel leert u enkele richt lijnen voor het maken van een effectief sleutel woord.

## <a name="choose-an-effective-keyword"></a>Een effectief tref woord kiezen

Houd rekening met de volgende richt lijnen wanneer u een tref woord kiest:

* Het sleutel woord moet een Engels woord of woord groep zijn.

* Het duurt langer dan twee seconden te zeggen hadden.

* Woorden van 4 tot en met 7 lettergrepen werken het beste. "Hoi computer" is bijvoorbeeld een goed tref woord. Alleen 'Hallo' is slecht.

* Tref woorden moeten volgen op algemene regels voor de uitspraak van het Engels.

* Een unieke of zelfs een fictieve woord dat voldoet aan de algemene regels voor Engels uitspraak van mogelijk fout-positieven verminderen. Zo kan ' computerama ' een goed tref woord zijn.

* Kies een algemene word niet. Bijvoorbeeld, 'eten' en 'Ga' zijn woorden die mensen vaak in gewone conversatie zeggen. Ze zijn mogelijk ONWAAR triggers voor uw apparaat.

* Vermijd het gebruik van een tref woord dat mogelijk alternatieve uitspraak heeft. Gebruikers hoeft te weten de uitspraak 'recht' om te zorgen dat het apparaat om te reageren. Bijvoorbeeld, '509' kunnen worden verergerd "vijf nul negen," "vijf oh negen," of "vijf honderd en 9." "R.E.I." kunnen worden verergerd 'r-e-i' of "ray." 'Live' kunnen worden verergerd "/līv/" of '/liv/'.

* Gebruik geen speciale tekens, symbolen en cijfers. Bijvoorbeeld ' go # ' en ' 20 + katten ' kunnen problematische tref woorden zijn. Echter, 'Ga sharp' of ' twintig plus katten ' werken mogelijk. U kunt nog steeds gebruik van de symbolen in uw huisstijl en gebruiken van marketing en documentatie te versterken van de juiste uitspraak.

> [!NOTE]
> Als u een woord in handels merk kiest als uw tref woord, zorg er dan voor dat u eigenaar bent van het handels merk of dat u toestemming hebt van de eigenaar van het handels merk om het woord te gebruiken. Micro soft is niet aansprakelijk voor juridische problemen die kunnen voortvloeien uit uw keuze aan tref woorden.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een aangepast sleutel woord met behulp van speech Studio](speech-devices-sdk-create-kws.md).
