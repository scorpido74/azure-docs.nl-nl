---
title: Configureer bare levens duur van tokens in Azure Active Directory | Microsoft Docs
description: Meer informatie over het instellen van de levens duur voor tokens die zijn uitgegeven door Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: ryanwi
ms.custom: aaddev, annaba, identityplatformtop40
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9244dfabef8b13105ef830f9f4543da9cb2cca9
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842648"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Configureer bare levens duur van tokens in Azure Active Directory (preview-versie)

U kunt de levens duur opgeven van een token dat is uitgegeven door Azure Active Directory (Azure AD). U kunt de levens duur van tokens instellen voor alle apps in uw organisatie, voor een multi tenant-toepassing (meerdere organisaties) of voor een specifieke Service-Principal in uw organisatie.

> [!IMPORTANT]
> Nadat klanten tijdens de preview-periode hebben gehoord, hebben we [verificatie sessie beheer functies](https://go.microsoft.com/fwlink/?linkid=2083106) geïmplementeerd in voorwaardelijke toegang van Azure AD. Met deze nieuwe functie kunt u de levens duur van het vernieuwings token configureren door de aanmeldings frequentie in te stellen. Na 1 november 2019 kunt u het beleid voor de levens duur van de tokens niet meer gebruiken voor het configureren van vernieuwings tokens, maar dit kan nog wel worden gebruikt om toegangs tokens te configureren.

In azure AD vertegenwoordigt een beleids object een set regels die worden afgedwongen voor afzonderlijke toepassingen of voor alle toepassingen in een organisatie. Elk beleids type heeft een unieke structuur, met een reeks eigenschappen die worden toegepast op objecten waaraan ze zijn toegewezen.

U kunt een beleid instellen als het standaard beleid voor uw organisatie. Het beleid wordt toegepast op alle toepassingen in de organisatie, zolang deze niet worden overschreven door een beleid met een hogere prioriteit. U kunt ook een beleid toewijzen aan specifieke toepassingen. De volg orde van prioriteit is afhankelijk van het beleids type.

> [!NOTE]
> Het beleid voor de levens duur van Configureer bare tokens wordt niet ondersteund voor share point online.  Hoewel u de mogelijkheid hebt om dit beleid via Power shell te maken, wordt dit beleid niet door share point online bevestigd. Raadpleeg de [share point online-blog](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) voor meer informatie over het configureren van time-outs voor inactieve sessies.
>* De standaard levensduur voor het share point online-toegangs token is 1 uur. 
>* De standaard waarde voor inactieve tijd van het share point online-vernieuwings token is 90 dagen.


## <a name="token-types"></a>Token typen

U kunt beleid voor levens duur van tokens instellen voor vernieuwings tokens, toegangs tokens, sessie tokens en ID-tokens.

### <a name="access-tokens"></a>Toegangstokens

Clients gebruiken toegangs tokens om toegang te krijgen tot een beveiligde bron. Een toegangs token kan alleen worden gebruikt voor een specifieke combi natie van gebruiker, client en resource. Toegangs tokens kunnen niet worden ingetrokken en zijn geldig tot de verval datum. Een kwaadwillende actor die een toegangs token heeft verkregen, kan deze gebruiken voor een omvang van de levens duur. Het aanpassen van de levens duur van een toegangs token is een afweging tussen het verbeteren van de systeem prestaties en het verg Roten van de hoeveelheid tijd die de client toegang behoudt nadat het gebruikers account is uitgeschakeld. De systeem prestaties zijn verbeterd door het aantal keren dat een client een nieuw toegangs token moet verkrijgen te verminderen.  De standaard waarde is 1 uur-na 1 uur moet de client het vernieuwings token gebruiken om (meestal op de achtergrond) een nieuw vernieuwings token en toegangs token te verkrijgen. 

### <a name="refresh-tokens"></a>Tokens vernieuwen

Wanneer een client een toegangs token verkrijgt om toegang te krijgen tot een beveiligde bron, ontvangt de client ook een vernieuwings token. Het vernieuwings token wordt gebruikt om nieuwe token paren voor toegang/vernieuwing op te halen wanneer het huidige toegangs token verloopt. Een vernieuwings token is gebonden aan een combi natie van gebruiker en client. Een vernieuwings token kan [op elk gewenst moment worden ingetrokken](access-tokens.md#token-revocation)en de geldigheid van het token wordt gecontroleerd elke keer dat het token wordt gebruikt.  Vernieuwings tokens worden niet ingetrokken wanneer deze worden gebruikt om nieuwe toegangs tokens op te halen. het is best practice echter om het oude token veilig te verwijderen wanneer er een nieuwe wordt opgehaald. 

Het is belang rijk om onderscheid te maken tussen vertrouwelijke clients en open bare clients, omdat dit van invloed is op hoe lang vernieuwings tokens kunnen worden gebruikt. Zie [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1)voor meer informatie over verschillende typen clients.

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Levens duur van tokens met vertrouwelijke client vernieuwings tokens
Vertrouwelijke clients zijn toepassingen die een client wachtwoord (geheim) veilig kunnen opslaan. Ze kunnen bewijzen dat aanvragen afkomstig zijn van de beveiligde client toepassing en niet van een schadelijke actor. Een web-app is bijvoorbeeld een vertrouwelijke client omdat hiermee een client geheim kan worden opgeslagen op de webserver. Het wordt niet weer gegeven. Omdat deze stromen veiliger zijn, kunnen de standaard levensduur van vernieuwings tokens die worden uitgegeven aan `until-revoked`deze stromen, niet worden gewijzigd met behulp van beleid en worden ze niet ingetrokken voor het opnieuw instellen van het wacht woord.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Levens duur van tokens met open bare client vernieuwings tokens

Open bare clients kunnen een client wachtwoord (geheim) niet veilig opslaan. Een iOS/Android-app kan bijvoorbeeld geen geheim van de resource-eigenaar maken, dus wordt het beschouwd als een open bare client. U kunt beleids regels instellen op resources om te voor komen dat de vernieuwings tokens van open bare clients ouder dan een opgegeven periode een nieuw token paar voor toegang/vernieuwing verkrijgen. (Als u dit wilt doen, gebruikt u de eigenschap voor het vernieuwen van`MaxInactiveTime`het token voor de maximale inactieve tijd ().) U kunt ook beleid gebruiken om een periode in te stellen waarboven de vernieuwings tokens niet langer worden geaccepteerd. (Als u dit wilt doen, gebruikt u de eigenschap maximum leeftijd van het token vernieuwen.) U kunt de levens duur van een vernieuwings token aanpassen om te bepalen wanneer en hoe vaak de gebruiker de referenties opnieuw moet invoeren, in plaats van dat deze wordt geauthenticeerd wanneer een open bare client toepassing wordt gebruikt.

### <a name="id-tokens"></a>Id-tokens
ID-tokens worden door gegeven aan websites en native clients. ID-tokens bevatten profiel informatie over een gebruiker. Een ID-token is gebonden aan een specifieke combi natie van gebruiker en client. ID-tokens worden beschouwd als geldig tot de verval datum. Normaal gesp roken komt een webtoepassing overeen met de levens duur van de sessie van een gebruiker in de toepassing tot de levens duur van het ID-token dat voor de gebruiker is uitgegeven. U kunt de levens duur van een ID-token aanpassen om te bepalen hoe vaak de webtoepassing de toepassings sessie verloopt en hoe vaak de gebruiker opnieuw moet worden geverifieerd met Azure AD (op de achtergrond of interactief).

### <a name="single-sign-on-session-tokens"></a>Sessie tokens voor eenmalige aanmelding
Wanneer een gebruiker wordt geverifieerd bij Azure AD, wordt een eenmalige aanmelding (SSO) tot stand gebracht met de browser van de gebruiker en Azure AD. De SSO-token, in de vorm van een cookie, vertegenwoordigt deze sessie. Het SSO-sessie token is niet gebonden aan een specifieke bron/client toepassing. SSO-sessie tokens kunnen worden ingetrokken en de geldigheid ervan wordt gecontroleerd elke keer dat ze worden gebruikt.

In azure AD worden twee soorten SSO-sessie tokens gebruikt: permanent en niet-persistent. Permanente sessie tokens worden opgeslagen als permanente cookies door de browser. Niet-permanente sessie tokens worden opgeslagen als sessie cookies. (Sessie cookies worden vernietigd wanneer de browser wordt gesloten.) Normaal gesp roken wordt een niet-persistent sessie token opgeslagen. Maar wanneer de gebruiker het selectie vakje **aangemeld blijven** tijdens de verificatie inschakelt, wordt een persistent sessie token opgeslagen.

Niet-permanente sessie tokens hebben een levens duur van 24 uur. Permanente tokens hebben een levens duur van 180 dagen. Wanneer een SSO-sessie token wordt gebruikt binnen de geldigheids periode, wordt de geldigheids periode nog eens 24 uur of 180 dagen verlengd, afhankelijk van het type token. Als een SSO-sessie token niet binnen de geldigheids periode wordt gebruikt, wordt dit beschouwd als verlopen en wordt het niet langer geaccepteerd.

U kunt een beleid gebruiken om de tijd in te stellen nadat het eerste sessie token is uitgegeven, waarboven het sessie token niet meer wordt geaccepteerd. (Hiervoor gebruikt u de eigenschap maximum leeftijd van sessie token.) U kunt de levens duur van een sessie token aanpassen om te bepalen wanneer en hoe vaak een gebruiker verplicht is om referenties opnieuw in te voeren, in plaats van op de achtergrond te worden geverifieerd wanneer een webtoepassing wordt gebruikt.

### <a name="token-lifetime-policy-properties"></a>Beleids eigenschappen levens duur token
Beleid voor levens duur van tokens is een type beleids object dat de levens duur van tokens bevat. Gebruik de eigenschappen van het beleid om de opgegeven levens duur van het token te beheren. Als er geen beleid is ingesteld, wordt de standaard levensduur waarde afgedwongen.

### <a name="configurable-token-lifetime-properties"></a>Eigenschappen van Configureer bare token levensduur
| Eigenschap | Teken reeks eigenschap van beleid | Alleen | Standaard | Minimum | Maximum |
| --- | --- | --- | --- | --- | --- |
| Levens duur van toegangs token |AccessTokenLifetime<sup>2</sup> |Toegangs tokens, ID-tokens, SAML2-tokens |1 uur |10 minuten |1 dag |
| Maximum aantal inactieve tijd voor het vernieuwen van token |MaxInactiveTime |Tokens vernieuwen |90 dagen |10 minuten |90 dagen |
| Maximum leeftijd van het token voor eenmalige vernieuwing |MaxAgeSingleFactor |Tokens vernieuwen (voor alle gebruikers) |Until-revoked |10 minuten |Until-revoked<sup>1</sup> |
| Maximum leeftijd van multi-factor Refresh-token |MaxAgeMultiFactor |Tokens vernieuwen (voor alle gebruikers) |Until-revoked |10 minuten |Until-revoked<sup>1</sup> |
| Maximum leeftijd van het token voor één factor-sessie |MaxAgeSessionSingleFactor |Sessie tokens (permanent en niet permanent) |Until-revoked |10 minuten |Until-revoked<sup>1</sup> |
| Maximale leeftijds duur multi-factor Session-token |MaxAgeSessionMultiFactor |Sessie tokens (permanent en niet permanent) |Until-revoked |10 minuten |Until-revoked<sup>1</sup> |

* <sup>1</sup>365 dagen is de maximale expliciete lengte die voor deze kenmerken kan worden ingesteld.
* <sup>2</sup> Als u de micro soft teams-webclient wilt gebruiken, is het raadzaam om AccessTokenLifetime in te stellen op meer dan 15 minuten voor de micro soft-teams.

### <a name="exceptions"></a>Uitzonderingen
| Eigenschap | Alleen | Standaard |
| --- | --- | --- |
| De maximale leeftijd van het vernieuwings token (uitgegeven voor federatieve gebruikers met onvoldoende intrekkings gegevens<sup>1</sup>) |Tokens vernieuwen (uitgegeven voor federatieve gebruikers met onvoldoende intrekkings gegevens<sup>1</sup>) |12 uur |
| De maximale inactieve tijd voor het vernieuwen van het token (uitgegeven voor vertrouwelijke clients) |Tokens vernieuwen (uitgegeven voor vertrouwelijke clients) |90 dagen |
| Maximale leeftijd van het vernieuwings token (uitgegeven voor vertrouwelijke clients) |Tokens vernieuwen (uitgegeven voor vertrouwelijke clients) |Until-revoked |

* <sup>1</sup> Federatieve gebruikers die onvoldoende intrekkings gegevens hebben, zijn gebruikers die het kenmerk ' LastPasswordChangeTimestamp ' niet hebben gesynchroniseerd. Deze gebruikers krijgen dit korte maximum leeftijd omdat AAD niet kan verifiëren wanneer tokens worden ingetrokken die zijn gekoppeld aan een oude referentie (zoals een wacht woord dat is gewijzigd) en om ervoor te zorgen dat de gebruiker en de bijbehorende tokens nog steeds goed zijn  Draag. Om deze ervaring te verbeteren, moeten Tenant beheerders ervoor zorgen dat ze het kenmerk ' LastPasswordChangeTimestamp ' synchroniseren (dit kan worden ingesteld voor het gebruikers object met behulp van Power shell of via AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Beleids evaluatie en prioriteits aanduiding
U kunt een token levensduur beleid maken en vervolgens toewijzen aan een specifieke toepassing, aan uw organisatie en aan service-principals. Meerdere beleids regels zijn mogelijk van toepassing op een specifieke toepassing. De levens duur van het token beleid dat van kracht is volgt deze regels:

* Als een beleid expliciet is toegewezen aan de Service-Principal, wordt dit afgedwongen.
* Als er geen beleid expliciet is toegewezen aan de Service-Principal, wordt een beleid afgedwongen dat expliciet is toegewezen aan de bovenliggende organisatie van de Service-Principal.
* Als er geen beleid expliciet is toegewezen aan de service-principal of aan de organisatie, wordt het beleid dat aan de toepassing is toegewezen, afgedwongen.
* Als er geen beleid is toegewezen aan de Service-Principal, de organisatie of het toepassings object, worden de standaard waarden afgedwongen. (Zie de tabel in [configuratie van levens duur van Configureer bare tokens](#configurable-token-lifetime-properties).)

Zie [Application and Service Principal Objects in azure Active Directory](app-objects-and-service-principals.md)voor meer informatie over de relatie tussen toepassings objecten en Service-Principal-objecten.

De geldigheid van een token wordt geëvalueerd op het moment dat het token wordt gebruikt. Het beleid met de hoogste prioriteit voor de toepassing die wordt geopend, treedt in werking.

Alle TimeSpans die hier worden gebruikt, zijn ingedeeld C# op basis van het [span](/dotnet/api/system.timespan) -object-D. uu: mm: SS.  80 dagen en 30 minuten zouden zijn `80.00:30:00`.  De voorloop D kan worden verwijderd als de waarde nul is, dus 90 `00:90:00`minuten.  

> [!NOTE]
> Hier volgt een voorbeeld scenario.
>
> Een gebruiker wil toegang tot twee webtoepassingen: Webtoepassing A en Web Application B.
> 
> Elementen
> * Beide webtoepassingen bevinden zich in dezelfde bovenliggende organisatie.
> * Het token levensduur beleid 1 met een sessie token van Maxi maal acht uur is ingesteld als de standaard waarde van de bovenliggende organisatie.
> * Webtoepassing A is een reguliere webtoepassing en is niet gekoppeld aan een beleid.
> * Web Application B wordt gebruikt voor zeer gevoelige processen. De Service-Principal is gekoppeld aan het token levensduur beleid 2, dat een sessie token heeft van een maximum leeftijd van 30 minuten.
>
> Om 12:00 uur wordt een nieuwe browser sessie gestart en wordt geprobeerd toegang te krijgen tot webtoepassing A. De gebruiker wordt omgeleid naar Azure AD en wordt gevraagd zich aan te melden. Hiermee maakt u een cookie met een sessie token in de browser. De gebruiker wordt teruggeleid naar de webtoepassing A met een ID-token waarmee de gebruiker toegang kan krijgen tot de toepassing.
>
> Om 12:15 uur, probeert de gebruiker toegang tot Web Application B te krijgen. De browser wordt omgeleid naar Azure AD, waardoor de sessie cookie wordt gedetecteerd. De service-principal van Web Application B is gekoppeld aan het token levensduur beleid 2, maar maakt ook deel uit van de bovenliggende organisatie, met het standaard token levensduur beleid 1. Het token levensduur beleid 2 treedt in werking omdat het beleid dat is gekoppeld aan service-principals een hogere prioriteit heeft dan het standaard beleid van de organisatie. Het sessie token is oorspronkelijk in de afgelopen 30 minuten uitgegeven. dit wordt dus als geldig beschouwd. De gebruiker wordt teruggeleid naar Web Application B met een ID-token waarmee ze toegang krijgen.
>
> Bij 1:00 uur probeert de gebruiker webtoepassing A te openen. De gebruiker wordt omgeleid naar Azure AD. Webtoepassing A is niet gekoppeld aan een beleid, maar omdat het zich in een organisatie met een standaard token levensduur beleid 1 bevindt, wordt dat beleid van kracht. De sessie cookie die oorspronkelijk in de afgelopen acht uur is uitgegeven, wordt gedetecteerd. De gebruiker wordt op de achtergrond teruggeleid naar de webtoepassing A met een nieuw ID-token. De gebruiker is niet verplicht om te verifiëren.
>
> Vervolgens probeert de gebruiker toegang tot Web Application B te krijgen. De gebruiker wordt omgeleid naar Azure AD. Net als voorheen heeft het token levensduur beleid 2 van kracht. Omdat het token meer dan 30 minuten geleden is uitgegeven, wordt de gebruiker gevraagd om de aanmeldings referenties opnieuw in te voeren. Er worden een merk-nieuwe sessie token en ID-token uitgegeven. De gebruiker kan vervolgens toegang tot Web Application B krijgen.
>
>

## <a name="configurable-policy-property-details"></a>Details van Configureer bare beleids eigenschap
### <a name="access-token-lifetime"></a>Levens duur van toegangs token
**Tekenreeksexpressie** AccessTokenLifetime

**Alleen** Toegangs tokens, ID-tokens

**Overzicht** Dit beleid bepaalt hoe lang de toegangs-en ID-tokens voor deze bron worden beschouwd als geldig. Het verminderen van de eigenschap levens duur van het toegangs token vermindert het risico dat een toegangs token of ID-token gedurende lange tijd wordt gebruikt door een schadelijke actor. (Deze tokens kunnen niet worden ingetrokken.) De afweging is dat de prestaties nadelig worden beïnvloed, omdat de tokens vaker moeten worden vervangen.

### <a name="refresh-token-max-inactive-time"></a>Maximum aantal inactieve tijd voor het vernieuwen van token
**Tekenreeksexpressie** MaxInactiveTime

**Alleen** Tokens vernieuwen

**Overzicht** Dit beleid bepaalt hoe oud een vernieuwings token kan zijn voordat een client deze niet meer kan gebruiken om een nieuw toegangs-en vernieuwings token paar op te halen wanneer er wordt geprobeerd toegang te krijgen tot deze bron. Omdat een nieuw vernieuwings token meestal wordt geretourneerd wanneer een vernieuwings token wordt gebruikt, voor komt dit beleid dat toegang wordt verkregen als de client toegang tot een bron probeert te krijgen met behulp van het huidige vernieuwings token tijdens de opgegeven periode.

Dit beleid dwingt gebruikers die niet actief zijn geweest op hun client om opnieuw te verifiëren om een nieuw vernieuwings token op te halen.

De eigenschap Max. voor het vernieuwings token van de inactieve tijd moet worden ingesteld op een lagere waarde dan de maximale leeftijd van het token met één factor en de maximale leeftijds eigenschappen voor multi-factor Refresh-tokens.

### <a name="single-factor-refresh-token-max-age"></a>Maximum leeftijd van het token voor eenmalige vernieuwing
**Tekenreeksexpressie** MaxAgeSingleFactor

**Alleen** Tokens vernieuwen

**Overzicht** Dit beleid bepaalt hoe lang een gebruiker een vernieuwings token kan gebruiken om een nieuw toegangs-en vernieuwings token paar te verkrijgen nadat het voor het laatst is geverifieerd met behulp van slechts één factor. Nadat een gebruiker een nieuw vernieuwings token heeft geverifieerd en ontvangen, kan de gebruiker de stroom voor het vernieuwen van tokens gebruiken voor de opgegeven periode. (Dit geldt zo lang het huidige vernieuwings token niet is ingetrokken en niet langer dan de inactieve tijd niet wordt gebruikt.) Op dat moment wordt de gebruiker gedwongen opnieuw te verifiëren om een nieuw vernieuwings token te ontvangen.

Het verminderen van de maximale leeftijd dwingt gebruikers vaker te verifiëren. Omdat verificatie met één factor minder veilig wordt beschouwd dan multi-factor Authentication, raden we u aan deze eigenschap in te stellen op een waarde die gelijk is aan of kleiner is dan de maximale leeftijds eigenschap van de multi-factor Refresh-token.

### <a name="multi-factor-refresh-token-max-age"></a>Maximum leeftijd van multi-factor Refresh-token
**Tekenreeksexpressie** MaxAgeMultiFactor

**Alleen** Tokens vernieuwen

**Overzicht** Dit beleid bepaalt hoe lang een gebruiker een vernieuwings token kan gebruiken om een nieuw toegangs-en vernieuwings token paar te verkrijgen nadat het voor het laatst is geverifieerd met behulp van meerdere factoren. Nadat een gebruiker een nieuw vernieuwings token heeft geverifieerd en ontvangen, kan de gebruiker de stroom voor het vernieuwen van tokens gebruiken voor de opgegeven periode. (Dit geldt zo lang het huidige vernieuwings token niet is ingetrokken en niet langer dan de inactieve tijd niet wordt gebruikt.) Op dat moment worden gebruikers gedwongen opnieuw te verifiëren om een nieuw vernieuwings token te ontvangen.

Het verminderen van de maximale leeftijd dwingt gebruikers vaker te verifiëren. Omdat verificatie met één factor minder veilig wordt beschouwd dan multi-factor Authentication, raden we u aan deze eigenschap in te stellen op een waarde die gelijk is aan of groter is dan de eigenschap Max Age van het token voor het vernieuwen van één factor.

### <a name="single-factor-session-token-max-age"></a>Maximum leeftijd van het token voor één factor-sessie
**Tekenreeksexpressie** MaxAgeSessionSingleFactor

**Alleen** Sessie tokens (permanent en niet permanent)

**Overzicht** Dit beleid bepaalt hoe lang een gebruiker een sessie token kan gebruiken om een nieuw ID-en sessie token te verkrijgen nadat deze de laatste keer is geverifieerd met behulp van slechts één factor. Nadat een gebruiker een nieuw sessie token heeft geverifieerd en ontvangen, kan de gebruiker de sessie token stroom voor de opgegeven periode gebruiken. (Dit geldt zo lang het token van de huidige sessie niet is ingetrokken en niet is verlopen.) Na de opgegeven periode moet de gebruiker opnieuw worden geverifieerd om een nieuw sessie token te ontvangen.

Het verminderen van de maximale leeftijd dwingt gebruikers vaker te verifiëren. Omdat verificatie met één factor minder veilig wordt beschouwd dan multi-factor Authentication, raden we u aan deze eigenschap in te stellen op een waarde die gelijk is aan of kleiner is dan de maximale leeftijds eigenschap voor het multi-factor-sessie token.

### <a name="multi-factor-session-token-max-age"></a>Maximale leeftijds duur multi-factor Session-token
**Tekenreeksexpressie** MaxAgeSessionMultiFactor

**Alleen** Sessie tokens (permanent en niet permanent)

**Overzicht** Dit beleid bepaalt hoe lang een gebruiker met behulp van meerdere factoren een sessie token kan gebruiken om een nieuw ID-en sessie token te verkrijgen. Nadat een gebruiker een nieuw sessie token heeft geverifieerd en ontvangen, kan de gebruiker de sessie token stroom voor de opgegeven periode gebruiken. (Dit geldt zo lang het token van de huidige sessie niet is ingetrokken en niet is verlopen.) Na de opgegeven periode moet de gebruiker opnieuw worden geverifieerd om een nieuw sessie token te ontvangen.

Het verminderen van de maximale leeftijd dwingt gebruikers vaker te verifiëren. Omdat verificatie met één factor minder veilig wordt beschouwd dan multi-factor Authentication, raden we u aan deze eigenschap in te stellen op een waarde die gelijk is aan of groter is dan de eigenschap Max Age van het token met één factor-sessie.

## <a name="example-token-lifetime-policies"></a>Voor beeld van levens duur beleid token
Er zijn veel scenario's mogelijk in azure AD wanneer u de levens duur van tokens voor apps, service-principals en uw hele organisatie kunt maken en beheren. In deze sectie laten we een paar algemene beleids scenario's door lopen waarmee u nieuwe regels kunt opstellen voor:

* Levensduur van token
* Maximale inactieve tijd van token
* Maximale leeftijd van token

In de voor beelden vindt u informatie over:

* Het standaard beleid van een organisatie beheren
* Een beleid maken voor aanmelden via het web
* Een beleid maken voor een systeem eigen app die een web-API aanroept
* Een geavanceerd beleid beheren

### <a name="prerequisites"></a>Vereisten
In de volgende voor beelden maakt, bijwerkt, koppelt en verwijdert u beleid voor apps, service-principals en uw hele organisatie. Als u geen ervaring hebt met Azure AD, raden we u aan meer te weten te komen over [het verkrijgen van een Azure AD-Tenant](quickstart-create-new-tenant.md) voordat u verdergaat met deze voor beelden.  

Voer de volgende stappen uit om aan de slag te gaan:

1. Down load de nieuwste [open bare preview-versie van Azure AD Power shell-module](https://www.powershellgallery.com/packages/AzureADPreview).
2. Voer de `Connect` opdracht uit om u aan te melden bij uw Azure AD-beheerders account. Voer deze opdracht telkens uit wanneer u een nieuwe sessie start.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Voer de volgende opdracht uit om alle beleids regels weer te geven die in uw organisatie zijn gemaakt. Voer deze opdracht na de meeste bewerkingen uit in de volgende scenario's. Als u de opdracht uitvoert, kunt u ook de * * * * van uw beleid ophalen.

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Voorbeeld: Het standaard beleid van een organisatie beheren
In dit voor beeld maakt u een beleid waarmee uw gebruikers zich minder vaak kunnen aanmelden in uw hele organisatie. Als u dit wilt doen, maakt u een beleid voor de levens duur van tokens voor het vernieuwen van tokens die in uw organisatie worden toegepast. Het beleid wordt toegepast op elke toepassing in uw organisatie en op elke service-principal waarvoor nog geen beleid is ingesteld.

1. Maak een beleid voor levens duur van tokens.

    1. Stel het token voor het vernieuwen van de enkelvoudige factor in op ' until-ingetrokken '. Het token verloopt niet totdat de toegang is ingetrokken. Maak de volgende beleids definitie:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2. Voer de volgende opdracht uit om het beleid te maken:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3. Voer de volgende opdracht uit om het nieuwe beleid te bekijken en de **ObjectId**van het beleid op te halen:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Werk het beleid bij.

    U kunt besluiten dat het eerste beleid dat u in dit voor beeld hebt ingesteld, niet zo strikt is als uw service vereist. Voer de volgende opdracht uit om in te stellen dat uw token voor het vernieuwen van één factor binnen twee dagen verloopt:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Voorbeeld: Een beleid maken voor aanmelden via het web

In dit voor beeld maakt u een beleid waarmee gebruikers vaker moeten worden geverifieerd in uw web-app. Met dit beleid wordt de levens duur van de toegangs-ID-tokens en de maximale leeftijd van een multi-factor-sessie token ingesteld op de service-principal van uw web-app.

1. Maak een beleid voor levens duur van tokens.

    Dit beleid, voor aanmelding bij het web, stelt de levens duur van het toegangs-en ID-token en de maximale leeftijd van het single-factor sessie token in op twee uur.

    1. Voer de volgende opdracht uit om het beleid te maken:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. Voer de volgende opdracht uit om het nieuwe beleid te bekijken en de beleids- **ObjectId**op te halen:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Wijs het beleid toe aan uw service-principal. U moet ook de **ObjectId** voor uw Service-Principal ophalen.

    1. Gebruik de cmdlet [Get-azureadserviceprincipal namelijk niet](/powershell/module/azuread/get-azureadserviceprincipal) om de service-principals van uw organisatie of een enkele service-principal te bekijken.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    2. Wanneer u de Service-Principal hebt, voert u de volgende opdracht uit:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Voorbeeld: Een beleid maken voor een systeem eigen app die een web-API aanroept
In dit voor beeld maakt u een beleid dat vereist dat gebruikers minder vaak verifiëren. Het beleid verlengt ook de hoeveelheid tijd die een gebruiker inactief mag zijn voordat de gebruiker opnieuw moet worden geverifieerd. Het beleid wordt toegepast op de Web-API. Wanneer de systeem eigen app de Web-API als bron aanvraagt, wordt dit beleid toegepast.

1. Maak een beleid voor levens duur van tokens.

    1. Voer de volgende opdracht uit om een strikt beleid voor een web-API te maken:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. Voer de volgende opdracht uit om het nieuwe beleid weer te geven:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Wijs het beleid toe aan uw web-API. U moet ook de **ObjectId** van uw toepassing ophalen. Gebruik de cmdlet [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) om de **ObjectId**van uw app te vinden, of gebruik de [Azure Portal](https://portal.azure.com/).

    Haal de **ObjectId** van uw app op en wijs het beleid toe:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>Voorbeeld: Een geavanceerd beleid beheren
In dit voor beeld maakt u een paar beleids regels om te leren hoe het prioriteits systeem werkt. U leert ook hoe u meerdere beleids regels beheert die op verschillende objecten worden toegepast.

1. Maak een beleid voor levens duur van tokens.

    1. Voer de volgende opdracht uit om een standaard beleid voor de organisatie te maken waarmee de levens duur van het token voor de vernieuwing van één factor wordt ingesteld op 30 dagen:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2. Voer de volgende opdracht uit om het nieuwe beleid weer te geven:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Wijs het beleid toe aan een service-principal.

    Nu hebt u een beleid dat van toepassing is op de hele organisatie. U kunt dit beleid voor 30 dagen voor een specifieke Service-Principal bewaren, maar u kunt het standaard beleid van de organisatie wijzigen in de bovengrens van ' tot-ingetrokken '.

    1. Als u de service-principals van uw organisatie wilt weer geven, gebruikt u de cmdlet [Get-azureadserviceprincipal namelijk niet](/powershell/module/azuread/get-azureadserviceprincipal) .

    2. Wanneer u de Service-Principal hebt, voert u de volgende opdracht uit:

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

3. Stel de `IsOrganizationDefault` vlag in op False:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. Maak een nieuw standaard beleid voor organisaties:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    U hebt nu het oorspronkelijke beleid gekoppeld aan uw Service-Principal en het nieuwe beleid is ingesteld als standaard beleid voor uw organisatie. Het is belang rijk om te onthouden dat beleids regels die worden toegepast op service-principals prioriteit hebben boven het standaard beleid van de organisatie.

## <a name="cmdlet-reference"></a>Cmdlet-naslaginformatie

### <a name="manage-policies"></a>Beleid beheren

U kunt de volgende cmdlets gebruiken voor het beheren van beleids regels.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Hiermee maakt u een nieuw beleid.

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parameters | Description | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Matrix van stringified JSON die alle regels van het beleid bevat. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |De teken reeks van de beleids naam. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Indien waar, wordt het beleid ingesteld als standaard beleid van de organisatie. Als onwaar is, gebeurt er niets. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Type beleid. Gebruik altijd ' TokenLifetimePolicy ' voor de levens duur van tokens. | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>Beschrijving |Hiermee stelt u een alternatieve ID voor het beleid in. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Hiermee haalt u alle Azure AD-beleids regels of een opgegeven beleid op.

```powershell
Get-AzureADPolicy
```

| Parameters | Description | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Id</code>Beschrijving |**ObjectId (id)** van het beleid dat u wilt. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Hiermee worden alle apps en service-principals opgehaald die zijn gekoppeld aan een beleid.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parameters | Description | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (id)** van het beleid dat u wilt. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Hiermee wordt een bestaand beleid bijgewerkt.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parameters | Description | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (id)** van het beleid dat u wilt. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |De teken reeks van de beleids naam. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code>Beschrijving |Matrix van stringified JSON die alle regels van het beleid bevat. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code>Beschrijving |Indien waar, wordt het beleid ingesteld als standaard beleid van de organisatie. Als onwaar is, gebeurt er niets. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code>Beschrijving |Type beleid. Gebruik altijd ' TokenLifetimePolicy ' voor de levens duur van tokens. |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>Beschrijving |Hiermee stelt u een alternatieve ID voor het beleid in. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Hiermee verwijdert u het opgegeven beleid.

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parameters | Description | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (id)** van het beleid dat u wilt. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Toepassings beleid
U kunt de volgende cmdlets voor toepassings beleid gebruiken.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Hiermee wordt het opgegeven beleid gekoppeld aan een toepassing.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parameters | Description | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (id)** van de toepassing. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** van het beleid. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Hiermee wordt het beleid opgehaald dat is toegewezen aan een toepassing.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parameters | Description | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (id)** van de toepassing. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Hiermee verwijdert u een beleid van een toepassing.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parameters | Description | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (id)** van de toepassing. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** van het beleid. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Service-principal-beleidsregels
U kunt de volgende cmdlets gebruiken voor Service Principal-beleids regels.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Hiermee wordt het opgegeven beleid gekoppeld aan een service-principal.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parameters | Description | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (id)** van de toepassing. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** van het beleid. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Hiermee wordt een beleid opgehaald dat is gekoppeld aan de opgegeven service-principal.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parameters | Description | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (id)** van de toepassing. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Hiermee verwijdert u het beleid van de opgegeven service-principal.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parameters | Description | Voorbeeld |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (id)** van de toepassing. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** van het beleid. | `-PolicyId <ObjectId of Policy>` |
