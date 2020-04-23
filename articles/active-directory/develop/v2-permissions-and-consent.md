---
title: Scopes, machtigingen en toestemming van microsoft-identiteitsplatform
description: Een beschrijving van autorisatie in het eindpunt van het Microsoft-identiteitsplatform, inclusief scopes, machtigingen en toestemming.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: aaddev, fasttrack-edit
ms.openlocfilehash: 5495aa6fda189897985ed2f198f6e92c996f6fef
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868388"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Machtigingen en toestemming in het eindpunt van het Microsoft-identiteitsplatform

Toepassingen die integreren met het Microsoft-identiteitsplatform volgen een autorisatiemodel dat gebruikers en beheerders controle geeft over hoe gegevens kunnen worden geopend. De implementatie van het autorisatiemodel is bijgewerkt op het eindpunt van het Microsoft-identiteitsplatform en verandert de manier waarop een app moet communiceren met het Microsoft-identiteitsplatform. Dit artikel behandelt de basisconcepten van dit autorisatiemodel, inclusief scopes, machtigingen en toestemming.

## <a name="scopes-and-permissions"></a>Scopes en machtigingen

Het Microsoft-identiteitsplatform implementeert het [OAuth 2.0-autorisatieprotocol.](active-directory-v2-protocols.md) OAuth 2.0 is een methode waarmee een app van derden toegang heeft tot webgehoste bronnen namens een gebruiker. Elke webgehoste bron die integreert met het Microsoft-identiteitsplatform heeft een resource-id of *Application ID URI.* Enkele van de webgehoste bronnen van Microsoft zijn bijvoorbeeld:

* Microsoft Graph:`https://graph.microsoft.com`
* Office 365 Mail API:`https://outlook.office.com`
* Azure Key Vault:`https://vault.azure.net`

> [!NOTE]
> We raden u ten zeerste aan Microsoft Graph te gebruiken in plaats van Office 365 Mail API, enz.

