---
title: Android MSAL-configuratiebestand | Azure
titleSuffix: Microsoft identity platform
description: Een overzicht van het MSAL-configuratiebestand (Android Microsoft Authentication Library), dat de configuratie van een toepassing in Azure Active Directory vertegenwoordigt.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 9e35ba5a3f3705a52e80262da9bbfbfda489bf83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050382"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>Configuratiebestand android Microsoft-verificatiebibliotheek

De MsON(Microsoft Authentication Library) (Android Microsoft Authentication Library) wordt geleverd met een [standaard-configuratie-JSON-bestand](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) dat u aanpast om het gedrag van uw openbare client-app te definiëren voor zaken zoals de standaardautoriteit, welke autoriteiten u zult gebruiken, enzovoort.

In dit artikel u inzicht krijgen in de verschillende instellingen in het configuratiebestand en hoe u het configuratiebestand opgeeft dat u wilt gebruiken in uw MSAL-gebaseerde app.

## <a name="configuration-settings"></a>Configuratie-instellingen

### <a name="general-settings"></a>Algemene instellingen

| Eigenschap | Gegevenstype | Vereist | Opmerkingen |
|-----------|------------|-------------|-------|
| `client_id` | Tekenreeks | Ja | De client-id van uw app vanaf de [pagina Toepassingsregistratie](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `redirect_uri`   | Tekenreeks | Ja | De URI van uw app omleiden vanaf de [pagina Toepassingsregistratie](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `authorities` | Lijstautoriteit\<> | Nee | De lijst met autoriteiten die uw app nodig heeft |
| `authorization_user_agent` | AuthorizationAgent (enum) | Nee | Mogelijke `DEFAULT`waarden: `BROWSER`, ,`WEBVIEW` |
| `http` | HttpConfiguratie | Nee | Configureren `HttpUrlConnection` `connect_timeout` en`read_timeout` |
| `logging` | Logboekconfiguratie | Nee | Hiermee geeft u het niveau van de logboekdetails op. Optionele `pii_enabled`configuraties omvatten: , die een booleaanse waarde neemt, en `log_level`, die neemt `ERROR`, `WARNING`, , `INFO`of `VERBOSE`. |

### <a name="client_id"></a>client_id

De client-id of app-id die is gemaakt toen u uw toepassing registreerde.

### <a name="redirect_uri"></a>redirect_uri

De omleiding URI u geregistreerd toen u uw aanvraag geregistreerd. Als de omleiding URI is naar een broker-app, raadpleegt u [URI omleiden voor openbare client-apps](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) om ervoor te zorgen dat u de juiste URI-indeling voor omleiding gebruikt voor uw broker-app.

### <a name="authorities"></a>Autoriteiten

De lijst van autoriteiten die door u bekend en vertrouwd zijn. Naast de hier genoemde autoriteiten, msal ook vragen Microsoft om een lijst van wolken en autoriteiten bekend bij Microsoft te krijgen. Geef in deze lijst met autoriteiten het type autoriteit en `"audience"`eventuele aanvullende optionele parameters op, zoals , die moeten worden afgestemd op het publiek van uw app op basis van de registratie van uw app. Hieronder volgt een voorbeeldlijst van autoriteiten:

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken or acquireTokenSilent call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenantId": "contoso.com" // Provide your specific tenant ID here
    }
},
// Example AzureAD Multiple Organizations
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMultipleOrgs"
    }
},
//Example PersonalMicrosoftAccount
{
    "type": "AAD",
    "audience": {
        "type": "PersonalMicrosoftAccount"
    }
}
```

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>Aad-autoriteit & publiek toewijzen aan eindpunten van microsoft-identiteitsplatform

| Type | Doelgroep | Tenant-id | Authority_Url | Resulterend eindpunt | Opmerkingen |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADandPersonalMicrosoftAccount | | | `https://login.microsoftonline.com/common` | `common`is een tenantalias voor waar de account zich bevindt. Zoals een specifieke Azure Active Directory-tenant of het Microsoft-accountsysteem. |
| AAD | AzureADMyOrg AzureADMyOrg | contoso.com | | `https://login.microsoftonline.com/contoso.com` | Alleen accounts die in contoso.com aanwezig zijn, kunnen een token verkrijgen. Elk geverifieerd domein of de tenant-GUID kan worden gebruikt als tenant-id. |
| AAD | AzureADMultipleOrgs | | | `https://login.microsoftonline.com/organizations` | Alleen Azure Active Directory-accounts kunnen met dit eindpunt worden gebruikt. Microsoft-accounts kunnen lid zijn van organisaties. Als u een token wilt aanschaffen met een Microsoft-account voor een resource in een organisatie, geeft u de organisatietenant op waarvan u het token wilt hebben. |
| AAD | PersoonlijkMicrosoftAccount | | | `https://login.microsoftonline.com/consumers` | Alleen Microsoft-accounts kunnen dit eindpunt gebruiken. |
| B2C | | | Zie Resultaateindpunt | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | Alleen accounts die aanwezig zijn in de contoso.onmicrosoft.com tenant kunnen een token aanschaffen. In dit voorbeeld maakt het B2C-beleid deel uit van het URL-pad van de Autoriteit. |

