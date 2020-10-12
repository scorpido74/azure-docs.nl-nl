---
title: Een door Azure gehoste API exporteren naar PowerApps en Microsoft Flow
description: Overzicht van het beschikbaar maken van een API die wordt gehost in App Service naar PowerApps en Microsoft Flow
ms.topic: conceptual
ms.date: 04/28/2020
ms.reviewer: sunayv
ms.openlocfilehash: 7d968c62afbfc92952f747e1e7627c98fe07436d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87015082"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Een door Azure gehoste API exporteren naar PowerApps en Microsoft Flow

[PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) is een service voor het maken en gebruiken van aangepaste zakelijke apps die verbinding maken met uw gegevens en werken op verschillende platforms. Met [Microsoft flow](/learn/modules/get-started-with-flow/index) kunt u eenvoudig werk stromen en bedrijfs processen automatiseren tussen uw favoriete apps en services. Zowel PowerApps als Microsoft Flow worden geleverd met een aantal ingebouwde connectors voor gegevens bronnen zoals Office 365, Dynamics 365, Sales Force en meer. In sommige gevallen willen app-en flow-Builders ook verbinding maken met gegevens bronnen en Api's die zijn gebouwd door hun organisatie.

Op dezelfde manier kunnen ontwikkel aars die hun Api's breed willen weer geven binnen een organisatie, hun Api's beschikbaar maken voor app-en flow-Builders. In dit artikel wordt beschreven hoe u een API exporteert die is gebouwd met [Azure functions](../azure-functions/functions-overview.md) of [Azure app service](../app-service/overview.md). De geëxporteerde API wordt een *aangepaste connector*die wordt gebruikt in PowerApps en Microsoft flow net als een ingebouwde connector.

