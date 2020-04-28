---
title: Veelgestelde vragen over identiteits beveiliging in Azure Active Directory
description: Veelgestelde vragen Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 140ad45d9c4f6b6f49a4ea4aefb9298e58a2cf10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75443576"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Veelgestelde vragen over identiteits beveiliging in Azure Active Directory

## <a name="dismiss-user-risk-known-issues"></a>Bekende problemen met gebruikers risico negeren

**Gebruikers risico** voor klassieke identiteits beveiliging sluiten Hiermee wordt de actor in de risico geschiedenis van de gebruiker in identiteits beveiliging ingesteld op **Azure AD**.

**Gebruikers risico** voor identiteits beveiliging negeren Hiermee wordt de actor in de risico geschiedenis van de gebruiker in identiteits beveiliging ingesteld op ** \<de naam van de beheerder met\>een Hyper link die verwijst naar de Blade van de gebruiker**.

Er is een huidige bekend probleem waardoor er een latentie in de stroom van de gebruikers risico is ontstaan. Als u een ' gebruikers risico beleid ' hebt, wordt dit beleid niet meer toegepast op genegeerde gebruikers binnen enkele minuten na het klikken op risico van gebruikers. Er zijn echter bekende vertragingen met de UX waarmee de ' risico status ' van genegeerde gebruikers wordt vernieuwd. Als tijdelijke oplossing kunt u de pagina op het niveau van de browser vernieuwen om de nieuwste gebruiker ' risico status ' te zien.

## <a name="risky-users-report-known-issues"></a>Risk ante gebruikers rapporteren bekende problemen

Query's op het veld **username** zijn hoofdletter gevoelig, terwijl query's op het **naam** veld neutraal zijn.

**Als u datums weer geven** inschakelt, wordt de kolom **risico laatst bijgewerkt** verborgen. Als u de kolom wilt lezen, klikt u boven aan de Blade Risk ante gebruikers op **kolommen** .

**Alle gebeurtenissen** in klassieke identiteits beveiliging sluiten Hiermee stelt u de status van de risico detecties in op **gesloten (opgelost)**.

## <a name="risky-sign-ins-report-known-issues"></a>Risk ante aanmeldingen melden bekende problemen

**Oplossen** met een risico detectie stelt de status in op **gebruikers die MFA door gegeven door beleid op basis van risico**.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="why-is-a-user-is-at-risk"></a>Waarom is een gebruiker risico?

