---
title: Wat is identity secure score? - Azure Active Directory
description: Hoe u de identiteitsveilige score gebruiken om de beveiligingshouding van uw directory te verbeteren
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f75dea2cffbe710bf2778ceab5eacc91ffcca9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523102"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Wat is de id-beveiligingsscore in Azure Active Directory?

Hoe veilig is uw Azure AD-tenant? Als u niet weet hoe u deze vraag moet beantwoorden, wordt in dit artikel uitgelegd hoe de identiteitsveilige score u helpt om uw identiteitsbeveiligingshouding te controleren en te verbeteren.

## <a name="what-is-an-identity-secure-score"></a>Wat is een id-beveiligingsscore?

De score voor identiteitsbeveiliging is getal tussen 1 en 223 dat fungeert als een indicator voor hoe uitgelijnd u bent met de aanbevelingen voor de beste praktijken van Microsoft voor beveiliging. Elke verbeteringsactie in identity secure score is afgestemd op uw specifieke configuratie.  

![Beveiligingsscore](./media/identity-secure-score/identity-secure-score-overview.png)

De score helpt bij het volgende:

- Objectief meten van het beveiligingspostuur van uw identiteit
- Plannen van verbeteringen aan de identiteitsbeveiliging
- Evalueren van het succes van uw verbeteringen

De score en verwante informatie zijn toegankelijk via het dashboard Identity Secure Score. In dit dashboard vindt u deze informatie:

- Uw identiteit veilige score
- Een vergelijkingsgrafiek met de manier waarop uw identiteitsveilige score zich verhoudt tot andere tenants in dezelfde branche en vergelijkbare grootte
- Een trendgrafiek die laat zien hoe uw identiteitsveilige score in de loop van de tijd is veranderd
- Een lijst van mogelijke verbeteringen

Door de verbeteringsacties te volgen, kunt u het volgende doen:

- Verbeter uw beveiligingshouding en uw score
- Profiteer van de functies die beschikbaar zijn voor uw organisatie als onderdeel van uw identiteitsinvesteringen

## <a name="how-do-i-get-my-secure-score"></a>Hoe kom ik aan mijn beveiligde score?

De score voor identiteitsveilige gegevens is beschikbaar in alle edities van Azure AD. Organisaties hebben toegang tot hun identiteitsveilige score via de **Azure Portal** > **Azure Active Directory** > **Security** > **Identity Score.**

## <a name="how-does-it-work"></a>Hoe werkt het?

Elke 48 uur wordt uw beveiligingsconfiguratie geanalyseerd door Azure en worden uw instellingen vergeleken met de aanbevolen procedures. Op basis van de resultaten van deze evaluatie wordt een nieuwe score berekend voor uw directory. Het is mogelijk dat uw beveiligingsconfiguratie niet volledig is afgestemd op de richtlijnen voor de beste praktijken en dat de verbeteringsacties slechts gedeeltelijk worden uitgevoerd. In deze scenario's krijgt u slechts een deel van de maximale score die beschikbaar is voor het besturingselement.

Elke aanbeveling wordt gemeten op basis van uw Azure AD-configuratie. Als u producten van derden gebruikt om een aanbeveling voor aanbevolen procedures in te schakelen, u deze configuratie aangeven in de instellingen van een verbeteringsactie. U hebt ook de mogelijkheid om aanbevelingen in te stellen die moeten worden genegeerd als ze niet van toepassing zijn op uw omgeving. Een genegeerde aanbeveling draagt niet bij aan de berekening van de score.

