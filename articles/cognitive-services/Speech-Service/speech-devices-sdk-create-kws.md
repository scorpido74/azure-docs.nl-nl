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
ms.date: 11/18/2019
ms.author: erhopf
ms.openlocfilehash: 9a5b5de71ee290b39603968cf4309171689e22e4
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184832"
---
# <a name="create-a-custom-keyword-by-using-the-speech-service"></a>Een aangepast tref woord maken met behulp van de speech-service

Het apparaat luistert altijd naar een tref woord (of woord groep). "Hey Cortana" is bijvoorbeeld een tref woord voor de Cortana-assistent. Wanneer de gebruiker het tref woord heeft gestaan, stuurt het apparaat alle volgende audio naar de Cloud totdat de gebruiker stopt met spreken. Het aanpassen van uw tref woord is een efficiënte manier om uw apparaat te onderscheiden en uw huis stijl te versterken.

In dit artikel leert u hoe u een aangepast tref woord maakt voor uw apparaat.

## <a name="choose-an-effective-keyword"></a>Een effectief tref woord kiezen

Houd rekening met de volgende richt lijnen wanneer u een tref woord kiest:

* Het sleutel woord moet een Engels woord of een woord groep zijn. Het duurt langer dan twee seconden te zeggen hadden.

* Woorden van 4 tot en met 7 lettergrepen werken het beste. "Hoi computer" is bijvoorbeeld een goed tref woord. Alleen 'Hallo' is slecht.

* Tref woorden moeten volgen op algemene regels voor de uitspraak van het Engels.

* Een unieke of zelfs een fictieve woord dat voldoet aan de algemene regels voor Engels uitspraak van mogelijk fout-positieven verminderen. Zo kan ' computerama ' een goed tref woord zijn.

* Kies een algemene word niet. Bijvoorbeeld, 'eten' en 'Ga' zijn woorden die mensen vaak in gewone conversatie zeggen. Ze zijn mogelijk ONWAAR triggers voor uw apparaat.

* Vermijd het gebruik van een tref woord dat mogelijk alternatieve uitspraak heeft. Gebruikers hoeft te weten de uitspraak 'recht' om te zorgen dat het apparaat om te reageren. Bijvoorbeeld, '509' kunnen worden verergerd "vijf nul negen," "vijf oh negen," of "vijf honderd en 9." "R.E.I." kunnen worden verergerd 'r-e-i' of "ray." 'Live' kunnen worden verergerd "/līv/" of '/liv/'.

* Gebruik geen speciale tekens, symbolen en cijfers. "Go #" en "20 + katten" zouden bijvoorbeeld geen goede tref woorden zijn. Echter, 'Ga sharp' of ' twintig plus katten ' werken mogelijk. U kunt nog steeds gebruik van de symbolen in uw huisstijl en gebruiken van marketing en documentatie te versterken van de juiste uitspraak.

> [!NOTE]
> Als u een woord in handels merk kiest als uw tref woord, zorg er dan voor dat u eigenaar bent van het handels merk of dat u toestemming hebt van de eigenaar van het handels merk om het woord te gebruiken. Micro soft is niet aansprakelijk voor juridische problemen die kunnen voortvloeien uit uw keuze aan tref woorden.

## <a name="create-your-keyword"></a>Uw tref woord maken

Voordat u een aangepast tref woord kunt gebruiken, moet u een tref woord maken met behulp van de [aangepaste trefwoord](https://aka.ms/sdsdk-wakewordportal) pagina in [Speech Studio](https://aka.ms/sdsdk-speechportal). Nadat u een tref woord hebt verstrekt, produceert het een bestand dat u op het apparaat implementeert.

1. Ga naar de [Speech Studio](https://aka.ms/sdsdk-speechportal) en **Meld** u aan of als u nog geen abonnement op spraak hebt, kiest u [**een abonnement maken**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Typ op de pagina [aangepast tref](https://aka.ms/sdsdk-wakewordportal) woord het tref woord van uw keuze en klik op **tref woord toevoegen**. Er zijn enkele [richt lijnen](#choose-an-effective-keyword) die u helpen bij het kiezen van een effectief tref woord. Ondersteuning is momenteel beperkt tot de taal en-US.

    ![Voer uw tref woord in](media/speech-devices-sdk/custom-kws-portal-enter-keyword.png)

1. De portal maakt nu kandidaat-uitspraak voor uw tref woord. Luister naar elke kandidaat door te klikken op de afspeel knoppen en de controles te verwijderen naast eventuele uitstaande uitspraaken. Wanneer u alleen goede uitspraaken hebt geselecteerd, selecteert u **verzenden** om te beginnen met het genereren van het tref woord. Als u het tref woord wilt wijzigen, moet u eerst de bestaande verwijderen door te klikken op de knop verwijderen die aan de rechter kant van de rij wordt weer gegeven terwijl u de muis aanwijzer erop houdt.

    ![Je tref woord controleren](media/speech-devices-sdk/custom-kws-portal-review-keyword.png)

1. Het kan Maxi maal één minuut duren voordat het model is gegenereerd. Vervolgens wordt u gevraagd het bestand te downloaden.

    ![Uw tref woord downloaden](media/speech-devices-sdk/custom-kws-portal-download-keyword.png)

1. Sla het ZIP-bestand op uw computer. U hebt dit bestand nodig om uw aangepaste tref woord te implementeren op het apparaat.

## <a name="next-steps"></a>Volgende stappen

Test uw aangepaste tref woord met [Speech apparaten SDK Quick](https://aka.ms/sdsdk-quickstart)start.
