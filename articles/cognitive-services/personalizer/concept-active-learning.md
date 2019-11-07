---
title: Actieve en inactieve gebeurtenissen-persoonlijker
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt het gebruik van actieve en inactieve gebeurtenissen, leer instellingen en het trainings beleid binnen de Personaler service besproken.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: 1641a1020193395d7d2ddb9c4893bd7bc89cdcd0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681862"
---
# <a name="active-and-inactive-events"></a>Actieve en inactieve gebeurtenissen

Wanneer uw toepassing de Rank API aanroept, ontvangt u de actie die de toepassing moet weer geven in het veld **rewardActionId** .  Vanaf dat moment verwacht de Personaler een belonings oproep met dezelfde gebeurtenis code. De belonings score wordt gebruikt om het model te trainen voor toekomstige positie oproepen. Als er geen belonings oproep wordt ontvangen voor de gebeurtenis-aanvraag, wordt een standaard beloning toegepast. Standaard beloningen worden ingesteld in de Azure Portal.

In sommige scenario's moet de toepassing mogelijk worden opgeroepen voordat deze zelfs weet of het resultaat wordt gebruikt of weer gegeven voor de gebruiker. Dit kan gebeuren in situaties waarin bijvoorbeeld de pagina weergave van gepromoveerde inhoud wordt overschreven door een marketing campagne. Als het resultaat van de positie aanroep nooit is gebruikt en de gebruiker deze nooit zag, stuurt u geen bijbehorende belonings oproep.

Deze scenario's worden meestal uitgevoerd wanneer:

* U prerendert de gebruikers interface die de gebruiker mogelijk niet kan zien. 
* Uw toepassing doet voorspellende personalisatie waarbij rang gesprekken worden gedaan met weinig realtime context en de toepassing kan de uitvoer mogelijk niet gebruiken. 

In dergelijke gevallen gebruikt u persoonlijke instellingen om de positie aan te roepen en vraagt u de gebeurtenis _inactief_te zijn. Personaler verwacht geen beloning voor deze gebeurtenis en past geen standaard beloning toe. Als de toepassing de informatie van de classificatie oproep gebruikt, wordt de gebeurtenis later in de bedrijfs logica _geactiveerd_ . Zodra de gebeurtenis actief is, verwacht de Personaler een gebeurtenis beloning. Als er geen expliciete aanroep wordt gedaan naar de belonings-API, past Personaler een standaard beloning toe.

## <a name="inactive-events"></a>Inactieve gebeurtenissen

Als u de training voor een gebeurtenis wilt uitschakelen, roept u de positie aan met behulp van `learningEnabled = False`. Voor een inactieve gebeurtenis wordt Learning impliciet geactiveerd als u een beloning voor de gebeurtenis-of aanroepen van de `activate`-API voor die gebeurtenis-activiteit stuurt.

## <a name="learning-settings"></a>Leer instellingen

Leer instellingen bepalen de *Hyper parameters* van de model training. Twee modellen van dezelfde gegevens die worden getraind op verschillende leer instellingen, worden uiteindelijk afwijkend.

### <a name="import-and-export-learning-policies"></a>Leer beleid importeren en exporteren

U kunt leer beleids bestanden importeren en exporteren vanuit het Azure Portal. Gebruik deze methode om bestaande beleids regels op te slaan, te testen, te vervangen en te archiveren in uw broncode beheer als artefacten voor toekomstige Naslag informatie en controle.

### <a name="understand-learning-policy-settings"></a>Informatie over de instellingen van het leer beleid

De instellingen in het trainings beleid zijn niet bedoeld om te worden gewijzigd. Wijzig de instellingen alleen als u begrijpt hoe deze van invloed zijn op persoonlijkere. U kunt zonder deze kennis problemen veroorzaken, waaronder het ongeldig maken van Personaler-modellen.

### <a name="compare-learning-policies"></a>Leer beleid vergelijken

U kunt vergelijken hoe verschillende leer beleidsregels worden uitgevoerd op eerdere gegevens in persoonlijke logboeken door [offline-evaluaties](concepts-offline-evaluation.md)uit te voeren.

[Upload uw eigen trainings beleid](how-to-offline-evaluation.md) om ze te vergelijken met het huidige leer beleid.

### <a name="optimize-learning-policies"></a>Leer beleid optimaliseren

Personaler kan een geoptimaliseerd leer beleid maken in een [offline-evaluatie](how-to-offline-evaluation.md). Een geoptimaliseerd leer beleid met betere voor delen in een offline-evaluatie levert betere resultaten op wanneer het online in Personaler wordt gebruikt.

Wanneer u een leer beleid optimaliseert, kunt u dit rechtstreeks op persoonlijker Toep assen zodat het huidige beleid wordt vervangen. Of u kunt het geoptimaliseerde beleid voor verdere evaluatie opslaan en later beslissen of u het wilt verwijderen, opslaan of Toep assen.
