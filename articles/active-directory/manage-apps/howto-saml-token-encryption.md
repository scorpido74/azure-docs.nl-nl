---
title: SAML-tokenversleuteling in Azure Active Directory
description: Meer informatie over het configureren van Azure Active Directory SAML-tokenversleuteling.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: mimart
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0082d841faf22745e609d38444f4a97553b3c867
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365863"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption"></a>How to: Azure AD SAML-tokenversleuteling configureren

> [!NOTE]
> Tokenversleuteling is een premium-functie voor Azure Active Directory (Azure AD). Zie [Azure AD-prijzen](https://azure.microsoft.com/pricing/details/active-directory/)voor meer informatie over Azure AD-edities, -functies en -prijzen.

SAML-tokenversleuteling maakt het gebruik van versleutelde SAML-beweringen mogelijk met een toepassing die deze ondersteunt. Wanneer Azure AD is geconfigureerd voor een toepassing, versleutelt het de SAML-beweringen die voor die toepassing worden uitgezonden met behulp van de openbare sleutel die is verkregen uit een certificaat dat is opgeslagen in Azure AD. De toepassing moet de overeenkomende privésleutel gebruiken om het token te decoderen voordat het kan worden gebruikt als bewijs van verificatie voor de aangemelde gebruiker.

Het versleutelen van de SAML-beweringen tussen Azure AD en de toepassing biedt extra zekerheid dat de inhoud van het token niet kan worden onderschept en persoonlijke of bedrijfsgegevens worden gecompromitteerd.

Zelfs zonder tokenversleuteling worden Azure AD SAML-tokens nooit duidelijk doorgegeven aan het netwerk. Azure AD vereist dat tokenrequest/response-uitwisselingen plaatsvinden via versleutelde HTTPS/TLS-kanalen, zodat communicatie tussen de IDP, browser en toepassing plaatsvindt via versleutelde koppelingen. Houd rekening met de waarde van tokenversleuteling voor uw situatie in vergelijking met de overhead van het beheren van extra certificaten.   

Als u tokenversleuteling wilt configureren, moet u een X.509-certificaatbestand uploaden dat de openbare sleutel bevat voor het Azure AD-toepassingsobject dat de toepassing vertegenwoordigt. Om het X.509-certificaat te verkrijgen, u het downloaden van de toepassing zelf, of het downloaden van de leverancier van de toepassing in gevallen waarin de leverancier van de toepassing versleutelingssleutels verstrekt of in gevallen waarin de toepassing verwacht dat u een privésleutel verstrekt, kan dit gemaakt met behulp van cryptografietools, het gedeelte met de privésleutel die is geüpload naar het sleutelarchief van de toepassing en het bijbehorende certificaat met openbare sleutels dat is geüpload naar Azure AD.

Azure AD gebruikt AES-256 om de SAML-beweringsgegevens te versleutelen.

## <a name="configure-saml-token-encryption"></a>SAML-tokenversleuteling configureren

Voer de volgende stappen uit om SAML-tokenversleuteling te configureren:

1. Verkrijg een certificaat met openbare sleutel dat overeenkomt met een privésleutel die is geconfigureerd in de toepassing.

    Maak een asymmetrisch sleutelpaar om te gebruiken voor versleuteling. Of, als de toepassing een openbare sleutel levert om te gebruiken voor versleuteling, volg dan de instructies van de toepassing om het X.509-certificaat te downloaden.

    De openbare sleutel moet worden opgeslagen in een X.509-certificaatbestand in .cer-indeling.

    Als de toepassing een sleutel gebruikt die u voor uw instantie maakt, volgt u de instructies van uw toepassing voor het installeren van de privésleutel die de toepassing zal gebruiken om tokens te decoderen van uw Azure AD-tenant.

1. Voeg het certificaat toe aan de toepassingsconfiguratie in Azure AD.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Tokenversleuteling configureren in de Azure-portal

U het openbare cert toevoegen aan uw toepassingsconfiguratie binnen de Azure-portal.

1. Ga naar de [Azure-portal.](https://portal.azure.com)

1. Ga naar het **Azure Active Directory > Enterprise-toepassingsblad** en selecteer vervolgens de toepassing waarvoor u tokenversleuteling wilt configureren.

1. Selecteer **tokenversleuteling**op de pagina van de toepassing .

    ![Tokenversleutelingsoptie in de Azure-portal](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > De optie **Tokenversleuteling** is alleen beschikbaar voor SAML-toepassingen die zijn ingesteld vanuit het **Bedrijfstoepassingsblad** in de Azure-portal, vanuit de Toepassingsgalerie of een niet-Galerij-app. Voor andere toepassingen is deze menuoptie uitgeschakeld. Voor toepassingen die zijn geregistreerd via de ervaring **met app-registraties** in de Azure-portal, u versleuteling voor SAML-tokens configureren via het toepassingsmanifest, via Microsoft Graph of via PowerShell.

1. Selecteer op de pagina **Tokenversleuteling** de optie **Certificaat importeren** om het .cer-bestand te importeren dat uw openbare X.509-certificaat bevat.

    ![Het .cer-bestand importeren dat het X.509-certificaat bevat](./media/howto-saml-token-encryption/import-certificate-small.png)

1. Zodra het certificaat is geïmporteerd en de privésleutel is geconfigureerd voor gebruik aan de toepassingszijde, activeert u versleuteling door de **status ...** naast de duimafdrukstatus te selecteren en selecteert u **Tokenversleuteling activeren** in de opties in het vervolgkeuzemenu.

1. Selecteer **Ja** om de activering van het tokenversleutelingscertificaat te bevestigen.

1. Controleer of de SAML-beweringen die voor de toepassing worden uitgezonden, zijn versleuteld.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Tokenversleuteling in de Azure-portal deactiveren

1. Ga in de Azure-portal naar **Azure Active Directory > Enterprise-toepassingen**en selecteer vervolgens de toepassing waarin SAML-tokenversleuteling is ingeschakeld.

1. Selecteer op de pagina van de toepassing **Token-versleuteling,** zoek het certificaat en selecteer vervolgens de **optie ...** om het vervolgkeuzemenu weer te geven.

1. Selecteer **Tokenversleuteling deactiveren**.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>SAML-tokenversleuteling configureren met Graph API, PowerShell of app-manifest

Versleutelingscertificaten worden opgeslagen op het toepassingsobject in Azure AD met een `encrypt` gebruikstag. U meerdere versleutelingscertificaten configureren en degene die actief is `tokenEncryptionKeyID` voor het versleutelen van tokens wordt geïdentificeerd door het kenmerk.

U hebt de object-id van de toepassing nodig om tokenversleuteling te configureren met Behulp van Microsoft Graph API of PowerShell. U deze waarde programmatisch vinden of door naar de pagina **Eigenschappen** van de toepassing in de Azure-portal te gaan en de **object-id-waarde** op te merken.

Wanneer u een keyCredential configureert met Grafiek, PowerShell of in het toepassingsmanifest, moet u een GUID genereren die u voor de keyId moet gebruiken.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Tokenversleuteling configureren met Microsoft Graph

1. Werk de toepassing `keyCredentials` bij met een X.509-certificaat voor versleuteling. In het volgende voorbeeld ziet u hoe u dit doet.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid>

    { 
       "keyCredentials":[ 
          { 
             "type":"AsymmetricX509Cert","usage":"Encrypt",
             "keyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35",    (Use a GUID generator to obtain a value for the keyId)
             "key": "MIICADCCAW2gAwIBAgIQ5j9/b+n2Q4pDvQUCcy3…"  (Base64Encoded .cer file)
          }
        ]
    }
    ```

1. Identificeer het versleutelingscertificaat dat actief is voor het versleutelen van tokens. In het volgende voorbeeld ziet u hoe u dit doet.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>Tokenversleuteling configureren met PowerShell

1. Gebruik de nieuwste Azure AD PowerShell-module om verbinding te maken met uw tenant.

1. Stel de tokencoderingsinstellingen in met de opdracht **[Set-AzureApplication.](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)**

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. Lees de tokencoderingsinstellingen met de volgende opdrachten.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Tokenversleuteling configureren met behulp van het toepassingsmanifest

1. Ga vanuit de Azure-portal naar **Azure Active Directory > App-registraties**.

1. Selecteer **Alle apps** in de vervolgkeuzelijst om alle apps weer te geven en selecteer vervolgens de bedrijfstoepassing die u wilt configureren.

1. Selecteer **Manifest** op de pagina van de toepassing om het [toepassingsmanifest](../develop/reference-app-manifest.md)te bewerken .

1. Stel de waarde `tokenEncryptionKeyId` voor het kenmerk in.

    In het volgende voorbeeld wordt een toepassingsmanifest weergegeven dat is geconfigureerd met twee versleutelingscertificaten en waarbij het tweede is geselecteerd als de actieve met behulp van de tokenEnryptionKeyId.

    ```json
    { 
      "id": "3cca40e2-367e-45a5-8440-ed94edd6cc35",
      "accessTokenAcceptedVersion": null,
      "allowPublicClient": false,
      "appId": "cb2df8fb-63c4-4c35-bba5-3d659dd81bf1",
      "appRoles": [],
      "oauth2AllowUrlPathMatching": false,
      "createdDateTime": "2017-12-15T02:10:56Z",
      "groupMembershipClaims": "SecurityGroup",
      "informationalUrls": { 
         "termsOfService": null, 
         "support": null, 
         "privacy": null, 
         "marketing": null 
      },
      "identifierUris": [ 
        "https://testapp"
      ],
      "keyCredentials": [ 
        { 
          "customKeyIdentifier": "Tog/O1Hv1LtdsbPU5nPphbMduD=", 
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "8be4cb65-59d9-404a-a6f5-3d3fb4030351", 
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest" 
        }, 
        {
          "customKeyIdentifier": "U5nPphbMduDmr3c9Q3p0msqp6eEI=",
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851",
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest2" 
        } 
      ], 
      "knownClientApplications": [], 
      "logoUrl": null, 
      "logoutUrl": null, 
      "name": "Test SAML Application", 
      "oauth2AllowIdTokenImplicitFlow": true, 
      "oauth2AllowImplicitFlow": false, 
      "oauth2Permissions": [], 
      "oauth2RequirePostResponse": false, 
      "orgRestrictions": [], 
      "parentalControlSettings": { 
         "countriesBlockedForMinors": [], 
         "legalAgeGroupRule": "Allow" 
        }, 
      "passwordCredentials": [], 
      "preAuthorizedApplications": [], 
      "publisherDomain": null, 
      "replyUrlsWithType": [], 
      "requiredResourceAccess": [], 
      "samlMetadataUrl": null, 
      "signInUrl": "https://127.0.0.1:444/applications/default.aspx?metadata=customappsso|ISV9.1|primary|z" 
      "signInAudience": "AzureADMyOrg",
      "tags": [], 
      "tokenEncryptionKeyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851" 
    }  
    ```

## <a name="next-steps"></a>Volgende stappen

* Ontdek [hoe Azure AD het SAML-protocol gebruikt](../develop/active-directory-saml-protocol-reference.md)
* Meer informatie over de indeling, beveiligingskenmerken en inhoud van [SAML-tokens in Azure AD](../develop/reference-saml-tokens.md)