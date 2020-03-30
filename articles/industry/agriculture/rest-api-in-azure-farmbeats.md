---
title: Verwijzingen
description: ''
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 318b3e3f774a4381434fd56154f4c0d95c28c7a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479512"
---
# <a name="references"></a>Verwijzingen

In dit artikel worden de Azure FarmBeats-API's beschreven.

## <a name="rest-api"></a>REST API

De Azure FarmBeats API's bieden agrarische bedrijven een gestandaardiseerde RESTful-interface met JSON-gebaseerde antwoorden om u te helpen profiteren van Azure FarmBeats-mogelijkheden, zoals:

- API's om sensor, camera, drone, weer, satelliet en samengestelde grondgegevens te krijgen.
- Normalisatie en contextualisatie van gegevens tussen veelvoorkomende gegevensproviders.
- Schematized toegang en query mogelijkheden op alle ingenomen gegevens.
- Automatische generatie van metadata die kan worden opgevraagd, op basis van agronomische functies.
- Automatisch gegenereerde tijdvolgordeaggregaten voor snelle modelbouw.
- Geïntegreerde Azure Data Factory-engine om eenvoudig aangepaste gegevensverwerkingspijplijnen te bouwen.

## <a name="application-development"></a>Toepassingen ontwikkelen

De FarmBeats API's bevatten technische documentatie van Swagger. Zie [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)voor informatie over alle API's en de bijbehorende verzoeken of antwoorden.

In de volgende tabel worden alle objecten en bronnen in FarmBeats Datahub samengevat:

| Objecten en resources | Beschrijving
--- | ---|
Boerderij | Farm komt overeen met een fysieke locatie van belang binnen het FarmBeats systeem. Elke boerderij heeft een boerderijnaam en een unieke boerderij-ID. |
Apparaat  | Het apparaat komt overeen met een fysiek apparaat dat op de boerderij aanwezig is. Elk apparaat heeft een unieke apparaat-ID. Een apparaat is meestal ingericht op een boerderij met een bedrijfs-ID.
DeviceModel  | DeviceModel komt overeen met de metagegevens van het apparaat, zoals de fabrikant en het type apparaat, dat gateway of knooppunt is.
Sensor  | Sensor komt overeen met een fysieke sensor die waarden registreert. Een sensor is meestal verbonden met een apparaat met een apparaat-id.
SensorModel  | SensorModel komt overeen met de metadata van de sensor, zoals de fabrikant, het type sensor, dat analoog of digitaal is, en de sensormeting, zoals omgevingstemperatuur en druk.
Telemetrie  | Telemetrie biedt de mogelijkheid om telemetrieberichten te lezen voor een bepaalde sensor en tijdsbereik.
Taak  | Taak komt overeen met elke werkstroom van activiteiten die worden uitgevoerd in het FarmBeats-systeem om een gewenste uitvoer te krijgen. Elke taak is gekoppeld aan een taak-ID en taaktype.
JobType  | JobType komt overeen met verschillende taaktypen die door het systeem worden ondersteund. Systeemgedefinieerde en door de gebruiker gedefinieerde taaktypen zijn opgenomen.
ExtendedType  | ExtendedType komt overeen met de lijst met systeem- en door de gebruiker gedefinieerde typen in het systeem. ExtendedType helpt bij het instellen van een nieuwe sensor, scène of scènebestandstype in het FarmBeats-systeem.
Partner  | Partner komt overeen met de sensor- en beeldintegratiepartner voor FarmBeats.
Scène  | Scène komt overeen met elke gegenereerde uitvoer in de context van een farm. Elke scène heeft een scène-id, scènebron, scènetype en farm-id die eraan is gekoppeld. Aan elke scène-ID kunnen meerdere scènebestanden zijn gekoppeld.
Scènebestand |SceneFile komt overeen met alle bestanden die worden gegenereerd voor één scène. Aan één scène-id kunnen meerdere SceneFile-id's zijn gekoppeld.
Regel  |Regel komt overeen met een voorwaarde voor farm-gerelateerde gegevens om een waarschuwing te activeren. Elke regel is in de context van de gegevens van een bedrijf.
Waarschuwing  | Waarschuwing komt overeen met een melding, die wordt gegenereerd wanneer aan een regelvoorwaarde is voldaan. Elke waarschuwing is in de context van een regel.
Roldefinitie  | RoleDefinition definieert toegestane en niet-toegestane acties voor een rol.
RoleAssignment  |RoleAssignment komt overeen met de toewijzing van een rol aan een gebruiker of een serviceprincipal.

### <a name="data-format"></a>Gegevensindeling

