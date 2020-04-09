---
title: Apps maken die zich aanmelden bij Azure AD-gebruikers
titleSuffix: Microsoft identity platform
description: Hier ziet u hoe u een multi-tenanttoepassing maakt die een gebruiker kan aanmelden vanuit een Azure Active Directory-tenant.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/17/2020
ms.author: ryanwi
ms.reviewer: jmprieur, lenalepa, sureshja, kkrishna
ms.custom: aaddev
ms.openlocfilehash: f22ecb13284eaf6fb2a833791b5563351ca19147
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884083"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>How to: Een Azure Active Directory-gebruiker aanmelden met het patroon van de toepassing met meerdere tenantn

Als u een SaaS-toepassing (Software as a Service) aanbiedt aan veel organisaties, u uw toepassing configureren om aanmeldingen te accepteren vanuit een Azure Active Directory-tenant (Azure AD). Deze configuratie wordt het *maken van uw toepassing multi-tenant*genoemd. Gebruikers in een Azure AD-tenant kunnen zich aanmelden bij uw toepassing nadat ze toestemming hebben gegeven om hun account met uw toepassing te gebruiken.

Als u een bestaande toepassing hebt die een eigen accountsysteem heeft of andere soorten aanmeldingen van andere cloudproviders ondersteunt, is het eenvoudig om Azure AD-aanmelding van elke tenant toe te voegen. Registreer uw app, voeg aanmeldingscode toe via OAuth2, OpenID Connect of SAML en plaats een [knop 'Aanmelden bij Microsoft'][AAD-App-Branding] in uw toepassing.

> [!NOTE]
> In dit artikel wordt ervan uitgegaan dat u al bekend bent met het bouwen van één tenanttoepassing voor Azure AD. Als u dat niet bent, begint u met een van de quickstarts op de startpagina van de [ontwikkelaarshandleiding.][AAD-Dev-Guide]

Er zijn vier eenvoudige stappen om uw toepassing om te zetten in een Azure AD-app met meerdere tenant's:

