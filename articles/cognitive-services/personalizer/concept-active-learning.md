---
title: Actieve en inactieve gebeurtenissen-persoonlijker
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: 321f12fef44cae43caf53d78b2908e68f9edd0a8
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043893"
---
# <a name="active-and-inactive-events"></a>Actieve en inactieve gebeurtenissen

Wanneer uw toepassing de Rank API aanroept, ontvangt u een actie die door de toepassing moet worden weer gegeven in het veld rewardActionId.  Vanaf dat moment zal Personaler een belonings oproep verwachten met dezelfde gebeurtenis-code. De belonings score wordt gebruikt voor het trainen van het model dat wordt gebruikt voor toekomstige positie oproepen. Als er geen belonings oproep wordt ontvangen voor de gebeurtenis-defaul, wordt er een berekenings beloning toegepast. Standaard beloningen worden vastgesteld in azure Portal.

In sommige gevallen moet de toepassing een positie aanroepen voordat deze zelfs weet of het resultaat wordt gebruikt of weer gegeven voor de gebruiker. Dit kan gebeuren in situaties waarin bijvoorbeeld de pagina weergave van gepromoveerde inhoud wordt overschreven met een marketing campagne. Als het resultaat van de rang nummer nooit is gebruikt en de gebruiker deze niet meer zou zien, zou het niet kunnen worden getraind met een vergoeding in alle, nul of anderszins.
Dit gebeurt meestal wanneer:

* U kunt een bepaalde gebruikers interface vooraf renderen die de gebruiker al dan niet kan zien. 
* Uw toepassing maakt mogelijk gebruik van een voorspellende personalisatie waarbij rang gesprekken worden gedaan met minder realtime-context en de uitvoer ervan kan of mogen niet worden gebruikt door de toepassing. 

In dergelijke gevallen is de juiste manier om persoonlijker te gebruiken, het aanroepen van de gebeurtenis om _inactief_te maken. Personaler verwacht geen beloning voor deze gebeurtenis en er wordt geen standaard beloning toegepast. Als de toepassing de informatie van de classificatie oproep gebruikt, moet u de gebeurtenis later in uw bedrijfs logica _activeren_ . Vanaf het moment dat de gebeurtenis actief is, zal Personaler een beloning voor het evenement verwachten of een standaard beloning Toep assen als er geen expliciete oproep wordt gedaan voor de belonings-API.

## <a name="get-inactive-events"></a>Inactieve gebeurtenissen ophalen

Als u de training voor een gebeurtenis wilt uitschakelen, roept u de positie aan met `learningEnabled = False`.

Het leren voor een inactieve gebeurtenis wordt impliciet geactiveerd als u een beloning voor de gebeurtenis-of-event verzendt, of als u de API voor de `activate` aanroept voor die gebeurtenis-activiteit.

## <a name="learning-settings"></a>Leer instellingen

De leer instellingen bepalen het specifieke *Hyper parameters* van de model training. Twee modellen van dezelfde gegevens, getraind met verschillende leer instellingen, zullen uiteindelijk afwijkend zijn.

### <a name="import-and-export-learning-policies"></a>Leer beleid importeren en exporteren

U kunt Learning-beleids bestanden importeren en exporteren vanuit het Azure Portal. Zo kunt u bestaande beleids regels opslaan, testen, vervangen en deze in uw broncode beheer archiveren als artefacten voor toekomstige Naslag informatie en controle.

### <a name="learning-policy-settings"></a>Instellingen voor het leer beleid

De instellingen in het **trainings beleid** zijn niet bedoeld om te worden gewijzigd. Wijzig de instellingen alleen wanneer u begrijpt hoe deze van invloed zijn op persoonlijkere. Als u instellingen wijzigt zonder deze kennis, zullen er neven effecten ontstaan, waaronder het ongeldig maken van Personaler-modellen.

### <a name="comparing-effectiveness-of-learning-policies"></a>Effectiviteit van het leer beleid vergelijken

U kunt vergelijken hoe verschillende leer beleid zou worden uitgevoerd op eerdere gegevens in persoonlijke logboeken door [offline-evaluaties](concepts-offline-evaluation.md)uit te voeren.

[Upload uw eigen trainings beleid](how-to-offline-evaluation.md) om te vergelijken met het huidige leer beleid.

### <a name="discovery-of-optimized-learning-policies"></a>Detectie van geoptimaliseerde leer beleid

Personaler kan een meer geoptimaliseerd leer beleid maken wanneer een [offline-evaluatie](how-to-offline-evaluation.md)wordt uitgevoerd. Een meer geoptimaliseerd leer beleid, dat wordt weer gegeven om betere beloningen te hebben in een offline-evaluatie, levert betere resultaten op wanneer u online gebruikt in Personaler.

Nadat u een geoptimaliseerd trainings beleid hebt gemaakt, kunt u dit rechtstreeks op persoonlijker Toep assen zodat het huidige beleid direct wordt vervangen, of u kunt het opslaan voor verdere evaluatie en in de toekomst besluiten of u dit later wilt negeren, opslaan of Toep assen.
