---
title: Evaluatie van doorlopende toegang in azure AD
description: Sneller reageren op wijzigingen in de gebruikers status met de evaluatie van continue toegang in azure AD
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
ms.openlocfilehash: 3713901dd3dd5d17c4e1ddcef529c663b68f5b43
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82112572"
---
# <a name="continuous-access-evaluation"></a>Evaluatie van voortdurende toegang

Micro soft-Services, zoals Azure Active Directory (Azure AD) en Office 365, gebruiken open standaarden en protocollen om interoperabiliteit te maximaliseren. Een van de meest essentiële items is open ID Connect (OIDC). Wanneer een client toepassing, zoals Outlook, verbinding maakt met een service zoals Exchange Online, worden de API-aanvragen geautoriseerd met OAuth 2,0-toegangs tokens. De toegangs tokens zijn standaard één uur geldig. Wanneer de client is verlopen, wordt deze teruggeleid naar Azure AD om ze te vernieuwen. Dit biedt ook de mogelijkheid om beleid voor gebruikers toegang opnieuw te evalueren. we kunnen ervoor kiezen om het token niet te vernieuwen vanwege een beleid voor voorwaardelijke toegang, of omdat de gebruiker is uitgeschakeld in de map. 

Het verlopen van tokens en vernieuwen is een standaard mechanisme in de branche. Dat gezegde klanten hebben het probleem geconstateerd over de vertraging tussen de wijziging van de risico omstandigheden voor de gebruiker (bijvoorbeeld: verplaatsen van het hoofd kantoor naar de lokale horeca of de gebruikers referenties die op de zwarte markt zijn gedetecteerd) en wanneer beleids regels kunnen worden afgedwongen voor deze wijziging. We hebben experimenteren met de benadering ' Blunt object ' van een gereduceerde levens duur van tokens, maar ze kunnen de gebruikers ervaringen en betrouw baarheid verlagen zonder Risico's te elimineren.

Voor een tijdige reactie op beleids schendingen of beveiligings problemen is een ' conversatie ' vereist tussen de uitgever van het token, zoals Azure AD, en de Relying Party, zoals Exchange Online. Deze twee richtings conversatie biedt ons twee belang rijke mogelijkheden. De Relying Party kan zien wanneer dingen zijn gewijzigd, zoals een client die afkomstig is van een nieuwe locatie, en de uitgever van het token kenbaar maakt. Het biedt ook de token uitgever een manier om de Relying Party te laten weten dat tokens voor een bepaalde gebruiker niet meer worden geëerbiedigd vanwege inbreuk op het account, het uitschakelen of andere problemen. Het mechanisme voor deze conversatie is een voortdurende toegangs beoordeling (CAE).

