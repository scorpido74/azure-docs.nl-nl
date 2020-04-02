---
title: Overzicht van de modus Gedeeld apparaat
titleSuffix: Microsoft identity platform | Azure
description: Meer informatie over gedeelde apparaatmodus om het delen van apparaten voor uw Firstline Workers in te schakelen.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 514782d62f117af5bfff4a5d2b3354c4e263eece
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550238"
---
# <a name="overview-of-shared-device-mode"></a>Overzicht van de modus gedeeld apparaat

De modus Gedeeld apparaat is een functie van Azure Active Directory waarmee u toepassingen bouwen die Firstline Workers ondersteunen en de modus gedeeld apparaat inschakelen op de apparaten die naar hen zijn geïmplementeerd.

> [!NOTE]
> Deze functie is beschikbaar voor openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

## <a name="what-are-firstline-workers"></a>Wat zijn Firstline Workers?

Firstline Workers zijn retailmedewerkers, onderhouds- en veldagenten, medisch personeel en andere gebruikers die niet voor een computer zitten of bedrijfse-mail gebruiken voor samenwerking. In de volgende secties worden de aspecten en uitdagingen van het ondersteunen van Firstline Workers geïntroduceerd, gevolgd door een inleiding tot de functies die door Microsoft worden geleverd en waarmee uw toepassing kan worden gebruikt door de Firstline Workers van een organisatie.

### <a name="challenges-of-supporting-firstline-workers"></a>Uitdagingen bij het ondersteunen van Firstline Workers

Het inschakelen van Firstline Worker-werkstromen omvat uitdagingen die doorgaans niet worden gepresenteerd door typische informatiewerkers. Dergelijke uitdagingen kunnen bestaan uit een hoge omloopsnelheid en minder vertrouwdheid met de belangrijkste productiviteitstools van een organisatie. Om hun Firstline Workers te versterken, nemen organisaties verschillende strategieën aan. Sommige zijn de vaststelling van een bring-your-own-device (BYOD) strategie waarin hun werknemers zakelijke apps gebruiken op hun persoonlijke telefoon, terwijl anderen hun werknemers te voorzien van gedeelde apparaten zoals iPads of Android-tablets.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>Ondersteuning van meerdere gebruikers op apparaten die zijn ontworpen voor één gebruiker

Omdat mobiele apparaten met iOS of Android zijn ontworpen voor afzonderlijke gebruikers, optimaliseren de meeste toepassingen hun ervaring voor gebruik door één gebruiker. Een deel van deze geoptimaliseerde ervaring betekent het inschakelen van eenmalige aanmelding tussen toepassingen en het laten inloggen van gebruikers op hun apparaat. Wanneer een gebruiker zijn account uit een toepassing verwijdert, beschouwt de app het doorgaans niet als een beveiligingsgerelateerde gebeurtenis. Veel apps houden zelfs de referenties van een gebruiker bij voor snelle aanmelding. Mogelijk hebt u dit zelfs zelf ervaren wanneer u een toepassing van uw mobiele apparaat hebt verwijderd en vervolgens opnieuw hebt geïnstalleerd, om vervolgens te ontdekken dat u nog steeds bent aangemeld.

### <a name="global-sign-in-and-sign-out-sso"></a>Wereldwijde aanmelding en afmelding (SSO)

Om de werknemers van een organisatie in staat te stellen de apps te gebruiken in een groep apparaten die door die werknemers worden gedeeld, moeten ontwikkelaars de tegenovergestelde ervaring inschakelen. Werknemers moeten in staat zijn om een apparaat uit het zwembad te halen en een enkel gebaar uit te voeren om "het van hen te maken" voor de duur van hun shift. Aan het einde van hun dienst moeten ze een ander gebaar kunnen uitvoeren om zich wereldwijd af te melden op het apparaat, met al hun persoonlijke en bedrijfsinformatie verwijderd, zodat ze deze kunnen retourneren naar de apparaatgroep. Bovendien, als een werknemer vergeet af te melden, moet het apparaat automatisch worden afgemeld aan het einde van zijn dienst en/of na een periode van inactiviteit.

Azure Active Directory maakt deze scenario's mogelijk met een functie die **de modus gedeeld apparaat**wordt genoemd.

## <a name="introducing-shared-device-mode"></a>Introductie van gedeelde apparaatmodus

Zoals gezegd is de modus voor gedeeld apparaat een functie van Azure Active Directory waarmee u:

