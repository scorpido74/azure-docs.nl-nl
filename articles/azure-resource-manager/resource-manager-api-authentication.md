---
title: Verificatie en Resource Manager Azure Active Directory | Microsoft Docs
description: Een hand leiding voor ontwikkel aars voor verificatie met de Azure Resource Manager-API en Azure Active Directory voor het integreren van een app met andere Azure-abonnementen.
author: dushyantgill
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: dugill
ms.openlocfilehash: 033f3ca9ca79903f884c625dc694b06a3e4fd04c
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263006"
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Resource Manager-verificatie-API gebruiken voor toegang tot abonnementen

Als u een software ontwikkelaar bent die een app moet maken die de Azure-resources van een klant beheert, laat dit artikel u zien hoe u met de Azure Resource Manager-Api's kunt verifiëren en toegang kunt krijgen tot bronnen in andere abonnementen.

Uw app kan op verschillende manieren toegang krijgen tot de Resource Manager-Api's:

1. **Gebruikers-en app-toegang**: voor apps die toegang hebben tot bronnen voor een aangemelde gebruiker. Deze benadering werkt voor apps, zoals web apps en opdracht regel Programma's, die alleen betrekking hebben op ' interactief beheer ' van Azure-resources.
2. **Alleen app-toegang**: voor apps die daemon-services en geplande taken uitvoeren. De identiteit van de app wordt direct toegang verleend tot de resources. Deze benadering werkt voor apps die lange termijn toegang tot Azure (zonder toezicht) nodig hebben.

In dit artikel vindt u stapsgewijze instructies voor het maken van een app die gebruikmaakt van beide verificatie methoden. U ziet hoe u elke stap kunt uitvoeren met REST API C#of. De volledige ASP.NET MVC-toepassing is beschikbaar op [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

## <a name="what-the-web-app-does"></a>Wat is de web-app?

De web-app:

1. Meldt zich aan bij een Azure-gebruiker.
2. De gebruiker wordt gevraagd om de web-app toegang te verlenen tot de Resource Manager.
3. Hiermee wordt het toegangs token voor gebruikers en apps opgehaald voor toegang tot Resource Manager.
4. Maakt gebruik van token (uit stap 3) om de service-principal van de app toe te wijzen aan een rol in het abonnement. Met deze stap krijgt de app lange termijn toegang tot het abonnement.
5. Hiermee wordt het toegangs token voor alleen apps opgehaald.
6. Maakt gebruik van token (uit stap 5) voor het beheren van resources in het abonnement via Resource Manager.

Hier volgt de stroom van de webtoepassing.

