---
title: Verwijzingen voor FarmBeats
description: ''
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 057037807a75e50eb2305bfab19d1fcff7fe77ce
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889596"
---
# <a name="references"></a>Verwijzingen

Hieronder vindt u een verzameling notities en instructies voor het overzicht van de Azure FarmBeats-Api's.

## <a name="rest-api"></a>REST-API

De Azure FarmBeats-Api's bieden agrarische ondernemingen met een gestandaardiseerde, doorgestuurde interface met antwoorden op basis van JSON. Dit helpt u bij het gebruik van Azure FarmBeats-mogelijkheden:

- API'S voor het verkrijgen van sensor-, camera-, Drone-, weers-, satelliet-en geaard-gegevens.
- Normalisatie/contextualization van gegevens over common data providers.
- Geschematiseerde-toegang en query mogelijkheden op alle opgenomen gegevens.
- Automatische generatie van meta gegevens die kunnen worden opgevraagd, op basis van agronomic-functies.  
- Automatisch gegenereerde tijdreeks aggregaties voor het snel bouwen van modellen.
- Geïntegreerde Azure Data Factory-Engine (ADF) om eenvoudig aangepaste gegevens verwerkings pijplijnen te bouwen.

## <a name="application-development"></a>Toepassingen ontwikkelen

De Api's bevatten technische documentatie voor Swagger. Zie [Swagger](https://aka.ms/FarmBeatsDatahubSwagger) voor informatie over alle api's en de bijbehorende aanvragen/antwoorden.

Dit is een samen vatting van alle objecten/resources in FarmBeats data hub:

Voorzien | De farm komt overeen met een fysieke locatie van belang in het FarmBeats-systeem. Elke farm heeft een farm naam en een unieke Farm-ID.
--- | ---|
Apparaat  | Het apparaat komt overeen met een fysiek apparaat dat aanwezig is in de farm. Elk apparaat heeft een unieke apparaat-ID. Het apparaat wordt doorgaans ingericht voor een farm met een farm-ID.
deviceModel  | DeviceModel komt overeen met de meta gegevens van het apparaat, zoals de fabrikant, het type van het apparaat of de gateway of het knoop punt.
Sensoren  | Sensor komt overeen met een fysieke sensor waarmee waarden worden vastgelegd. Een sensor is doorgaans verbonden met een apparaat met een apparaat-ID.
SensorModel  | SensorModel komt overeen met de meta gegevens van de sensor, zoals de fabrikant, het type van de sensor ofwel analoog of digitaal, de sensor meting, zoals omgevings temperatuur, druk enz.
Telemetrie  | Telemetrie biedt de mogelijkheid om telemetrie-berichten te lezen voor een bepaalde sensor en tijds bereik.
Job  | De taak komt overeen met elke werk stroom van activiteiten die in het FarmBeats-systeem worden uitgevoerd om een gewenste uitvoer te krijgen. Elke taak is gekoppeld aan een taak-ID en taak type.
Taak type  | Taak type komt overeen met verschillende taak typen die door het systeem worden ondersteund. Dit omvat het systeem dat is gedefinieerd & door de gebruiker gedefinieerde taak typen.
Extended type  | Extended type komt overeen met de lijst met door de gebruiker gedefinieerde typen systeem & in het systeem. Dit helpt bij het instellen van een nieuwe sensor of scène of Scenefile type in het FarmBeats-systeem.
Partner  | Partner komt overeen met de sensor/imagey Integration-partner voor FarmBeats
Scène  | Scène komt overeen met een gegenereerde uitvoer in de context van een farm. Aan elke scène zijn een scène-ID, scène bron, type scène en Farm-ID gekoppeld. Aan elke scène-ID kunnen meerdere scène bestanden zijn gekoppeld.
SceneFile |SceneFile komt overeen met alle bestanden, die voor één scène worden gegenereerd. Er kunnen meerdere SceneFile-Id's aan één scène-ID zijn gekoppeld.
Regel  |De regel komt overeen met een voor waarde voor Farm gegevens om een waarschuwing te activeren. Elke regel wordt weer gegeven in de context van de gegevens van een farm.
Waarschuwing  | De waarschuwing komt overeen met een melding die wordt gegenereerd wanneer aan de voor waarde van een regel wordt voldaan. Elke waarschuwing wordt weer in de context van een regel.
RoleDefinition  | RoleDefinition definieert toegestane en niet-toegestane acties voor een rol.
RoleAssignment  |RoleAssignment komt overeen met de toewijzing van een rol aan een gebruiker of een service-principal.

**Gegevensindeling**

JSON (JavaScript Object Notation) is een gemeen schappelijke, taal onafhankelijke gegevens indeling die een eenvoudige tekst weergave bevat van wille keurige gegevens structuren. Zie json.org voor meer informatie.

## <a name="authentication-and-authorization"></a>Verificatie en autorisatie

HTTP-aanvragen voor de REST API worden beveiligd met Azure Active Directory (Azure AD).
Voor het maken van een geverifieerde aanvraag voor de REST-Api's, vereist de client code verificatie met geldige referenties voordat u de API kunt aanroepen. Verificatie wordt gecoördineerd tussen de verschillende actoren van Azure AD en biedt uw client een toegangs token als bewijs van de verificatie. Het token wordt vervolgens verzonden in de HTTP-autorisatie-header van REST API-aanvragen. Zie [Azure Active Directory](https://portal.azure.com) voor ontwikkel aars voor meer informatie over Azure AD-verificatie.

Het toegangs token moet in volgende API-aanvragen in de koptekst sectie worden verzonden als:

```
headers = {"Authorization": "Bearer " + **access_token**}
```

**HTTP-aanvraag headers**

Hier volgen de meest voorkomende aanvraag headers die moeten worden opgegeven bij het maken van een API-aanroep naar Azure FarmBeats data hub:


**Header** | **Beschrijving en voor beeld**
--- | ---
Content-Type  | De indeling van de aanvraag (content-type: Application/<format>) voor de Azure FarmBeats data hub Api's-indeling is JSON. Content-type: Application/JSON
Autorisatie  | Hiermee geeft u het toegangs token op dat vereist is om een API-aanroep te maken. **Autorisatie: Bearer < Access-token >**
zodat | De antwoord indeling. Voor Azure FarmBeats data hub-Api's is de indeling JSON **Accept: Application/JSON**

**API-aanvragen**

Als u een REST API aanvraag wilt indienen, combineert u de HTTP-methode (GET, POST, PUT of DELETE), de URL van de API-service, de URI naar een resource om een query uit te voeren, gegevens in te dienen bij, bij te werken of te verwijderen, en een of meer HTTP-aanvraag headers.

De URL naar de API-service is uw data hub-URL https://\<yourdatahub-website-name >. azurewebsites. net optioneel, kunt u query parameters toevoegen in GET-aanroepen om te filteren, de grootte van de gegevens in de antwoorden te beperken en te sorteren.

De onderstaande voorbeeld aanvraag is om de lijst met apparaten op te halen:

```azurepowershell-interactive
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

Voor de meeste GET-, POST-en PUT-aanroepen is een JSON-aanvraag tekst vereist.

De onderstaande voorbeeld aanvraag is het maken van een apparaat (deze heeft een invoer-JSON met de hoofd tekst van de aanvraag).

```json
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

**Query parameters**

Voor REST **Get** -aanroepen kunt u de grootte van de gegevens in een API-antwoord filteren, beperken en sorteren door een of meer query parameters op de aanvraag-URI op te nemen. Raadpleeg de API-documentatie voor de query parameters en Bekijk de afzonderlijke GET-aanroepen.
Als u bijvoorbeeld een query uitvoert op de lijst met apparaten (aanroep ophalen op/Device), kunnen de volgende query parameters worden opgegeven:  

![Maten van project-Farm](./media/for-references/query-parameters-device-1.png)

**Foutafhandeling**

De Azure FarmBeats data hub API retourneert de standaard HTTP-fouten. De meest voorkomende fout codes zijn als volgt:

 |Fout code             | Beschrijving |
 |---                    | --- |
 |200                    | Geslaagd |
 |201                    | Maken (post) geslaagd |
 |400                    | Ongeldige aanvraag. Er is een fout opgetreden in de aanvraag |
 |401                    | Gasten. De aanroeper van de API is niet gemachtigd om toegang te krijgen tot de resource |
 |404                    | Kan de resource niet vinden |
 |5XX                    | Interne server fout. De fout codes die beginnen met 5XX betekenen dat er een fout is opgetreden op de server. Raadpleeg server logboeken en de volgende sectie voor meer informatie. |


Naast de standaard HTTP-fouten retour neren Api's van Azure FarmBeats data hub ook interne fouten in de onderstaande notatie:

    ```
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
    ```

Voor beeld: bij het maken van een farm is een verplicht veld ' naam ' niet opgegeven in de invoer lading. Het volgende fout bericht wordt weer gegeven:

    ```json
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
    ```

## <a name="adding-users-or-app-registrations-to-azure-active-directory"></a>Gebruikers of app-registraties toevoegen aan Azure Active Directory

Azure FarmBeats-Api's kunnen worden gebruikt door een gebruiker of een app-registratie in de Azure Active Directory. Ga als volgt te werk om een app-registratie te maken op uw Azure Active Directory:  

1. Ga naar [Azure Portal](https://portal.azure.com) **Azure Active Directory, app-registraties**> **nieuwe registratie**. U kunt ook een bestaand account gebruiken.
2. Voor een nieuw account zorgt u ervoor dat u het volgende kunt volt ooien:

    - Voer een naam in
    - Selecteer **alleen accounts in deze organisatie Directory (één Tenant)**
    - De standaard waarden in de rest van de velden
    - Selecteer **registreren**

3. In de nieuwe/bestaande app-registratie, **overzicht**, voert u het volgende uit:

    - Leg de **client-id** en **Tenant-id**vast.
    - Ga naar **certificaten en geheimen** om een nieuw client geheim te genereren en het **client geheim**vast te leggen.
    - Ga terug naar overzicht en klik op de koppeling naast **toepassing beheren in de lokale map** .
    - Ga naar **Eigenschappen** om de **object-id** vast te leggen

4. Ga naar uw [Data hub Swagger](https://<yourdatahub>.azurewebsites.net/swagger/index.html) en voer de volgende stappen uit:
    - Ga naar **RoleAssignment-API**
    - Voer een **bericht** uit om een RoleAssignment te maken voor de **object-id** die u zojuist hebt gemaakt. – De invoer-JSON is:

  > [!NOTE]
  > Zie [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) voor meer informatie over het toevoegen van gebruikers en het registreren van een AD-advertentie.

Nadat u de bovenstaande stappen hebt voltooid, kunt u met de app-registratie (client) de Azure FarmBeats-Api's aanroepen met behulp van een toegangs token via Bearer-verificatie.  

Gebruik het toegangs token om dit in volgende API-aanvragen in de koptekst sectie te verzenden als:

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