1. [Uw toepassingsregistratie bijwerken om multi-tenant te zijn](#update-registration-to-be-multi-tenant)
2. [Uw code bijwerken om aanvragen naar het /common endpoint te verzenden](#update-your-code-to-send-requests-to-common)
3. [Uw code bijwerken om meerdere emittentwaarden te verwerken](#update-your-code-to-handle-multiple-issuer-values)
4. [Inzicht in de toestemming van gebruikers en beheerders en de juiste codewijzigingen aanbrengen](#understand-user-and-admin-consent)

Laten we eens kijken naar elke stap in detail. U ook rechtstreeks naar het voorbeeld [springen Een SaaS-webtoepassing met meerdere tenant's maken die Microsoft Graph aanroept met Azure AD en OpenID Connect.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)

## <a name="update-registration-to-be-multi-tenant"></a>Registratie bijwerken om multi-tenant te zijn

Web-app/API-registraties in Azure AD zijn standaard één tenant. U uw registratie meerdere tenant maken door de **ingeschakelde accounttypen** te zoeken in het **verificatiedeelvenster** van uw toepassingsregistratie in de [Azure-portal][AZURE-portal] en deze in te stellen op **Accounts in een organisatiemap.**

Voordat een toepassing met meerdere tenant kan worden gemaakt, vereist Azure AD dat de App ID URI van de toepassing wereldwijd uniek is. De URI van de app-id is een van de manieren waarop een toepassing wordt geïdentificeerd in protocolberichten. Voor een toepassing met één tenant is het voldoende dat de URI van de app-id uniek is binnen die tenant. Voor een multitenant toepassing moet deze wereldwijd uniek zijn, zodat Azure Active Directory de toepassing in alle tenants kan vinden. Wereldwijde uniekheid wordt afgedwongen door te vereisen dat de URI van de app-id een hostnaam heeft die overeenkomt met een geverifieerd domein van de Azure Active Directory-tenant.

Apps die via de Azure-portal zijn gemaakt, hebben standaard een wereldwijd unieke URI-app-id ingesteld bij het maken van apps, maar u deze waarde wijzigen. Als de naam van uw tenant bijvoorbeeld contoso.onmicrosoft.com was, `https://contoso.onmicrosoft.com/myapp`is een geldige App ID URI . Als uw tenant een geverifieerd `contoso.com`domein van , dan een `https://contoso.com/myapp`geldige App ID URI zou ook . Als de URI van de app-id dit patroon niet volgt, mislukt het instellen van een multitenant toepassing.

> [!NOTE]
> Native clientregistraties en [Microsoft-identiteitsplatformtoepassingen](./active-directory-appmodel-v2-overview.md) zijn standaard multi-tenant. U hoeft geen actie te ondernemen om deze toepassingsregistraties multi-tenant te maken.

## <a name="update-your-code-to-send-requests-to-common"></a>Uw code bijwerken om aanvragen naar /common te verzenden

In één tenanttoepassing worden aanmeldingsaanvragen verzonden naar het aanmeldingspunt van de tenant. Bijvoorbeeld, voor contoso.onmicrosoft.com het eindpunt `https://login.microsoftonline.com/contoso.onmicrosoft.com`zou zijn: . Aanvragen die naar het eindpunt van een tenant worden verzonden, kunnen gebruikers (of gasten) in die tenant aanmelden bij toepassingen in die tenant.

Met een toepassing met meerdere tenants weet de toepassing niet van voorin van welke tenant de gebruiker afkomstig is, zodat u geen aanvragen naar het eindpunt van een tenant verzenden. In plaats daarvan worden aanvragen verzonden naar een eindpunt dat multiplexen voor alle Azure AD-tenants:`https://login.microsoftonline.com/common`

Wanneer het Microsoft-identiteitsplatform een aanvraag ontvangt op het gemeenschappelijk eindpunt, wordt de gebruiker in- en als gevolg daarvan ontdekt van welke tenant de gebruiker afkomstig is. Het /common endpoint werkt met alle verificatieprotocollen die worden ondersteund door de Azure AD: OpenID Connect, OAuth 2.0, SAML 2.0 en WS-Federation.

Het aanmeldingsantwoord op de toepassing bevat vervolgens een token dat de gebruiker vertegenwoordigt. De waarde van de uitgever in het token vertelt een toepassing van welke tenant de gebruiker afkomstig is. Wanneer een antwoord terugkeert van het /common endpoint, komt de waarde van de uitgever in het token overeen met de tenant van de gebruiker.

> [!IMPORTANT]
> Het /common endpoint is geen tenant en is geen emittent, het is gewoon een multiplexer. Bij het gebruik van /common moet de logica in uw toepassing om tokens te valideren worden bijgewerkt om hiermee rekening te houden.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Uw code bijwerken om meerdere emittentwaarden te verwerken

Webtoepassingen en web-API's ontvangen en valideren tokens van het Microsoft-identiteitsplatform.

> [!NOTE]
> Terwijl native clienttoepassingen tokens aanvragen en ontvangen van het Microsoft-identiteitsplatform, doen ze dit om ze naar API's te sturen, waar ze worden gevalideerd. Native applicaties valideren tokens niet en moeten ze als ondoorzichtig behandelen.

Laten we eens kijken hoe een toepassing tokens valideert die het ontvangt van het Microsoft-identiteitsplatform. Een enkele tenanttoepassing heeft normaal gesproken een eindpuntwaarde zoals:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

en gebruikt deze om een URL met metagegevens te construeren (in dit geval OpenID Connect) zoals:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

om twee kritieke informatie te downloaden die wordt gebruikt om tokens te valideren: de ondertekeningssleutels van de tenant en de waarde van de uitgever. Elke Azure AD-tenant heeft een unieke uitgeverwaarde van het formulier:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

waarbij de GUID-waarde de hernoemde veilige versie van de tenant-id van de tenant is. Als u de vorige koppeling `contoso.onmicrosoft.com`met metagegevens selecteert voor, u deze waarde van de uitgever in het document zien.

Wanneer een enkele tenanttoepassing een token valideert, controleert het de handtekening van het token op de ondertekeningssleutels uit het metagegevensdocument. Deze test maakt het mogelijk om ervoor te zorgen dat de waarde van de uitgever in het token overeenkomt met de waarde die in het metagegevensdocument is gevonden.

Omdat het /common endpoint niet overeenkomt met een tenant en geen uitgever is, heeft het, wanneer u de waarde van de uitgever in de metagegevens voor /common onderzoekt, een getemplatede URL in plaats van een werkelijke waarde:

    https://sts.windows.net/{tenantid}/

Daarom kan een multi-tenanttoepassing tokens niet alleen valideren door de `issuer` waarde van de uitgever in de metagegevens te matchen met de waarde in het token. Een multi-tenanttoepassing heeft logica nodig om te bepalen welke emittentwaarden geldig zijn en welke niet zijn gebaseerd op het tenant-id-gedeelte van de waarde van de uitgever. 

Als een toepassing met meerdere tenants bijvoorbeeld alleen aanmelding toestaat van specifieke tenants die zich hebben aangemeld `tid` voor hun service, moet deze de waarde van de uitgever of de claimwaarde in het token controleren om ervoor te zorgen dat de tenant in hun lijst met abonnees staat. Als een multi-tenanttoepassing alleen betrekking heeft op individuen en geen toegangsbeslissingen neemt op basis van tenants, kan deze de waarde van de uitgever volledig negeren.

In de voorbeelden met meerdere tenants is de validatie van de uitgever uitgeschakeld om elke Azure [AD-tenant][AAD-Samples-MT]in staat te stellen zich aan te melden.

## <a name="understand-user-and-admin-consent"></a>Inzicht in toestemming van gebruiker en beheerder

Als een gebruiker zich wil aanmelden bij een toepassing in Azure AD, moet de toepassing worden weergegeven in de tenant van de gebruiker. Hierdoor kan de organisatie dingen doen zoals het toepassen van uniek beleid wanneer gebruikers van hun tenant zich aanmelden bij de toepassing. Voor één tenanttoepassing is deze registratie eenvoudig; het is degene die gebeurt wanneer u de toepassing registreert in de [Azure-portal.][AZURE-portal]

Voor een toepassing met meerdere tenants wordt de eerste registratie voor de toepassing weergegeven in de Azure AD-tenant die door de ontwikkelaar wordt gebruikt. Wanneer een gebruiker van een andere tenant zich voor de eerste keer aanmeldt bij de toepassing, vraagt Azure AD hen toestemming te geven voor de door de toepassing gevraagde machtigingen. Als ze hiermee instemmen, wordt een weergave van de toepassing genaamd een *serviceprincipal* gemaakt in de tenant van de gebruiker en kan de aanmelding worden voortgezet. Er wordt ook een delegatie gemaakt in de map die de toestemming van de gebruiker voor de toepassing registreert. Zie [Toepassingsobjecten en servicehoofdobjecten][AAD-App-SP-Objects]voor meer informatie over de toepassings- en serviceprincipal-objecten en hoe deze zich tot elkaar verhouden.

![Illustreert toestemming voor app met één laag][Consent-Single-Tier]

Deze toestemmingservaring wordt beïnvloed door de door de toepassing gevraagde machtigingen. Microsoft-identiteitsplatform ondersteunt twee soorten machtigingen, alleen voor apps en gedelegeerd.

* Een gedelegeerde machtiging verleent een toepassing de mogelijkheid om als aangemelde gebruiker op te treden voor een subset van de dingen die de gebruiker kan doen. U een toepassing bijvoorbeeld de gedelegeerde toestemming geven om de ondertekende in de gebruikersagenda te lezen.
* Een app-only toestemming wordt rechtstreeks verleend aan de identiteit van de toepassing. U bijvoorbeeld een toepassing de app-only toestemming geven om de lijst met gebruikers in een tenant te lezen, ongeacht wie is aangemeld bij de toepassing.

Sommige machtigingen kunnen worden goedgekeurd door een gewone gebruiker, terwijl anderen toestemming van een tenantbeheerder vereisen. 

### <a name="admin-consent"></a>toestemming van de beheerder

Bij app-specifieke machtigingen is er altijd toestemming van een tenantbeheerder nodig. Als uw toepassing een app-only toestemming aanvraagt en een gebruiker zich bij de toepassing probeert aan te melden, wordt een foutbericht weergegeven waarin staat dat de gebruiker geen toestemming kan geven.

Voor bepaalde gedelegeerde machtigingen is ook toestemming van een tenantbeheerder vereist. De mogelijkheid om bijvoorbeeld terug te schrijven naar Azure AD als de aangemelde gebruiker vereist de toestemming van een tenantbeheerder. Net als app-only machtigingen, als een gewone gebruiker probeert aan te melden bij een toepassing die een gedelegeerde toestemming vraagt waarvoor beheerderstoestemming vereist is, ontvangt uw toepassing een foutmelding. Of een toestemming van de beheerder vereist, wordt bepaald door de ontwikkelaar die de bron heeft gepubliceerd en kan worden gevonden in de documentatie voor de bron. De documentatie over machtigingen voor de [Microsoft Graph-API][MSFT-Graph-permission-scopes] geeft aan welke machtigingen beheerderstoestemming vereisen.

Als uw toepassing machtigingen gebruikt waarvoor toestemming van de beheerder vereist is, moet u een gebaar hebben, zoals een knop of een koppeling waar de beheerder de actie kan starten. De aanvraag die uw toepassing voor deze actie verzendt, is het gebruikelijke `prompt=admin_consent` Autorisatieverzoek OAuth2/OpenID Connect dat ook de parameter querytekenreeks bevat. Zodra de beheerder heeft ingestemd en de serviceprincipal is gemaakt in de tenant van `prompt=admin_consent` de klant, hebben volgende aanmeldingsaanvragen de parameter niet nodig. Aangezien de beheerder heeft besloten dat de gevraagde machtigingen aanvaardbaar zijn, worden geen andere gebruikers in de tenant vanaf dat moment om toestemming gevraagd.

Tenantbeheerders kunnen uitschakelen dat normale gebruikers toestemming kunnen geven voor toepassingen. Als dit wordt uitgeschakeld, is er altijd beheerderstoestemming nodig om een toepassing in een tenant te kunnen gebruiken. Als u uw toepassing wilt testen met toestemming van de eindgebruiker uitgeschakeld, u de configuratieschakelaar vinden in de [Azure-portal][AZURE-portal] in de sectie **[Gebruikersinstellingen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** onder **Enterprise-toepassingen**.

De `prompt=admin_consent` parameter kan ook worden gebruikt door toepassingen die machtigingen aanvragen waarvoor geen beheerderstoestemming vereist is. Een voorbeeld van wanneer dit zou worden gebruikt is als de toepassing een ervaring vereist waarbij de tenantbeheerder zich één keer "aanmeldt" en er vanaf dat moment geen andere gebruikers om toestemming worden gevraagd.

Als een toepassing toestemming van de beheerder `prompt=admin_consent` vereist en een beheerder zich aanmeldt zonder dat de parameter wordt verzonden, wanneer de beheerder met succes instemt met de toepassing, geldt deze **alleen voor hun gebruikersaccount.** Regelmatige gebruikers kunnen zich nog steeds niet aanmelden of toestemming geven voor de toepassing. Deze functie is handig als u de tenantbeheerder de mogelijkheid wilt geven om uw toepassing te verkennen voordat u andere gebruikers toegang geeft.

> [!NOTE]
> Sommige toepassingen willen een ervaring waarbij regelmatige gebruikers in eerste instantie toestemming kunnen geven, en later kan de toepassing de beheerder betrekken en machtigingen aanvragen waarvoor beheerderstoestemming vereist is. Dit kun je niet doen met een v1.0-toepassingsregistratie in Azure AD. Met behulp van het Microsoft-identiteitsplatform (v2.0) kunnen toepassingen echter machtigingen aanvragen bij runtime in plaats van op registratietijd, waardoor dit scenario mogelijk is. Zie [Eindpunt van het Microsoft-identiteitsplatform voor][AAD-V2-Dev-Guide]meer informatie.

### <a name="consent-and-multi-tier-applications"></a>Toestemming en toepassingen met meerdere niveaus

Uw toepassing kan meerdere lagen hebben, elk vertegenwoordigd door een eigen registratie in Azure AD. Bijvoorbeeld een native toepassing die een web-API aanroept, of een webtoepassing die een web-API aanroept. In beide gevallen vraagt de client (native app of web app) machtigingen aan om de resource (web API) aan te roepen. Om de client met succes in te stemmen bij de tenant van een klant, moeten alle resources waaraan hij machtigingen vraagt, al bestaan in de tenant van de klant. Als niet aan deze voorwaarde is voldaan, retourneert Azure AD een fout dat de bron eerst moet worden toegevoegd.

#### <a name="multiple-tiers-in-a-single-tenant"></a>Meerdere lagen in één tenant

Dit kan een probleem zijn als uw logische toepassing bestaat uit twee of meer toepassingsregistraties, bijvoorbeeld een afzonderlijke client en resource. Hoe krijg je de bron eerst in de klanttenant? Azure AD dekt deze aanvraag door client en resource in één stap toe te laten. De gebruiker ziet de som van de door de client en de resource gevraagde machtigingen op de toestemmingspagina. Om dit gedrag mogelijk te maken, moet de registratie van `knownClientApplications` de toepassing van de bron de app-id van de client als een in het [toepassingsmanifest bevatten.][AAD-App-Manifest] Bijvoorbeeld:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Dit wordt aangetoond in een multi-tier native client calling web API-voorbeeld in de sectie [Gerelateerde inhoud](#related-content) aan het einde van dit artikel. Het volgende diagram geeft een overzicht van de toestemming voor een multi-tier app geregistreerd in een enkele tenant.

![Illustreert toestemming voor bekende client-app met meerdere lagen][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>Meerdere lagen in meerdere tenants

Een soortgelijk geval gebeurt als de verschillende lagen van een toepassing zijn geregistreerd in verschillende tenants. Denk bijvoorbeeld aan het geval van het bouwen van een native clienttoepassing die de Office 365 Exchange Online API aanroept. Om de native applicatie te ontwikkelen en later voor de native toepassing in de tenant van een klant te laten draaien, moet de Exchange Online-serviceprincipal aanwezig zijn. In dit geval moeten de ontwikkelaar en de klant Exchange Online kopen voor de serviceprincipal die in zijn huurders moet worden gemaakt.

Als het een API is die is gebouwd door een andere organisatie dan Microsoft, moet de ontwikkelaar van de API een manier bieden voor hun klanten om de toepassing toe te geven aan de tenants van hun klanten. Het aanbevolen ontwerp is voor de externe ontwikkelaar om de API zodanig te bouwen dat het ook kan functioneren als een webclient om aanmelding te implementeren. Om dit te doen:

1. Volg de eerdere secties om ervoor te zorgen dat de API de vereisten voor registratie/code voor multi-tenanttoepassingen implementeert.
2. Zorg er niet in dat de api de scopes/rollen van de API blootlegt, maar zorg ervoor dat de registratie de machtiging 'Aanmelden en gebruikersprofiel' bevat (standaard opgegeven).
3. Implementeer een aanmeldings-/aanmeldingspagina in de webclient en volg de richtlijnen [voor beheerderstoestemming.](#admin-consent)
4. Zodra de gebruiker toestemming geeft voor de toepassing, worden de koppelingen voor serviceprincipal en toestemmingsdelegatie in hun tenant gemaakt en kan de native toepassing tokens voor de API krijgen.

Het volgende diagram geeft een overzicht van de toestemming voor een multi-tier app geregistreerd in verschillende tenants.

![Illustreert toestemming voor multi-tier multi-party app][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>Toestemming intrekken

Gebruikers en beheerders kunnen op elk gewenst moment de toestemming voor uw aanvraag intrekken:

* Gebruikers trekken de toegang tot afzonderlijke toepassingen in door ze uit hun lijst [met toegangspaneeltoepassingen te][AAD-Access-Panel] verwijderen.
* Beheerders trekken de toegang tot toepassingen in door ze te verwijderen met behulp van de sectie [Enterprise-toepassingen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) van de [Azure-portal.][AZURE-portal]

Als een beheerder instemt met een toepassing voor alle gebruikers in een tenant, kunnen gebruikers de toegang niet afzonderlijk intrekken. Alleen de beheerder kan de toegang intrekken, en alleen voor de hele toepassing.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Multi-tenant toepassingen en caching access tokens

Multitenant-toepassingen kunnen ook toegangstokens krijgen om API's aan te roepen die zijn beschermd door Azure AD. Een veelvoorkomende fout bij het gebruik van de Active Directory Authentication Library (ADAL) met een multi-tenant toepassing is om in eerste instantie een token aan te vragen voor een gebruiker die /common gebruikt, een antwoord te ontvangen en vervolgens een volgend token aan te vragen voor diezelfde gebruiker die ook /common gebruikt. Omdat het antwoord van Azure AD afkomstig is van een tenant, niet /gewoon, slaat ADAL het token in de cache als afkomstig van de tenant. De daaropvolgende oproep aan /common om een toegangstoken voor de gebruiker te krijgen, mist de cache-vermelding en de gebruiker wordt gevraagd zich opnieuw aan te melden. Om te voorkomen dat de cache ontbreekt, moet u ervoor zorgen dat volgende oproepen voor een reeds aangemelde gebruiker worden uitgevoerd naar het eindpunt van de tenant.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een toepassing maken die zich kan aanmelden bij een gebruiker vanuit een Azure AD-tenant. Nadat u Single Sign-On (SSO) hebt ingeschakeld tussen uw app en Azure AD, u uw toepassing ook bijwerken om toegang te krijgen tot API's die worden blootgesteld door Microsoft-bronnen, zoals Office 365. Hiermee u een persoonlijke ervaring in uw toepassing bieden, zoals het tonen van contextuele informatie aan de gebruikers, zoals hun profielfoto of hun volgende agenda-afspraak. Ga voor meer informatie over het voeren van API-aanroepen naar Azure AD- en Office 365-services zoals Exchange, SharePoint, OneDrive, OneNote en meer naar [Microsoft Graph API][MSFT-Graph-overview].

## <a name="related-content"></a>Gerelateerde inhoud

* [Voorbeeld van toepassingen met meerdere tenant's](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)
* [Huisstijlrichtlijnen voor apps][AAD-App-Branding]
* [Toepassingsobjecten en servicehoofdobjecten][AAD-App-SP-Objects]
* [Toepassingen integreren met Azure Active Directory][AAD-Integrating-Apps]
* [Overzicht van het Consent Framework][AAD-Consent-Overview]
* [Machtigingen voor Microsoft Graph API-machtigingen][MSFT-Graph-permission-scopes]

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: https://docs.microsoft.com/samples/browse/?products=azure-active-directory
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://developer.microsoft.com/graph/docs/overview/overview
[MSFT-Graph-permission-scopes]: https://developer.microsoft.com/graph/docs/concepts/permissions_reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-single-tier.svg
[Consent-Multi-Tier-Known-Client]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-known-clients.svg
[Consent-Multi-Tier-Multi-Party]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-multi-party.svg

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AAD-V2-Dev-Guide]: v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