Hetzelfde geldt voor alle bronnen van derden die zijn geïntegreerd met het Microsoft-identiteitsplatform. Elk van deze bronnen kan ook een set machtigingen definiëren die kunnen worden gebruikt om de functionaliteit van die bron in kleinere segmenten te verdelen. [Microsoft Graph](https://graph.microsoft.com) heeft bijvoorbeeld machtigingen gedefinieerd voor de volgende taken, onder andere:

* De agenda van een gebruiker lezen
* Naar de agenda van een gebruiker schrijven
* E-mail verzenden als gebruiker

Door dit soort machtigingen te definiëren, heeft de resource fijnmazige controle over zijn gegevens en hoe API-functionaliteit wordt blootgesteld. Een app van derden kan deze machtigingen aanvragen bij gebruikers en beheerders, die het verzoek moeten goedkeuren voordat de app toegang heeft tot gegevens of namens een gebruiker kan handelen. Door de functionaliteit van de resource in kleinere machtigingssets te splitsen, kunnen apps van derden worden gebouwd om alleen de specifieke machtigingen op te vragen die ze nodig hebben om hun functie uit te voeren. Gebruikers en beheerders kunnen precies weten tot welke gegevens de app toegang heeft en ze kunnen er meer vertrouwen in hebben dat deze zich niet met kwade bedoelingen gedraagt. Ontwikkelaars moeten zich altijd houden aan het concept van de minste bevoegdheden, vragen om alleen de machtigingen die ze nodig hebben voor hun toepassingen om te functioneren.

In OAuth 2.0 worden dit soort machtigingen *scopes*genoemd. Ze worden ook vaak aangeduid als *machtigingen*. Een machtiging wordt weergegeven in het Microsoft-identiteitsplatform als een tekenreekswaarde. Als u verdergaat met het voorbeeld van Microsoft Graph, is de tekenreekswaarde voor elke machtiging:

* De agenda van een gebruiker lezen met behulp van`Calendars.Read`
* De agenda van een gebruiker schrijven met behulp van`Calendars.ReadWrite`
* E-mail verzenden als gebruiker met behulp van`Mail.Send`

Een app vraagt deze machtigingen meestal aan door de scopes op te geven in aanvragen voor het eindpunt voor het autoisme van het Microsoft-identiteitsplatform. Bepaalde machtigingen met hoge bevoegdheden kunnen echter alleen worden verleend door toestemming van de beheerder en worden aangevraagd/verleend met behulp van het eindpunt van de [beheerderstoestemming.](v2-permissions-and-consent.md#admin-restricted-permissions) Lees verder voor meer informatie.

## <a name="permission-types"></a>Machtigingstypen

Microsoft-identiteitsplatform ondersteunt twee soorten machtigingen: **gedelegeerde machtigingen** en **toepassingsmachtigingen**.

* **Gedelegeerde machtigingen** worden gebruikt door apps die een aangemelde gebruiker aanwezig hebben. Voor deze apps stemt de gebruiker of een beheerder in met de machtigingen die de app opvraagt en de app is de machtiging gedelegeerd om als de aangemelde gebruiker op te treden bij het voeren van oproepen naar de doelbron. Sommige gedelegeerde machtigingen kunnen worden goedgekeurd door niet-administratieve gebruikers, maar voor sommige machtigingen met een hogere bevoegdheden is toestemming van [de beheerder](v2-permissions-and-consent.md#admin-restricted-permissions)vereist. Zie [Beheerdersrolmachtigingen in Azure AD](../users-groups-roles/directory-assign-admin-roles.md)voor meer informatie over welke beheerdersrollen kunnen instemmen met gedelegeerde machtigingen.

* **Toepassingsmachtigingen** worden gebruikt door apps die worden uitgevoerd zonder dat een aangemelde gebruiker aanwezig is. apps die bijvoorbeeld worden uitgevoerd als achtergrondservices of daemons.  Toepassingsmachtigingen kunnen alleen worden [goedgekeurd door een beheerder.](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)

_Effectieve machtigingen_ zijn de machtigingen die uw app heeft bij het indienen van aanvragen voor de doelbron. Het is belangrijk om het verschil te begrijpen tussen de gedelegeerde en toepassingsmachtigingen die uw app wordt verleend en de effectieve machtigingen ervan bij het voeren van oproepen naar de doelbron.

- Voor gedelegeerde machtigingen zijn de _effectieve machtigingen_ van uw app het minst bevoorrechte snijpunt van de gedelegeerde machtigingen die de app heeft gekregen (via toestemming) en de bevoegdheden van de momenteel aangemelde gebruiker. Uw app kan nooit meer machtigingen hebben dan de aangemelde gebruiker. De machtigingen van de aangemelde gebruiker kunnen in organisaties worden bepaald door beleid of door lidmaatschap in een of meer beheerdersrollen. Zie [Beheerdersrolmachtigingen in Azure AD](../users-groups-roles/directory-assign-admin-roles.md)voor meer informatie over welke beheerdersrollen kunnen instemmen met gedelegeerde machtigingen.

   Stel dat uw app de gedelegeerde machtiging _Gebruiker.ReadWrite.All_ heeft gekregen. Deze machtiging verleent uw app in feite machtigingen om het profiel van elke gebruiker in een organisatie te lezen en bij te werken. Als de aangemelde gebruiker een globale beheerder is, kan uw app het profiel van elke gebruiker in de organisatie bijwerken. Als de aangemelde gebruiker zich echter niet in een beheerdersrol bevindt, kan uw app alleen het profiel van de aangemelde gebruiker bijwerken. De app kan geen profielen van andere gebruikers in de organisatie bijwerken omdat de gebruiker namens welke de app machtigingen heeft om te handelen niet over deze rechten beschikt.

- Voor toepassingsmachtigingen zijn de _effectieve machtigingen_ van uw app het volledige niveau van bevoegdheden die door de machtiging worden geïmpliceerd. Een app met de _user.ReadWrite.All-toepassingsmachtiging_ kan bijvoorbeeld het profiel van elke gebruiker in de organisatie bijwerken.

## <a name="openid-connect-scopes"></a>OpenID Connect-scopes

De implementatie van OpenID Connect van het Microsoft-identiteitsplatform heeft een aantal goed `openid` `email`gedefinieerde `profile`scopes die niet van toepassing zijn op een specifieke bron: , , en `offline_access`. De `address` `phone` scopes en OpenID Connect worden niet ondersteund.

### <a name="openid"></a>Openid

Als een app zich aanmeldt met [OpenID Connect,](active-directory-v2-protocols.md)moet deze het `openid` bereik opvragen. Het `openid` bereik wordt op de toestemmingspagina voor werkaccount weergegeven als de machtiging 'Aanmelden' en op de pagina met persoonlijke toestemming voor toestemming van Microsoft-account als de machtiging 'Uw profiel bekijken en verbinding maken met apps en services met uw Microsoft-account'. Met deze toestemming kan een app een unieke id voor `sub` de gebruiker ontvangen in de vorm van de claim. Het geeft de app ook toegang tot het UserInfo-eindpunt. Het `openid` bereik kan worden gebruikt op het tokeneindpunt van het Microsoft-identiteitsplatform om ID-tokens te verkrijgen, die door de app kunnen worden gebruikt voor verificatie.

### <a name="email"></a>e-mail

Het `email` bereik kan worden `openid` gebruikt met het bereik en alle andere. Het geeft de app toegang tot het primaire e-mailadres van de gebruiker in de vorm van de `email` claim. De `email` claim wordt alleen in een token opgenomen als een e-mailadres is gekoppeld aan het gebruikersaccount, wat niet altijd het geval is. Als het `email` bereik wordt gebruikt, moet uw app bereid `email` zijn een aanvraag te behandelen waarin de claim niet in het token bestaat.

### <a name="profile"></a>profiel

Het `profile` bereik kan worden `openid` gebruikt met het bereik en alle andere. Het geeft de app toegang tot een aanzienlijke hoeveelheid informatie over de gebruiker. De informatie waartoe het toegang heeft, omvat, maar is niet beperkt tot, de voornaam, achternaam, voorkeursgebruikersnaam en object-ID van de gebruiker. Zie de [ `id_tokens` referentie](id-tokens.md)voor een volledige lijst met de profielclaims die beschikbaar zijn in de parameter id_tokens voor een specifieke gebruiker.

### <a name="offline_access"></a>offline_access

Het [ `offline_access` bereik](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) geeft uw app langere tijd toegang tot bronnen namens de gebruiker. Op de toestemmingspagina wordt dit bereik weergegeven als de machtiging 'Toegang behouden tot gegevens waartoe u toegang hebt gegeven'. Wanneer een gebruiker `offline_access` het bereik goedkeurt, kan uw app vernieuwingstokens ontvangen van het tokeneindpunt van het Microsoft-identiteitsplatform. Vernieuwingstokens zijn van lange duur. Uw app kan nieuwe toegangstokens krijgen als oudere verlopen.

> [!NOTE]
> Deze toestemming wordt vandaag weergegeven op alle toestemmingsschermen, zelfs voor stromen die geen vernieuwingstoken bieden (de [impliciete stroom).](v2-oauth2-implicit-grant-flow.md)  Dit is om scenario's te dekken waarin een client kan beginnen binnen de impliciete stroom en vervolgens naar de codestroom kan gaan waar een vernieuwingstoken wordt verwacht.

Op het Microsoft-identiteitsplatform (verzoeken aan het v2.0-eindpunt) `offline_access` moet uw app expliciet het bereik aanvragen om vernieuwingstokens te ontvangen. Dit betekent dat wanneer u een autorisatiecode inwisselt in de [oauth 2.0-autorisatiecodestroom,](active-directory-v2-protocols.md)u alleen een toegangstoken van het `/token` eindpunt ontvangt. Het toegangstoken is korte tijd geldig. Het toegangstoken verloopt meestal binnen een uur. Op dat moment moet uw app de `/authorize` gebruiker terugleiden naar het eindpunt om een nieuwe autorisatiecode te krijgen. Tijdens deze omleiding moet de gebruiker, afhankelijk van het type app, mogelijk opnieuw zijn referenties invoeren of toestemming geven voor machtigingen.

Zie de referentie van het [Microsoft-identiteitsplatform-protocol voor](active-directory-v2-protocols.md)meer informatie over het aanvragen en gebruiken van vernieuwingstokens.

## <a name="requesting-individual-user-consent"></a>Individuele toestemming van de gebruiker aanvragen

In een [OpenID Connect- of OAuth 2.0-autorisatieaanvraag](active-directory-v2-protocols.md) kan een `scope` app de machtigingen aanvragen die deze nodig heeft met behulp van de queryparameter. Wanneer een gebruiker zich bijvoorbeeld aanmeldt bij een app, stuurt de app een verzoek zoals het volgende voorbeeld (met regeleinden toegevoegd voor leesbaarheid):

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

De `scope` parameter is een lijst met gedelegeerde machtigingen die de app aanvraagt. Elke toestemming wordt aangegeven door de machtigingswaarde toe te te kennen aan de id van de resource (de toepassings-id URI). In het voorbeeld van het verzoek heeft de app toestemming nodig om de agenda van de gebruiker te lezen en e-mail als gebruiker te verzenden.

Nadat de gebruiker zijn referenties heeft ingebracht, controleert het eindpunt van het Microsoft-identiteitsplatform op een overeenkomende registratie van toestemming van *de gebruiker.* Als de gebruiker in het verleden geen toestemming heeft gegeven voor een van de gevraagde machtigingen en een beheerder namens de hele organisatie niet heeft ingestemd met deze machtigingen, vraagt het eindpunt van het Microsoft-identiteitsplatform de gebruiker om de gevraagde machtigingen te verlenen.

> [!NOTE]
>Op dit moment `offline_access` worden de machtigingen ("Toegang tot gegevens `user.read` behouden waartoe u toegang tot hebt gegeven") en ("Meld u aan en lees uw profiel lezen") automatisch opgenomen in de eerste toestemming voor een toepassing.  Deze machtigingen zijn over het algemeen vereist `offline_access` voor de juiste app-functionaliteit - geeft de `user.read` app toegang `sub` tot vernieuwing van tokens, van cruciaal belang voor native en web-apps, terwijl geeft toegang tot de claim, waardoor de client of app correct identificeren van de gebruiker na verloop van tijd en toegang tot rudimentaire gebruikersinformatie.

![Voorbeeldschermafbeelding met toestemming voor werkaccount](./media/v2-permissions-and-consent/work_account_consent.png)

Wanneer de gebruiker de toestemmingsaanvraag goedkeurt, wordt de toestemming geregistreerd en hoeft de gebruiker geen toestemming meer te geven voor latere aanmeldingen voor de toepassing.

## <a name="requesting-consent-for-an-entire-tenant"></a>Toestemming aanvragen voor een hele huurder

Vaak, wanneer een organisatie een licentie of abonnement voor een toepassing koopt, wil de organisatie proactief de toepassing voor gebruik door alle leden van de organisatie opzetten. Als onderdeel van dit proces kan een beheerder toestemming verlenen voor de toepassing om namens elke gebruiker in de tenant op te treden. Als de beheerder toestemming verleent voor de gehele tenant, zien de gebruikers van de organisatie geen toestemmingspagina voor de toepassing.

Als u toestemming wilt vragen voor gedelegeerde machtigingen voor alle gebruikers in een tenant, kan uw app het eindpunt voor beheerderstoestemming gebruiken.

Daarnaast moeten toepassingen het eindpunt voor beheerderstoestemming gebruiken om toepassingsmachtigingen aan te vragen.

## <a name="admin-restricted-permissions"></a>Beheerdersmachtigingen

Sommige machtigingen met hoge bevoegdheden in het Microsoft-ecosysteem kunnen worden ingesteld op *beheerdersbeperkingen.* Voorbeelden van dit soort machtigingen zijn de volgende:

* Lees alle volledige profielen van de gebruiker met behulp van`User.Read.All`
* Gegevens naar de map van een organisatie schrijven met behulp van`Directory.ReadWrite.All`
* Alle groepen in de map van een organisatie lezen met behulp van`Groups.Read.All`

Hoewel een gebruiker van een consument een toepassing toegang tot dit soort gegevens kan verlenen, mogen gebruikers van organisaties geen toegang verlenen tot dezelfde set gevoelige bedrijfsgegevens. Als uw toepassing toegang vraagt tot een van deze machtigingen van een organisatiegebruiker, ontvangt de gebruiker een foutbericht waarin staat dat hij geen toestemming heeft om in te stemmen met de machtigingen van uw app.

Als uw app toegang nodig heeft tot beheerdersbeperkte scopes voor organisaties, moet u deze rechtstreeks aanvragen bij een bedrijfsbeheerder, ook met behulp van het eindpunt voor beheerderstoestemming, dat hierna wordt beschreven.

Als de toepassing gedelegeerde machtigingen met hoge bevoegdheden aanvraagt en een beheerder deze machtigingen verleent via het eindpunt voor beheerderstoestemming, wordt toestemming verleend voor alle gebruikers in de tenant.

Als de toepassing toepassingsmachtigingen aanvraagt en een beheerder deze machtigingen verleent via het eindpunt voor beheerderstoestemming, wordt deze subsidie niet gedaan namens een specifieke gebruiker. In plaats daarvan wordt de clienttoepassing *direct*machtigingen verleend. Deze typen machtigingen worden alleen gebruikt door daemonservices en andere niet-interactieve toepassingen die op de achtergrond worden uitgevoerd.

## <a name="using-the-admin-consent-endpoint"></a>Het eindpunt voor beheerderstoestemming gebruiken

> [!NOTE]
> Let op: nadat u toestemming van de beheerder hebt gegeven met behulp van het eindpunt voor beheerderstoestemming, bent u klaar met het verlenen van toestemming van de beheerder en hoeven gebruikers geen aanvullende acties meer uit te voeren. Na het verlenen van toestemming van de beheerder, kunnen gebruikers een toegangstoken krijgen via een typische auth-stroom en het resulterende toegangstoken heeft de toestemminggegeven machtigingen.

Wanneer een bedrijfsbeheerder uw toepassing gebruikt en naar het geautoriseerde eindpunt wordt geleid, detecteert het Microsoft-identiteitsplatform de rol van de gebruiker en vraagt u of hij namens de gehele tenant toestemming wil geven voor de machtigingen die u hebt aangevraagd. Er is echter ook een speciaal eindpunt voor beheerderstoestemming dat u gebruiken als u proactief wilt vragen dat een beheerder namens de gehele tenant toestemming verleent. Het gebruik van dit eindpunt is ook noodzakelijk voor het aanvragen van toepassingsmachtigingen (die niet kunnen worden aangevraagd met behulp van het geautoriseerde eindpunt).

Als u deze stappen volgt, kan uw app machtigingen aanvragen voor alle gebruikers in een tenant, inclusief beheerdersbeperkte scopes. Dit is een bewerking met hoge bevoegdheden en mag alleen worden gedaan als dat nodig is voor uw scenario.

Zie het voorbeeld van scopes met [beperkte beheerscopes](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2)om een codevoorbeeld te zien dat de stappen implementeert.

### <a name="request-the-permissions-in-the-app-registration-portal"></a>De machtigingen aanvragen in de app-registratieportal

Toepassingen kunnen in de app-registratieportal noteren welke machtigingen ze nodig hebben (zowel gedelegeerd als toepassing).  Hierdoor u `/.default` gebruik maken van het bereik en de optie 'Toestemming verlenen van beheerderstoestemming' van de Azure-portal.  In het algemeen is het best practice om ervoor te zorgen dat de machtigingen die statisch zijn gedefinieerd voor een bepaalde toepassing een superset zijn van de machtigingen die het dynamisch/stapsgewijs zal aanvragen.

> [!NOTE]
>Toepassingsmachtigingen kunnen alleen worden aangevraagd via [`/.default`](#the-default-scope) het gebruik van - dus als uw app toepassingsmachtigingen nodig heeft, moet u ervoor zorgen dat deze worden weergegeven in de app-registratieportal.

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>De lijst met statisch aangevraagde machtigingen voor een toepassing configureren

1. Ga naar uw toepassing in de [Azure-portal - Ervaring met app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) of [maak een app](quickstart-register-app.md) als u dat nog niet hebt gedaan.
2. Zoek de sectie **API-machtigingen** en klik binnen de API-machtigingen op Een machtiging toevoegen.
3. Selecteer **Microsoft Graph** in de lijst met beschikbare API's en voeg vervolgens de machtigingen toe die uw app vereist.
3. Sla de app-registratie **op.**

### <a name="recommended-sign-the-user-into-your-app"></a>Aanbevolen: de gebruiker aanmelden bij uw app

Wanneer u een toepassing bouwt die gebruikmaakt van het eindpunt voor beheerderstoestemming, heeft de app doorgaans een pagina of weergave nodig waarin de beheerder de machtigingen van de app kan goedkeuren. Deze pagina kan deel uitmaken van de aanmeldingsstroom van de app, een deel van de instellingen van de app, of het kan een speciale "connect"-stroom zijn. In veel gevallen is het zinvol dat de app deze 'connect'-weergave pas weergeeft nadat een gebruiker zich heeft aangemeld met een Microsoft-account op het werk of op school.

Wanneer u de gebruiker aanmeldt bij uw app, u de organisatie identificeren waartoe de beheerder behoort voordat u hem of haar vraagt de benodigde machtigingen goed te keuren. Hoewel het niet strikt noodzakelijk is, kan het u helpen een intuïtievere ervaring te creëren voor uw organisatiegebruikers. Als u de gebruiker wilt aanmelden, volgt u onze zelfstudies van het [Microsoft-identiteitsplatformprotocol](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>De machtigingen aanvragen bij een directorybeheerder

Wanneer u klaar bent om machtigingen aan te vragen bij de beheerder van uw organisatie, u de gebruiker doorverwijzen naar het eindpunt van de *toestemming van*de microsoft-identiteitsplatformbeheerder.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| Parameter        | Voorwaarde        | Beschrijving                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | Vereist | De directorytenant waarvan u toestemming wilt vragen. Kan worden verstrekt in GUID of vriendelijke naam formaat of generiek verwezen met organisaties zoals te zien in het voorbeeld. Gebruik geen 'gewoons', omdat persoonlijke accounts geen beheerderstoestemming kunnen geven, behalve in de context van een tenant. Gebruik de tenant-id waar mogelijk om de beste compatibiliteit met persoonlijke accounts die huurders beheren, zo goed mogelijk te beheren. |
| `client_id` | Vereist | De **toepassings-id (client)** die de [Azure-portal – App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring toegewezen aan uw app. |
| `redirect_uri` | Vereist |De omleiding URI waar u wilt dat het antwoord wordt verzonden voor uw app te verwerken. Het moet precies overeenkomen met een van de omleiding URI's die u geregistreerd in de app registratie portal. |
| `state` | Aanbevolen | Een waarde die is opgenomen in het verzoek en die ook wordt geretourneerd in het tokenantwoord. Het kan een reeks van elke inhoud die u wilt. Gebruik de status om informatie te coderen over de status van de gebruiker in de app voordat het verificatieverzoek is ingediend, zoals de pagina of weergave waarop ze zich bevonden. |
|`scope`        | Vereist        | Hiermee definieert u de set machtigingen die door de toepassing worden aangevraagd. Dit kan statische (met behulp van) [`/.default`](#the-default-scope)of dynamische scopes zijn.  Dit kan de OIDC`openid`scopes ( , `profile`, ) `email`omvatten. Als u toepassingsmachtigingen nodig hebt, moet `/.default` u de statisch geconfigureerde lijst met machtigingen aanvragen.  |


Op dit moment vereist Azure AD dat een tenantbeheerder zich aanmeldt om de aanvraag te voltooien. De beheerder wordt gevraagd alle machtigingen goed te keuren `scope` die u in de parameter hebt aangevraagd.  Als u een statische`/.default`( ) waarde hebt gebruikt, werkt deze zoals het eindpunt v1.0-beheerderstoestemming en vraagt u toestemming voor alle scopes die in de vereiste machtigingen voor de app zijn gevonden.

#### <a name="successful-response"></a>Succesvolle reactie

Als de beheerder de machtigingen voor uw app goedkeurt, ziet het geslaagde antwoord er als volgt uit:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | Beschrijving |
| --- | --- |
| `tenant` | De directorytenant die uw aanvraag de door deze toepassing gevraagde machtigingen heeft verleend, in GUID-indeling. |
| `state` | Een waarde die is opgenomen in het verzoek en die ook wordt geretourneerd in het tokenantwoord. Het kan een reeks van elke inhoud die u wilt. De status wordt gebruikt om informatie over de status van de gebruiker in de app te coderen voordat het verificatieverzoek is ingediend, zoals de pagina of weergave waarop ze zich bevonden. |
| `admin_consent` | Zal worden `True`ingesteld op . |

#### <a name="error-response"></a>Foutreactie

Als de beheerder de machtigingen voor uw app niet goedkeurt, ziet het mislukte antwoord er als volgt uit:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | Beschrijving |
| --- | --- |
| `error` | Een foutcodetekenreeks die kan worden gebruikt om typen fouten die zich voordoen te classificeren en kan worden gebruikt om op fouten te reageren. |
| `error_description` | Een specifiek foutbericht waarmee een ontwikkelaar de hoofdoorzaak van een fout kan identificeren. |

Nadat u een succesvol antwoord hebt ontvangen van het eindpunt voor beheerderstoestemming, heeft uw app de gevraagde machtigingen gekregen. Vervolgens u een token aanvragen voor de gewenste bron.

## <a name="using-permissions"></a>Machtigingen gebruiken

Nadat de gebruiker toestemming heeft gegeven voor machtigingen voor uw app, kan uw app toegangstokens verkrijgen die de toestemming van uw app vertegenwoordigen om toegang te krijgen tot een bron in een bepaalde hoedanigheid. Een toegangstoken kan alleen worden gebruikt voor één bron, maar gecodeerd in het toegangstoken is elke toestemming dat uw app is verleend voor die bron. Als u een toegangstoken wilt aanschaffen, kan uw app een verzoek indienen bij het eindpunt van het Microsoft-identiteitsplatformtoken, zoals dit:

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

U het resulterende toegangstoken gebruiken in HTTP-aanvragen voor de bron. Het geeft op betrouwbare wijze aan de resource aan dat uw app de juiste toestemming heeft om een specifieke taak uit te voeren.

Zie de verwijzing naar het [eindpuntprotocol van het Microsoft-identiteitsplatform](active-directory-v2-protocols.md)voor meer informatie over het OAuth 2.0-protocol en hoe u toegangstokens krijgen.

## <a name="the-default-scope"></a>De /.default scope

U `/.default` het bereik gebruiken om uw apps te migreren van het v1.0-eindpunt naar het eindpunt van het Microsoft-identiteitsplatform. Dit is een ingebouwde scope voor elke toepassing die verwijst naar de statische lijst met machtigingen die zijn geconfigureerd op de toepassingsregistratie. Een `scope` waarde `https://graph.microsoft.com/.default` van is functioneel hetzelfde als de `resource=https://graph.microsoft.com` v1.0-eindpunten - namelijk dat het een token opvraagt met de scopes in Microsoft Graph waarvoor de toepassing is geregistreerd in de Azure-portal.  Het is gebouwd met behulp `/.default` van de resource URI + `https://contosoApp.com`(bijvoorbeeld als de `https://contosoApp.com/.default`resource URI is, dan is de gevraagde scope zou zijn).  Zie de [sectie over slepende slashes](#trailing-slash-and-default) voor gevallen waarin u een tweede slash moet opnemen om het token correct aan te vragen.

De /.default-scope kan worden gebruikt in een OAuth 2.0-stroom, maar is noodzakelijk in de [stroom-en-clientreferentiesstroom en clientreferenties,](v2-oauth2-client-creds-grant-flow.md)evenals bij het gebruik van het eindpunt van de v2-beheerdertoestemming om toepassingsmachtigingen aan te vragen. [On-Behalf-Of flow](v2-oauth2-on-behalf-of-flow.md)

> [!NOTE]
> Clients kunnen statische (`/.default`) en dynamische toestemming niet combineren in één aanvraag. Dus, `scope=https://graph.microsoft.com/.default+mail.read` zal resulteren in een fout als gevolg van de combinatie van scope types.

### <a name="default-and-consent"></a>/.default en toestemming

Het `/.default` bereik activeert het v1.0-eindpuntgedrag ook voor. `prompt=consent` Het vraagt toestemming voor alle machtigingen die door de toepassing zijn geregistreerd, ongeacht de bron. Als het `/.default` bereik als onderdeel van de aanvraag wordt opgenomen, retourneert het bereik een token met de scopes voor de gevraagde bron.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default wanneer de gebruiker al toestemming heeft gegeven

Omdat `/.default` functioneel identiek is `resource`aan het gedrag van het -centric v1.0-eindpunt, brengt het ook het toestemmingsgedrag van het v1.0-eindpunt met zich mee. Namelijk, `/.default` alleen triggers een toestemming prompt als er geen toestemming is verleend tussen de client en de bron door de gebruiker. Als een dergelijke toestemming bestaat, wordt een token geretourneerd met alle scopes die door de gebruiker voor die bron zijn verleend. Als er echter geen toestemming is `prompt=consent` verleend of als de parameter is opgegeven, wordt een toestemmingsprompt weergegeven voor alle scopes die door de clienttoepassing zijn geregistreerd.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Voorbeeld 1: De gebruiker of tenantbeheerder heeft machtigingen verleend

In dit voorbeeld heeft de gebruiker (of een tenantbeheerder) `mail.read` de `user.read`client de Microsoft Graph-machtigingen verleend en . Als de client een `scope=https://graph.microsoft.com/.default`verzoek indient voor , wordt er geen toestemmingsprompt weergegeven, ongeacht de inhoud van de geregistreerde machtigingen voor microsoft graph van de clienttoepassingen. Een token wordt geretourneerd met `mail.read` de `user.read`scopes en .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Voorbeeld 2: De gebruiker heeft geen machtigingen verleend tussen de client en de resource

In dit voorbeeld bestaat er geen toestemming voor de gebruiker tussen de client en Microsoft Graph. De client heeft `user.read` zich `contacts.read` geregistreerd voor de machtigingen en `https://vault.azure.net/user_impersonation`machtigingen, evenals de Azure Key Vault-scope. Wanneer de client een `scope=https://graph.microsoft.com/.default`token aanvraagt voor , `user.read`ziet `contacts.read`de gebruiker `user_impersonation` een toestemmingsscherm voor de , en de Key Vault-scopes. Het geretourneerde token `user.read` heeft `contacts.read` alleen de scopes en scopes erin en is alleen bruikbaar tegen Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>Voorbeeld 3: De gebruiker heeft toestemming gegeven en de client vraagt om extra scopes

In dit voorbeeld heeft de gebruiker `mail.read` al ingestemd met voor de client. De klant heeft `contacts.read` zich ingeschreven voor de scope in zijn registratie. Wanneer de klant een aanvraag `scope=https://graph.microsoft.com/.default` voor een `prompt=consent`token doet en toestemming vraagt via , dan ziet de gebruiker een toestemmingsscherm voor alle (en alleen) de machtigingen die door de toepassing zijn geregistreerd. `contacts.read`zal aanwezig zijn in het `mail.read` toestemmingsscherm, maar zal dat niet doen. Het geretourneerde token is voor `mail.read` Microsoft `contacts.read`Graph en bevat en .

### <a name="using-the-default-scope-with-the-client"></a>De /.default-scope met de client gebruiken

Er bestaat een `/.default` speciaal geval van de `/.default` scope wanneer een client zijn eigen scope aanvraagt. In het volgende voorbeeld wordt dit scenario aangetoond.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Dit produceert een toestemmingsscherm voor alle geregistreerde machtigingen (indien van `/.default`toepassing op basis van de bovenstaande beschrijvingen van toestemming en), retourneert vervolgens een id_token, in plaats van een toegangstoken.  Dit gedrag bestaat voor bepaalde oudere clients die van ADAL naar MSAL gaan en **mag niet** worden gebruikt door nieuwe clients die zich richten op het eindpunt van het Microsoft-identiteitsplatform.

### <a name="trailing-slash-and-default"></a>Trailing slash en /.default

Sommige bron-URI's hebben`https://contoso.com/` een `https://contoso.com`trailing slash (in tegenstelling tot), die problemen kan veroorzaken met tokenvalidatie.  Dit kan voornamelijk gebeuren bij het aanvragen`https://management.azure.com/`van een token voor Azure Resource Management ( ), die een trailing slash op hun resource URI heeft en vereist dat het aanwezig is wanneer het token wordt aangevraagd.  Dus, bij het aanvragen `https://management.azure.com/` van `/.default`een token `https://management.azure.com//.default` voor en het gebruik , moet u vragen - let op de dubbele slash!

In het algemeen - als u hebt gevalideerd dat het token wordt uitgegeven en het token wordt geweigerd door de API die het moet accepteren, u overwegen een tweede slash toe te voegen en opnieuw te proberen. Dit gebeurt omdat de inlogserver een token uitzendt `scope` met het `/.default` publiek dat overeenkomt met de URI's in de parameter - met verwijderd van het einde.  Als hiermee de trailing slash wordt verwijderd, verwerkt de inlogserver de aanvraag nog steeds en valideert deze tegen de bron URI, ook al komen ze niet meer overeen - dit is niet-standaard en mag niet worden ingeroepen door uw toepassing.

## <a name="troubleshooting-permissions-and-consent"></a>Machtigingen en toestemming voor het oplossen van problemen oplossen

Als u of de gebruikers van uw toepassing onverwachte fouten zien tijdens het toestemmingsproces, raadpleegt u dit artikel voor het oplossen van problemen: [Onverwachte fout bij het uitvoeren van toestemming voor een toepassing.](../manage-apps/application-sign-in-unexpected-user-consent-error.md)