> [!IMPORTANT]
> De API-definitie functionaliteit die in dit artikel wordt weer gegeven, wordt alleen ondersteund voor [versie 1. x van de Azure functions runtime](functions-versions.md#creating-1x-apps) -en app Services-apps. Versie 2. x van functions integreert met API Management voor het maken en onderhouden van OpenAPI definities. Zie [een OpenAPI-definitie maken voor een functie met Azure API Management voor](functions-openapi-definition.md)meer informatie. 

## <a name="create-and-export-an-api-definition"></a>Een API-definitie maken en exporteren
Voordat u een API exporteert, moet u de API beschrijven met behulp van een OpenAPI-definitie (voorheen bekend als een [Swagger](https://swagger.io/) -bestand). Deze definitie bevat informatie over welke bewerkingen beschikbaar zijn in een API en hoe de gegevens van de aanvraag en respons voor de API moeten worden opgebouwd. PowerApps en Microsoft Flow kunnen aangepaste connectors maken voor elke OpenAPI 2,0-definitie. Azure Functions en Azure App Service hebben ingebouwde ondersteuning voor het maken, hosten en beheren van OpenAPI definities. Zie [een resterende API hosten met CORS in azure app service](../app-service/app-service-web-tutorial-rest-api.md)voor meer informatie.

> [!NOTE]
> U kunt ook aangepaste connectors maken in de gebruikers interface van PowerApps en Microsoft Flow, zonder gebruik te maken van een OpenAPI-definitie. Zie [een aangepaste connector (PowerApps) registreren en gebruiken](https://powerapps.microsoft.com/tutorials/register-custom-api/) en [een aangepaste connector (Microsoft flow) registreren en gebruiken](/power-automate/developer/register-custom-api)voor meer informatie.

Als u de API-definitie wilt exporteren, voert u de volgende stappen uit:

1. Navigeer in het [Azure Portal](https://portal.azure.com)naar uw functie-app of een app service toepassing.

    Selecteer in het menu links onder **API**de optie **API-definitie**.

    :::image type="content" source="media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png" alt-text="Azure Functions API-definitie":::

2. De knop **exporteren naar PowerApps + Microsoft flow** moet beschikbaar zijn (als dat niet het geval is, moet u eerst een OpenAPI-definitie maken). Selecteer deze knop om het export proces te starten.

    ![Knop exporteren naar PowerApps + Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Selecteer de **export modus**:

    Met **Express** kunt u de aangepaste connector maken in de Azure Portal. Hiervoor moet u zijn aangemeld bij PowerApps of Microsoft Flow en gemachtigd zijn om connectors te maken in de doel omgeving. Deze methode wordt aanbevolen als aan deze twee vereisten kan worden voldaan. Als u deze modus gebruikt, volgt u de onderstaande instructies voor het [exporteren van snelle uitvoer](#express) .

    **Hand matig** kunt u de API-definitie exporteren, die u vervolgens importeert met behulp van de PowerApps-of Microsoft flow portals. Deze methode wordt aanbevolen als de gebruiker van Azure en de gebruiker die gemachtigd is om connectors te maken verschillende personen zijn of als de connector moet worden gemaakt in een andere Azure-Tenant. Als u deze modus gebruikt, volgt [u de onderstaande hand matige export](#manual) instructies.

    ![Export modus](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> De aangepaste connector gebruikt een *kopie* van de API-definitie, zodat PowerApps en Microsoft flow niet meteen weten of u wijzigingen aanbrengt in de toepassing en de bijbehorende API-definitie. Als u wijzigingen aanbrengt, herhaalt u de export stappen voor de nieuwe versie.

<a name="express"></a>
## <a name="use-express-export"></a>Snelle export gebruiken

Voer de volgende stappen uit om de export in **Express** -modus te volt ooien:

1. Zorg ervoor dat u bent aangemeld bij de PowerApps of Microsoft Flow Tenant waarnaar u wilt exporteren. 

2. Gebruik de instellingen zoals opgegeven in de tabel.

    |Instelling|Beschrijving|
    |--------|------------|
    |**Omgeving**|Selecteer de omgeving waarin de aangepaste connector moet worden opgeslagen. Zie voor meer informatie [Overzicht van omgevingen](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Aangepaste API-naam**|Voer een naam in, die PowerApps en Microsoft Flow bouwers worden weer geven in de lijst met connectors.|
    |**Beveiligings configuratie voorbereiden**|Geef indien nodig de beveiligings configuratie gegevens op die nodig zijn om gebruikers toegang te verlenen tot uw API. Dit voor beeld toont een API-sleutel. Zie voor meer informatie [verificatie type opgeven](#auth) hieronder.|
 
    ![Snelle export naar PowerApps en Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Klik op **OK**. De aangepaste connector is nu gemaakt en toegevoegd aan de omgeving die u hebt opgegeven.

<a name="manual"></a>
## <a name="use-manual-export"></a>Hand matig exporteren gebruiken

Voer de volgende stappen uit om het exporteren in de **hand matige** modus te volt ooien:

1. Klik op **downloaden** en sla het bestand op, of klik op de knop kopiëren en sla de URL op. U gebruikt het Download bestand of de URL tijdens het importeren.
 
    ![Hand matig exporteren naar PowerApps en Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Als uw API-definitie beveiligings definities bevat, worden deze definities in stap #2 genoemd. Tijdens het importeren worden deze definities door PowerApps en Microsoft Flow gedetecteerd en wordt u gevraagd om beveiligings gegevens op te vragen. Verzamel de referenties die zijn gerelateerd aan elke definitie voor gebruik in de volgende sectie. Zie voor meer informatie [verificatie type opgeven](#auth) hieronder.

    ![Beveiliging voor hand matig exporteren](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Dit voor beeld toont de definitie van de API-sleutel beveiliging die is opgenomen in de OpenAPI-definitie.

Nu u de API-definitie hebt geëxporteerd, importeert u deze voor het maken van een aangepaste connector in PowerApps en Microsoft Flow. Aangepaste connectors worden gedeeld tussen de twee services, zodat u de definitie slechts eenmaal hoeft te importeren.

Voer de volgende stappen uit om de API-definitie te importeren in PowerApps en Microsoft Flow:

1. Ga naar [powerapps.com](https://web.powerapps.com) of [flow.microsoft.com](https://flow.microsoft.com).

2. Klik in de rechter bovenhoek op het tandwiel pictogram en klik vervolgens op **aangepaste connectors**.

   ![Tandwielpictogram in service](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Klik op **aangepaste connector maken**en klik vervolgens op **een OpenAPI-definitie importeren**.

   ![Een aangepaste connector maken](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Voer een naam in voor de aangepaste connector, navigeer naar de OpenAPI-definitie die u hebt geëxporteerd en klik op **door gaan**.

   ![OpenAPI definitie uploaden](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. Controleer op het tabblad **Algemeen** de informatie die afkomstig is van de OpenAPI-definitie.

5. Als u wordt gevraagd om verificatie gegevens op het tabblad **beveiliging** op te geven, voert u de juiste waarden voor het verificatie type in. Klik op **Doorgaan**.

    ![Tabblad Beveiliging](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    In dit voor beeld worden de vereiste velden voor API-sleutel verificatie weer gegeven. De velden variëren afhankelijk van het verificatie type.

6. Op het tabblad **definities** worden alle bewerkingen die zijn gedefinieerd in uw OpenAPI-bestand automatisch ingevuld. Als alle vereiste bewerkingen zijn gedefinieerd, kunt u naar de volgende stap gaan. Als dat niet het geval is, kunt u hier bewerkingen toevoegen en wijzigen.

    ![Tabblad definities](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Dit voor beeld heeft één bewerking, met de naam `CalculateCosts` . De meta gegevens, zoals **Beschrijving**, zijn afkomstig uit het OpenAPI-bestand.

7. Klik boven aan de pagina op **connector maken** .

U kunt nu verbinding maken met de aangepaste connector in PowerApps en Microsoft Flow. Zie [uw aangepaste connector (PowerApps) registreren](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) en [uw aangepaste connector registreren (Microsoft flow)](/power-automate/get-started-flow-dev#create-a-custom-connector)voor meer informatie over het maken van connectors in PowerApps en Microsoft flow portals.

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Verificatietype opgeven

PowerApps en Microsoft Flow bieden ondersteuning voor een verzameling id-providers die verificatie bieden voor aangepaste connectors. Als voor uw API verificatie is vereist, moet u ervoor zorgen dat deze wordt vastgelegd als een _beveiligings definitie_ in uw OpenAPI-document, zoals in het volgende voor beeld:

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
Tijdens het exporteren geeft u de configuratie waarden op waarmee PowerApps en Microsoft Flow gebruikers kunnen verifiëren.

In deze sectie worden de verificatie typen beschreven die worden ondersteund in de **Express** -modus: API-sleutel, Azure Active Directory en algemene OAuth 2,0. PowerApps en Microsoft Flow ondersteunen ook basis verificatie en OAuth 2,0 voor specifieke services zoals Dropbox, Facebook en Sales Force.

### <a name="api-key"></a>API-sleutel
Wanneer u een API-sleutel gebruikt, worden de gebruikers van uw connector gevraagd de sleutel op te geven wanneer ze een verbinding maken. U geeft de naam van een API-sleutel op om ze te helpen begrijpen welke sleutel nodig is. In het vorige voor beeld gebruiken we de naam `API Key (contact meganb@contoso.com)` zodat mensen weten waar ze informatie over de API-sleutel moeten ophalen. Voor Azure Functions is de sleutel doorgaans een van de sleutels van de host, die betrekking hebben op verschillende functies in de functie-app.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Wanneer u Azure AD gebruikt, hebt u twee Azure AD-toepassings registraties nodig: een voor de API zelf en één voor de aangepaste connector:

- Als u de registratie voor de API wilt configureren, gebruikt u de functie voor [app service verificatie/autorisatie](../app-service/configure-authentication-provider-aad.md) .

- Als u registratie voor de connector wilt configureren, volgt u de stappen in [een Azure AD-toepassing toevoegen](../active-directory/develop/quickstart-register-app.md). De registratie moet gedelegeerde toegang hebben tot uw API en een antwoord-URL van `https://msmanaged-na.consent.azure-apim.net/redirect` . 

Zie de registratie voorbeelden van Azure AD voor [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) en [Microsoft flow](/connectors/custom-connectors/azure-active-directory-authentication)voor meer informatie. In deze voor beelden wordt Azure Resource Manager als de API gebruikt. Vervang de API door de volgende stappen uit te voeren.

De volgende configuratie waarden zijn vereist:
- **Client-id** : de client-id van de connector Azure AD-registratie
- **Client geheim** : het client geheim van uw connector Azure AD-registratie
- **Aanmeldings-URL** : de basis-URL voor Azure AD. Normaal gesp roken in azure `https://login.windows.net` .
- **Tenant-id** : de id van de Tenant die moet worden gebruikt voor de aanmelding. Deze ID moet ' common ' zijn of de ID van de Tenant waarin de connector is gemaakt.
- **Bron-URL** : de bron-URL van de Azure AD-registratie voor uw API

> [!IMPORTANT]
> Als iemand anders de API-definitie gaat importeren in PowerApps en Microsoft Flow als onderdeel van de hand matige stroom, moet u deze opgeven met de client-ID en het client geheim van de registratie van de *connector*, evenals de bron-URL van uw API. Zorg ervoor dat deze geheimen veilig worden beheerd. **Deel de beveiligings referenties van de API zelf niet.**

### <a name="generic-oauth-20"></a>Algemene OAuth 2.0
Wanneer u algemene OAuth 2,0 gebruikt, kunt u integreren met elke OAuth 2,0-provider. Als u dit doet, kunt u werken met aangepaste providers die niet systeem eigen worden ondersteund.

De volgende configuratie waarden zijn vereist:
- **Client-id** : de OAuth 2,0-client-id
- **Client geheim** : het OAuth 2,0-client geheim
- **Autorisatie-URL** : de OAuth 2,0-autorisatie-URL
- **URL van token** : de URL van het OAuth 2,0-token
- **URL vernieuwen** -de OAuth 2,0-vernieuwings-URL
