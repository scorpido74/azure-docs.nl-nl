---
title: Verificatie toevoegen voor het beveiligen van oproepen naar aangepaste API's
description: Verificatie instellen om de beveiliging van oproepen naar aangepaste API's van Azure Logic Apps te verbeteren
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: 110a684cf6ad21c13411d3bc2ada84750744f00e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191407"
---
# <a name="increase-security-for-calls-to-custom-apis-from-azure-logic-apps"></a>De beveiliging voor oproepen naar aangepaste API's van Azure Logic Apps verhogen

Als u de beveiliging voor oproepen naar uw API's wilt verbeteren, u Azure Active Directory-verificatie (Azure AD) instellen via de Azure-portal, zodat u uw code niet hoeft bij te werken. Of u kunt verificatie vereisen en afdwingen via de code van uw API.

## <a name="authentication-options-for-your-api"></a>Verificatieopties voor uw API

U de beveiliging voor oproepen naar uw aangepaste API op de volgende manieren verbeteren:

* [Geen codewijzigingen](#no-code): Bescherm uw API met [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) via de Azure-portal, zodat u uw code niet hoeft bij te werken of uw API opnieuw hoeft te implementeren.

  > [!NOTE]
  > Standaard biedt de Azure AD-verificatie die u inschakelt in de Azure-portal geen fijnmazige autorisatie. Deze verificatie vergrendelt uw API bijvoorbeeld naar slechts een specifieke tenant, niet naar een specifieke gebruiker of app. 

* [De code van uw API bijwerken:](#update-code)Bescherm uw API door [certificaatverificatie,](#certificate) [basisverificatie](#basic)of [Azure AD-verificatie](#azure-ad-code) via code af te dwingen.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Oproepen naar uw API verifiëren zonder code te wijzigen

Hier zijn de algemene stappen voor deze methode:

1. Maak twee Azure Active Directory-toepassingsidentiteiten (Azure AD) : één voor uw logische app en één voor uw web-app (of API-app).

2. Als u oproepen naar uw API wilt verifiëren, gebruikt u de referenties (client-id en geheim) voor de serviceprincipal die is gekoppeld aan de Azure AD-toepassingsidentiteit voor uw logische app.

3. Neem de toepassings-id's op in de definitie van uw logische app.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Deel 1: Een Azure AD-toepassingsidentiteit maken voor uw logische app

Uw logische app gebruikt deze Azure AD-toepassingsidentiteit om te verifiëren tegen Azure AD. U hoeft deze identiteit slechts één keer in te stellen voor uw directory. U er bijvoorbeeld voor kiezen om dezelfde identiteit te gebruiken voor al uw logische apps, ook al u unieke identiteiten maken voor elke logische app. U deze identiteiten instellen in de Azure-portal of [PowerShell](#powershell)gebruiken.

**De toepassingsidentiteit voor uw logische app maken in de Azure-portal**

1. Kies azure active directory in de [Azure-portal](https://portal.azure.com "https://portal.azure.com"). **Azure Active Directory** 

2. Controleer of u zich in dezelfde map bevindt als uw web-app of API-app.

   > [!TIP]
   > Als u van directeur wilt wisselen, kiest u uw profiel en selecteert u een andere map. Kies > **overzichtsmap overschakelen**. **Overview**

3. Kies in het mapmenu onder **Beheren**de optie **App-registraties** > **Nieuwe toepassingsregistratie**kiezen .

   > [!TIP]
   > Standaard worden in de lijst met app-registraties alle app-registraties in uw map weergegeven. Als u alleen uw app-registraties wilt weergeven, selecteert u naast het zoekvak De optie **Mijn apps**. 

   ![Nieuwe app-registratie maken](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Geef uw toepassingsidentiteit een naam, laat **toepassingstype** instellen op **web-app / API,** geef een unieke tekenreeks opgemaakt als domein voor **aanmeldings-URL**en kies **Maken**.

   ![Url van naam en aanmelding opgeven voor toepassingsidentiteit](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   De toepassingsidentiteit die u voor uw logische app hebt gemaakt, wordt nu weergegeven in de lijst met app-registraties.

   ![Toepassingsidentiteit voor uw logische app](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. Selecteer in de lijst met app-registraties uw nieuwe toepassingsidentiteit. Kopieer en sla de **toepassings-id** op die u wilt gebruiken als de 'client-id' voor uw logische app in deel 3.

   ![Toepassings-id voor logische app kopiëren en opslaan](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Als de instellingen van de toepassingsidentiteit niet zichtbaar zijn, kiest u **Instellingen** of **Alle instellingen**.

7. Kies Onder **API Access**de optie **Sleutels**. Geef **onder Beschrijving**een naam voor uw sleutel op. Selecteer **onder Verloopt**een duur voor uw sleutel.

   De sleutel die u maakt, fungeert als het 'geheim' of wachtwoord van de toepassingsidentiteit voor uw logische app.

   ![Sleutel maken voor de identiteit van logische apps](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Kies op de werkbalk **Opslaan**. Onder **Waarde**wordt uw sleutel nu weergegeven. 
**Zorg ervoor dat u uw sleutel kopieert en opslaat** voor later gebruik, omdat de sleutel is verborgen wanneer u de pagina **Sleutels** verlaat.

   Wanneer u uw logische app configureert in deel 3, geeft u deze sleutel op als het 'geheim' of wachtwoord.

   ![Sleutel kopiëren en opslaan voor later](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**De toepassingsidentiteit voor uw logische app maken in PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U deze taak uitvoeren via Azure Resource Manager met PowerShell. Voer in PowerShell de volgende opdrachten uit:

1. `Add-AzAccount`

1. `$SecurePassword = Read-Host -AsSecureString`

1. Voer een wachtwoord in en druk op Enter.

1. `New-AzADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password $SecurePassword`

1. Zorg ervoor dat u de **tenant-id** (GUID voor uw Azure AD-tenant), de **toepassings-id**en het wachtwoord dat u hebt gebruikt, kopieert.

Voor meer informatie, meer informatie over hoe [u een serviceprincipal maakt met PowerShell om toegang te krijgen tot bronnen](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Deel 2: Een Azure AD-toepassingsidentiteit maken voor uw web-app of API-app

Als uw web-app of API-app al is geïmplementeerd, u verificatie inschakelen en de toepassingsidentiteit maken in de Azure-portal. Anders u [verificatie inschakelen wanneer u implementeert met een Azure Resource Manager-sjabloon.](#authen-deploy) 

**De toepassingsidentiteit maken en verificatie inschakelen in de Azure-portal voor geïmplementeerde apps**

1. Zoek en selecteer in de [Azure-portal](https://portal.azure.com "https://portal.azure.com")uw web-app of API-app. 

2. Kies **onder Instellingen**de optie **Verificatie/Autorisatie**. Schakel verificatie in onder **Verificatie van appservice** **.** Kies **onder Verificatieproviders**de optie **Azure Active Directory**.

   ![Verificatie inschakelen](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Maak nu een toepassingsidentiteit voor uw web-app of API-app zoals hier wordt weergegeven. Stel op de pagina **Azure Active Directory Settings** de **beheermodus** in **op Express.** Kies **Nieuwe AD-app maken**. Geef uw toepassingsidentiteit een naam en kies **OK.** 

   ![Toepassingsidentiteit maken voor uw web-app of API-app](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. Kies **Opslaan** op de pagina **Verificatie/autorisatie**.

Nu moet u de client-id en tenant-id vinden voor de toepassingsidentiteit die is gekoppeld aan uw web-app of API-app. Je gebruikt deze id's in deel 3. Ga dus verder met deze stappen voor de Azure-portal.

**De client-id en de tenant-id van de toepassing-identiteit zoeken voor uw web-app of API-app in de Azure-portal**

1. Kies **onder Verificatieproviders**de optie **Azure Active Directory**. 

   ![Azure Active Directory kiezen](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. Stel op de pagina **Azure Active Directory-instellingen** de **beheermodus** in **op Geavanceerd**.

3. Kopieer de **client-id**en sla die GUID op voor gebruik in deel 3.

   > [!TIP] 
   > Als **client-id** en **url van de uitgever** niet worden weergegeven, probeert u de Azure-portal te vernieuwen en herhaalt u stap 1.

4. Onder **Uitgever Url,** kopieer en sla alleen de GUID voor deel 3. U deze GUID ook gebruiken in de implementatiesjabloon van uw web-app of API-app, indien nodig.

   Deze GUID is de GUID ('tenant-id') van uw specifieke tenant en moet in deze URL worden weergegeven:`https://sts.windows.net/{GUID}`

5. Sluit de pagina **Azure Active Directory Settings** zonder uw wijzigingen op te slaan.

<a name="authen-deploy"></a>

**Verificatie inschakelen wanneer u implementeert met een Azure Resource Manager-sjabloon**

U moet nog steeds een Azure AD-toepassingsidentiteit maken voor uw web-app of API-app die verschilt van de app-identiteit voor uw logische app. Als u de toepassingsidentiteit wilt maken, volgt u de vorige stappen in deel 2 voor de Azure-portal. 

U ook de stappen in deel 1 volgen, maar zorg ervoor `https://{URL}` dat u de werkelijke web-app of API-app gebruikt voor **aanmeldings-URL** en **App ID URI.** Vanuit deze stappen moet u zowel de client-id als de tenant-id opslaan voor gebruik in de implementatiesjabloon van uw app en ook voor deel 3.

> [!NOTE]
> Wanneer u de Azure AD-toepassingsidentiteit voor uw web-app of API-app maakt, moet u de Azure-portal gebruiken, niet PowerShell. De PowerShell-opdrachtbevat niet de vereiste machtigingen instellen om gebruikers aan te melden bij een website.

Nadat u de client-id en tenant-id hebt opgemaakt, neemt u deze id's op als subbron van uw web-app of API-app in uw implementatiesjabloon:

``` json
"resources": [ 
   {
      "apiVersion": "2015-08-01",
      "name": "web",
      "type": "config",
      "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
      "properties": {
         "siteAuthEnabled": true,
         "siteAuthSettings": {
            "clientId": "<client-ID>",
            "issuer": "https://sts.windows.net/<tenant-ID>/"
         }
      }
   } 
]
```

Als u een lege web-app en een logische app automatisch wilt implementeren in combinatie met Azure Active Directory-verificatie, [bekijkt u hier de volledige sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json)of klikt u hier op Implementeren naar **Azure:**

[![Implementeren naar Azure](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Deel 3: De sectie Autorisatie in uw logica-app invullen

De vorige sjabloon heeft deze autorisatiesectie al ingesteld, maar als u de logische app rechtstreeks aan het ontwerpen bent, moet u de volledige autorisatiesectie opnemen.

Open de definitie van uw logische app in de codeweergave, ga naar de **HTTP-actiedefinitie,** zoek de sectie **Autorisatie** en voeg deze eigenschappen toe:

```json
{
   "tenant": "<tenant-ID>",
   "audience": "<client-ID-from-Part-2-web-app-or-API app>", 
   "clientId": "<client-ID-from-Part-1-logic-app>",
   "secret": "<key-from-Part-1-logic-app>", 
   "type": "ActiveDirectoryOAuth"
}
```

| Eigenschap | Vereist | Beschrijving | 
| -------- | -------- | ----------- | 
| tenant | Ja | De GUID voor de Azure AD-tenant | 
| Publiek | Ja | De GUID voor de doelbron die u wilt openen, dat is de client-id van de toepassingsidentiteit voor uw web-app of API-app | 
| clientId | Ja | De GUID voor de client die toegang aanvraagt, dat is de client-id van de toepassingsidentiteit voor uw logische app | 
| geheim | Ja | De sleutel of het wachtwoord van de toepassingsidentiteit voor de client die het toegangstoken aanvraagt | 
| type | Ja | Het verificatietype. Voor ActiveDirectoryOAuth-verificatie is `ActiveDirectoryOAuth`de waarde . | 
|||| 

Bijvoorbeeld:

``` json
{
   "actions": {
      "HTTP": {
         "inputs": {
            "method": "POST",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         }
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Beveiligde API-aanroepen via code

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Verificatie via certificaat

Als u de binnenkomende aanvragen van uw logische app wilt valideren naar uw web-app of API-app, u clientcertificaten gebruiken. Als u uw code wilt instellen, leest u [hoe u wederzijdse verificatie van TLS configureert.](../app-service/app-service-web-configure-tls-mutual-auth.md)

Neem **in** de sectie Autorisatie de volgende eigenschappen op:

```json
{
   "type": "ClientCertificate",
   "password": "<password>",
   "pfx": "<long-pfx-key>"
} 
```

| Eigenschap | Vereist | Beschrijving |
| -------- | -------- | ----------- |
| `type` | Ja | Het verificatietype. Voor SSL-clientcertificaten moet `ClientCertificate`de waarde . |
| `password` | Nee | Het wachtwoord voor toegang tot het clientcertificaat (PFX-bestand) |
| `pfx` | Ja | De basis64-gecodeerde inhoud van het clientcertificaat (PFX-bestand) |
||||

<a name="basic"></a>

#### <a name="basic-authentication"></a>Basisverificatie

Als u binnenkomende aanvragen van uw logische app naar uw web-app of API-app wilt valideren, u basisverificatie gebruiken, zoals een gebruikersnaam en wachtwoord. Basisverificatie is een veelvoorkomend patroon en u deze verificatie gebruiken in elke taal die wordt gebruikt om uw web-app of API-app te bouwen.

Neem **in** de sectie Autorisatie de volgende eigenschappen op:

```json
{
   "type": "Basic",
   "username": "<username>",
   "password": "<password>"
}
```

| Eigenschap | Vereist | Beschrijving | 
| -------- | -------- | ----------- | 
| type | Ja | Het verificatietype dat u wilt gebruiken. Voor basisverificatie moet de `Basic`waarde . | 
| gebruikersnaam | Ja | De gebruikersnaam die u wilt gebruiken voor verificatie | 
| wachtwoord | Ja | Het wachtwoord dat u wilt gebruiken voor verificatie | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Azure Active Directory-verificatie via code

Standaard biedt de Azure AD-verificatie die u inschakelt in de Azure-portal geen fijnmazige autorisatie. Deze verificatie vergrendelt uw API bijvoorbeeld naar slechts een specifieke tenant, niet naar een specifieke gebruiker of app. 

Als u api-toegang tot uw logische app wilt beperken via code, haalt u de koptekst met het JSON-webtoken (JWT) uit. Controleer de identiteit van de beller en weiger verzoeken die niet overeenkomen.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/overview-authentication-authorization.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Volgende stappen

* [Aangepaste API's implementeren en aanroepen vanuit logische app-werkstromen](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