![Actie negeren of markeren zoals gedekt door derden](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>Wat heb ik eraan?

De id-beveiligingsscore helpt bij het volgende:

- Objectief meten van het beveiligingspostuur van uw identiteit
- Plannen van verbeteringen aan de identiteitsbeveiliging
- Evalueren van het succes van uw verbeteringen

## <a name="what-you-should-know"></a>Wat u moet weten

### <a name="who-can-use-the-identity-secure-score"></a>Wie kan de id-beveiligingsscore gebruiken?

De id-beveiligingsscore kan worden gebruikt door de volgende rollen:

- Globale beheerder
- Beveiligingsbeheerder
- Beveiligingslezers

### <a name="how-are-controls-scored"></a>Hoe worden besturingselementen beoordeeld?

Besturingselementen kunnen op twee manieren worden gescoord. Sommige worden gescoord in een binaire manier - je krijgt 100% van de score als u de functie of instelling geconfigureerd op basis van onze aanbeveling. Andere scores worden berekend als een percentage van de totale configuratie. Bijvoorbeeld, als de verbetering aanbeveling bepaalt dat je krijgt 30 punten als je al je gebruikers te beschermen met MFA en je hebt slechts 5 van de 100 totale gebruikers beschermd, zou je een gedeeltelijke score rond 2 punten (5 beschermd / 100 totaal * 30 max pts = 2 pts partiële score).

### <a name="what-does-not-scored-mean"></a>Wat betekent [Geen score]?

Acties met het label [Geen score] zijn acties die u kunt uitvoeren in uw organisatie, maar die geen score krijgen omdat ze nog niet zijn gekoppeld aan de tool. U kunt dus wel uw beveiliging verbeteren, maar u krijgt hier op dit moment geen punten voor.

### <a name="how-often-is-my-score-updated"></a>Hoe vaak wordt mijn score bijgewerkt?

De score wordt één keer per dag berekend (om ongeveer 01:00 PST). Als u een wijziging aanbrengt in een gemeten actie, wordt de score automatisch de volgende dag bijgewerkt. Het duurt maximaal 48 uur voordat een wijziging is verwerkt in de score.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Mijn score is gewijzigd. Hoe kom ik erachter waarom?

Ga naar het [Microsoft 365-beveiligingscentrum,](https://security.microsoft.com/)waar u uw volledige beveiligde score van Microsoft vindt. U eenvoudig alle wijzigingen in uw beveiligde score bekijken door de diepgaande wijzigingen op het tabblad geschiedenis te bekijken.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Meet de beveiligde score mijn risico op overtreding?

Nee, dat niet. De beveiligde score geeft geen absolute maatstaf voor hoe waarschijnlijk het is dat u wordt geschonden. De score geeft aan in welke mate u functies hebt geïmplementeerd die het risico van een beveiligingsprobleem beperken. Geen enkele service kan garanderen dat u niet wordt geschonden, en de beveiligde score mag op geen enkele manier worden geïnterpreteerd als een garantie.

### <a name="how-should-i-interpret-my-score"></a>Hoe moet ik mijn score interpreteren?

U krijgt punten voor het configureren van aanbevolen beveiligingsfuncties of het uitvoeren van beveiligingstaken (zoals het lezen van rapporten). Sommige acties krijgen een score voor gedeeltelijke uitvoering, zoals het inschakelen van meervoudige verificatie (MFA) voor uw gebruikers. Uw beveiligde score is rechtstreeks representatief voor de Microsoft-beveiligingsservices die u gebruikt. Vergeet niet dat de veiligheid moet worden afgewogen tegen bruikbaarheid. Alle beveiligingsmechanismen hebben in meer of mindere mate impact op de gebruiker. Mechanismen met een kleine impact op de gebruiker hebben geen tot nauwelijks gevolgen voor de dagelijkse taken van uw gebruikers.

Als u uw scoregeschiedenis wilt bekijken, gaat u naar het [Microsoft 365-beveiligingscentrum](https://security.microsoft.com/) en bekijkt u uw algemene beveiligde score van Microsoft. U wijzigingen in uw algemene beveiligde score bekijken door op Weergavegeschiedenis te klikken. Kies een specifieke datum om te zien welke mechanismen waren ingeschakeld voor die dag en wat dat voor punten heeft opgeleverd.

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Hoe is id-beveiligingsscore gerelateerd aan de beveiligingsscore van Office 365?

De [beveiligde score van Microsoft](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score) bevat vijf verschillende controle- en scorecategorieën:

- Identiteit
- Gegevens
- Apparaten
- Infrastructuur
- Apps

De identiteitsbeveiligde score vertegenwoordigt het identiteitsonderdeel van de beveiligde score van Microsoft. Deze overlapping betekent dat uw aanbevelingen voor de identiteitsveilige score en de identiteitsscore in Microsoft hetzelfde zijn.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over de beveiligde score van Microsoft](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
