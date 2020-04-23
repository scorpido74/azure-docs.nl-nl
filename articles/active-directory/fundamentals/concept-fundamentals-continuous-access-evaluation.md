---
title: Evaluatie van continue toegang in Azure AD
description: Sneller reageren op wijzigingen in de gebruikersstatus met continue toegangsevaluatie in Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5b70c11cd6bc24f945b437decf22586cfb97557
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873301"
---
# <a name="continuous-access-evaluation"></a>Continue toegangsevaluatie

Microsoft-services, zoals Azure Active Directory (Azure AD) en Office 365, gebruiken open standaarden en protocollen om de interoperabiliteit te maximaliseren. Een van de meest kritische is Open ID Connect (OIDC). Wanneer een clienttoepassing zoals Outlook verbinding maakt met een service zoals Exchange Online, worden de API-aanvragen geautoriseerd met OAuth 2.0-toegangstokens. Standaard zijn deze toegangstokens een uur geldig. Wanneer deze verlopen, wordt de client doorgestuurd naar Azure AD om ze te vernieuwen. Dat biedt ook de mogelijkheid om beleid voor gebruikerstoegang opnieuw te evalueren : we kunnen ervoor kiezen om het token niet te vernieuwen vanwege een beleid voor voorwaardelijke toegang of omdat de gebruiker is uitgeschakeld in de directory. 

We hebben de overweldigende feedback van onze klanten gehoord: een vertraging van een uur als gevolg van de levensduur van toegangstoken voor het opnieuw toepassen van voorwaardelijke toegangsbeleid en wijzigingen in de gebruikersstatus (bijvoorbeeld uitgeschakeld als gevolg van verlof) is niet goed genoeg.