> [!NOTE]
> Authority validatie kan niet worden ingeschakeld en uitgeschakeld in MSAL.
> Autoriteiten zijn bekend bij u als de ontwikkelaar zoals gespecificeerd via configuratie of bekend bij Microsoft via metadata.
> Als MSAL een aanvraag voor een token `MsalClientException` ontvangt `UnknownAuthority` bij een onbekende autoriteit, een van de tekstresultaten.

#### <a name="authority-properties"></a>Eigenschappen van de autoriteit

| Eigenschap | Gegevenstype  | Vereist | Opmerkingen |
|-----------|-------------|-----------|--------|
| `type` | Tekenreeks | Ja | Weerspiegelt het publiek of accounttype van uw app-doelen. Mogelijke waarden: `AAD`,`B2C` |
| `audience` | Object | Nee | Geldt alleen wanneer`AAD`type= . Hiermee geeft u de identiteit op die uw app target. De waarde van uw app-registratie gebruiken |
| `authority_url` | Tekenreeks | Ja | Alleen vereist wanneer`B2C`type= . Geeft de URL of het beleid van de autoriteit op die uw app moet gebruiken  |
| `default` | booleaans | Ja | Een `"default":true` enkele is vereist wanneer een of meer autoriteiten is gespecificeerd. |

#### <a name="audience-properties"></a>Doelgroepeigenschappen

| Eigenschap | Gegevenstype  | Vereist | Opmerkingen |
|-----------|-------------|------------|-------|
| `type` | Tekenreeks | Ja | Hiermee geeft u de doelgroep op die uw app wil targeten. Mogelijke `AzureADandPersonalMicrosoftAccount`waarden: `PersonalMicrosoftAccount` `AzureADMultipleOrgs`, ,`AzureADMyOrg` |
| `tenant_id` | Tekenreeks | Ja | Alleen vereist `"type":"AzureADMyOrg"`wanneer . Optioneel voor `type` andere waarden. Dit kan een tenantdomein `contoso.com`zijn, zoals , `72f988bf-86f1-41af-91ab-2d7cd011db46`of een tenant-id zoals ) |

### <a name="authorization_user_agent"></a>authorization_user_agent

Geeft aan of u een ingesloten webweergave of de standaardbrowser op het apparaat wilt gebruiken bij het aanmelden van een account of het toestaan van toegang tot een bron.

Mogelijke waarden:
- `DEFAULT`: Geeft de voorkeur aan de systeembrowser. Gebruikt de ingesloten webweergave als een browser niet beschikbaar is op het apparaat.
- `WEBVIEW`: Gebruik de ingesloten webweergave.
- `BROWSER`: Gebruikt de standaardbrowser op het apparaat.

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

Voor clients die meerdere nationale `true`clouds ondersteunen, geeft u op . Het Microsoft-identiteitsplatform wordt dan automatisch omgeleid naar de juiste nationale cloud tijdens autorisatie en het inwisselen van token. U de nationale cloud van het aangemelde account `AuthenticationResult`bepalen door de autoriteit te onderzoeken die is gekoppeld aan de. Houd er `AuthenticationResult` rekening mee dat het nationale cloudspecifieke eindpuntadres van de bron waarvoor u een token aanvraagt, niet wordt verstrekt.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Een booleaan die aangeeft of u een Microsoft Identity broker gebruikt die compatibel is met in-broker, URI. Stel `false` in op als u de makelaar niet in uw app wilt gebruiken.

Als u de AAD-autoriteit met `"MicrosoftPersonalAccount"`doelgroep gebruikt die is ingesteld op , wordt de broker niet gebruikt.

### <a name="http"></a>http

Globale instellingen configureren voor HTTP-time-outs, zoals:

| Eigenschap | Gegevenstype | Vereist | Opmerkingen |
| ---------|-----------|------------|--------|
| `connect_timeout` | int | Nee | Tijd in milliseconden |
| `read_timeout` | int | Nee | Tijd in milliseconden |

### <a name="logging"></a>logboekregistratie

De volgende algemene instellingen zijn voor logboekregistratie:

| Eigenschap | Gegevenstype  | Vereist | Opmerkingen |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | booleaans | Nee | Of persoonsgegevens moeten worden uitsmeerd |
| `log_level`   | booleaans | Nee | Welke logboekberichten naar uitvoer |
| `logcat_enabled` | booleaans | Nee | Of de output om kat te loggen in aanvulling op de logging interface |

### <a name="account_mode"></a>account_mode

Hiermee geeft u op hoeveel accounts er tegelijk in uw app kunnen worden gebruikt. De mogelijke waarden zijn:

- `MULTIPLE`(Standaard)
- `SINGLE`