Als u een Azure AD Identity Protection klant bent, gaat u naar de weer gave [Risk ante gebruikers](howto-identity-protection-investigate-risk.md#risky-users) en klikt u op een gebruiker met een risico. In de lade aan de onderkant worden in het tabblad risico geschiedenis alle gebeurtenissen weer gegeven die hebben geleid tot een wijziging in de gebruikers risico. Als u alle Risk ante aanmeldingen voor de gebruiker wilt weer geven, klikt u op Risk ante aanmeldingen van gebruiker. Als u alle risico detecties voor deze gebruiker wilt weer geven, klikt u op risico detectie gebruiker.

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>Hoe kan ik een rapport met detecties van een specifiek type krijgen?

Ga naar de weer gave risico detecties en filter op detectie type. U kunt dit rapport vervolgens downloaden in. CSV of. JSON-indeling met behulp van de knop **downloaden** bovenaan. Zie voor meer informatie het artikel [How to: risico onderzoeken](howto-identity-protection-investigate-risk.md#risk-detections).

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Waarom kan ik mijn eigen risico niveaus niet instellen voor elke risico detectie?

Risico niveaus in identiteits beveiliging zijn gebaseerd op de nauw keurigheid van de detectie en worden mogelijk gemaakt door onze gecontroleerde machine learning. Als u wilt aanpassen welke gebruikers ervaring worden gepresenteerd, kan de beheerder bepaalde gebruikers/groepen opnemen/uitsluiten van het risico beleid voor gebruikers Risico's en aanmeldings Risico's.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Waarom komt de locatie van een aanmelding niet overeen met waar de gebruiker zich daad werkelijk heeft aangemeld?

Toewijzing van IP-geolocatie is een toonaangevende uitdaging. Als u denkt dat de locatie die wordt vermeld in het rapport aanmeldingen niet overeenkomt met de werkelijke locatie, neemt u contact op met micro soft ondersteuning. 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>Hoe kan ik specifieke risico detecties sluiten, zoals ik heb in de oude gebruikers interface?

U kunt feedback geven over risico detectie door de gekoppelde aanmelding te bevestigen als aangetast of veilig. De feedback die is gegeven op het aanmeldings trickles naar alle detecties die zijn uitgevoerd op die aanmelding. Als u detecties wilt sluiten die niet aan een aanmelding zijn gekoppeld, kunt u de feedback op het gebruikers niveau opgeven. Zie voor meer informatie het artikel [How to: feedback geven over Risico's in azure AD Identity Protection](howto-identity-protection-risk-feedback.md).

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>Hoe ver kan ik teruggaan in de tijd om te begrijpen wat er gebeurt met mijn gebruiker?

- In de weer gave [Risk ante gebruikers](howto-identity-protection-investigate-risk.md#risky-users) wordt het risico van een gebruiker weer gegeven op basis van alle eerdere aanmeldingen. 
- In de weer gave [Risk ante aanmeldingen](howto-identity-protection-investigate-risk.md#risky-sign-ins) worden in de afgelopen 30 dagen een risico teken weer gegeven. 
- De weer gave [risico detectie](howto-identity-protection-investigate-risk.md#risk-detections) toont de risico detecties die zijn gemaakt in de afgelopen 90 dagen.

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>Hoe kan ik meer informatie vinden over een specifieke detectie?

Alle risico detecties worden beschreven in het artikel [Wat is risico](concept-identity-protection-risks.md#risk-types-and-detection). U kunt de muis aanwijzer boven het symbool (i) naast de detectie op de Azure Portal voor meer informatie over een detectie.

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Hoe werken de feedback mechanismen voor identiteits beveiliging?

**Aangetaste bevestiging** (bij aanmelding): informeert Azure AD Identity Protection dat de aanmelding niet is uitgevoerd door de eigenaar van de identiteit en duidt op een inbreuk.

- Wanneer deze feedback wordt ontvangen, verplaatsen we de status van het aanmeld-en gebruikers risico naar een **hoge**mate van **aangetast** en risico niveau.

- Daarnaast bieden we de informatie aan onze machine learning systemen voor toekomstige verbeteringen in de risico beoordeling.

    > [!NOTE]
    > Als de gebruiker al is doorgevoerd, klikt u niet op **geïnfecteerde bevestigen** omdat hiermee de aanmeld-en gebruikers risico status wordt verplaatst naar een **hoge**mate van **aangetast** en risico.

**Bevestig veilig** (bij een aanmelding): informeert Azure AD Identity Protection dat de aanmelding is uitgevoerd door de eigenaar van de identiteit en geeft geen inbreuk op.

- Wanneer deze feedback wordt ontvangen, verplaatsen we de risico status aanmelden (niet de gebruiker) om **veilig** en risico niveau te **-** bevestigen.

- Daarnaast bieden we de informatie aan onze machine learning systemen voor toekomstige verbeteringen in de risico beoordeling.

    > [!NOTE]
    > Als u denkt dat de gebruiker niet heeft geknoeid, gebruikt u **gebruikers risico sluiten** op gebruikers niveau in plaats van **bevestigd veilig** op aanmeldings niveau. Als u het risico van gebruikers **negeert** voor het gebruikers niveau, sluit u het gebruikers risico en alle achterstallige Risk ante aanmeldingen en risico detecties.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Waarom kan ik een gebruiker met een laag (of hoger) risico Score zien, zelfs als er geen Risk ante aanmeldingen of risico detecties worden weer gegeven in identiteits beveiliging?

Omdat het gebruikers risico cumulatief is en niet verloopt, kan een gebruiker een risico van weinig of meer gebruikers hebben, zelfs als er geen recente Risk ante aanmeldingen of risico detecties worden weer gegeven in identiteits beveiliging. Deze situatie kan zich voordoen als de enige schadelijke activiteit van een gebruiker voorbij het tijds bestek heeft geduurd waarvoor we de details van Risk ante aanmeldingen en risico detecties opslaan. Er wordt geen gebruikers risico verstrijkt omdat er sprake is van een bekend aantal actors in de 140 omgeving van de klant. Klanten kunnen de risico tijdlijn van de gebruiker bekijken om te begrijpen waarom een gebruiker risico loopt door te gaan naar:`Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Waarom heeft een aanmelding een hoge score voor een ' Sign-in-risico (aggregatie) ' wanneer de detecties zijn van een laag of gemiddeld risico?

De hoge cumulatieve risico Score kan worden gebaseerd op andere functies van de aanmelding of het feit dat er meer dan één detectie voor die aanmelding is geactiveerd. Het is ook mogelijk dat een aanmelding een normaal risico (aggregatie) van het medium heeft, zelfs als de detecties die zijn gekoppeld aan de aanmelding, een hoog risico opleveren. 
