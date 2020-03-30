---
title: Inzicht in de impliciete subsidiestroom van OAuth2 in Azure AD | Microsoft Documenten
description: Meer informatie over de implementatie van de impliciete subsidiestroom van Azure Active Directory en of deze geschikt is voor uw toepassing.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 08/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: eaa3844bfbbef8cb71dbe8691cab894c921ce00a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154505"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>De impliciete subsidiestroom van OAuth2 in Azure Active Directory (AD) begrijpen

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

De OAuth2 impliciete subsidie is berucht voor het feit dat de subsidie met de langste lijst van veiligheidsproblemen in de OAuth2 specificatie. En toch, dat is de aanpak uitgevoerd door ADAL JS en degene die we aanbevelen bij het schrijven van SPA-toepassingen. Wat geeft? Het is allemaal een kwestie van afwegingen: en als het blijkt, de impliciete subsidie is de beste aanpak die u nastreven voor toepassingen die een Web API via JavaScript verbruiken vanuit een browser.

## <a name="what-is-the-oauth2-implicit-grant"></a>Wat is de Impliciete Subsidie OAuth2?

De typische [OAuth2-autorisatiecodesubsidie](https://tools.ietf.org/html/rfc6749#section-1.3.1) is de autorisatieverlening die twee afzonderlijke eindpunten gebruikt. Het autorisatieeindpunt wordt gebruikt voor de interactiefase van de gebruiker, wat resulteert in een autorisatiecode. Het tokeneindpunt wordt vervolgens door de client gebruikt voor het uitwisselen van de code voor een toegangstoken en vaak ook een vernieuwingstoken. Webtoepassingen zijn verplicht om hun eigen toepassingsreferenties aan het token-eindpunt te presenteren, zodat de autorisatieserver de client kan verifiëren.

De [impliciete oauth2subsidie](https://tools.ietf.org/html/rfc6749#section-1.3.2) is een variant van andere vergunningssubsidies. Hiermee kan een client een toegangstoken (en id_token, wanneer hij [OpenId Connect](https://openid.net/specs/openid-connect-core-1_0.html)gebruikt) rechtstreeks vanuit het autorisatieeindpunt verkrijgen, zonder contact op te nemen met het tokeneindpunt of de client te authenticeren. Deze variant is ontworpen voor JavaScript-gebaseerde toepassingen die worden uitgevoerd in een webbrowser: in de oorspronkelijke OAuth2-specificatie worden tokens geretourneerd in een URI-fragment. Dat maakt de tokenbits beschikbaar voor de JavaScript-code in de client, maar het garandeert dat ze niet worden opgenomen in omleidingen naar de server. In OAuth2 impliciete subsidie geeft het autorisatieeindpunt toegangstokens rechtstreeks aan de client uit met behulp van een omleidingsURI die eerder werd geleverd. Het heeft ook het voordeel van het elimineren van alle vereisten voor cross origin calls, die nodig zijn als de JavaScript-toepassing nodig is om contact op te nemen met het token eindpunt.

Een belangrijk kenmerk van de Impliciete OAuth2-subsidie is het feit dat dergelijke stromen nooit refresh tokens teruggeven aan de client. De volgende sectie laat zien hoe dit niet nodig is en zou in feite een beveiligingsprobleem.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Geschikte scenario's voor de Impliciete OAuth2-subsidie

De OAuth2 specificatie verklaart dat de impliciete subsidie is bedacht om user-agent toepassingen mogelijk te maken - dat wil zeggen, JavaScript-toepassingen uitvoeren binnen een browser. Het bepalende kenmerk van dergelijke toepassingen is dat JavaScript-code wordt gebruikt voor toegang tot serverbronnen (meestal een web-API) en voor het bijwerken van de gebruikerservaring van de toepassing dienovereenkomstig. Denk aan toepassingen zoals Gmail of Outlook Web Access: wanneer u een bericht selecteert in uw postvak IN, wordt alleen het berichtvisualisatiepaneel gewijzigd om de nieuwe selectie weer te geven, terwijl de rest van de pagina ongewijzigd blijft. Dit kenmerk staat in contrast met traditionele redirect-gebaseerde webapps, waarbij elke gebruikersinteractie resulteert in een volledige pagina postback en een volledige paginaweergave van de nieuwe serverrespons.

Toepassingen die de JavaScript-gebaseerde benadering van het uiterste nemen, worden toepassingen met één pagina of SA's genoemd. Het idee is dat deze toepassingen alleen een eerste HTML-pagina en bijbehorende JavaScript bevatten, waarbij alle volgende interacties worden aangestuurd door Web API-aanroepen die via JavaScript worden uitgevoerd. Echter, hybride benaderingen, waar de toepassing is meestal postback-driven, maar voert af en toe JS-oproepen, zijn niet ongewoon - de discussie over impliciete stroom gebruik is relevant voor die ook.

Op omleidingen gebaseerde toepassingen beveiligen hun verzoeken doorgaans via cookies, maar die aanpak werkt niet zo goed voor JavaScript-toepassingen. Cookies werken alleen tegen het domein waarvoor ze zijn gegenereerd, terwijl JavaScript-oproepen kunnen worden gericht op andere domeinen. In feite zal dat vaak het geval zijn: denk aan toepassingen die een beroep doen op Microsoft Graph API, Office API, Azure API - allemaal buiten het domein van waaruit de toepassing wordt weergegeven. Een groeiende trend voor JavaScript-toepassingen is om helemaal geen backend te hebben, waarbij 100% afhankelijk is van webAPI's van derden om hun bedrijfsfunctie te implementeren.

Momenteel is de voorkeursmethode voor het beschermen van aanroepen naar een Web-API het gebruik van de OAuth2-tokenbenadering aan toonder, waarbij elke aanroep vergezeld gaat van een OAuth2-toegangstoken. De Web API onderzoekt het binnenkomende toegangstoken en verleent, als het daarin de benodigde scopes vindt, toegang tot de gevraagde bewerking. De impliciete stroom biedt een handig mechanisme voor JavaScript-toepassingen om toegangstokens voor een Web API te verkrijgen, met tal van voordelen met betrekking tot cookies:

* Tokens kunnen betrouwbaar worden verkregen zonder dat er kruisoorsprongsoproepen nodig zijn - verplichte registratie van de omleiding URI naar welke tokens terugkeergaranties zijn dat tokens niet worden verplaatst
* JavaScript-toepassingen kunnen zoveel toegangstokens verkrijgen als ze nodig hebben, voor zoveel web-API's die ze targeten - zonder beperking op domeinen
* HTML5-functies zoals sessie of lokale opslag bieden volledige controle over tokencaching en levenslang beheer, terwijl cookiesbeheer ondoorzichtig is voor de app
* Toegangstokens zijn niet gevoelig voor Cross-site request forgery (CSRF) aanvallen

De impliciete subsidiestroom geeft geen vernieuwingstokens uit, meestal om veiligheidsredenen. Een refresh token is niet zo eng scoped als access tokens, waardoor veel meer macht wordt toegekend, waardoor het veel meer schade toebrengt in het geval het wordt uitgelekt. In de impliciete stroom worden tokens geleverd in de URL, vandaar dat het risico op onderschepping hoger is dan in de autorisatiecodeverlening.

Een JavaScript-toepassing heeft echter een ander mechanisme tot zijn beschikking voor het vernieuwen van toegangstokens zonder herhaaldelijk de gebruiker om referenties te vragen. De toepassing kan een verborgen iframe gebruiken om nieuwe tokenaanvragen uit te voeren tegen het autorisatieeindpunt van Azure AD: zolang de browser nog steeds een actieve sessie heeft (lees: heeft een sessiecookie) tegen het Azure AD-domein, kan de verificatieaanvraag met succes optreden zonder enige noodzaak voor interactie met de gebruiker.

Dit model geeft de JavaScript-toepassing de mogelijkheid om toegangstokens onafhankelijk te vernieuwen en zelfs nieuwe te verkrijgen voor een nieuwe API (op voorwaarde dat de gebruiker er eerder toestemming voor gaf). Dit voorkomt de extra belasting van het verwerven, onderhouden en beschermen van een artefact met een hoge waarde, zoals een vernieuwingstoken. Het artefact dat de stille verlenging mogelijk maakt, de Azure AD-sessiecookie, wordt buiten de toepassing beheerd. Een ander voordeel van deze aanpak is dat een gebruiker zich kan afmelden bij Azure AD, met behulp van een van de toepassingen die zijn aangemeld bij Azure AD, uitgevoerd in een van de browsertabbladen. Dit resulteert in het verwijderen van de Azure AD-sessiecookie en de JavaScript-toepassing verliest automatisch de mogelijkheid om tokens te verlengen voor de afgemelde gebruiker.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Is de impliciete subsidie geschikt voor mijn app?

De impliciete subsidie brengt meer risico's met zich mee dan andere subsidies en de gebieden waar u op moet letten zijn goed gedocumenteerd (bijvoorbeeld [Misbruik van Toegangstoken om zich voor te doen als resource-eigenaar in Impliciete stroom][OAuth2-Spec-Implicit-Misuse] en [OAuth 2.0-bedreigingsmodel en beveiligingsoverwegingen).][OAuth2-Threat-Model-And-Security-Implications] Het hogere risicoprofiel is echter grotendeels te wijten aan het feit dat het bedoeld is om toepassingen in te schakelen die actieve code uitvoeren, die door een externe bron aan een browser worden geserveerd. Als u een SPA-architectuur plant, geen backend-componenten hebt of van plan bent een web-API aan te roepen via JavaScript, wordt het gebruik van de impliciete stroom voor tokenacquisitie aanbevolen.

Als uw toepassing een native client is, past de impliciete stroom niet goed. Het ontbreken van de Azure AD-sessiecookie in de context van een native client ontneemt uw toepassing de middelen om een langdurige sessie te onderhouden. Dat betekent dat uw toepassing herhaaldelijk de gebruiker zal vragen wanneer u toegangstokens voor nieuwe bronnen verkrijgt.

Als u een webtoepassing ontwikkelt die een backend bevat en een API uit de backendcode verbruikt, past de impliciete stroom ook niet goed. Andere subsidies geven je veel meer macht. De OAuth2-clientreferenties grant biedt bijvoorbeeld de mogelijkheid om tokens te verkrijgen die de machtigingen weerspiegelen die aan de toepassing zelf zijn toegewezen, in tegenstelling tot gebruikersdelegaties. Dit betekent dat de client de mogelijkheid heeft om programmatische toegang tot bronnen te behouden, zelfs wanneer een gebruiker niet actief betrokken is bij een sessie, enzovoort. Niet alleen dat, maar dergelijke subsidies geven hogere zekerheidgaranties. Toegangstokens gaan bijvoorbeeld nooit door de browser van de gebruiker, ze lopen niet het risico te worden opgeslagen in de browsergeschiedenis, enzovoort. De clienttoepassing kan ook sterke verificatie uitvoeren bij het aanvragen van een token.

## <a name="next-steps"></a>Volgende stappen

* Zie [Hoe u een toepassing integreren met Azure AD][ACOM-How-To-Integrate] voor meer informatie over het toepassingsintegratieproces.

<!--Image references-->

<!--Reference style links in use-->
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: ../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
