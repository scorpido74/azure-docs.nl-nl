---
title: Configureerbare a-azure-levensduur van AD-token
titleSuffix: Microsoft identity platform
description: Meer informatie over het instellen van levens voor tokens die zijn uitgegeven door Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: f4138c4ae24ae599d4058c9fd06c33b69657fe38
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680076"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Configureerbare tokenlevensduur in Azure Active Directory (Voorbeeld)

U kunt de levensduur opgeven van een token dat is uitgegeven door Azure Active Directory (Azure AD). U kunt de levensduur van een token instellen voor alle apps in uw organisatie, voor een multitenanttoepassing (voor meerdere organisaties) of voor een specifieke service-principal in uw organisatie.

> [!IMPORTANT]
> Nadat we tijdens de preview van klanten hebben gehoord, hebben we [verificatiesessiebeheermogelijkheden](https://go.microsoft.com/fwlink/?linkid=2083106) geïmplementeerd in Voorwaardelijke toegang tot Azure AD. U deze nieuwe functie gebruiken om de levensduur van vernieuwingstoken's te configureren door aanmeldingsfrequentie in te stellen. Na 1 mei 2020 kun je het Configurable Token Lifetime-beleid niet meer gebruiken om sessie- en vernieuwingstokens te configureren. U nog steeds de levensduur van toegangstoken's configureren na de afschaffing.

In Azure AD vertegenwoordigt een beleidsobject een set regels die worden afgedwongen voor afzonderlijke toepassingen of op alle toepassingen in een organisatie. Elk beleidstype heeft een unieke structuur, met een set eigenschappen die worden toegepast op objecten waaraan ze zijn toegewezen.

U een beleid aanwijzen als het standaardbeleid voor uw organisatie. Het beleid wordt toegepast op elke toepassing in de organisatie, zolang het niet wordt overschreven door een beleid met een hogere prioriteit. U ook een beleid toewijzen aan specifieke toepassingen. De volgorde van prioriteit verschilt per beleidstype.

> [!NOTE]
> Configureerbaar tokenlevenslangbeleid is alleen van toepassing op mobiele en desktopclients die toegang hebben tot SharePoint Online- en OneDrive voor Bedrijven-bronnen en is niet van toepassing op webbrowsersessies.
> Als u de levensduur van webbrowsersessies voor SharePoint Online en OneDrive voor Bedrijven wilt beheren, gebruikt u de functie [Voorwaardelijke toegang gedurende de sessie.](../conditional-access/howto-conditional-access-session-lifetime.md) Raadpleeg de [SharePoint Online-blog](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) voor meer informatie over het configureren van inactieve sessie-time-outs.

## <a name="token-types"></a>Tokentypen

U tokenlevenslangbeleid instellen voor vernieuwingstokens, toegangstokens, SAML-tokens, sessietokens en ID-tokens.

### <a name="access-tokens"></a>Toegangstokens

Clients gebruiken toegangstokens om toegang te krijgen tot een beveiligde bron. Een toegangstoken kan alleen worden gebruikt voor een specifieke combinatie van gebruiker, client en resource. Toegangstokens kunnen niet worden ingetrokken en zijn geldig tot het verstrijken ervan. Een kwaadwillende actor die een toegangstoken heeft verkregen, kan het gebruiken voor de omvang van zijn levensduur. Het aanpassen van de levensduur van een toegangstoken is een afweging tussen het verbeteren van de systeemprestaties en het verhogen van de hoeveelheid tijd die de client behoudt nadat het account van de gebruiker is uitgeschakeld. Verbeterde systeemprestaties worden bereikt door het aantal keren dat een client een nieuw toegangstoken moet aanschaffen, te verminderen.  De standaardinstelling is 1 uur - na 1 uur moet de client het vernieuwingstoken gebruiken om (meestal in stilte) een nieuw vernieuwingstoken en toegangstoken te verkrijgen. 

### <a name="saml-tokens"></a>SAML-tokens

SAML-tokens worden gebruikt door veel webgebaseerde SAAS-toepassingen en worden verkregen met het SAML2-protocoleindpunt van Azure Active Directory. Ze worden ook verbruikt door toepassingen met behulp van WS-Federation. De standaardlevensduur van het token is 1 uur. Vanuit het perspectief van een toepassing wordt de geldigheidsperiode van het token `<conditions …>` opgegeven door de NotOnOrAfter-waarde van het element in het token. Nadat de geldigheidsperiode van het token is afgelopen, moet de client een nieuwe verificatieaanvraag starten, die vaak wordt voldaan zonder interactief aanmelden als gevolg van het SSO-sessietoken (Single Sign On).

De waarde van NotOnOrAfter kan `AccessTokenLifetime` worden `TokenLifetimePolicy`gewijzigd met behulp van de parameter in een . Het zal worden ingesteld op de levensduur geconfigureerd in het beleid indien van toepassing, plus een klok scheefgroei factor van vijf minuten.

Houd er rekening mee dat de `<SubjectConfirmationData>` in het element opgegeven objectbevestiging NotOnOrAfter niet wordt beïnvloed door de tokenlevensduurconfiguratie. 

### <a name="refresh-tokens"></a>Tokens vernieuwen

Wanneer een client een toegangstoken verwerft om toegang te krijgen tot een beveiligde bron, ontvangt de client ook een vernieuwingstoken. Het vernieuwingstoken wordt gebruikt om nieuwe tokenparen voor toegang/vernieuwing te verkrijgen wanneer het huidige toegangstoken verloopt. Een vernieuwingstoken is gebonden aan een combinatie van gebruiker en client. Een vernieuwingstoken kan op elk gewenst moment worden [ingetrokken](access-tokens.md#token-revocation)en de geldigheid van het token wordt gecontroleerd telkens wanneer het token wordt gebruikt.  Vernieuwingstokens worden niet ingetrokken wanneer ze worden gebruikt om nieuwe toegangstokens op te halen - het is echter de beste manier om het oude token veilig te verwijderen wanneer u een nieuw token krijgt. 

Het is belangrijk om een onderscheid te maken tussen vertrouwelijke klanten en openbare klanten, omdat dit van invloed is op hoe lang vernieuwingstokens kunnen worden gebruikt. Zie [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1)voor meer informatie over verschillende soorten clients.

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Token-levensduur met vertrouwelijke tokens voor het vernieuwen van klanten
Vertrouwelijke clients zijn toepassingen die veilig een clientwachtwoord (geheim) kunnen opslaan. Ze kunnen bewijzen dat verzoeken afkomstig zijn van de beveiligde clientapplicatie en niet van een kwaadwillende actor. Een web-app is bijvoorbeeld een vertrouwelijke client omdat deze een clientgeheim op de webserver kan opslaan. Het is niet blootgesteld. Omdat deze stromen veiliger zijn, kunnen `until-revoked`de standaardlevensduurvan vernieuwingstokens die aan deze stromen zijn uitgegeven, niet worden gewijzigd met behulp van beleid en worden ze niet ingetrokken bij vrijwillige wachtwoordresets.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Token-levensduur met openbare tokens voor het vernieuwen van klanten

Openbare clients kunnen een clientwachtwoord (geheim) niet veilig opslaan. Een iOS/Android-app kan bijvoorbeeld een geheim van de eigenaar van de bron niet verdoezelen, dus wordt het beschouwd als een openbare client. U beleid voor resources instellen om te voorkomen dat vernieuwingstokens van openbare clients ouder dan een bepaalde periode een nieuw tokenpaar voor toegang/vernieuwing verkrijgen. (Gebruik hiervoor de eigenschap Token max inactieve`MaxInactiveTime`tijd vernieuwen ( ).) U ook beleid gebruiken om een periode in te stellen waarboven de vernieuwingstokens niet langer worden geaccepteerd. (Gebruik hiervoor de eigenschap Token Max Age vernieuwen.) U de levensduur van een vernieuwingstoken aanpassen om te bepalen wanneer en hoe vaak de gebruiker referenties opnieuw moet invoeren, in plaats van in stilte opnieuw te worden geverifieerd wanneer u een openbare clienttoepassing gebruikt.

> [!NOTE]
> De eigenschap Max Age is de tijdsduur dat een enkel token kan worden gebruikt. 

### <a name="id-tokens"></a>Id-tokens
ID-tokens worden doorgegeven aan websites en native clients. ID-tokens bevatten profielinformatie over een gebruiker. Een ID-token is gebonden aan een specifieke combinatie van gebruiker en client. ID-tokens worden als geldig beschouwd tot het verstrijken ervan. Meestal koppelt een webtoepassing de sessielevensduur van een gebruiker in de toepassing aan de levensduur van het ID-token dat voor de gebruiker is uitgegeven. U de levensduur van een ID-token aanpassen om te bepalen hoe vaak de webtoepassing de toepassingssessie verloopt en hoe vaak de gebruiker moet worden geverifieerd met Azure AD (in stilte of interactief).

### <a name="single-sign-on-session-tokens"></a>Single sign-on sessietokens
Wanneer een gebruiker zich verifieert met Azure AD, wordt één aanmeldingssessie (SSO) ingesteld met de browser van de gebruiker en Azure AD. Het SSO-token, in de vorm van een cookie, vertegenwoordigt deze sessie. Het SSO-sessietoken is niet gebonden aan een specifieke resource/clienttoepassing. SSO-sessietokens kunnen worden ingetrokken en de geldigheid ervan wordt gecontroleerd elke keer dat ze worden gebruikt.

Azure AD gebruikt twee soorten SSO-sessietokens: persistent en niet-persistent. Permanente sessietokens worden door de browser opgeslagen als permanente cookies. Niet-persistente sessietokens worden opgeslagen als sessiecookies. (Sessiecookies worden vernietigd wanneer de browser wordt gesloten.) Meestal wordt een niet-persistente sessietoken opgeslagen. Maar wanneer de gebruiker het selectievakje **Houd me aangemeld** inschakelt tijdens verificatie, wordt een permanente sessietoken opgeslagen.

Niet-persistente sessietokens hebben een levensduur van 24 uur. Persistente tokens hebben een levensduur van 180 dagen. Wanneer een SSO-sessietoken binnen de geldigheidsperiode wordt gebruikt, wordt de geldigheidsperiode nog eens 24 uur of 180 dagen verlengd, afhankelijk van het tokentype. Als een SSO-sessietoken niet binnen de geldigheidsperiode wordt gebruikt, wordt het als verlopen beschouwd en wordt het niet langer geaccepteerd.

U een beleid gebruiken om de tijd in te stellen waarop het eerste sessietoken is uitgegeven, waarna het sessietoken niet langer wordt geaccepteerd. (Gebruik hiervoor de eigenschap Session Token Max Age.) U de levensduur van een sessietoken aanpassen om te bepalen wanneer en hoe vaak een gebruiker referenties opnieuw moet invoeren, in plaats van in stilte te worden geverifieerd, wanneer u een webtoepassing gebruikt.

### <a name="token-lifetime-policy-properties"></a>Eigenschappen van tokenlevenslangbeleid
Een token levenslang beleid is een type beleidsobject dat token levenslange regels bevat. Gebruik de eigenschappen van het beleid om bepaalde tokenlevensduuren te beheren. Als er geen beleid is ingesteld, wordt de standaardlevensduurwaarde door het systeem afgedwongen.

### <a name="configurable-token-lifetime-properties"></a>Configureerbare tokenlevensduureigenschappen
| Eigenschap | Tekenreeks beleidseigenschap | Beïnvloedt | Standaard | Minimum | Maximum |
| --- | --- | --- | --- | --- | --- |
| Levensduur van access-token |AccessTokenLifetime<sup>2</sup> |Toegangstokens, ID-tokens, SAML2-tokens |1 uur |10 minuten |1 dag |
| Token maximaal inactieve tijd vernieuwen |MaxInactiveTime |Tokens vernieuwen |90 dagen |10 minuten |90 dagen |
| Token max- leeftijd met één factor vernieuwen |MaxageSingleFactor MaxageSingleFactor |Tokens vernieuwen (voor alle gebruikers) |Tot intrekking |10 minuten |Tot ingetrokken<sup>1</sup> |
| Multi-Factor Refresh Token Max Age |MaxageMultifactor |Tokens vernieuwen (voor alle gebruikers) |Tot intrekking |10 minuten |Tot ingetrokken<sup>1</sup> |
| Single-Factor Session Token Max Leeftijd |MaxagesessionSingleFactor MaxagesessionSingleFactor |Sessietokens (persistent en niet-persistent) |Tot intrekking |10 minuten |Tot ingetrokken<sup>1</sup> |
| Multi-Factor Session Token Max Leeftijd |MaxagesessionMultiFactor |Sessietokens (persistent en niet-persistent) |Tot intrekking |10 minuten |Tot ingetrokken<sup>1</sup> |

* <sup>1</sup>365 dagen is de maximale expliciete lengte die voor deze kenmerken kan worden ingesteld.
* <sup>2.</sup> Om ervoor te zorgen dat de Microsoft Teams Web-client werkt, wordt aanbevolen om AccessTokenLifetime tot meer dan 15 minuten te houden voor Microsoft Teams.

### <a name="exceptions"></a>Uitzonderingen
| Eigenschap | Beïnvloedt | Standaard |
| --- | --- | --- |
| Token Max Age vernieuwen (uitgegeven voor federatieve gebruikers die onvoldoende intrekkingsgegevens hebben<sup>1</sup>) |Tokens vernieuwen (uitgegeven voor federatieve gebruikers die onvoldoende intrekkingsgegevens hebben<sup>1</sup>) |12 uur |
| Token Max Inactieve tijd vernieuwen (uitgegeven voor vertrouwelijke clients) |Tokens vernieuwen (uitgegeven voor vertrouwelijke klanten) |90 dagen |
| Token Max-leeftijd vernieuwen (uitgegeven voor vertrouwelijke clients) |Tokens vernieuwen (uitgegeven voor vertrouwelijke klanten) |Tot intrekking |

* <sup>1.</sup> Federatieve gebruikers die onvoldoende intrekkingsgegevens hebben, zijn gebruikers die het kenmerk "LastPasswordChangeTimestamp" niet hebben gesynchroniseerd. Deze gebruikers krijgen deze korte Max Age omdat AAD niet kan verifiëren wanneer tokens die zijn gekoppeld aan een oude referentie (zoals een wachtwoord dat is gewijzigd) moeten intrekken en vaker moeten inchecken om ervoor te zorgen dat de gebruiker en bijbehorende tokens nog steeds een goede reputatie hebben. Om deze ervaring te verbeteren, moeten tenantbeheerders ervoor zorgen dat ze het kenmerk 'LastPasswordChangeTimestamp' synchroniseren (dit kan worden ingesteld op het gebruikersobject met Powershell of via AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Beleidsevaluatie en prioritering
U een tokenlevenslangbeleid maken en vervolgens toewijzen aan een specifieke toepassing, aan uw organisatie en aan serviceprincipals. Er kunnen meerdere beleidsregels van toepassing zijn op een specifieke toepassing. Het token-levenslang beleid dat van kracht wordt, volgt de volgende regels:

* Als een beleid expliciet is toegewezen aan de serviceprincipal, wordt het afgedwongen.
* Als er geen beleid expliciet is toegewezen aan de serviceprincipal, wordt een beleid dat expliciet is toegewezen aan de bovenliggende organisatie van de serviceprincipal afgedwongen.
* Als er geen beleid expliciet is toegewezen aan de serviceprincipal of aan de organisatie, wordt het beleid dat aan de toepassing is toegewezen, afgedwongen.
* Als er geen beleid is toegewezen aan de serviceprincipal, de organisatie of het toepassingsobject, worden de standaardwaarden afgedwongen. (Zie de tabel in [Configureerbare tokenlevensduureigenschappen](#configurable-token-lifetime-properties).)

Zie [Hoofdobjecten voor toepassingen en serviceprincipal in Azure Active Directory](app-objects-and-service-principals.md)voor meer informatie over de relatie tussen toepassingsobjecten en servicehoofdobjecten.

De geldigheid van een token wordt geëvalueerd op het moment dat het token wordt gebruikt. Het beleid met de hoogste prioriteit voor de toepassing die wordt geopend, wordt van kracht.

Alle timespans die hier worden gebruikt, zijn opgemaakt volgens het c# [TimeSpan-object](/dotnet/api/system.timespan) - D.HH:MM:SS.  Dus 80 dagen en 30 minuten zou zijn. `80.00:30:00`  De toonaangevende D kan worden gelaten vallen `00:90:00`als nul, dus 90 minuten zou zijn .  

> [!NOTE]
> Hier is een voorbeeldscenario.
>
> Een gebruiker wil toegang krijgen tot twee webtoepassingen: webtoepassing A en webtoepassing B.
> 
> Factoren:
> * Beide webtoepassingen bevinden zich in dezelfde bovenliggende organisatie.
> * Token Lifetime Policy 1 met een sessietoken max leeftijd van acht uur is ingesteld als de standaardinstelling van de bovenliggende organisatie.
> * Webapplication A is een webtoepassing voor regelmatig gebruik en is niet gekoppeld aan beleid.
> * Web Application B wordt gebruikt voor zeer gevoelige processen. De serviceprincipal is gekoppeld aan Token Lifetime Policy 2, dat een Sessie Token Max Age van 30 minuten heeft.
>
> Om 12:00 uur start de gebruiker een nieuwe browsersessie en probeert hij toegang te krijgen tot webtoepassing A. De gebruiker wordt doorgestuurd naar Azure AD en wordt gevraagd zich aan te melden. Hiermee wordt een cookie met een sessietoken in de browser. De gebruiker wordt omgeleid naar webtoepassing A met een ID-token waarmee de gebruiker toegang heeft tot de toepassing.
>
> Om 12:15 uur probeert de gebruiker toegang te krijgen tot webtoepassing B. De browser wordt omgeleid naar Azure AD, dat de sessiecookie detecteert. De serviceprincipal van Web Application B is gekoppeld aan Token Lifetime Policy 2, maar maakt ook deel uit van de bovenliggende organisatie, met standaard Token Lifetime Policy 1. Token Lifetime Policy 2 wordt van kracht omdat beleid dat is gekoppeld aan serviceprincipals een hogere prioriteit heeft dan het standaardbeleid van de organisatie. Het sessietoken werd oorspronkelijk binnen de laatste 30 minuten uitgegeven, dus het wordt als geldig beschouwd. De gebruiker wordt omgeleid naar webtoepassing B met een ID-token waarmee hij toegang krijgt.
>
> Om 13:00 uur probeert de gebruiker toegang te krijgen tot webtoepassing A. De gebruiker wordt doorgestuurd naar Azure AD. Webtoepassing A is niet gekoppeld aan een beleid, maar omdat dit zich in een organisatie met standaard Token Lifetime Policy 1 bevindt, wordt dat beleid van kracht. De sessiecookie die oorspronkelijk in de afgelopen acht uur is uitgegeven, wordt gedetecteerd. De gebruiker wordt stilletjes doorgestuurd naar webtoepassing A met een nieuw ID-token. De gebruiker hoeft zich niet te verifiëren.
>
> Onmiddellijk daarna probeert de gebruiker toegang te krijgen tot webtoepassing B. De gebruiker wordt doorgestuurd naar Azure AD. Net als voorheen wordt Token Lifetime Policy 2 van kracht. Omdat het token meer dan 30 minuten geleden is uitgegeven, wordt de gebruiker gevraagd om zijn aanmeldingsgegevens opnieuw in te voeren. Er worden een gloednieuw sessietoken en ID-token uitgegeven. De gebruiker heeft dan toegang tot webtoepassing B.
>
>

## <a name="configurable-policy-property-details"></a>Configureerbare eigenschappenvan het beleid
### <a name="access-token-lifetime"></a>Levensduur van access-token
**Tekenreeks:** AccessTokenLifetime AccessTokenLifetime

**Beïnvloedt:** Toegangstokens, ID-tokens, SAML-tokens

**Samenvatting:** Dit beleid bepaalt hoe lang toegangs- en ID-tokens voor deze bron als geldig worden beschouwd. Als u de eigenschap Lifetime van Access Token vermindert, wordt het risico beperkt dat een toegangstoken of ID-token gedurende een langere periode door een kwaadwillende actor wordt gebruikt. (Deze tokens kunnen niet worden ingetrokken.) De afweging is dat de prestaties negatief worden beïnvloed, omdat de tokens vaker moeten worden vervangen.

### <a name="refresh-token-max-inactive-time"></a>Token maximaal inactieve tijd vernieuwen
**Tekenreeks:** MaxInactiveTime

**Beïnvloedt:** Tokens vernieuwen

**Samenvatting:** Met dit beleid bepaalt u hoe oud een vernieuwingstoken kan zijn voordat een client het niet langer kan gebruiken om een nieuw tokenpaar voor toegang/vernieuwing op te halen wanneer een poging om toegang te krijgen tot deze bron. Omdat een nieuw vernieuwingstoken meestal wordt geretourneerd wanneer een vernieuwingstoken wordt gebruikt, voorkomt dit beleid toegang als de client gedurende de opgegeven periode toegang probeert te krijgen tot een resource met behulp van het huidige vernieuwingstoken.

Dit beleid dwingt gebruikers die niet actief zijn geweest op hun client om opnieuw te verifiëren om een nieuw vernieuwingstoken op te halen.

De eigenschap Token max inactieve tijd vernieuwen moet worden ingesteld op een lagere waarde dan de single-factor token max-leeftijd en de eigenschappen van multifactorvernieuwingstoken maxage.

### <a name="single-factor-refresh-token-max-age"></a>Token max- leeftijd met één factor vernieuwen
**Tekenreeks:** MaxageSingleFactor MaxageSingleFactor

**Beïnvloedt:** Tokens vernieuwen

**Samenvatting:** Met dit beleid bepaalt u hoe lang een gebruiker een vernieuwingstoken kan gebruiken om een nieuw tokenpaar voor toegang/vernieuwing te krijgen nadat deze voor het laatst is geverifieerd met slechts één factor. Nadat een gebruiker een nieuw vernieuwingstoken heeft geverifieerd en ontvangen, kan de gebruiker de vernieuwingstokenstroom voor de opgegeven periode gebruiken. (Dit geldt zolang het huidige vernieuwingstoken niet wordt ingetrokken en het niet langer dan de inactieve tijd ongebruikt blijft.) Op dat moment wordt de gebruiker gedwongen om opnieuw te verifiëren om een nieuw vernieuwingstoken te ontvangen.

Het verminderen van de maximale leeftijd dwingt gebruikers om vaker te verifiëren. Omdat éénstapsverificatie als minder veilig wordt beschouwd dan meervoudige verificatie, raden we u aan deze eigenschap in te stellen op een waarde die gelijk is aan of kleiner is dan de eigenschap Multi-Factor Refresh Token Max Age.

### <a name="multi-factor-refresh-token-max-age"></a>Multi-Factor Refresh Token Max Age
**Tekenreeks:** MaxageMultifactor

**Beïnvloedt:** Tokens vernieuwen

**Samenvatting:** Met dit beleid bepaalt u hoe lang een gebruiker een vernieuwingstoken kan gebruiken om een nieuw tokenpaar voor toegang/vernieuwing te krijgen nadat deze voor het laatst met meerdere factoren is geverifieerd. Nadat een gebruiker een nieuw vernieuwingstoken heeft geverifieerd en ontvangen, kan de gebruiker de vernieuwingstokenstroom voor de opgegeven periode gebruiken. (Dit geldt zolang het huidige vernieuwingstoken niet wordt ingetrokken en het niet langer dan de inactieve tijd ongebruikt is.) Op dat moment worden gebruikers gedwongen om opnieuw te verifiëren om een nieuw vernieuwingstoken te ontvangen.

Het verminderen van de maximale leeftijd dwingt gebruikers om vaker te verifiëren. Omdat éénstapsverificatie als minder veilig wordt beschouwd dan meervoudige verificatie, raden we u aan deze eigenschap in te stellen op een waarde die gelijk is aan of groter is dan de eigenschap Single-Factor Refresh Token Max Age.

### <a name="single-factor-session-token-max-age"></a>Single-Factor Session Token Max Leeftijd
**Tekenreeks:** MaxagesessionSingleFactor MaxagesessionSingleFactor

**Beïnvloedt:** Sessietokens (persistent en niet-persistent)

**Samenvatting:** Met dit beleid bepaalt u hoe lang een gebruiker een sessietoken kan gebruiken om een nieuwe id en sessietoken te krijgen nadat deze voor het laatst met succes is geverifieerd met slechts één factor. Nadat een gebruiker een nieuw sessietoken heeft geverifieerd en ontvangen, kan de gebruiker de sessietokenstroom voor de opgegeven periode gebruiken. (Dit geldt zolang het huidige sessietoken niet wordt ingetrokken en niet is verlopen.) Na de opgegeven periode wordt de gebruiker gedwongen zich opnieuw te authenticeren om een nieuw sessietoken te ontvangen.

Het verminderen van de maximale leeftijd dwingt gebruikers om vaker te verifiëren. Omdat éénstapsverificatie als minder veilig wordt beschouwd dan meervoudige verificatie, raden we u aan deze eigenschap in te stellen op een waarde die gelijk is aan of minder is dan de eigenschap Multi-Factor Session Token Max Age.

### <a name="multi-factor-session-token-max-age"></a>Multi-Factor Session Token Max Leeftijd
**Tekenreeks:** MaxagesessionMultiFactor

**Beïnvloedt:** Sessietokens (persistent en niet-persistent)

**Samenvatting:** Met dit beleid bepaalt u hoe lang een gebruiker een sessietoken kan gebruiken om een nieuwe id en sessietoken te krijgen nadat deze voor het laatst met meerdere factoren is geverifieerd. Nadat een gebruiker een nieuw sessietoken heeft geverifieerd en ontvangen, kan de gebruiker de sessietokenstroom voor de opgegeven periode gebruiken. (Dit geldt zolang het huidige sessietoken niet wordt ingetrokken en niet is verlopen.) Na de opgegeven periode wordt de gebruiker gedwongen zich opnieuw te authenticeren om een nieuw sessietoken te ontvangen.

Het verminderen van de maximale leeftijd dwingt gebruikers om vaker te verifiëren. Omdat éénstapsverificatie als minder veilig wordt beschouwd dan meervoudige verificatie, raden we u aan deze eigenschap in te stellen op een waarde die gelijk is aan of groter is dan de eigenschap Single-Factor Session Token Max Age.

## <a name="example-token-lifetime-policies"></a>Voorbeeld beleid voor tokenlevensduur
Veel scenario's zijn mogelijk in Azure AD wanneer u tokenlevensduur maken en beheren voor apps, serviceprincipals en uw algemene organisatie. In deze sectie doorlopen we een aantal gemeenschappelijke beleidsscenario's die u kunnen helpen nieuwe regels op te leggen voor:

* Levensduur van token
* Token Max Inactieve tijd
* Token Max-leeftijd

In de voorbeelden u leren hoe u:

* Het standaardbeleid van een organisatie beheren
* Een beleid maken voor webaanmelding
* Een beleid maken voor een native app die een web-API aanroept
* Een geavanceerd beleid beheren

### <a name="prerequisites"></a>Vereisten
In de volgende voorbeelden maakt, updatet, linkt en verwijdert u beleidsregels voor apps, serviceprincipals en uw algemene organisatie. Als u nieuw bent in Azure AD, raden we u aan meer te weten te komen over [het aanschaffen van een Azure AD-tenant](quickstart-create-new-tenant.md) voordat u verdergaat met deze voorbeelden.  

Ga als volgt te werk om aan de slag te gaan:

1. Download de nieuwste [Azure AD PowerShell Module Public Preview-release](https://www.powershellgallery.com/packages/AzureADPreview).
2. Voer `Connect` de opdracht uit om u aan te melden bij uw Azure AD-beheerdersaccount. Voer deze opdracht uit telkens wanneer u een nieuwe sessie start.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Voer de volgende opdracht uit om alle beleidsregels te bekijken die in uw organisatie zijn gemaakt. Voer deze opdracht uit na de meeste bewerkingen in de volgende scenario's. Als u de opdracht uitvoert, krijgt u ook de ** ** van uw beleid.

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Voorbeeld: Het standaardbeleid van een organisatie beheren
In dit voorbeeld maakt u een beleid waarmee uw gebruikers zich minder vaak kunnen aanmelden in uw hele organisatie. Maak hiervoor een tokenlevenslangbeleid voor Single-Factor Refresh Tokens, dat in uw hele organisatie wordt toegepast. Het beleid wordt toegepast op elke toepassing in uw organisatie en op elke serviceprincipal die nog geen beleidsset heeft.

1. Maak een token levenslang beleid.

    1. Stel het single-factor refresh token in op 'tot-ingetrokken'. Het token vervalt pas nadat de toegang is ingetrokken. Maak de volgende beleidsdefinitie:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. Voer de volgende opdracht uit om het beleid te maken:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Voer de volgende opdracht uit om witruimte te verwijderen:

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Voer de volgende opdracht uit om uw nieuwe beleid te bekijken en de **ObjectId**van het beleid te krijgen:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Werk het beleid bij.

    U besluiten dat het eerste beleid dat u in dit voorbeeld instelt niet zo streng is als uw service vereist. Voer de volgende opdracht uit om te bepalen dat het single-factor refresh token binnen twee dagen verloopt:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Voorbeeld: Een beleid maken voor webaanmelding

In dit voorbeeld maakt u een beleid waarmee gebruikers vaker moeten verifiëren in uw web-app. Dit beleid stelt de levensduur van de access/ID-tokens en de maximale leeftijd van een multi-factor sessietoken in op de serviceprincipal van uw web-app.

1. Maak een token levenslang beleid.

    Dit beleid voor webaanmelding stelt de levensduur van het access/ID-token en de maximale single-factor sessietokenleeftijd in op twee uur.

    1. Voer de opdracht uit om het beleid te maken:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Voer de volgende opdracht uit om uw nieuwe beleid te bekijken en de **beleidsobjectid**op te halen:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Wijs het beleid toe aan uw serviceprincipal. U moet ook de **ObjectId** van uw serviceprincipal krijgen.

    1. Gebruik de cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) om alle serviceprincipals of één serviceprincipal van uw organisatie te bekijken.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. Wanneer u de serviceprincipal hebt, voert u de volgende opdracht uit:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Voorbeeld: Een beleid maken voor een native app die een web-API aanroept
In dit voorbeeld maakt u een beleid waarmee gebruikers minder vaak moeten verifiëren. Het beleid verlengt ook de tijd dat een gebruiker inactief kan zijn voordat de gebruiker opnieuw moet verifiëren. Het beleid wordt toegepast op de web-API. Wanneer de native app de web-API als bron aanvraagt, wordt dit beleid toegepast.

1. Maak een token levenslang beleid.

    1. Voer de volgende opdracht uit om een strikt beleid voor een web-API te maken:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Voer de volgende opdracht uit om uw nieuwe beleid te bekijken:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Wijs het beleid toe aan uw web-API. U moet ook de **ObjectId** van uw toepassing krijgen. Gebruik de cmdlet [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) om de **ObjectId**van uw app te vinden of de [Azure-portal](https://portal.azure.com/)te gebruiken.

    Haal de **objectid** van uw app op en wijs het beleid toe:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>Voorbeeld: Een geavanceerd beleid beheren
In dit voorbeeld maakt u een aantal beleidsregels om te leren hoe het prioriteitssysteem werkt. U leert ook hoe u meerdere beleidsregels beheert die op meerdere objecten worden toegepast.

1. Maak een token levenslang beleid.

    1. Voer de volgende opdracht uit om een standaardbeleid voor organisaties te maken dat de levensduur van single-factor Refresh Token instelt op 30 dagen:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Voer de volgende opdracht uit om uw nieuwe beleid te bekijken:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Wijs het beleid toe aan een serviceprincipal.

    Nu heb je een beleid dat van toepassing is op de hele organisatie. U dit beleid van 30 dagen behouden voor een specifieke serviceprincipal, maar het standaardbeleid van de organisatie wijzigen in de bovengrens van 'tot ingetrokken'.

    1. Als u alle serviceprincipals van uw organisatie wilt bekijken, gebruikt u de cmdlet [Get-AzureADServicePrincipal.](/powershell/module/azuread/get-azureadserviceprincipal)

    1. Wanneer u de serviceprincipal hebt, voert u de volgende opdracht uit:

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. Stel `IsOrganizationDefault` de vlag in op false:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Een nieuw standaardbeleid voor de organisatie maken:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    U hebt nu het oorspronkelijke beleid gekoppeld aan uw serviceprincipal en het nieuwe beleid is ingesteld als standaardbeleid voor uw organisatie. Het is belangrijk om te onthouden dat beleid dat wordt toegepast op serviceprincipals voorrang heeft op het standaardbeleid van de organisatie.

## <a name="cmdlet-reference"></a>Cmdlet-naslaginformatie

### <a name="manage-policies"></a>Beleid beheren

U de volgende cmdlets gebruiken om beleid te beheren.

#### <a name="new-azureadpolicy"></a>Nieuw AzureAD-beleid

Hiermee maakt u een nieuw beleid.

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parameters | Beschrijving | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Array van gestringificeerde JSON die alle regels van het beleid bevat. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Tekenreeks van de beleidsnaam. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Als dit waar is, stelt u het beleid in als het standaardbeleid van de organisatie. Als vals, doet niets. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Type beleid. Gebruik voor tokenlevensduur altijd 'TokenLifetimePolicy'. | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Optioneel] |Hiermee stelt u een alternatieve id in voor het beleid. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Hier worden alle Azure AD-beleidsregels of een opgegeven beleid weergegeven.

```powershell
Get-AzureADPolicy
```

| Parameters | Beschrijving | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Id</code>[Optioneel] |**ObjectId (ID)** van het gewenste beleid. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Hier worden alle apps en serviceprincipals opgebruikt die zijn gekoppeld aan een beleid.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parameters | Beschrijving | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** van het gewenste beleid. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>AzureAD-beleid instellen
Werkt een bestaand beleid bij.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parameters | Beschrijving | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** van het gewenste beleid. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Tekenreeks van de beleidsnaam. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code>[Optioneel] |Array van gestringificeerde JSON die alle regels van het beleid bevat. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code>[Optioneel] |Als dit waar is, stelt u het beleid in als het standaardbeleid van de organisatie. Als vals, doet niets. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code>[Optioneel] |Type beleid. Gebruik voor tokenlevensduur altijd 'TokenLifetimePolicy'. |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Optioneel] |Hiermee stelt u een alternatieve id in voor het beleid. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>AzureADPolicy verwijderen
Hiermee verwijdert u het opgegeven beleid.

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parameters | Beschrijving | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** van het gewenste beleid. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Toepassingsbeleid
U de volgende cmdlets gebruiken voor toepassingsbeleid.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Koppelt het opgegeven beleid aan een toepassing.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parameters | Beschrijving | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** van de toepassing. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** van het beleid. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureAD-toepassingsbeleid
Krijgt het beleid dat is toegewezen aan een toepassing.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parameters | Beschrijving | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** van de toepassing. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>AzureADApplicationPolicy verwijderen
Hiermee verwijdert u een beleid uit een toepassing.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parameters | Beschrijving | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** van de toepassing. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** van het beleid. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Serviceprincipal-beleid
U de volgende cmdlets gebruiken voor serviceprincipal-beleid.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Koppelt het opgegeven beleid aan een serviceprincipal.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parameters | Beschrijving | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** van de toepassing. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** van het beleid. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Hier wordt elk beleid gekoppeld aan de opgegeven serviceprincipal.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parameters | Beschrijving | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** van de toepassing. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Beleid voor verwijderen-AzureADServicePrincipal
Hiermee verwijdert u het beleid van de opgegeven serviceprincipal.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parameters | Beschrijving | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** van de toepassing. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** van het beleid. | `-PolicyId <ObjectId of Policy>` |