Microsoft heeft al vroeg deelgenomen aan het Continuous Access Evaluation Protocol (CAEP) als onderdeel van de werkgroep [Gedeelde signalen en evenementen](https://openid.net/wg/sse/) van de OpenID Foundation. Identiteitsproviders en relying parties kunnen gebruik maken van de beveiligingsgebeurtenissen en -signalen die door de werkgroep zijn gedefinieerd om de toegang opnieuw te autoriseren of te beëindigen. Het is spannend werk en zal de beveiliging op veel platforms en toepassingen verbeteren.

Omdat de beveiligingsvoordelen zo groot zijn, rollen we een Microsoft-specifieke eerste implementatie uit, parallel aan ons voortdurende werk binnen de normalisatie-instellingen. Terwijl we werken aan het implementeren van deze CAE-mogelijkheden (Continuous Access Evaluation) in Microsoft-services, hebben we veel geleerd en delen we deze informatie met de standaardencommunity. We hopen dat onze ervaring met implementatie kan helpen om een nog betere industriestandaard te helpen en zijn toegewijd aan de implementatie van die standaard nadat deze is geratificeerd, zodat alle deelnemende diensten kunnen profiteren.

## <a name="how-does-cae-work-in-microsoft-services"></a>Hoe werkt CAE in Microsoft-services?

We richten onze eerste implementatie van continue toegangsevaluatie op Exchange en Teams. We hopen de ondersteuning voor andere Microsoft-services in de toekomst uit te breiden. We zullen beginnen met het inschakelen van continue toegang evaluatie alleen voor huurders zonder voorwaardelijke toegang beleid. We zullen onze lessen uit deze fase van CAE gebruiken om onze voortdurende uitrol van CAE te informeren.

## <a name="service-side-requirements"></a>Service-eisen

Continue toegangsevaluatie wordt geïmplementeerd door services (resourceproviders) in staat te stellen zich te abonneren op kritieke gebeurtenissen in Azure AD, zodat deze gebeurtenissen in de buurt van realtime kunnen worden geëvalueerd en afgedwongen. De volgende gebeurtenissen worden afgedwongen in deze eerste CAE-implementatie:

- Gebruikersaccount wordt verwijderd of uitgeschakeld
- Wachtwoord voor een gebruiker wordt gewijzigd of opnieuw ingesteld
- Beheerder trekt expliciet alle vernieuwingstokens in voor een gebruiker
- Verhoogd gebruikersrisico gedetecteerd door Azure AD-identiteitsbeveiliging

In de toekomst hopen we meer evenementen toe te voegen, waaronder gebeurtenissen zoals wijzigingen in de locatie- en apparaatstatus. **Terwijl ons doel is voor de handhaving om onmiddellijk te zijn, in sommige gevallen latentie van maximaal 15 minuten kan worden waargenomen als gevolg van gebeurtenis voortplantingtijd**. 

## <a name="client-side-claim-challenge"></a>Client-side claim uitdaging

Vóór continue toegangsevaluatie zouden clients altijd proberen het toegangstoken opnieuw uit de cache te spelen zolang het niet is verlopen. Met CAE introduceren we een nieuwe case dat een resourceprovider een token kan weigeren, zelfs als het niet is verlopen. Om klanten te informeren om hun cache te omzeilen, ook al zijn de tokens in de cache nog niet verlopen, introduceren we een mechanisme genaamd **claimchallenge.** CAE vereist een clientupdate om de claimuitdaging te begrijpen. De nieuwste versie van de volgende toepassingen hieronder ondersteuning claim uitdaging:

- Outlook voor Windows 
- Outlook iOS 
- Outlook Android 
- Outlook Mac 
- Teams voor Windows
- Teams iOS 
- Teams Android 
- Teams Mac 

## <a name="token-lifetime"></a>Levensduur van token

Omdat risico's en beleid in realtime worden geëvalueerd, vertrouwen clients die onderhandelen over doorlopende toegangsevaluatiebewuste sessies op CAE in plaats van op bestaande statische toegangstokenlevenslangbeleid, wat betekent dat configureerbaar tokenlevenslangbeleid niet meer wordt gehonoreerd voor CAE-geschikte clients die cae-bewuste sessies onderhandelen.

We verhogen de levensduur van toegangstoken tot 24 uur in CAE-sessies. Intrekking wordt gedreven door kritieke gebeurtenissen en beleidsevaluatie, niet door een willekeurige periode. Deze wijziging verhoogt de stabiliteit van uw toepassingen zonder dat dit gevolgen heeft voor uw beveiligingshouding. 

## <a name="example-flows"></a>Voorbeeldstromen

### <a name="user-revocation-event-flow"></a>Gebeurtenisstroom voor herroeping van gebruikers:

![Gebeurtenisstroom voor gebruikersintrekking](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. Een CLIENT die geschikt is voor CAE presenteert referenties of een vernieuwingstoken aan AAD met de vraag om een toegangstoken voor een bepaalde bron.
1. Een toegangstoken wordt samen met andere artefacten teruggestuurd naar de client.
1. Een beheerder trekt expliciet [alle vernieuwingstokens voor de gebruiker in.](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) Vanuit Azure AD wordt een intrekkingsgebeurtenis naar de resourceprovider verzonden.
1. Een toegangstoken wordt gepresenteerd aan de resourceprovider. De resourceprovider evalueert de geldigheid van het token en controleert of er een intrekkingsgebeurtenis voor de gebruiker is. De resourceprovider gebruikt deze informatie om te beslissen om al dan niet toegang te verlenen tot de bron.
1. In dit geval weigert de resourceprovider de toegang en stuurt een 401+ claimuitdaging terug naar de klant
1. De CAE-capabele klant begrijpt de 401+ claim uitdaging. Het omzeilt de caches en gaat terug naar stap 1, het verzenden van de refresh token samen met de claim uitdaging terug naar Azure AD. Azure AD evalueert vervolgens alle voorwaarden opnieuw en vraagt de gebruiker om in dit geval opnieuw te verifiëren.
 
## <a name="faqs"></a>Veelgestelde vragen

### <a name="what-is-the-lifetime-of-my-access-token"></a>Wat is de levensduur van mijn Access Token?

Als u geen CAE-geschikte clients gebruikt, bedraagt de standaard levensduur van toegangstoken nog steeds 1 uur, tenzij u de levensduur van Access Token hebt geconfigureerd met de previewfunctie [Configurable Token Lifetime (CTL).](../develop/active-directory-configurable-token-lifetimes.md)

Als u CAE-geschikte clients gebruikt die onderhandelen over CAE-bewuste sessies, worden uw CTL-instellingen voor de levensduur van Access Token overschreven en duurt de levensduur van Access Token 24 uur.

### <a name="how-quick-is-enforcement"></a>Hoe snel gaat handhaving?

Hoewel ons doel is dat handhaving onmiddellijk is, kan in sommige gevallen latentie van maximaal 15 minuten worden waargenomen vanwege de voortplantingstijd van gebeurtenissen.

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Hoe werkt CAE met Aanmeldingsfrequentie?

Aanmeldingsfrequentie wordt gehonoreerd met of zonder CAE.

## <a name="next-steps"></a>Volgende stappen

[Aankondiging van continue toegangsevaluatie](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
