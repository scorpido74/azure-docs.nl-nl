---
title: Overzicht van tokens - Azure Active Directory B2C
description: Meer informatie over de tokens die worden gebruikt in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cbbd083a6b62733d71c316af95dffaa188b28955
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186485"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Overzicht van tokens in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) zendt verschillende soorten beveiligingstokens uit terwijl elke [verificatiestroom wordt verwerkt.](application-types.md) In dit document worden de indeling, beveiligingskenmerken en inhoud van elk type token beschreven.

## <a name="token-types"></a>Tokentypen

Azure AD B2C ondersteunt de [OAuth 2.0- en OpenID Connect-protocollen](protocols-overview.md), die gebruik maken van tokens voor verificatie en veilige toegang tot bronnen. Alle tokens die worden gebruikt in Azure AD B2C zijn [JSON-webtokens (JWT's)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) die beweringen bevatten over informatie over de drager en het onderwerp van het token.

De volgende tokens worden gebruikt in communicatie met Azure AD B2C:

- *ID-token* - Een JWT die claims bevat die u gebruiken om gebruikers in uw toepassing te identificeren. Dit token wordt veilig verzonden in HTTP-verzoeken voor communicatie tussen twee onderdelen van dezelfde toepassing of service. U de claims naar eigen inzicht gebruiken in een ID-token. Ze worden vaak gebruikt om accountgegevens weer te geven of om toegangscontrolebeslissingen te nemen in een toepassing. ID-tokens zijn ondertekend, maar ze zijn niet versleuteld. Wanneer uw toepassing of API een ID-token ontvangt, moet deze de handtekening valideren om te bewijzen dat het token authentiek is. Uw toepassing of API moet ook een paar claims in het token valideren om te bewijzen dat deze geldig is. Afhankelijk van de scenariovereisten kunnen de claims die door een toepassing zijn gevalideerd, variëren, maar uw toepassing moet in elk scenario een aantal veelvoorkomende claimvalidaties uitvoeren.
- *Toegangstoken* - Een JWT die claims bevat die u gebruiken om de verleende machtigingen voor uw API's te identificeren. Toegangstokens zijn ondertekend, maar ze zijn niet versleuteld. Toegangstokens worden gebruikt om toegang te bieden tot API's en resourceservers.  Wanneer uw API een toegangstoken ontvangt, moet deze de handtekening valideren om te bewijzen dat het token authentiek is. Uw API moet ook een paar claims in het token valideren om te bewijzen dat deze geldig is. Afhankelijk van de scenariovereisten kunnen de claims die door een toepassing zijn gevalideerd, variëren, maar uw toepassing moet in elk scenario een aantal veelvoorkomende claimvalidaties uitvoeren.
- *Vernieuwingstoken* - Refresh-tokens worden gebruikt om nieuwe ID-tokens en toegangstokens te verkrijgen in een OAuth 2.0-stroom. Ze bieden uw toepassing op lange termijn toegang tot bronnen namens gebruikers zonder interactie met die gebruikers. Vernieuwingstokens zijn ondoorzichtig voor uw toepassing. Ze worden uitgegeven door Azure AD B2C en kunnen alleen worden geïnspecteerd en geïnterpreteerd door Azure AD B2C. Ze zijn van lange duur, maar uw toepassing mag niet worden geschreven met de verwachting dat een vernieuwingstoken een bepaalde periode zal duren. Vernieuwingstokens kunnen op elk moment om verschillende redenen ongeldig worden gemaakt. De enige manier waarop uw toepassing weet of een vernieuwingstoken geldig is, is door te proberen het in te wisselen door een tokenverzoek in te dienen bij Azure AD B2C. Wanneer u een vernieuwingstoken inwisselt voor een nieuw token, ontvangt u een nieuw vernieuwingstoken in het token-antwoord. Sla het nieuwe vernieuwingstoken op. Het vervangt het vernieuwingstoken dat u eerder in de aanvraag hebt gebruikt. Deze actie helpt ervoor te zorgen dat uw vernieuwingstokens zo lang mogelijk geldig blijven.

## <a name="endpoints"></a>Eindpunten

Een [geregistreerde toepassing](tutorial-register-applications.md) ontvangt tokens en communiceert met Azure AD B2C door aanvragen naar deze eindpunten te verzenden:

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

Beveiligingstokens die uw toepassing ontvangt van Azure AD `/authorize` `/token` B2C, kunnen afkomstig zijn van de of eindpunten. Wanneer ID-tokens worden `/authorize` verkregen vanaf het eindpunt, wordt dit gedaan met behulp van de [impliciete stroom](implicit-flow-single-page-application.md), die vaak wordt gebruikt voor gebruikers die zich aanmelden bij JavaScript-gebaseerde webtoepassingen. Wanneer ID-tokens worden `/token` verkregen vanaf het eindpunt, wordt het gedaan met behulp van de [autorisatiecodestroom](openid-connect.md#get-a-token), waardoor het token verborgen blijft voor de browser.

## <a name="claims"></a>Claims

Wanneer u Azure AD B2C gebruikt, hebt u een fijnmazige controle over de inhoud van uw tokens. U [gebruikersstromen](user-flow-overview.md) en [aangepaste beleidsregels](custom-policy-overview.md) configureren om bepaalde sets gebruikersgegevens te verzenden in claims die nodig zijn voor uw toepassing. Deze claims kunnen standaardeigenschappen bevatten, zoals **displayName** en **emailAddress.** Uw toepassingen kunnen deze claims gebruiken om gebruikers en verzoeken veilig te verifiëren.

De claims in ID-tokens worden niet geretourneerd in een bepaalde volgorde. Nieuwe claims kunnen op elk gewenst moment worden geïntroduceerd in ID-tokens. Uw aanvraag mag niet breken als nieuwe claims worden geïntroduceerd. U ook [aangepaste gebruikerskenmerken](user-flow-custom-attributes.md) in uw claims opnemen.

In de volgende tabel worden de claims weergegeven die u verwachten in ID-tokens en toegangstokens die zijn uitgegeven door Azure AD B2C.

| Name | Claim | Voorbeeldwaarde | Beschrijving |
| ---- | ----- | ------------- | ----------- |
| Doelgroep | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Hiermee wordt de beoogde ontvanger van het token geïdentificeerd. Voor Azure AD B2C is de doelgroep de toepassings-id. Uw toepassing moet deze waarde valideren en het token afwijzen als deze niet overeenkomt. Publiek is synoniem met resource. |
| Verlener | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Hiermee identificeert u de beveiligingstokenservice (STS) die het token construeert en retourneert. Het identificeert ook de directory waarin de gebruiker is geverifieerd. Uw toepassing moet de claim van de uitgever valideren om ervoor te zorgen dat het token afkomstig is van het juiste eindpunt. |
| Uitgegeven op | `iat` | `1438535543` | Het tijdstip waarop het token werd uitgegeven, vertegenwoordigd in tijdvaktijd. |
| Vervaldatum | `exp` | `1438539443` | Het tijdstip waarop het token ongeldig wordt, weergegeven in tijdvaktijd. Uw toepassing moet deze claim gebruiken om de geldigheid van de tokenlevensduur te verifiëren. |
| Niet eerder | `nbf` | `1438535543` | Het tijdstip waarop het token geldig wordt, weergegeven in tijdvaktijd. Deze tijd is meestal hetzelfde als de tijd dat het token werd uitgegeven. Uw toepassing moet deze claim gebruiken om de geldigheid van de tokenlevensduur te verifiëren. |
| Versie | `ver` | `1.0` | De versie van het ID-token, zoals gedefinieerd door Azure AD B2C. |
| Codehash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Een codehash die alleen in een ID-token is opgenomen wanneer het token wordt uitgegeven samen met een OAuth 2.0-autorisatiecode. Een codehash kan worden gebruikt om de authenticiteit van een autorisatiecode te valideren. Zie de [OpenID Connect-specificatie](https://openid.net/specs/openid-connect-core-1_0.html)voor meer informatie over het uitvoeren van deze validatie.  |
| Toegang tot tokenhash | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Een access token hash die alleen in een ID-token is opgenomen wanneer het token wordt uitgegeven samen met een OAuth 2.0-toegangstoken. Een access token hash kan worden gebruikt om de authenticiteit van een toegangstoken te valideren. Zie de [OpenID Connect-specificatie](https://openid.net/specs/openid-connect-core-1_0.html) voor meer informatie over het uitvoeren van deze validatie  |
| Nonce | `nonce` | `12345` | Een nonce is een strategie die wordt gebruikt om token replay-aanvallen te beperken. Uw toepassing kan een nonce in een `nonce` autorisatieaanvraag opgeven met behulp van de queryparameter. De waarde die u in de aanvraag `nonce` opgeeft, wordt alleen ongewijzigd uitgezonden in de claim van een ID-token. Met deze claim kan uw toepassing de waarde verifiëren ten opzichte van de waarde die op de aanvraag is opgegeven. Uw toepassing moet deze validatie uitvoeren tijdens het validatieproces voor ID-tokens. |
| Onderwerp | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | De principal waarover het token informatie bevestigt, zoals de gebruiker van een toepassing. Deze waarde is onveranderlijk en kan niet opnieuw worden toegewezen of opnieuw worden gebruikt. Het kan worden gebruikt om autorisatiecontroles veilig uit te voeren, bijvoorbeeld wanneer het token wordt gebruikt om toegang te krijgen tot een bron. Standaard wordt de onderwerpclaim gevuld met de object-id van de gebruiker in de map. |
| Verwijzing naar verificatiecontextklasse | `acr` | Niet van toepassing | Alleen gebruikt met ouder beleid. |
| Beleid voor vertrouwenskader | `tfp` | `b2c_1_signupsignin1` | De naam van het beleid dat is gebruikt om het ID-token te verkrijgen. |
| Verificatietijd | `auth_time` | `1438535543` | Het tijdstip waarop een gebruiker voor het laatst referenties heeft ingevoerd, weergegeven in tijdvaktijd. Er is geen discriminatie tussen deze verificatie is een nieuwe aanmelding, een enkele sign-on (SSO) sessie, of een ander aanmeldingstype. Het `auth_time` is de laatste keer dat de toepassing (of gebruiker) een verificatiepoging heeft gestart tegen Azure AD B2C. De methode die wordt gebruikt om te verifiëren wordt niet gedifferentieerd. |
| Bereik | `scp` | `Read`| De machtigingen die aan de bron worden verleend voor een toegangstoken. Meerdere verleende machtigingen worden gescheiden door een spatie. |
| Geautoriseerde partij | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | De **toepassings-ID** van de clienttoepassing die de aanvraag heeft gestart. |

## <a name="configuration"></a>Configuratie

De volgende eigenschappen worden gebruikt voor [het beheren van levensduur van beveiligingstokens](configure-tokens.md) die worden uitgezonden door Azure AD B2C:

- **Toegang &-ID-tokenlevensduur (minuten)** - De levensduur van het OAuth 2.0-token aan toonder dat wordt gebruikt om toegang te krijgen tot een beveiligde bron. De standaardinstelling is 60 minuten. Het minimum (inclusief) is 5 minuten. Het maximum (inclusief) is 1440 minuten.

- **Tokenlevensduur vernieuwen (dagen)** - De maximale periode waarvoor een vernieuwingstoken kan worden gebruikt om een nieuw toegangs- of ID-token te verkrijgen. De periode omvat ook het verkrijgen van een nieuw `offline_access` vernieuwingstoken als uw toepassing het bereik heeft gekregen. De standaardinstelling is 14 dagen. Het minimum (inclusief) is één dag. Het maximum (inclusief) is 90 dagen.

- **Tokenschuiven venster levensduur (dagen) -** Na deze periode verstreken is de gebruiker gedwongen om opnieuw te verifiëren, ongeacht de geldigheidsperiode van de meest recente refresh token verworven door de toepassing. Deze kan alleen worden geleverd als de schakelaar is ingesteld op **Begrensd**. Het moet groter zijn dan of gelijk zijn aan de waarde **Levensduur van het token vernieuwen (dagen).** Als de switch is ingesteld op **Niet-begrensd,** u geen specifieke waarde geven. De standaardinstelling is 90 dagen. Het minimum (inclusief) is één dag. Het maximum (inclusief) is 365 dagen.

De volgende use cases zijn ingeschakeld met behulp van deze eigenschappen:

- Sta een gebruiker toe om voor onbepaalde tijd aangemeld te blijven bij een mobiele toepassing, zolang de gebruiker voortdurend actief is op de toepassing. U **de levensduur van het schuiven van een token voor token (dagen)** vernieuwen op **Niet-begrensd** in uw aanmeldingsgebruikersstroom instellen.
- Voldoe aan de beveiligings- en nalevingsvereisten van uw branche door de juiste levensduur van toegangstoken's in te stellen.

Deze instellingen zijn niet beschikbaar voor gebruikersstromen voor het opnieuw instellen van wachtwoorden.

## <a name="compatibility"></a>Compatibiliteit

De volgende eigenschappen worden gebruikt om tokencompatibiliteit te [beheren:](configure-tokens.md)

- **Uitgever (iss)-claim** - Deze eigenschap identificeert de Azure AD B2C-tenant die het token heeft uitgegeven. De standaardwaarde is `https://<domain>/{B2C tenant GUID}/v2.0/`. De waarde `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` van omvat id's voor zowel de Azure AD B2C-tenant als de gebruikersstroom die in de tokenaanvraag is gebruikt. Als voor uw toepassing of bibliotheek Azure AD B2C moet voldoen aan de [detectie 1.0-spec van OpenID Connect,](https://openid.net/specs/openid-connect-discovery-1_0.html)gebruikt u deze waarde.

- **Onderwerp (sub) claim** - Deze eigenschap identificeert de entiteit waarvoor het token informatie claimt. De standaardwaarde is **ObjectID**, `sub` die de claim in het token vult met de object-id van de gebruiker. De waarde van **Niet ondersteund** wordt alleen opgegeven voor achterwaartse compatibiliteit. Het is raadzaam om over te schakelen naar **ObjectID** zodra u in staat bent om.

- **Claim die beleids-ID vertegenwoordigt** - Deze eigenschap identificeert het claimtype waarin de beleidsnaam die wordt gebruikt in de tokenaanvraag wordt ingevuld. De standaardwaarde is `tfp`. De waarde `acr` van is alleen voorzien voor achterwaartse compatibiliteit.

## <a name="pass-through"></a>Pass-through

Wanneer een gebruikersreis begint, ontvangt Azure AD B2C een toegangstoken van een identiteitsprovider. Azure AD B2C gebruikt dat token om informatie over de gebruiker op te halen. U [schakelt een claim in uw gebruikersstroom in](idp-pass-through-user-flow.md) of [definieert een claim in uw aangepaste beleid](idp-pass-through-custom.md) om het token door te geven aan de toepassingen die u registreert in Azure AD B2C. Uw toepassing moet een [v2-gebruikersstroom](user-flow-versions.md) gebruiken om te profiteren van het doorgeven van het token als claim.

Azure AD B2C ondersteunt momenteel alleen het doorgeven van het toegangstoken van OAuth 2.0-identiteitsproviders, waaronder Facebook en Google. Voor alle andere identiteitsproviders wordt de claim leeg geretourneerd.

## <a name="validation"></a>Validatie

Als u een token wilt valideren, moet uw toepassing zowel de handtekening als de claims van het token controleren. Veel open-source bibliotheken zijn beschikbaar voor het valideren van JWT's, afhankelijk van uw voorkeurstaal. Het wordt aanbevolen om deze opties te verkennen in plaats van uw eigen validatielogica te implementeren.

### <a name="validate-signature"></a>Handtekening valideren

Een JWT bevat drie segmenten, een *koptekst,* een *hoofden*en een *handtekening*. Het handtekeningsegment kan worden gebruikt om de authenticiteit van het token te valideren, zodat het door uw toepassing kan worden vertrouwd. Azure AD B2C-tokens worden ondertekend met behulp van industriestandaard asymmetrische versleutelingsalgoritmen, zoals RSA 256.

De koptekst van het token bevat informatie over de sleutel- en versleutelingsmethode die wordt gebruikt om het token te ondertekenen:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

De waarde van de **alg-claim** is het algoritme dat werd gebruikt om het token te ondertekenen. De waarde van de **kinderclaim** is de openbare sleutel die werd gebruikt om het token te ondertekenen. Op een gegeven moment kan Azure AD B2C een token ondertekenen met behulp van een van een set publiek-private sleutelparen. Azure AD B2C roteert de mogelijke set sleutels periodiek. Uw aanvraag moet worden geschreven om deze belangrijke wijzigingen automatisch te verwerken. Een redelijke frequentie om te controleren op updates voor de openbare sleutels die worden gebruikt door Azure AD B2C is elke 24 uur.

Azure AD B2C heeft een eindpunt met metagegevens van OpenID Connect. Met dit eindpunt kunnen toepassingen informatie opvragen over Azure AD B2C tijdens runtime. Deze informatie omvat eindpunten, tokeninhoud en tokenondertekeningssleutels. Uw Azure AD B2C-tenant bevat voor elk beleid een JSON-metagegevensdocument. Het metagegevensdocument is een JSON-object dat verschillende nuttige informatie bevat. De metagegevens bevatten **jwks_uri**, die de locatie van de set van openbare sleutels die worden gebruikt om tokens te ondertekenen geeft. Die locatie is hier aanwezig, maar het is het beste om de locatie dynamisch op te halen met behulp van het metadatadocument en **jwks_uri**te ontwijs:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
Het JSON-document op deze URL bevat alle openbare belangrijke informatie die op een bepaald moment wordt gebruikt. Uw app kan `kid` de claim in de JWT-header gebruiken om de openbare sleutel te selecteren in het JSON-document dat wordt gebruikt om een bepaald token te ondertekenen. Het kan vervolgens handtekeningvalidatie uitvoeren met behulp van de juiste openbare sleutel en het aangegeven algoritme.

Het metagegevensdocument voor het `B2C_1_signupsignin1` beleid in de `contoso.onmicrosoft.com` tenant bevindt zich op:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

Als u wilt bepalen welk beleid is gebruikt om een token te ondertekenen (en waar u naartoe moet om de metagegevens aan te vragen), hebt u twee opties. Ten eerste wordt de beleidsnaam opgenomen in de `acr` claim in het token. U parse claims uit het lichaam van de JWT door basis-64 decoderen van het lichaam en deserialiseren van de JSON string die resultaten. De `acr` claim is de naam van het beleid dat is gebruikt om het token uit te geven. De andere optie is om het beleid `state` in de waarde van de parameter te coderen wanneer u de aanvraag uitgeeft en deze vervolgens te decoderen om te bepalen welk beleid is gebruikt. Beide methoden zijn geldig.

Een beschrijving van het uitvoeren van handtekeningvalidatie valt buiten het bereik van dit document. Er zijn veel open-sourcebibliotheken beschikbaar om u te helpen een token te valideren.

### <a name="validate-claims"></a>Claims valideren

Wanneer uw toepassingen of API een ID-token ontvangt, moet het ook verschillende controles uitvoeren op de claims in het ID-token. De volgende claims moeten worden gecontroleerd:

- **doelgroep** - Controleert of het ID-token bedoeld was om aan uw toepassing te worden gegeven.
- **niet voor** en **vervaldatum** - Controleert of het ID-token niet is verlopen.
- **uitgever** - Controleert of het token is uitgegeven aan uw toepassing door Azure AD B2C.
- **nonce** - Een strategie voor token replay aanval mitigatie.

Raadpleeg de [OpenID Connect-specificatie](https://openid.net)voor een volledige lijst met validaties die uw toepassing moet uitvoeren.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [gebruik van toegangstokens](access-tokens.md).