![Resource Manager-verificatie stroom](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Als gebruiker geeft u de abonnements-ID op voor het abonnement dat u wilt gebruiken:

![abonnements-ID opgeven](./media/resource-manager-api-authentication/sample-ux-1.png)

Selecteer het account dat moet worden gebruikt voor het aanmelden.

![account selecteren](./media/resource-manager-api-authentication/sample-ux-2.png)

Geef uw referenties op.

![referenties opgeven](./media/resource-manager-api-authentication/sample-ux-3.png)

De app toegang verlenen tot uw Azure-abonnementen:

![Toegang verlenen](./media/resource-manager-api-authentication/sample-ux-4.png)

Uw verbonden abonnementen beheren:

![Abonnement verbinden](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Toepassing registreren
Voordat u begint met het coderen, registreert u uw web-app met Azure Active Directory (AD). De app-registratie maakt een centrale identiteit voor uw app in azure AD. Het bevat basis informatie over uw toepassing, zoals OAuth-client-ID, antwoord-Url's en referenties die uw toepassing gebruikt voor verificatie en toegang tot Azure Resource Manager-Api's. De app-registratie registreert ook de verschillende gedelegeerde machtigingen die uw toepassing nodig heeft bij het openen van micro soft-Api's voor de gebruiker.

Als u uw app wilt registreren, raadpleegt u [Quick Start: een toepassing registreren bij het micro soft Identity-platform](../active-directory/develop/quickstart-register-app.md). Geef uw app een naam en selecteer **accounts in elke organisatie Directory** voor de ondersteunde account typen. Geef voor omleidings-URL een domein op dat is gekoppeld aan uw Azure Active Directory.

Als u zich wilt aanmelden als AD-toepassing, hebt u de toepassings-ID en een geheim nodig. De toepassings-ID wordt weer gegeven in het overzicht voor de toepassing. Als u een geheim wilt maken en API-machtigingen wilt aanvragen, raadpleegt u [Quick Start: een client toepassing configureren voor toegang tot Web-api's](../active-directory/develop/quickstart-configure-app-access-web-apis.md). Geef een nieuw client geheim op. Selecteer voor API-machtigingen **Azure Service Management**. Selecteer **gedelegeerde machtigingen** en **user_impersonation**.

### <a name="optional-configuration---certificate-credential"></a>Optionele configuratie: certificaat referentie
Azure AD biedt ook ondersteuning voor certificaat referenties voor toepassingen: u maakt een zelfondertekend certificaat, behoudt de persoonlijke sleutel en voegt de open bare sleutel toe aan uw Azure AD-toepassings registratie. Voor verificatie verzendt uw toepassing een kleine lading naar Azure AD die is ondertekend met uw persoonlijke sleutel. Azure AD valideert de hand tekening met behulp van de open bare sleutel die u hebt geregistreerd.

Zie [Azure PowerShell gebruiken om een service-principal te maken voor toegang tot resources](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority) of [Azure CLI gebruiken om een service-principal te maken voor toegang tot resources](resource-group-authenticate-service-principal-cli.md)voor meer informatie over het maken van een AD-app met een certificaat.

## <a name="get-tenant-id-from-subscription-id"></a>Tenant-ID ophalen van abonnements-ID
Als u een token wilt aanvragen dat kan worden gebruikt om Resource Manager aan te roepen, moet uw toepassing weten wat de Tenant-ID is van de Azure AD-Tenant die als host fungeert voor het Azure-abonnement. Waarschijnlijk weten uw gebruikers hun abonnement-Id's, maar kunnen ze hun Tenant-Id's niet weten voor Azure Active Directory. Als u de Tenant-ID van de gebruiker wilt ophalen, vraagt u de gebruiker om de abonnements-ID. Geef de abonnements-ID op bij het verzenden van een aanvraag over het abonnement:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

De aanvraag is mislukt omdat de gebruiker zich nog niet heeft aangemeld, maar u kunt de Tenant-ID uit het antwoord ophalen. In die uitzonde ring haalt u de Tenant-ID op uit de waarde voor de reactie header voor **www-verificatie**. U ziet deze implementatie in de methode [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) .

## <a name="get-user--app-access-token"></a>Gebruikers-en toegangs token voor apps ophalen
Uw toepassing stuurt de gebruiker door naar Azure AD met een OAuth 2,0-autorisatie aanvraag-om de referenties van de gebruiker te verifiëren en een autorisatie code terug te krijgen. Uw toepassing gebruikt de autorisatie code voor het ophalen van een toegangs token voor de Resource Manager. Met de methode [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) wordt de autorisatie aanvraag gemaakt.

In dit artikel worden de REST API aanvragen voor het verifiëren van de gebruiker weer gegeven. U kunt ook hulp bibliotheken gebruiken om in uw code te verifiëren. Zie voor meer informatie over deze bibliotheken [Azure Active Directory-verificatie bibliotheken](../active-directory/active-directory-authentication-libraries.md). Zie [Azure Active Directory hand leiding voor ontwikkel aars](../active-directory/develop/v1-overview.md)voor meer informatie over het integreren van identiteits beheer in een toepassing.

### <a name="auth-request-oauth-20"></a>Verificatie aanvraag (OAuth 2,0)
Een open-ID-aanvraag voor het autoriseren van een verbinding/OAuth 2.0 uitgeven aan het Azure AD-autorisatie-eind punt:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

De query reeks parameters die beschikbaar zijn voor deze aanvraag, worden beschreven in het artikel [een autorisatie code aanvragen](../active-directory/develop/v1-protocols-oauth-code.md#request-an-authorization-code) .

In het volgende voor beeld ziet u hoe u de OAuth 2.0-verificatie kunt aanvragen:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD verifieert de gebruiker en vraagt, indien nodig, de gebruiker om toestemming te geven voor de app. De autorisatie code wordt geretourneerd naar de antwoord-URL van uw toepassing. Afhankelijk van de aangevraagde response_mode, stuurt Azure AD de gegevens terug in de query reeks of als post gegevens.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Verificatie aanvraag (Open-ID-verbinding)
Als u niet alleen Azure Resource Manager voor de gebruiker wilt gebruiken, maar de gebruiker ook de mogelijkheid wilt geven om zich aan te melden bij uw toepassing met behulp van hun Azure AD-account, geeft u een aanvraag Open ID Connect Authorization op. Met open ID Connect ontvangt uw toepassing ook een id_token van Azure AD die uw app kan gebruiken om zich aan te melden bij de gebruiker.

De query reeks parameters die beschikbaar zijn voor deze aanvraag, worden beschreven in het artikel [de aanmeldings aanvraag verzenden](../active-directory/develop/v1-protocols-openid-connect-code.md#send-the-sign-in-request) .

Een voor beeld van een open ID-verbindings aanvraag is:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD verifieert de gebruiker en vraagt, indien nodig, de gebruiker om toestemming te geven voor de app. De autorisatie code wordt geretourneerd naar de antwoord-URL van uw toepassing. Afhankelijk van de aangevraagde response_mode, stuurt Azure AD de gegevens terug in de query reeks of als post gegevens.

Een voor beeld van een open-ID-verbindings reactie is:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Token aanvraag (OAuth 2.0 code subsidie stroom)
Nu uw toepassing de autorisatie code van Azure AD heeft ontvangen, is het tijd om het toegangs token voor Azure Resource Manager op te halen.  Een OAuth 2.0-aanvraag voor het toekennen van tokens aanvragen verzenden naar het Azure AD-token eindpunt:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

De query reeks parameters die beschikbaar zijn voor deze aanvraag, worden beschreven in het artikel [de autorisatie code gebruiken](../active-directory/develop/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) .

In het volgende voor beeld wordt een aanvraag voor code Grant token met wachtwoord referentie weer gegeven:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Bij het werken met certificaat referenties maakt u een JSON Web Token (JWT) en ondertekenen (RSA SHA256) met behulp van de persoonlijke sleutel van de certificaat referentie van uw toepassing. Het bouwen van dit token wordt weer gegeven in de [client referentie stroom](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#second-case-access-token-request-with-a-certificate).

Zie de [specificatie Open ID Connect](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) voor meer informatie over client verificatie.

In het volgende voor beeld wordt een aanvraag voor code Grant token met certificaat referentie weer gegeven:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Een voorbeeld reactie voor code subsidie token:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Reactie van code subsidie token afhandelen
Een geslaagd token antwoord bevat het toegangs token (gebruiker + app) voor Azure Resource Manager. Uw toepassing gebruikt dit toegangs token voor toegang tot Resource Manager voor de gebruiker. De levens duur van toegangs tokens die zijn uitgegeven door Azure AD is een uur. Het is niet waarschijnlijk dat uw webtoepassing het toegangs token (gebruikers en apps) moet vernieuwen. Als het toegangs token moet worden vernieuwd, gebruikt u het vernieuwings token dat uw toepassing in het token antwoord ontvangt. Een OAuth 2.0-token aanvraag op het Azure AD-token eindpunt plaatsen:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

De para meters voor gebruik met de vernieuwings aanvraag worden beschreven bij [het vernieuwen van het toegangs token](../active-directory/develop/v1-protocols-oauth-code.md#refreshing-the-access-tokens).

In het volgende voor beeld ziet u hoe u het vernieuwings token gebruikt:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Hoewel het vernieuwen van tokens kan worden gebruikt om nieuwe toegangs tokens voor Azure Resource Manager op te halen, zijn ze niet geschikt voor offline toegang door uw toepassing. De levens duur van het vernieuwings token is beperkt en de vernieuwings tokens zijn gebonden aan de gebruiker. Als de gebruiker de organisatie verlaat, verliest de toepassing die het vernieuwings token gebruikt de toegang. Deze benadering is niet geschikt voor toepassingen die door teams worden gebruikt voor het beheren van hun Azure-resources.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Controleren of de gebruiker toegang tot het abonnement kan toewijzen
Uw toepassing heeft nu een token voor toegang tot Azure Resource Manager voor de gebruiker. De volgende stap is het verbinden van uw app met het abonnement. Nadat u verbinding hebt gemaakt, kan uw app deze abonnementen beheren, zelfs als de gebruiker niet aanwezig is (lange termijn off-line toegang).

Voor elk abonnement dat u wilt verbinden, roept u de machtigingen van de [Resource Manager List](https://docs.microsoft.com/rest/api/authorization/permissions) -API aan om te bepalen of de gebruiker toegangs beheer rechten heeft voor het abonnement.

De methode [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) van de ASP.NET MVC-voor beeld-app implementeert deze aanroep.

In het volgende voor beeld ziet u hoe u de machtigingen van een gebruiker voor een abonnement kunt aanvragen. 83cfe939-2402-4581-b761-4f59b0a041e4 is de ID van het abonnement.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Een voor beeld van het antwoord om de machtigingen van de gebruiker op te halen voor het abonnement is:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

De permissions-API retourneert meerdere machtigingen. Elke machtiging bestaat uit toegestane acties (**acties**) en niet-toegestane acties (**verblijvend**). Als er een actie aanwezig is in de toegestane acties van elke machtiging en niet aanwezig zijn in de niet-toegestane acties van die machtiging, mag de gebruiker die actie uitvoeren. **micro soft. Authorization/roleassignments/write** is de actie waarmee toegangs beheer rechten worden verleend. Uw toepassing moet het resultaat van de machtigingen parseren om te zoeken naar een regex match voor deze actie teken reeks in de **acties** **en de** verhoudingen van elke machtiging.

## <a name="get-app-only-access-token"></a>Alleen app-toegangs Token ophalen
Nu weet u of de gebruiker toegang kan toewijzen aan het Azure-abonnement. De volgende stappen zijn:

1. Wijs de juiste RBAC-rol toe aan de identiteit van uw toepassing voor het abonnement.
2. Valideer de toegangs toewijzing door een query uit te geven voor de machtiging van de toepassing voor het abonnement of door Resource Manager te openen met een alleen-app-token.
3. Registreer de verbinding in de gegevens structuur ' Connected abonnementen ' van uw toepassingen. de ID van het abonnement wordt bewaard.

Laten we eens kijken naar de eerste stap. Om de juiste RBAC-rol toe te wijzen aan de identiteit van de toepassing, moet u het volgende bepalen:

* De object-ID van de identiteit van uw toepassing in de Azure Active Directory van de gebruiker
* De id van de RBAC-rol die uw toepassing vereist voor het abonnement

Wanneer uw toepassing een gebruiker van een Azure AD verifieert, wordt er een Service-Principal-object voor uw toepassing gemaakt in die Azure AD. Met Azure kunnen RBAC-rollen worden toegewezen aan service-principals om direct toegang te verlenen tot de bijbehorende toepassingen op Azure-resources. Deze actie is precies wat u wilt doen. Vraag de Azure AD-Graph API om de id van de service-principal van uw toepassing in de Azure AD van de aangemelde gebruiker te bepalen.

U hebt alleen een toegangs token voor Azure Resource Manager: u hebt een nieuw toegangs token nodig om de Azure AD-Graph API aan te roepen. Elke toepassing in azure AD is gemachtigd om een eigen Service-Principal-object op te vragen, zodat een alleen-app-toegangs token voldoende is.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Alleen app-toegangs token verkrijgen voor Azure AD-Graph API

Als u uw app wilt verifiëren en een token wilt ontvangen voor Azure AD Graph API, geeft u een client referentie een OAuth 2.0-stroom token aanvraag door aan Azure AD token-eind punt (**https: \//login. microsoftonline. com/{directory_domain_name}/OAuth2/token**).

De methode [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) van de voorbeeld toepassing ASP.NET MVC haalt een toegangs token voor alleen apps op voor Graph API met behulp van de Active Directory Authentication Library voor .net.

De query reeks parameters die beschikbaar zijn voor deze aanvraag, worden beschreven in het artikel [een toegangs token aanvragen](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#request-an-access-token) .

Een voorbeeld aanvraag voor het token voor de toekenning van client referenties:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Een voorbeeld antwoord voor het token voor de toekenning van client referenties:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>ObjectId ophalen van de toepassings Service-Principal in de Azure AD-gebruiker
Gebruik nu het token voor alleen app-toegang [om de object](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) -id van de service-principal van de toepassing in de map op te vragen.

De methode [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) van de voorbeeld toepassing ASP.NET MVC implementeert deze aanroep.

In het volgende voor beeld ziet u hoe u de service-principal van een toepassing aanvraagt. a0448380-c346-4f9f-b897-c18733de9394 is de client-ID van de toepassing.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

In het volgende voor beeld ziet u een reactie op de aanvraag voor de service-principal van een toepassing

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Azure RBAC-rol-id ophalen
Als u de juiste RBAC-rol aan uw Service-Principal wilt toewijzen, moet u de id van de Azure RBAC-rol bepalen.

De juiste RBAC-rol voor uw toepassing:

* Als uw toepassing alleen het abonnement bewaakt zonder wijzigingen aan te brengen, hebt u alleen lezer-machtigingen nodig voor het abonnement. Wijs de rol van **lezer** toe.
* Als uw toepassing Azure het abonnement beheert, entiteiten maakt/wijzigt of verwijdert, is een van de Inzender machtigingen vereist.
  * Als u een bepaald type resource wilt beheren, wijst u de resource-specifieke Inzender rollen toe (Inzender voor virtuele machines, Virtual Network Inzender, Inzender voor opslag accounts, enzovoort).
  * Als u een resource type wilt beheren, wijst u de rol **Inzender** toe.

De roltoewijzing voor uw toepassing is zichtbaar voor gebruikers. Selecteer hiervoor de minimale vereiste bevoegdheid.

Roep de [functie definitie-API van Resource Manager](https://docs.microsoft.com/rest/api/authorization/roledefinitions) aan om alle Azure RBAC-rollen weer te geven en herhaal vervolgens het resultaat om de roldefinitie op naam te vinden.

De methode [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) van de ASP.NET MVC-voor beeld-app implementeert deze aanroep.

In het volgende voor beeld van een aanvraag ziet u hoe u de Azure RBAC-rol-id kunt ophalen. 09cbd307-aa71-4aca-b346-5f253e6e3ebb is de ID van het abonnement.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

Het antwoord heeft de volgende indeling:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

U hoeft deze API niet voortdurend aan te roepen. Wanneer u de bekende GUID van de roldefinitie hebt bepaald, kunt u de roldefinitie-ID als volgt maken:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Hier volgen de id's van veelgebruikte ingebouwde rollen:

| Rol | GPT |
| --- | --- |
| Lezer |acdd72a7-3385-48ef-bd42-f606fba81ae7 |
| Inzender |b24988ac-6180-42a0-ab88-20f7382dd24c |
| Inzender voor virtuele machines |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Inzender Virtual Network |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Inzender voor opslag accounts |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Website bijdrager |de139f84-1756-47ae-9be6-808fbbe84772 |
| Inzender voor webabonnementen |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| Inzender SQL Server |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| Inzender voor SQL-data base |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>RBAC-rol toewijzen aan toepassing
U hebt alles wat u nodig hebt om de juiste RBAC-rol toe te wijzen aan uw service-principal met behulp van de [Resource Manager-functie](https://docs.microsoft.com/rest/api/authorization/roleassignments) voor het maken van roltoewijzings-API.

De methode [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) van de ASP.NET MVC-voor beeld-app implementeert deze aanroep.

Een voorbeeld aanvraag om een RBAC-rol toe te wijzen aan de toepassing:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

In de aanvraag worden de volgende waarden gebruikt:

| GUID | Beschrijving |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |de ID van het abonnement |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |de object-ID van de service-principal van de toepassing |
| b24988ac-6180-42a0-ab88-20f7382dd24c |de ID van de rol Inzender |
| 4f87261d-2816-465d-8311-70a27558df4c |Er is een nieuwe GUID gemaakt voor de nieuwe roltoewijzing |

Het antwoord heeft de volgende indeling:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Alleen app-toegangs token voor Azure Resource Manager ophalen
U kunt controleren of de app toegang heeft tot het abonnement door een test taak uit te voeren op het abonnement met behulp van een token dat alleen voor apps is.

Als u een toegangs token voor app wilt ophalen, volgt u de instructies in de sectie [alleen app-alleen-apps ophalen voor Azure AD Graph API](#app-azure-ad-graph), met een andere waarde voor de para meter resource:

    https://management.core.windows.net/

De methode [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) van de voorbeeld toepassing ASP.NET MVC haalt een toegangs token voor alleen apps op voor Azure Resource Manager met behulp van de Active Directory Authentication Library voor .net.

#### <a name="get-applications-permissions-on-subscription"></a>De machtigingen van de toepassing voor het abonnement ophalen
Als u wilt controleren of uw toepassing toegang heeft tot een Azure-abonnement, kunt u ook de API voor de [Resource Manager-machtigingen](https://docs.microsoft.com/rest/api/authorization/permissions) aanroepen. Deze benadering is vergelijkbaar met de manier waarop u hebt vastgesteld of de gebruiker toegangs beheer rechten heeft voor het abonnement. Maar deze keer roept u de machtigings-API aan met het token alleen-app-toegang dat u in de vorige stap hebt ontvangen.

De methode [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) van de ASP.NET MVC-voor beeld-app implementeert deze aanroep.

## <a name="manage-connected-subscriptions"></a>Verbonden abonnementen beheren
Wanneer de juiste RBAC-rol is toegewezen aan de service-principal van uw toepassing op het abonnement, kan uw toepassing de bewaking houden en beheren met behulp van toegangs tokens voor app-alleen voor Azure Resource Manager.

Als de eigenaar van een abonnement de roltoewijzing van uw toepassing verwijdert met behulp van de portal of opdracht regel Programma's, heeft uw toepassing geen toegang meer tot dat abonnement. In dat geval moet u de gebruiker op de hoogte stellen dat de verbinding met het abonnement is verholpen van buiten de toepassing en ze een optie geven om de verbinding te herstellen. Als u ' herstellen ' opgeeft, wordt de roltoewijzing die offline is verwijderd, opnieuw gemaakt.

Net zoals u hebt ingeschakeld dat de gebruiker abonnementen verbindt met uw toepassing, moet u de gebruiker toestaan om de abonnementen te verbreken. Vanuit een toegangs beheer punt van de weer gave verbreekt de verbinding met het verwijderen van de roltoewijzing die de service-principal van de toepassing op het abonnement heeft. Eventueel kan elke status in de toepassing voor het abonnement ook worden verwijderd.
Alleen gebruikers met de machtiging toegangs beheer voor het abonnement kunnen het abonnement loskoppelen.

De [methode RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) van de ASP.NET MVC-voor beeld-app implementeert deze aanroep.

Dat zijn IT-gebruikers kunnen nu eenvoudig hun Azure-abonnementen verbinden en beheren met uw toepassing.