Micro soft is een vroege deel nemer geweest in het CAEP-initiatief (continue Access Evaluation Protocol) als onderdeel van de werk groep [gedeelde signalen en gebeurtenissen](https://openid.net/wg/sse/) in de OpenID Connect Foundation. Id-providers en relying party's kunnen gebruikmaken van de beveiligings gebeurtenissen en signalen die zijn gedefinieerd door de werk groep om de toegang opnieuw te autoriseren of te beëindigen. Het is interessante werkzaamheden en verbetert de beveiliging op verschillende platforms en toepassingen.

Omdat de beveiligings voordelen zo fantastisch zijn, implementeren we een micro soft-specifieke initiële implementatie parallel met onze voortgezette werkzaamheden binnen de standaards. We werken samen met het implementeren van deze functies voor continue toegang Evaluation (CAE) in micro soft-Services, maar we hebben een hoop geleerd en delen deze gegevens met de standaard community. We hopen dat onze ervaring in implementatie u kan helpen een nog betere industrie norm te informeren en deze standaard te implementeren, zodat alle deelnemende Services kunnen profiteren.

## <a name="how-does-cae-work-in-microsoft-services"></a>Hoe werkt CAE in micro soft-Services?

We richten onze eerste implementatie van voortdurende toegang tot Exchange en teams. We hopen in de toekomst ondersteuning voor andere micro soft-services uit te breiden. We gaan de evaluatie van continue toegang alleen inschakelen voor tenants zonder beleid voor voorwaardelijke toegang. We zullen onze informatie uit deze fase van CAE gebruiken om onze voortdurende implementatie van CAE te informeren.

## <a name="service-side-requirements"></a>Vereisten aan de service zijde

De evaluatie van voortdurende toegang wordt geïmplementeerd door Services (resource providers) in te scha kelen voor het abonneren op kritieke gebeurtenissen in azure AD, zodat deze gebeurtenissen in de buurt van real-time kunnen worden geëvalueerd en afgedwongen. De volgende gebeurtenissen worden afgedwongen in deze eerste CAE-implementatie:

- Gebruikers account is verwijderd of uitgeschakeld
- Het wacht woord voor een gebruiker wordt gewijzigd of opnieuw ingesteld
- De beheerder trekt expliciet alle vernieuwings tokens voor een gebruiker in
- Er is een verhoogd gebruikers risico gedetecteerd door Azure AD Identity Protection

In de toekomst hopen we meer gebeurtenissen toe, waaronder gebeurtenissen zoals locatie en Apparaatstatus. **Hoewel ons doel is om direct te kunnen afdwingen, kan in sommige gevallen een latentie van Maxi maal 15 minuten worden waargenomen als gevolg van de doorgifte tijd van de gebeurtenis**. 

## <a name="client-side-claim-challenge"></a>Claim vraag aan client zijde

Voordat de evaluatie doorlopend wordt gedetecteerd, proberen clients altijd het toegangs token opnieuw af te spelen uit de cache zolang het niet is verlopen. Met CAE wordt een nieuw geval geïntroduceerd dat een resource provider een token kan afwijzen, zelfs wanneer het niet is verlopen. Om clients te informeren dat hun cache wordt omzeild, zelfs als de tokens in de cache niet zijn verlopen, introduceren we een mechanisme dat **claim Challenge**wordt genoemd. Voor CAE is een client update vereist om de claim Challenge te begrijpen. De meest recente versie van de volgende toepassingen ondersteunen claim Challenge:

- Outlook voor Windows 
- Outlook iOS 
- Outlook Android 
- Outlook Mac 
- Teams voor Windows
- Teams iOS 
- Teams Android 
- Mac voor teams 

## <a name="token-lifetime"></a>Levensduur van token

Omdat het risico en het beleid in realtime worden geëvalueerd, zullen clients die onderhandelen over continue toegang-evaluatie sessies, vertrouwen op CAE in plaats van het bestaande beleid voor de levens duur van een statisch toegangs token, wat betekent dat het Configureer bare token levensduur beleid niet meer wordt gehonoreerd voor CAE-compatibele clients die onderhandelen over CAE-compatibele sessies.

De levens duur van het toegangs token wordt verhoogd naar 24 uur in CAE-sessies. Intrekken wordt aangestuurd door kritieke gebeurtenissen en beleids evaluatie, geen wille keurige tijds periode. Met deze wijziging wordt de stabiliteit van uw toepassingen verhoogd zonder dat dit van invloed is op uw beveiligings postuur. 

## <a name="example-flows"></a>Voorbeeld stromen

### <a name="user-revocation-event-flow"></a>Stroom voor het intrekken van de gebruiker:

![Stroom voor het intrekken van de gebruiker](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. Een client met CAE-ondersteuning presenteert referenties of een vernieuwings token naar AAD die vraagt om een toegangs token voor een bepaalde resource.
1. Een toegangs token wordt samen met andere artefacten naar de client geretourneerd.
1. Een beheerder [trekt expliciet alle vernieuwings tokens voor de gebruiker in](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Er wordt een intrekkings gebeurtenis verzonden naar de resource provider vanuit Azure AD.
1. Er wordt een toegangs token aan de resource provider door gegeven. De resource provider evalueert de geldigheid van het token en controleert of er een intrekkings gebeurtenis voor de gebruiker is. De resource provider gebruikt deze informatie om te bepalen of u toegang wilt verlenen aan de resource.
1. In dit geval weigert de resource provider de toegang en stuurt een 401 + claim-Challenge terug naar de client
1. De-client die geschikt is voor CAE, begrijpt de 401 + claim Challenge. Het omzeilt de caches en gaat terug naar stap 1, verzendt het vernieuwings token samen met de claim uitdaging terug naar Azure AD. Azure AD evalueert vervolgens alle voor waarden en vraagt de gebruiker in dit geval opnieuw te verifiëren.
 
## <a name="faqs"></a>Veelgestelde vragen

### <a name="what-is-the-lifetime-of-my-access-token"></a>Wat is de levens duur van mijn toegangs token?

Als u geen gebruik maakt van CAE-compatibele clients, is de standaard levens duur van het toegangs token nog steeds 1 uur, tenzij u de levens duur van uw toegangs token hebt geconfigureerd met de preview-functie voor de [Configureer bare levens duur van de token (CTL)](../develop/active-directory-configurable-token-lifetimes.md) .

Als u gebruik wilt maken van CAE-compatibele clients die onderhandelen over CAE-sessies, worden uw CTL-instellingen voor de levens duur van het toegangs token overschreven en is de levens duur van het toegangs token 24 uur.

### <a name="how-quick-is-enforcement"></a>Hoe snel is afdwinging?

Hoewel ons doel is om direct te kunnen afdwingen, kan in sommige gevallen een latentie van Maxi maal 15 minuten worden waargenomen als gevolg van de doorgifte tijd van de gebeurtenis.

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Hoe wordt het gebruik van CAE met de aanmeldings frequentie?

De aanmeldings frequentie wordt gerespecteerd met of zonder CAE.

## <a name="next-steps"></a>Volgende stappen

[Aangekondigde evaluatie van voortdurende toegang](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