JSON is een veelgebruikte taalonafhankelijke gegevensindeling die een eenvoudige tekstweergave van willekeurige gegevensstructuren biedt. Zie voor meer informatie de [JSON website.](https://www.json.org/)

## <a name="authentication-and-authorization"></a>Verificatie en autorisatie

HTTP-aanvragen voor de REST-API worden beveiligd met Azure Active Directory (Azure AD).
Als u een geverifieerd verzoek wilt indienen bij de REST-API's, vereist clientcode verificatie met geldige referenties voordat u de API aanroepen. Verificatie wordt gecoördineerd tussen de verschillende actoren door Azure AD. Het biedt uw client een toegangstoken als bewijs van de verificatie. Het token wordt vervolgens verzonden in de HTTP Authorization-header van REST API-aanvragen. Zie [Azure Active Directory](https://portal.azure.com) voor ontwikkelaars voor meer informatie over Azure AD-verificatie.

Het toegangstoken moet worden verzonden in volgende API-aanvragen, in de sectie koptekst, als:

```
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>HTTP-aanvraagkoppen

Dit zijn de meest voorkomende aanvraagkoppen die u moet opgeven wanneer u een API-aanroep naar Azure FarmBeats Datahub maakt.


**Header** | **Beschrijving en voorbeeld**
--- | ---
Content-Type  | De aanvraagindeling (Inhoudstype:<format>toepassing/ ). Voor Azure FarmBeats Datahub API's is de indeling JSON. Inhoudstype: toepassing/json
Autorisatie  | Hiermee geeft u het toegangstoken op dat nodig is om een API-aanroep te maken. Autorisatie:> van Access-Token aan touw <
Accepteren | De antwoordnotatie. Voor Azure FarmBeats Datahub API's is de indeling JSON. Accepteren: toepassing/json

### <a name="api-requests"></a>API-aanvragen

Als u een REST API-aanvraag wilt indienen, combineert u de methode HTTP (GET, POST, PUT of DELETE), de URL naar de API-service, de URI naar een bron om op te vragen, gegevens in te dienen, bij te werken of te verwijderen en voegt u vervolgens een of meer HTTP-aanvraagkoppen toe.

De URL naar de API-service is bijvoorbeeld\<uw Datahub-URL https:// uw datahub-websitenaam>.azurewebsites.net.

Optioneel u queryparameters opnemen in GET-oproepen om te filteren, de grootte van de gegevens te beperken en de gegevens in de antwoorden te sorteren.

De volgende voorbeeldaanvraag wordt gebruikt om de lijst met apparaten op te halen:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

De meeste GET, POST, en PUT oproepen vereisen een JSON aanvraag lichaam.

Met de volgende voorbeeldaanvraag wordt een apparaat gemaakt. Deze aanvraag heeft input JSON met de aanvraaginstantie.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>Queryparameters

Voor REST GET-aanroepen u de grootte van de gegevens in een API-antwoord filteren, beperken en sorteren door een of meer queryparameters op de aanvraag-URI op te nemen. Zie voor de queryparameters de API-documentatie en de afzonderlijke GET-aanroepen.
Wanneer u bijvoorbeeld de lijst met apparaten opvraagt (GET-aanroep op /Apparaat), kunnen de volgende queryparameters worden opgegeven:

![Lijst van apparaten](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>Foutafhandeling

Azure FarmBeats Datahub API's retourneren de standaard HTTP-fouten. De meest voorkomende foutcodes zijn als volgt:

 |Foutcode             | Beschrijving |
 |---                    | --- |
 |200                    | Geslaagd |
 |201                    | Maak (Post) Succes |
 |400                    | Slecht verzoek. Er is een fout in de aanvraag. |
 |401                    | Onbevoegde. De beller van de API is niet gemachtigd om toegang te krijgen tot de bron. |
 |404                    | Resource niet gevonden |
 |5XX                    | Fout interne server. De foutcodes die beginnen met 5XX betekent dat er een fout op de server. Raadpleeg serverlogboeken en de volgende sectie voor meer informatie. |


Naast de standaard HTTP-fouten retourneert Azure FarmBeats Datahub API's ook interne fouten in de volgende indeling:

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

In dit voorbeeld is het verplichte veld 'Naam' niet opgegeven in de invoerpayload wanneer een farm is gemaakt. De resulterende foutmelding zou zijn:

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Gebruikers- of app-registraties toevoegen aan Azure Active Directory

Azure FarmBeats API's kunnen worden geopend door een gebruiker of een app-registratie in Azure Active Directory. Voer de volgende stappen uit om een app-registratie in Azure Active Directory te maken:

1. Ga naar de [Azure-portal](https://portal.azure.com)en selecteer **Azure Active Directory** > **App-registraties** > **Nieuwe registratie**. U ook een bestaand account gebruiken.
2. Ga als volgt te werk voor een nieuw account:

    - Voer een naam in.
    - Selecteer **alleen accounts in deze organisatiemap (één tenant).**
    - Gebruik de standaardwaarden in de rest van de velden.
    - Selecteer **Registreren**.

3. Ga als volgt te werk in het deelvenster **Overzicht van** nieuwe en bestaande apps:

    - Leg de **client-id** en **de tenant-id vast**.
    - Ga naar **Certificaten en Geheimen** om een nieuw klantgeheim te genereren en het **clientgeheim**vast te leggen.
    - Ga terug naar **Overzicht**en selecteer de koppeling naast **Toepassing beheren in de lokale map**.
    - Ga naar **Eigenschappen** om de **object-id**vast te leggen.

4. Ga naar je [Datahub Swagger](https://<yourdatahub>.azurewebsites.net/swagger/index.html) en ga als volgt te werk:
    - Ga naar de **API RoleAssignment**.
    - Voer een bericht uit om een **object RoleAssignment** te maken voor de **object-id** die u zojuist hebt gemaakt.

  > [!NOTE]
  > Zie [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)voor meer informatie over het toevoegen van gebruikers en Active Directory-registratie.

Nadat u de vorige stappen hebt voltooid, kan uw app-registratie (client) de Azure FarmBeats-API's aanroepen met behulp van een toegangstoken via verificatie aan toonder.

Gebruik het toegangstoken om het in volgende API-aanvragen in de kopsectie te verzenden als:

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
