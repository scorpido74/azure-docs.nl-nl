---
title: Een aangepast tref woord maken-spraak service
titleSuffix: Azure Cognitive Services
description: Het apparaat luistert altijd naar een tref woord (of woord groep). Wanneer de gebruiker het tref woord heeft gestaan, stuurt het apparaat alle volgende audio naar de Cloud totdat de gebruiker stopt met spreken. Het aanpassen van uw tref woord is een efficiënte manier om uw apparaat te onderscheiden en uw huis stijl te versterken.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: bf9afb66163532b4095e0d30b1167010320abbf8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490944"
---
# <a name="create-a-custom-keyword-by-using-the-speech-service"></a>Een aangepast tref woord maken met behulp van de speech-service

Het apparaat luistert altijd naar een tref woord (of woord groep). "Hey Cortana" is bijvoorbeeld een tref woord voor de Cortana-assistent. Wanneer de gebruiker het tref woord heeft gestaan, stuurt het apparaat alle volgende audio naar de Cloud totdat de gebruiker stopt met spreken. Het aanpassen van uw tref woord is een efficiënte manier om uw apparaat te onderscheiden en uw huis stijl te versterken.

In dit artikel leert u hoe u een aangepast tref woord maakt voor uw apparaat.

## <a name="choose-an-effective-keyword"></a>Een effectief tref woord kiezen

Houd rekening met de volgende richt lijnen wanneer u een tref woord kiest:

* Het sleutel woord moet een Engels woord of een woord groep zijn. Het duurt niet langer dan twee seconden om te zeggen.

* Woorden van 4 tot 7 letter grepen werken het beste. "Hoi computer" is bijvoorbeeld een goed tref woord. Alleen een ' Hoi ' is een slechte versie.

* Tref woorden moeten volgen op algemene regels voor de uitspraak van het Engels.

* Een uniek of zelfs een opgesteld woord dat voldoet aan de algemene Engelse regels voor uitspraak, kan fout-positieven verminderen. Zo kan ' computerama ' een goed tref woord zijn.

* Kies geen algemeen woord. "Eten" en "Go" zijn bijvoorbeeld woorden die mensen vaak in een gewone conversatie hebben gedicteerd. Ze kunnen worden ingesteld op ONWAAR voor uw apparaat.

* Vermijd het gebruik van een tref woord dat mogelijk alternatieve uitspraak heeft. Gebruikers moeten de uitspraak van het ' recht ' weten om hun apparaat te laten reageren. "509" kan bijvoorbeeld "5 0 9," "5 0 9," of "509" worden uitgesp roken. "R.E.I." kan worden uitgesp roken "r-e-i" of "Ray". ' Live ' kan worden uitgesp roken '/līv/' of '/Liv/'.

* Gebruik geen speciale tekens, symbolen of cijfers. "Go #" en "20 + katten" zouden bijvoorbeeld geen goede tref woorden zijn. ' Go Sharp ' of ' twintig plus katten ' kan echter wel werken. U kunt nog steeds de symbolen in uw huis stijl gebruiken en marketing en documentatie gebruiken om de juiste uitspraak te versterken.

> [!NOTE]
> Als u een woord in handels merk kiest als uw tref woord, zorg er dan voor dat u eigenaar bent van het handels merk of dat u toestemming hebt van de eigenaar van het handels merk om het woord te gebruiken. Micro soft is niet aansprakelijk voor juridische problemen die kunnen voortvloeien uit uw keuze aan tref woorden.

## <a name="create-your-keyword"></a>Uw tref woord maken

Voordat u een aangepast tref woord kunt gebruiken met uw apparaat, moet u een tref woord maken met de micro soft-service voor het genereren van aangepaste tref woorden. Nadat u een tref woord hebt verstrekt, produceert de service een bestand dat u implementeert in uw Development Kit om het tref woord op uw apparaat in te scha kelen.

1. Ga naar de [Speech Studio](https://aka.ms/sdsdk-speechportal) en **Meld** u aan of als u nog geen abonnement op spraak hebt, kiest u [**een abonnement maken**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Typ op de pagina [aangepast tref](https://aka.ms/sdsdk-wakewordportal) woord het tref woord van uw keuze en klik op **tref woord toevoegen**. Er zijn enkele [richt lijnen](#choose-an-effective-keyword) die u helpen bij het kiezen van een effectief tref woord. Ondersteuning is momenteel beperkt tot de taal en-US.

    ![Voer uw tref woord in](media/speech-devices-sdk/custom-kws-portal-enter-keyword.png)

1. De portal maakt nu kandidaat-uitspraak voor uw tref woord. Luister naar elke kandidaat door te klikken op de afspeel knoppen en de controles te verwijderen naast eventuele uitstaande uitspraaken. Wanneer u alleen goede uitspraaken hebt geselecteerd, selecteert u **verzenden** om te beginnen met het genereren van het tref woord. Als u het tref woord wilt wijzigen, moet u eerst de bestaande verwijderen door te klikken op de knop verwijderen die aan de rechter kant van de rij wordt weer gegeven terwijl u de muis aanwijzer erop houdt.

    ![Je tref woord controleren](media/speech-devices-sdk/custom-kws-portal-review-keyword.png)

1. Het kan Maxi maal één minuut duren voordat het model is gegenereerd. Vervolgens wordt u gevraagd het bestand te downloaden.

    ![Uw tref woord downloaden](media/speech-devices-sdk/custom-kws-portal-download-keyword.png)

1. Sla het zip-bestand op uw computer op. U hebt dit bestand nodig om uw aangepaste tref woord te implementeren in de Development Kit.

## <a name="next-steps"></a>Volgende stappen

Test uw aangepaste tref woord met [Speech apparaten SDK Quick](https://aka.ms/sdsdk-quickstart)start.