* Toepassingen bouwen die Firstline Workers ondersteunen
* Apparaten implementeren voor Firstline Workers en de modus Gedeeld apparaat inschakelen

### <a name="build-applications-that-support-firstline-workers"></a>Toepassingen bouwen die Firstline Workers ondersteunen

U Firstline Workers in uw toepassingen ondersteunen met behulp van de Microsoft Authentication Library (MSAL) en [de Microsoft Authenticator-app](../user-help/user-help-auth-app-overview.md) om de apparaatstatus *genaamd shared device mode*in te schakelen. Wanneer een apparaat zich in de modus gedeeld apparaat bevindt, verstrekt Microsoft uw toepassing informatie om het gedrag ervan te laten wijzigen op basis van de status van de gebruiker op het apparaat, waardoor gebruikersgegevens worden beschermd.

Ondersteunde functies zijn:

* **Meld u aan bij een gebruikersapparaat breed** via een ondersteunde toepassing.
* **Meld u af voor een apparaat breed** van een gebruiker via een ondersteunde toepassing.
* **Vraag de status van het apparaat** op om te bepalen of uw toepassing zich op een apparaat bevindt dat zich in de modus gedeeld apparaat bevindt.
* **Vraag de apparaatstatus van de gebruiker** op het apparaat op om te bepalen of er iets is veranderd sinds de laatste keer dat uw toepassing is gebruikt.

Ondersteuning van de modus voor gedeelde apparaten moet worden beschouwd als zowel een beveiligingsverbetering als een upgrade van functies voor uw toepassing en kan helpen de acceptatie ervan te vergroten in sterk gereguleerde omgevingen zoals gezondheidszorg en financiën.

Uw gebruikers zijn afhankelijk van u om ervoor te zorgen dat hun gegevens niet worden gelekt naar een andere gebruiker. De apparaatmodus delen biedt nuttige signalen om uw toepassing aan te geven dat er een wijziging is opgetreden die u moet beheren. Uw toepassing is verantwoordelijk voor het controleren van de status van de gebruiker op het apparaat elke keer dat de app wordt gebruikt, het wissen van de gegevens van de vorige gebruiker. Dit geldt ook als het wordt herladen vanaf de achtergrond in multi-tasking. Bij een gebruikerswijziging moet u ervoor zorgen dat zowel de gegevens van de vorige gebruiker worden gewist als dat alle gegevens in de cache die in uw toepassing worden weergegeven, worden verwijderd. We raden u aan altijd een grondig beveiligingscontroleproces uit te voeren nadat u de mogelijkheid voor gedeelde apparaatmodus aan uw app hebt toegevoegd.

Zie de sectie [Volgende stappen](#next-steps) aan het einde van dit artikel voor meer informatie over het wijzigen van uw toepassingen om de modus gedeeld apparaat te ondersteunen.

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>Apparaten implementeren voor Firstline Workers en de modus Gedeeld apparaat inschakelen

Zodra uw toepassingen de gedeelde apparaatmodus ondersteunen en de vereiste gegevens en beveiligingswijzigingen bevatten, u deze adverteren als bruikbaar voor Firstline Workers.

De apparaatbeheerders van een organisatie kunnen hun apparaten en toepassingen implementeren in hun winkels en werkplekken via een MDM-oplossing (Mobile Device Management), zoals Microsoft Intune. Een deel van het inrichtingsproces markeert het apparaat als een *gedeeld apparaat.* Beheerders configureren de modus voor gedeelde apparaten door de [Microsoft Authenticator-app te](../user-help/user-help-auth-app-overview.md) implementeren en de modus gedeeld apparaat in te stellen via configuratieparameters. Na het uitvoeren van deze stappen gebruiken alle toepassingen die de modus gedeeld apparaat ondersteunen, de Microsoft Authenticator-toepassing om de gebruikersstatus te beheren en beveiligingsfuncties voor het apparaat en de organisatie te bieden.

## <a name="next-steps"></a>Volgende stappen

We ondersteunen iOS- en Android-platforms voor de modus voor gedeelde apparaten. Bekijk de onderstaande documentatie voor uw platform om Firstline Workers te ondersteunen in uw toepassingen.

* [Ondersteuning voor gedeelde apparaatmodus voor iOS](msal-ios-shared-devices.md)
* [Ondersteuning voor gedeelde apparaatmodus voor Android](msal-android-shared-devices.md)