Als u `PublicClientApplication` een accountmodus maakt die niet overeenkomt met deze instelling, wordt een uitzondering gemaakt.

Zie Apps voor één [en meerdere](single-multi-account.md)accounts voor meer informatie over de verschillen tussen één en meerdere accounts.

### <a name="browser_safelist"></a>browser_safelist

Een lijst met browsers die compatibel zijn met MSAL. Deze browsers verwerken omleidingen naar aangepaste intents correct. U toevoegen aan deze lijst. De standaardinstelling wordt weergegeven in de standaardconfiguratie hieronder.
``
## <a name="the-default-msal-configuration-file"></a>Het standaard MSAL-configuratiebestand

De standaard MSAL-configuratie die wordt verzonden met MSAL wordt hieronder weergegeven. U de nieuwste versie op [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json)zien.

Deze configuratie wordt aangevuld met waarden die u opgeeft. De waarden die u opgeeft, overschrijven de standaardwaarden.

```javascript
{
  "authorities": [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      },
      "default": true
    }
  ],
  "authorization_user_agent": "DEFAULT",
  "multiple_clouds_supported": false,
  "broker_redirect_uri_registered": false,
  "http": {
    "connect_timeout": 10000,
    "read_timeout": 30000
  },
  "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": false
  },
  "shared_device_mode_supported": false,
  "account_mode": "MULTIPLE",
  "browser_safelist": [
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "45"
    },
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "57"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "4.0"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.cloudmosa.puffinFree",
      "browser_signature_hashes": [
        "1WqG8SoK2WvE4NTYgr2550TRhjhxT-7DWxu6C_o6GrOLK6xzG67Hq7GCGDjkAFRCOChlo2XUUglLRAYu3Mn8Ag=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.duckduckgo.mobile.android",
      "browser_signature_hashes": [
        "S5Av4cfEycCvIvKPpKGjyCuAE5gZ8y60-knFfGkAEIZWPr9lU5kA7iOAlSZxaJei08s0ruDvuEzFYlmH-jAi4Q=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.explore.web.browser",
      "browser_signature_hashes": [
        "BzDzBVSAwah8f_A0MYJCPOkt0eb7WcIEw6Udn7VLcizjoU3wxAzVisCm6bW7uTs4WpMfBEJYf0nDgzTYvYHCag=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.ksmobile.cb",
      "browser_signature_hashes": [
        "lFDYx1Rwc7_XUn4KlfQk2klXLufRyuGHLa3a7rNjqQMkMaxZueQfxukVTvA7yKKp3Md3XUeeDSWGIZcRy7nouw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.microsoft.emmx",
      "browser_signature_hashes": [
        "Ivy-Rk6ztai_IudfbyUrSHugzRqAtHWslFvHT0PTvLMsEKLUIgv7ZZbVxygWy_M5mOPpfjZrd3vOx3t-cA6fVQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.browser",
      "browser_signature_hashes": [
        "FIJ3IIeqB7V0qHpRNEpYNkhEGA_eJaf7ntca-Oa_6Feev3UkgnpguTNV31JdAmpEFPGNPo0RHqdlU0k-3jWJWw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.mini.native",
      "browser_signature_hashes": [
        "TOTyHs086iGIEdxrX_24aAewTZxV7Wbi6niS2ZrpPhLkjuZPAh1c3NQ_U4Lx1KdgyhQE4BiS36MIfP6LbmmUYQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "mobi.mgeek.TunnyBrowser",
      "browser_signature_hashes": [
        "RMVoXuK1sfJZuGZ8onG1yhMc-sKiAV2NiB_GZfdNlN8XJ78XEE2wPM6LnQiyltF25GkHiPN2iKQiGwaO2bkyyQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "org.mozilla.focus",
      "browser_signature_hashes": [
        "L72dT-stFqomSY7sYySrgBJ3VYKbipMZapmUXfTZNqOzN_dekT5wdBACJkpz0C6P0yx5EmZ5IciI93Q0hq0oYA=="
      ],
      "browser_use_customTab" : false
    }
  ]
}
```
## <a name="example-basic-configuration"></a>Voorbeeld basisconfiguratie

In het volgende voorbeeld wordt een basisconfiguratie weergegeven die de client-id opgeeft, URI omleiden, of een omleiding van een broker is geregistreerd en een lijst met autoriteiten.

```javascript
{
  "client_id" : "4b0db8c2-9f26-4417-8bde-3f0e3656f8e0",
  "redirect_uri" : "msauth://com.microsoft.identity.client.sample.local/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      }
      "default": true
    }
  ]
}
```

## <a name="how-to-use-a-configuration-file"></a>Een configuratiebestand gebruiken

1. Maak een configuratiebestand. We raden u aan uw `res/raw/auth_config.json`aangepaste configuratiebestand te maken in . Maar je het overal neerzetten wat je wilt.
2. Vertel MSAL waar u uw configuratie `PublicClientApplication`moet zoeken wanneer u de . Bijvoorbeeld:

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
