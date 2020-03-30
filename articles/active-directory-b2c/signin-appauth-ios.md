---
title: AppAuth gebruiken in een iOS-toepassing
titleSuffix: Azure AD B2C
description: Een iOS-app maken die AppAuth met Azure Active Directory B2C gebruikt om gebruikersidentiteiten te beheren en gebruikers te authenticeren.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c659280ebc8c91b53cbc3a176c84397edd942c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186825"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: aanmelden met een iOS-toepassing

Op het Microsoft Identity-platform wordt gebruikgemaakt van open standaarden, zoals OAuth2 en OpenID Connect. Het gebruik van een open standaard protocol biedt meer keuze voor ontwikkelaars bij het selecteren van een bibliotheek om te integreren met onze services. We hebben deze walkthrough en anderen vinden het leuk om ontwikkelaars te helpen met het schrijven van applicaties die verbinding maken met het Microsoft Identity-platform. De meeste bibliotheken die [de RFC6749 OAuth2-specificaties](https://tools.ietf.org/html/rfc6749) implementeren, kunnen verbinding maken met het Microsoft Identity-platform.

> [!WARNING]
> Microsoft biedt geen oplossingen voor bibliotheken van derden en heeft deze bibliotheken niet beoordeeld. In dit voorbeeld wordt gebruik gemaakt van een bibliotheek van derden genaamd AppAuth die is getest op compatibiliteit in basisscenario's met de Azure AD B2C. Problemen en functieaanvragen moeten worden gericht op het open-sourceproject van de bibliotheek. Raadpleeg [dit artikel](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) voor meer informatie.
>
>

Als u nieuw bent bij OAuth2 of OpenID Connect, heeft een groot deel van deze voorbeeldconfiguratie mogelijk niet veel zin voor u. U wordt geadviseerd eerst een beknopt [overzicht van het hier gedocumenteerde protocol te bekijken](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Een Azure AD B2C-directory maken
Voordat u Azure AD B2C kunt gebruiken, moet u een directory, of tenant, maken. Een map is een container voor al uw gebruikers, apps, groepen en meer. Als u nog geen directory hebt, [maakt u een B2C-directory](tutorial-create-tenant.md) voordat u verdergaat.

## <a name="create-an-application"></a>Een app maken

Registreer vervolgens een toepassing in uw Azure AD B2C-tenant. Dit geeft Azure AD de informatie die het nodig heeft om veilig te communiceren met uw app.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Neem de **toepassings-id (client) op** voor gebruik in een latere stap.

Neem ook uw aangepaste omleiding URI voor gebruik in een latere stap. Bijvoorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Uw gebruikersstromen maken
In Azure AD B2C wordt elke gebruikerservaring gedefinieerd door een [gebruikersstroom.](user-flow-overview.md) Deze applicatie bevat één identiteitservaring: een gecombineerde aanmelding en aanmelding. Wanneer u de gebruikersstroom maakt, moet u het als:

* Selecteer **onder Aanmeldingskenmerken**de **kenmerkWeergavenaam**.  U ook andere kenmerken selecteren.
* Selecteer **onder Toepassingsclaims**de naam van de **claimweergave** en **de object-id van de gebruiker**. U ook andere claims selecteren.
* Kopieer de **naam** van elke gebruikersstroom nadat u deze hebt gemaakt. Uw gebruikersnaam wordt vooraf `b2c_1_` vastgesteld wanneer u de gebruikersstroom opslaat.  U hebt de naam van de gebruikersstroom later nodig.

Nadat u uw gebruikersstromen hebt gemaakt, bent u klaar om uw app te bouwen.

## <a name="download-the-sample-code"></a>De voorbeeldcode downloaden
We hebben een werkend voorbeeld geleverd dat AppAuth gebruikt met Azure AD B2C [op GitHub.](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c) U de code downloaden en uitvoeren. Als u uw eigen Azure AD B2C-tenant wilt gebruiken, volgt u de instructies in de [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Dit voorbeeld is gemaakt door het volgen van de README-instructies van het [iOS AppAuth-project op GitHub.](https://github.com/openid/AppAuth-iOS) Raadpleeg de AppAuth README op GitHub voor meer informatie over hoe het voorbeeld en de bibliotheek werken.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Uw app wijzigen om Azure AD B2C met AppAuth te gebruiken

> [!NOTE]
> AppAuth ondersteunt iOS 7 en hoger.  Om sociale aanmeldingen op Google te ondersteunen, is sfSafariViewController echter nodig waarvoor iOS 9 of hoger nodig is.
>

### <a name="configuration"></a>Configuratie

U de communicatie met Azure AD B2C configureren door zowel het autorisatieeindpunt als de tokeneindpunt-URI's op te geven.  Om deze URI's te genereren, hebt u de volgende informatie nodig:
* Tenant-ID (bijvoorbeeld contoso.onmicrosoft.com)
* Gebruikersnaam (bijvoorbeeld B2C\_1\_SignUpIn)

Het tokeneindpunt URI kan worden gegenereerd\_door de\_tenant-id en de beleidsnaam in de volgende URL te vervangen:

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

Het autorisatieeindpunt URI kan worden gegenereerd\_door de\_tenant-id en de beleidsnaam in de volgende URL te vervangen:

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Voer de volgende code uit om het object AuthorizationServiceConfiguration te maken:

```objc
OIDServiceConfiguration *configuration =
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Machtigen

Na het configureren of ophalen van een autorisatieserviceconfiguratie kan een autorisatieaanvraag worden samengesteld. Als u het verzoek wilt maken, hebt u de volgende informatie nodig:

* Client-ID (APPLICATION ID) die u eerder hebt opgenomen. Bijvoorbeeld `00000000-0000-0000-0000-000000000000`.
* Aangepaste omleiding URI die u eerder hebt opgenomen. Bijvoorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Beide items hadden moeten worden opgeslagen toen u [uw app registreerde.](#create-an-application)

```objc
OIDAuthorizationRequest *request =
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow =
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

Als u uw toepassing wilt instellen om de omleiding naar de URI met het aangepaste schema te verwerken, moet u de lijst met 'URL-schema's' in uw Info.pList bijwerken:
* Open Info.pList.
* Plaats de muisaanwijzer op een rij als \+ 'Bundle OS Type Code' en klik op het symbool.
* Wijzig de naam van de nieuwe rij 'URL-typen'.
* Klik op de pijl links van 'URL-typen' om de boom te openen.
* Klik op de pijl links van 'Punt 0' om de boom te openen.
* Wijzig de naam eerste item onder punt 0 in 'URL-schema's'.
* Klik op de pijl links van 'URL-schema's' om de boom te openen.
* In de kolom 'Waarde' bevindt zich een leeg veld links van 'Item 0' onder 'URL-schema's'.  Stel de waarde in op het unieke schema van uw toepassing.  De waarde moet overeenkomen met het schema dat wordt gebruikt in redirectURL bij het maken van het object OIDAuthorizationRequest.  In de steekproef wordt de regeling "com.onmicrosoft.fabrikamb2c.exampleapp" gebruikt.

Raadpleeg de [AppAuth-handleiding](https://openid.github.io/AppAuth-iOS/) voor het voltooien van de rest van het proces. Als u snel aan de slag wilt met een werkende app, raadpleegt u [het voorbeeld.](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c) Volg de stappen in de [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) om uw eigen Azure AD B2C-configuratie in te voeren.

Wij staan altijd open voor feedback en suggesties! Als je problemen hebt met dit artikel, of aanbevelingen hebt voor het verbeteren van deze inhoud, stellen we je feedback onderaan de pagina op prijs. Voeg ze voor functieaanvragen toe aan [UserVoice.](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)
