---
title: Een api met Azure exporteren naar PowerApps en Microsoft Flow
description: Overzicht van hoe u een API die in App Service wordt gehost, blootstellen aan PowerApps en Microsoft Flow
ms.topic: conceptual
ms.date: 12/15/2017
ms.reviewer: sunayv
ms.openlocfilehash: 632818bf82e41e6be0a96d30cc1c4fa631718a3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74233075"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Een api met Azure exporteren naar PowerApps en Microsoft Flow

[PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) is een service voor het bouwen en gebruiken van aangepaste zakelijke apps die verbinding maken met uw gegevens en werken op verschillende platforms. [Met Microsoft Flow](/learn/modules/get-started-with-flow/index) u eenvoudig workflows en bedrijfsprocessen automatiseren tussen uw favoriete apps en services. Zowel PowerApps als Microsoft Flow worden geleverd met verschillende ingebouwde connectors voor gegevensbronnen zoals Office 365, Dynamics 365, Salesforce en meer. In sommige gevallen willen app- en flowbouwers ook verbinding maken met gegevensbronnen en API's die door hun organisatie zijn gebouwd.

Op dezelfde manier kunnen ontwikkelaars die hun API's breder willen blootstellen binnen een organisatie hun API's beschikbaar maken voor app- en flowbouwers. In dit onderwerp ziet u hoe u een API exporteert die is gebouwd met [Azure Functions](../azure-functions/functions-overview.md) of Azure [App Service.](../app-service/overview.md) De geëxporteerde API wordt een *aangepaste connector*, die wordt gebruikt in PowerApps en Microsoft Flow, net als een ingebouwde connector.

