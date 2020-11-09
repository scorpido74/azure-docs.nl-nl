---
title: Nauw keurige reactie met behulp van de detectie van antwoord bereik-QnA Maker
description: Inzicht in de nauw keurige antwoord functie die beschikbaar is in QnA Maker beheerd.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 4f64bab698cb87e26fa4fd1587c4269acf99fa59
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94384090"
---
# <a name="precise-answering"></a>Nauw keurige antwoorden

Met de functie voor nauw keurige beantwoording kunt u het exacte korte antwoord krijgen van het antwoord op de best mogelijke vraag die in de kennis basis voor elke gebruikers query wordt weer gegeven. Deze functie maakt gebruik van een diepe leer model dat in runtime inzicht heeft in het doel van de gebruikers query en detecteert het precieze korte antwoord van het antwoord op de hand. als er een kort antwoord aanwezig is in het antwoord dat wordt door gegeven. 

Deze functie is standaard ingeschakeld in het test venster, zodat u de functionaliteit kunt testen die specifiek is voor uw scenario. Deze functie is zeer nuttig voor ontwikkel aars van inhoud en eind gebruikers. Ontwikkel aars van inhoud hoeven de specifieke QnA-paren nu niet hand matig toe te kennen voor elk feit dat aanwezig is in de Knowledge Base, en de eind gebruiker hoeft niet te kijken naar het volledige antwoord dat wordt geretourneerd door de service om het daad werkelijke feit te vinden dat de query van de gebruiker beantwoordt. 

## <a name="precise-answering-on-qna-maker-portal"></a>Nauw keurige beantwoording op QnA Maker Portal

Wanneer u in de QnA Maker-Portal het deel venster testen opent, ziet u een optie om het **korte antwoord bovenaan weer te geven** . Deze optie wordt standaard geselecteerd. Wanneer u een query in het test venster invoert, ziet u een korte antwoord samen met de antwoord richting, als er een kort antwoord aanwezig is in het antwoord door te passeren. 
 
![Gemanaged test venster voor ingeschakelde tests](../QnAMaker/media/conversational-context/test-pane-with-managed.png)

U kunt de selectie van de optie **kort antwoord weer geven** opheffen als u alleen de beantwoording wilt zien in het test venster. 

De service retourneert ook de betrouwbaarheids Score van het exacte antwoord als een **Score voor een antwoord bereik** die u kunt controleren door de optie **controleren** te selecteren, net onder de query in het test venster.

![Score Managed answer-bereik](../QnAMaker/media/conversational-context/managed-answer-span-score.png)

## <a name="publishing-a-qna-maker-bot"></a>Een QnA Maker-bot publiceren

Wanneer u een bot publiceert, krijgt u de exacte antwoord ervaring standaard in uw toepassing, waar u een kort antwoord ziet samen met het antwoord dat u ontvangt. De gebruiker heeft de flexibiliteit om andere ervaringen te kiezen door de sjabloon via de eBot app service bij te werken. 

## <a name="language-support"></a>Taalondersteuning

De exacte antwoord functie wordt momenteel alleen ondersteund voor Engels.