> [!IMPORTANT]
> De API-definitiefunctionaliteit in dit artikel wordt alleen ondersteund voor [versie 1.x van de runtime van Azure Functions](functions-versions.md#creating-1x-apps) en App Services-apps. Versie 2.x van Functies integreert met API Management om OpenAPI-definities te maken en te onderhouden. Zie [Een OpenAPI-definitie maken voor een functie met Azure API Management](functions-openapi-definition.md)voor meer informatie. 

## <a name="create-and-export-an-api-definition"></a>Een API-definitie maken en exporteren
Voordat u een API exporteert, moet u de API beschrijven met behulp van een OpenAPI-definitie (voorheen bekend als een [Swagger-bestand).](https://swagger.io/) Deze definitie bevat informatie over welke bewerkingen beschikbaar zijn in een API en hoe de gegevens van de aanvraag en respons voor de API moeten worden opgebouwd. PowerApps en Microsoft Flow kunnen aangepaste connectors maken voor elke OpenAPI 2.0-definitie. Azure Functions en Azure App Service hebben ingebouwde ondersteuning voor het maken, hosten en beheren van OpenAPI-definities. Zie [Host een RESTful API met CORS in Azure App Service](../app-service/app-service-web-tutorial-rest-api.md)voor meer informatie.

> [!NOTE]
> U ook aangepaste connectors bouwen in de PowerApps en Microsoft Flow UI, zonder een OpenAPI-definitie te gebruiken. Zie [Registreren en gebruiken van een aangepaste connector (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) en Registreer en gebruik een aangepaste connector [(Microsoft Flow) voor](/power-automate/developer/register-custom-api)meer informatie.

Voer de volgende stappen uit om de API-definitie te exporteren:

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar uw Azure-functies of een andere App Service-toepassing.

    Als u Azure-functies gebruikt, selecteert u de functie-app, kiest u **Platformfuncties**en vervolgens **API-definitie**.

    ![Azure Functions API-definitie](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Als u Azure App Service gebruikt, selecteert u **API-definitie** in de lijst met instellingen.

    ![App Service API-definitie](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. De knop **Exporteren naar PowerApps + Microsoft Flow** moet beschikbaar zijn (zo niet, dan moet u eerst een OpenAPI-definitie maken). Klik op deze knop om het exportproces te starten.

    ![Exporteren naar PowerApps + Knop Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Selecteer de **exportmodus:**

    **Met Express** u de aangepaste connector maken vanuit de Azure-portal. Het vereist dat u bent aangemeld bij PowerApps of Microsoft Flow en toestemming hebt om connectors in de doelomgeving te maken. Dit is de aanbevolen aanpak als aan deze twee eisen kan worden voldaan. Als u deze modus gebruikt, volgt u hieronder de instructies [voor express-export gebruik.](#express)

    **Met Manual** u de API-definitie exporteren, die u vervolgens importeert met de PowerApps- of Microsoft Flow-portals. Dit is de aanbevolen aanpak als de Azure-gebruiker en de gebruiker met toestemming om connectors te maken verschillende personen zijn of als de connector moet worden gemaakt in een andere Azure-tenant. Als u deze modus gebruikt, volgt u hieronder de instructies [voor handmatige export gebruiken.](#manual)

    ![Exportmodus](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> De aangepaste connector maakt gebruik van een *kopie* van de API-definitie, zodat PowerApps en Microsoft Flow niet meteen weten of u wijzigingen aanbrengt in de toepassing en de API-definitie. Als u wijzigingen aanbrengt, herhaalt u de exportstappen voor de nieuwe versie.

<a name="express"></a>
## <a name="use-express-export"></a>Express-export gebruiken

Voer de volgende stappen uit om de export in **de Express-modus** te voltooien:

1. Controleer of u bent aangemeld bij de Windows-app of de Microsoft Flow-tenant waarvoor u wilt exporteren. 

2. Gebruik de instellingen zoals aangegeven in de tabel.

    |Instelling|Beschrijving|
    |--------|------------|
    |**Omgeving**|Selecteer de omgeving waarop de aangepaste connector moet worden opgeslagen. Zie voor meer informatie [Overzicht van omgevingen](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Aangepaste API-naam**|Voer een naam in die PowerApps- en Microsoft Flow-bouwers in hun connectorlijst zullen zien.|
    |**Beveiligingsconfiguratie voorbereiden**|Geef indien nodig de beveiligingsconfiguratiegegevens op die nodig zijn om gebruikers toegang te verlenen tot uw API. In dit voorbeeld wordt een API-sleutel weergegeven. Zie Hieronder [verificatietype opgeven](#auth) voor meer informatie.|
 
    ![Express-export naar PowerApps en Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Klik op **OK**. De aangepaste connector is nu gebouwd en toegevoegd aan de omgeving die u hebt opgegeven.

<a name="manual"></a>
## <a name="use-manual-export"></a>Handmatig exporteren gebruiken

Voer de volgende stappen uit om de export in **de handmatige** modus te voltooien:

1. Klik **op Downloaden** en sla het bestand op, of klik op de kopieerknop en sla de URL op. U gebruikt het downloadbestand of de URL tijdens het importeren.
 
    ![Handmatig exporteren naar PowerApps en Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Als uw API-definitie beveiligingsdefinities bevat, worden deze stap #2. Tijdens import detecteert PowerApps en Microsoft Flow deze en vragen om beveiligingsinformatie. Verzamel de referenties met betrekking tot elke definitie voor gebruik in de volgende sectie. Zie Hieronder [verificatietype opgeven](#auth) voor meer informatie.

    ![Beveiliging voor handmatige export](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    In dit voorbeeld wordt de beveiligingsdefinitie van API-sleutel weergegeven die is opgenomen in de OpenAPI-definitie.

Nu u de API-definitie hebt geëxporteerd, importeert u deze om een aangepaste connector te maken in PowerApps en Microsoft Flow. Aangepaste connectors worden gedeeld tussen de twee services, dus u hoeft de definitie slechts één keer te importeren.

Voer de volgende stappen uit om de API-definitie in PowerApps en Microsoft Flow te importeren:

1. Ga naar [powerapps.com](https://web.powerapps.com) of [flow.microsoft.com](https://flow.microsoft.com).

2. Klik in de rechterbovenhoek op het tandwielpictogram en klik vervolgens op **Aangepaste connectoren**.

   ![Tandwielpictogram in service](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Klik **op Aangepaste connector maken**en klik vervolgens op Een **OpenAPI-definitie importeren**.

   ![Een aangepaste connector maken](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Voer een naam in voor de aangepaste connector, navigeer naar de OpenAPI-definitie die u hebt geëxporteerd en klik op **Doorgaan**.

   ![OpenAPI-definitie uploaden](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. Bekijk op het tabblad **Algemeen** de informatie die afkomstig is van de OpenAPI-definitie.

5. Voer op het tabblad **Beveiliging** als u wordt gevraagd om verificatiegegevens op te geven, de waarden in die geschikt zijn voor het verificatietype. Klik **op Doorgaan**.

    ![Tabblad Beveiliging](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    In dit voorbeeld worden de vereiste velden voor verificatie van API-sleutels weergegeven. De velden verschillen afhankelijk van het verificatietype.

6. Op het tabblad **Definities** worden alle bewerkingen die in uw OpenAPI-bestand zijn gedefinieerd, automatisch ingevuld. Als al uw vereiste bewerkingen zijn gedefinieerd, u naar de volgende stap gaan. Zo niet, dan u hier bewerkingen toevoegen en wijzigen.

    ![Tabblad Definities](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    In dit voorbeeld wordt `CalculateCosts`één bewerking uitgevoerd met de naam . De metagegevens, zoals **Beschrijving,** komen allemaal uit het OpenAPI-bestand.

7. Klik boven aan de pagina op **Connector maken.**

U nu verbinding maken met de aangepaste connector in PowerApps en Microsoft Flow. Zie [Uw aangepaste connector registreren (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) en Uw aangepaste connector registreren [(Microsoft Flow)](/power-automate/get-started-flow-dev#create-a-custom-connector)voor meer informatie over het maken van connectors in de PowerApps- en Microsoft Flow-portals.

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Verificatietype opgeven

PowerApps en Microsoft Flow ondersteunen een verzameling identiteitsproviders die verificatie bieden voor aangepaste connectors. Als uw API verificatie vereist, moet u ervoor zorgen dat deze wordt vastgelegd als een _beveiligingsdefinitie_ in uw OpenAPI-document, zoals het volgende voorbeeld:

```json
"securityDefinitions": {
    "AAD": {
    "type": "oauth2",
    "flow": "accessCode",
    "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
    "scopes": {}
    }
}
``` 
Tijdens het exporteren geeft u configuratiewaarden waarmee PowerApps en Microsoft Flow gebruikers kunnen verifiëren.

In deze sectie worden de verificatietypen gebruikt die worden ondersteund in **de Express-modus:** API-toets, Azure Active Directory en Generic OAuth 2.0. PowerApps en Microsoft Flow ondersteunen ook basisverificatie en OAuth 2.0 voor specifieke services zoals Dropbox, Facebook en SalesForce.

### <a name="api-key"></a>API-sleutel
Wanneer u een API-sleutel gebruikt, wordt de gebruikers van uw connector gevraagd de sleutel te verstrekken wanneer ze een verbinding maken. U geeft een API-sleutelnaam op om hen te helpen begrijpen welke sleutel nodig is. In het eerdere voorbeeld gebruiken `API Key (contact meganb@contoso.com)` we de naam zodat mensen weten waar ze informatie over de API-sleutel kunnen krijgen. Voor Azure-functies is de sleutel meestal een van de hostsleutels, die verschillende functies in de functie-app omvat.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Wanneer u Azure AD gebruikt, hebt u twee Azure AD-toepassingsregistraties nodig: één voor de API zelf en één voor de aangepaste connector:

- Als u de registratie voor de API wilt configureren, gebruikt u de functie [Verificatie/autorisatie van appservice.](../app-service/configure-authentication-provider-aad.md)

- Als u de registratie voor de connector wilt configureren, voert u de stappen uit in [Het toevoegen van een Azure AD-toepassing](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). De registratie moet de toegang tot uw API `https://msmanaged-na.consent.azure-apim.net/redirect`hebben gedelegeerd en een antwoord-URL van . 

Zie de voorbeelden van Azure AD-registratie voor [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) en [Microsoft Flow](https://docs.microsoft.com/connectors/custom-connectors/azure-active-directory-authentication)voor meer informatie. In deze voorbeelden wordt Azure Resource Manager als API gebruikt. vervang uw API als u de stappen volgt.

De volgende configuratiewaarden zijn vereist:
- **Client-id** - de client-id van uw azure AD-registratie voor connectoren
- **Clientgeheim** - het clientgeheim van uw azure AD-registratie voor connectoren
- **Aanmeldings-URL** - de basis-URL voor Azure AD. In Azure is dit `https://login.windows.net`meestal .
- **Tenant-ID** - de ID van de tenant die moet worden gebruikt voor de login. Dit moet "gemeenschappelijk" zijn of de ID van de tenant waarin de connector is gemaakt.
- **Url van resources** - de bron-URL van de Azure AD-registratie voor uw API

> [!IMPORTANT]
> Als iemand anders de API-definitie importeert in PowerApps en Microsoft Flow als onderdeel van de handmatige stroom, moet u deze persoon de client-id en het clientgeheim van de *connectorregistratie*en de bron-URL van uw API verstrekken. Zorg ervoor dat deze geheimen veilig worden beheerd. **Deel de beveiligingsreferenties van de API zelf niet.**

### <a name="generic-oauth-20"></a>Algemene OAuth 2.0
Bij het gebruik van generieke OAuth 2.0 u integreren met elke OAuth 2.0-provider. Hiermee u werken met aangepaste providers die niet native worden ondersteund.

De volgende configuratiewaarden zijn vereist:
- **Client-ID** - de OAuth 2.0-client-id
- **Client geheim** - de OAuth 2.0 client geheim
- **Autorisatie-URL** - de URL van de OAuth 2.0-autorisatie
- **Token-URL** - de URL van oauth 2.0-token
- **URL vernieuwen** - de URL voor het vernieuwen van OAuth 2.0


