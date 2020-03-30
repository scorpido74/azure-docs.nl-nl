---
title: Beheerde identiteiten op een virtuele machine gebruiken om toegangstoken te verkrijgen - Azure AD
description: Stapsgewijze instructies en voorbeelden voor het gebruik van beheerde identiteiten voor Azure-resources op een virtuele machine om een OAuth-toegangstoken te verkrijgen.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a58103bad3914bd0c0c6e70f8e3d2882271e1070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049197"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Beheerde identiteiten voor Azure-resources op een Azure VM gebruiken om een toegangstoken te verkrijgen 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Beheerde identiteiten voor Azure-resources bieden Azure-services een automatisch beheerde identiteit in Azure Active Directory. U deze identiteit gebruiken om te verifiëren voor elke service die Azure AD-verificatie ondersteunt, zonder referenties in uw code te hebben. 

Dit artikel bevat verschillende code- en scriptvoorbeelden voor tokenacquisitie, evenals richtlijnen voor belangrijke onderwerpen zoals het verwerken van tokenexpiratie en HTTP-fouten. 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Als u van plan bent de Azure PowerShell-voorbeelden in dit artikel te gebruiken, moet u de nieuwste versie van [Azure PowerShell](/powershell/azure/install-az-ps)installeren.


> [!IMPORTANT]
> - In dit artikel wordt ervan uitgegaan dat alle voorbeeldcode/script in dit artikel ervan uitgaat dat de client wordt uitgevoerd op een virtuele machine met beheerde identiteiten voor Azure-resources. Gebruik de functie 'Connect' van de virtuele machine in de Azure-portal om op afstand verbinding te maken met uw vm. Zie [Beheerde identiteiten voor Azure-resources op een VM configureren met behulp van de Azure-portal](qs-configure-portal-windows-vm.md)of een van de varianten (met PowerShell, CLI, een sjabloon of een Azure SDK) voor meer informatie over het inschakelen van beheerde identiteiten voor Azure-resources op een vm. 

> [!IMPORTANT]
> - De beveiligingsgrens van beheerde identiteiten voor Azure-resources is de bron waarop deze wordt gebruikt. Alle code/scripts die op een virtuele machine worden uitgevoerd, kunnen tokens aanvragen en ophalen voor alle beheerde identiteiten die erop beschikbaar zijn. 

## <a name="overview"></a>Overzicht

Een clienttoepassing kan beheerde identiteiten aanvragen voor [azure-bronnen die alleen app-toegangstoken gebruiken](../develop/developer-glossary.md#access-token) voor toegang tot een bepaalde bron. Het token is [gebaseerd op de beheerde identiteiten voor azure resources service principal](overview.md#how-does-the-managed-identities-for-azure-resources-work). Als zodanig is het niet nodig dat de klant zich registreert om een toegangstoken te verkrijgen onder zijn eigen serviceprincipal. Het token is geschikt voor gebruik als token aan toonder in [service-to-service-gesprekken waarvoor clientreferenties vereist zijn.](../develop/v2-oauth2-client-creds-grant-flow.md)

|  |  |
| -------------- | -------------------- |
| [Een token downloaden met HTTP](#get-a-token-using-http) | Protocoldetails voor beheerde identiteiten voor tokeneindpunt azure-bronnen |
| [Een token downloaden met de Microsoft.Azure.Services.AppAuthentication-bibliotheek voor .NET](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Voorbeeld van het gebruik van de Microsoft.Azure.Services.AppAuthentication-bibliotheek vanaf een .NET-client
| [Een token opdoen met C #](#get-a-token-using-c) | Voorbeeld van het gebruik van beheerde identiteiten voor het REST-eindpunt van Azure-resources van een C#-client |
| [Een token opdoen via Java](#get-a-token-using-java) | Voorbeeld van het gebruik van beheerde identiteiten voor het REST-eindpunt van Azure-bronnen van een Java-client |
| [Een token opdoen met Go](#get-a-token-using-go) | Voorbeeld van het gebruik van beheerde identiteiten voor restpunt van Azure-resources vanuit een Go-client |
| [Een token opgebruiken met Azure PowerShell](#get-a-token-using-azure-powershell) | Voorbeeld van het gebruik van beheerde identiteiten voor REST-eindpunt van Azure-resources van een PowerShell-client |
| [Een token opdoen met CURL](#get-a-token-using-curl) | Voorbeeld van het gebruik van beheerde identiteiten voor het REST-eindpunt van Azure-bronnen van een Bash/CURL-client |
| Tokencache verwerken | Richtlijnen voor het verwerken van verlopen toegangstokens |
| [Foutafhandeling](#error-handling) | Richtlijnen voor het verwerken van HTTP-fouten die zijn geretourneerd uit de beheerde identiteiten voor tokeneindpunt azure-bronnen |
| [Bron-iD's voor Azure-services](#resource-ids-for-azure-services) | Waar u bron-i-adressen voor ondersteunde Azure-services krijgen |

## <a name="get-a-token-using-http"></a>Een token downloaden met HTTP 

De fundamentele interface voor het verkrijgen van een access token is gebaseerd op REST, waardoor het toegankelijk is voor elke clienttoepassing die op de VM wordt uitgevoerd en http rest-aanroepen kan uitvoeren. Dit is vergelijkbaar met het Azure AD-programmeermodel, behalve dat de client een eindpunt op de virtuele machine gebruikt (vs een Azure AD-eindpunt).

Voorbeeldaanvraag met behulp van het IMDS-eindpunt (Azure Instance Metadata Service) *(aanbevolen):*

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Element | Beschrijving |
| ------- | ----------- |
| `GET` | Het HTTP-werkwoord, dat aangeeft dat u gegevens uit het eindpunt wilt ophalen. In dit geval een OAuth-toegangstoken. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Het beheerde identiteiten voor het eindpunt van Azure-resources voor de instantiemetagegevensservice. |
| `api-version`  | Een parameter querytekenreeks die de API-versie voor het IMDS-eindpunt aangeeft. Gebruik api-versie `2018-02-01` of meer. |
| `resource` | Een parameter querytekenreeks die de URI van de app-id van de doelbron aangeeft. Het verschijnt ook `aud` in de (publieks)claim van het uitgegeven token. In dit voorbeeld wordt een token gevraagd om toegang `https://management.azure.com/`te krijgen tot Azure Resource Manager, met een URI voor app-id's van . |
| `Metadata` | Een http-aanvraagkopveld, vereist door beheerde identiteiten voor Azure-resources als beperking tegen De Server Side Request Forgery (SSRF) aanval. Deze waarde moet worden ingesteld op "true", in alle kleine letters. |
| `object_id` | (Optioneel) Een querytekenreeksparameter die aangeeft hoe object_id van de beheerde identiteit waarvoor u het token wilt hebben. Vereist als uw VM meerdere door de gebruiker toegewezen beheerde identiteiten heeft.|
| `client_id` | (Optioneel) Een querytekenreeksparameter die de client_id aangeeft van de beheerde identiteit waarvoor u het token wilt hebben. Vereist als uw VM meerdere door de gebruiker toegewezen beheerde identiteiten heeft.|
| `mi_res_id` | (Optioneel) Een querytekenreeksparameter die de mi_res_id (Azure Resource ID) aangeeft van de beheerde identiteit waarvoor u het token wilt hebben. Vereist als uw VM meerdere door de gebruiker toegewezen beheerde identiteiten heeft. |

Voorbeeldaanvraag met de beheerde identiteiten voor het VM Extension Endpoint van Azure-bronnen *(gepland voor afschaffing in januari 2019):*

```http
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Beschrijving |
| ------- | ----------- |
| `GET` | Het HTTP-werkwoord, dat aangeeft dat u gegevens uit het eindpunt wilt ophalen. In dit geval een OAuth-toegangstoken. | 
| `http://localhost:50342/oauth2/token` | Het beheerde identiteiten voor het eindpunt van Azure-resources, waarbij 50342 de standaardpoort is en configureerbaar is. |
| `resource` | Een parameter querytekenreeks die de URI van de app-id van de doelbron aangeeft. Het verschijnt ook `aud` in de (publieks)claim van het uitgegeven token. In dit voorbeeld wordt een token gevraagd om toegang `https://management.azure.com/`te krijgen tot Azure Resource Manager, met een URI voor app-id's van . |
| `Metadata` | Een http-aanvraagkopveld, vereist door beheerde identiteiten voor Azure-resources als beperking tegen De Server Side Request Forgery (SSRF) aanval. Deze waarde moet worden ingesteld op "true", in alle kleine letters.|
| `object_id` | (Optioneel) Een querytekenreeksparameter die aangeeft hoe object_id van de beheerde identiteit waarvoor u het token wilt hebben. Vereist als uw VM meerdere door de gebruiker toegewezen beheerde identiteiten heeft.|
| `client_id` | (Optioneel) Een querytekenreeksparameter die de client_id aangeeft van de beheerde identiteit waarvoor u het token wilt hebben. Vereist als uw VM meerdere door de gebruiker toegewezen beheerde identiteiten heeft.|

Voorbeeldrespons:

```json
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Element | Beschrijving |
| ------- | ----------- |
| `access_token` | Het gevraagde toegangstoken. Wanneer u een beveiligde REST-API aanroept, wordt het token ingesloten in het veld van de `Authorization` aanvraagkoptekst als een 'drager'-token, waardoor de API de beller kan verifiëren. | 
| `refresh_token` | Niet gebruikt door beheerde identiteiten voor Azure-resources. |
| `expires_in` | Het aantal seconden dat het toegangstoken geldig blijft, voordat het afloopt, vanaf het moment van uitgifte. Tijd van uitgifte is te vinden `iat` in de claim van het token. |
| `expires_on` | De tijdspanne wanneer het toegangstoken verloopt. De datum wordt weergegeven als het aantal seconden van "1970-01-01T0:0:0Z UTC" `exp` (komt overeen met de claim van het token). |
| `not_before` | De tijdspanne waarin het toegangstoken van kracht wordt en kan worden geaccepteerd. De datum wordt weergegeven als het aantal seconden van "1970-01-01T0:0:0Z UTC" `nbf` (komt overeen met de claim van het token). |
| `resource` | De bron waarvoor het toegangstoken is `resource` aangevraagd, komt overeen met de parameter querytekenreeks van de aanvraag. |
| `token_type` | Het type token, dat een toegangstoken "Drager" is, wat betekent dat de bron toegang kan geven tot de drager van dit token. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Een token downloaden met de Microsoft.Azure.Services.AppAuthentication-bibliotheek voor .NET

Voor .NET-toepassingen en -functies is de eenvoudigste manier om te werken met beheerde identiteiten voor Azure-resources via het Microsoft.Azure.Services.AppAuthentication-pakket. Met deze bibliotheek u uw code ook lokaal testen op uw ontwikkelingsmachine met behulp van uw gebruikersaccount van Visual Studio, Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)of Active Directory Integrated Authentication. Zie de [verwijzing naar Microsoft.Azure.Services.AppAuthentication](/azure/key-vault/service-to-service-authentication)voor meer informatie over lokale ontwikkelingsopties met deze bibliotheek. In deze sectie ziet u hoe u aan de slag met de bibliotheek in uw code.

1. Voeg verwijzingen naar de [pakketten Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) en [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet toe aan uw toepassing.

2.  Voeg de volgende code toe aan uw toepassing:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```
    
Zie de [verwijzing Microsoft.Azure.Services.AppAuthentication](/azure/key-vault/service-to-service-authentication) en de bewerkingen die worden blootgesteld voor meer informatie over Microsoft.Azure.Services.AppAuthentication en de [app-service en KeyVault met beheerde identiteiten voor Azure-bronnen .NET.](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

## <a name="get-a-token-using-c"></a>Een token opdoen met C #

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire managed identities for Azure resources token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

## <a name="get-a-token-using-java"></a>Een token opdoen via Java

Gebruik deze [JSON-bibliotheek](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) om een token op te halen met Java.

```Java
import java.io.*;
import java.net.*;
import com.fasterxml.jackson.core.*;
 
class GetMSIToken {
    public static void main(String[] args) throws Exception {
 
        URL msiEndpoint = new URL("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
        HttpURLConnection con = (HttpURLConnection) msiEndpoint.openConnection();
        con.setRequestMethod("GET");
        con.setRequestProperty("Metadata", "true");
 
        if (con.getResponseCode()!=200) {
            throw new Exception("Error calling managed identity token endpoint.");
        }
 
        InputStream responseStream = con.getInputStream();
 
        JsonFactory factory = new JsonFactory();
        JsonParser parser = factory.createParser(responseStream);
 
        while(!parser.isClosed()){
            JsonToken jsonToken = parser.nextToken();
 
            if(JsonToken.FIELD_NAME.equals(jsonToken)){
                String fieldName = parser.getCurrentName();
                jsonToken = parser.nextToken();
 
                if("access_token".equals(fieldName)){
                    String accesstoken = parser.getValueAsString();
                    System.out.println("Access Token: " + accesstoken.substring(0,5)+ "..." + accesstoken.substring(accesstoken.length()-5));
                    return;
                }
            }
        }
    }
}
```

## <a name="get-a-token-using-go"></a>Een token opdoen met Go

```
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "net/url"
  "encoding/json"
)

type responseJson struct {
  AccessToken string `json:"access_token"`
  RefreshToken string `json:"refresh_token"`
  ExpiresIn string `json:"expires_in"`
  ExpiresOn string `json:"expires_on"`
  NotBefore string `json:"not_before"`
  Resource string `json:"resource"`
  TokenType string `json:"token_type"`
}

func main() {
    
    // Create HTTP request for a managed services for Azure resources token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01")
    if err != nil {
      fmt.Println("Error creating URL: ", err)
      return 
    }
    msi_parameters := url.Values{}
    msi_parameters.Add("resource", "https://management.azure.com/")
    msi_endpoint.RawQuery = msi_parameters.Encode()
    req, err := http.NewRequest("GET", msi_endpoint.String(), nil)
    if err != nil {
      fmt.Println("Error creating HTTP request: ", err)
      return 
    }
    req.Header.Add("Metadata", "true")

    // Call managed services for Azure resources token endpoint
    client := &http.Client{}
    resp, err := client.Do(req) 
    if err != nil{
      fmt.Println("Error calling token endpoint: ", err)
      return
    }

    // Pull out response body
    responseBytes,err := ioutil.ReadAll(resp.Body)
    defer resp.Body.Close()
    if err != nil {
      fmt.Println("Error reading response body : ", err)
      return
    }

    // Unmarshall response body into struct
    var r responseJson
    err = json.Unmarshal(responseBytes, &r)
    if err != nil {
      fmt.Println("Error unmarshalling the response:", err)
      return
    }

    // Print HTTP response and marshalled response body elements to console
    fmt.Println("Response status:", resp.Status)
    fmt.Println("access_token: ", r.AccessToken)
    fmt.Println("refresh_token: ", r.RefreshToken)
    fmt.Println("expires_in: ", r.ExpiresIn)
    fmt.Println("expires_on: ", r.ExpiresOn)
    fmt.Println("not_before: ", r.NotBefore)
    fmt.Println("resource: ", r.Resource)
    fmt.Println("token_type: ", r.TokenType)
}
```

## <a name="get-a-token-using-azure-powershell"></a>Een token opgebruiken met Azure PowerShell

In het volgende voorbeeld wordt uitgelegd hoe u de beheerde identiteiten voor het REST-eindpunt van Azure-bronnen gebruiken van een PowerShell-client naar:

1. Een toegangstoken aanschaffen.
2. Gebruik het toegangstoken om een Azure Resource Manager REST API aan te roepen en informatie over de VM op te vragen. Zorg ervoor dat u uw abonnements-ID, naam `<SUBSCRIPTION-ID>`van `<RESOURCE-GROUP>`de `<VM-NAME>`resourcegroep en de naam van de virtuele machine vervangt voor respectievelijk , en .

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Voorbeeld over hoe u het toegangstoken ontzien van het antwoord:
```azurepowershell
# Get an access token for managed identities for Azure resources
$response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The managed identities for Azure resources access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01' -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Een token opdoen met CURL

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Voorbeeld over hoe u het toegangstoken ontzien van het antwoord:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Tokencache

Hoewel de beheerde identiteiten voor het subsysteem Azure-bronnen (IMDS/managed identities for Azure resources VM Extension) cachetokens doen, raden we ook aan om tokencaching in uw code te implementeren. Als gevolg hiervan moet u zich voorbereiden op scenario's waarin de resource aangeeft dat het token is verlopen. 

On-the-wire calls naar Azure AD resulteren alleen wanneer:
- cachemist optreedt als gevolg van geen token in de beheerde identiteiten voor de subsysteemcache van Azure-bronnen
- het gecachede token is verlopen

## <a name="error-handling"></a>Foutafhandeling

De beheerde identiteiten voor het eindpunt van Azure-resources signaleren fouten via het veld statuscode van de HTTP-antwoordberichtkop, als 4xx- of 5xx-fouten:

| Statuscode | Foutreden | Hoe om te gaan |
| ----------- | ------------ | ------------- |
| 404 Niet gevonden. | IMDS-eindpunt wordt bijgewerkt. | Probeer opnieuw met Expontential Backoff. Zie de richtlijnen hieronder. |
| 429 Te veel verzoeken. |  IMDS Throttle limiet bereikt. | Probeer het opnieuw met Exponential Backoff. Zie de richtlijnen hieronder. |
| 4xx Fout in aanvraag. | Een of meer van de aanvraagparameters waren onjuist. | Probeer het niet opnieuw.  Bekijk de foutgegevens voor meer informatie.  4xx fouten zijn ontwerptijdfouten.|
| 5xx Tijdelijke fout van service. | De beheerde identiteiten voor subsysteem Azure-bronnen of Azure Active Directory hebben een tijdelijke fout geretourneerd. | Het is veilig om opnieuw te proberen na het wachten voor ten minste 1 seconde.  Als u het te snel of te vaak opnieuw probeert, kunnen IMDS en/of Azure AD een fout in tarieflimiet retourneren (429).|
| timeout | IMDS-eindpunt wordt bijgewerkt. | Probeer opnieuw met Expontential Backoff. Zie de richtlijnen hieronder. |

Als er een fout optreedt, bevat de bijbehorende HTTP-antwoordbody JSON met de foutgegevens:

| Element | Beschrijving |
| ------- | ----------- |
| error   | Fout-id. |
| error_description | Verbose beschrijving van de fout. **Foutbeschrijvingen kunnen op elk gewenst moment worden gewijzigd. Schrijf geen code die vertakt op basis van waarden in de foutbeschrijving.**|

### <a name="http-response-reference"></a>HTTP-antwoordverwijzing

In deze sectie worden de mogelijke foutreacties weergegeven. Een "200 OK"-status is een geslaagde reactie en het toegangstoken is opgenomen in de json van de reactieinstantie, in het access_token-element.

| Statuscode | Fout | Beschrijving van de fout | Oplossing |
| ----------- | ----- | ----------------- | -------- |
| 400 Slecht verzoek | invalid_resource | AADSTS50001: De * \<toepassing\> * met de naam URI is niet gevonden in de tenant met de naam * \<TENANT-ID\>*. Dit kan gebeuren als de toepassing niet is geïnstalleerd door de beheerder van de tenant of toestemming heeft gegeven door een gebruiker in de tenant. Mogelijk hebt u uw verificatieverzoek naar de verkeerde tenant verzonden.\ | (Alleen Linux) |
| 400 Slecht verzoek | bad_request_102 | Vereiste metagegevenskop niet opgegeven | Het `Metadata` veld met de aanvraagkopontbreekt in uw aanvraag of is onjuist opgemaakt. De waarde moet `true`worden opgegeven als , in alle kleine letters. Zie de voorbeeldaanvraag 'Voorbeeldaanvraag' in de vorige REST-sectie.|
| 401 Ongeautoriseerd | unknown_source | Onbekende * \<bron URI\>* | Controleer of uw HTTP GET-aanvraag URI correct is opgemaakt. Het `scheme:host/resource-path` gedeelte moet `http://localhost:50342/oauth2/token`worden opgegeven als . Zie de voorbeeldaanvraag 'Voorbeeldaanvraag' in de vorige REST-sectie.|
|           | invalid_request | De aanvraag mist een vereiste parameter, bevat een ongeldige parameterwaarde, bevat meer dan één keer een parameter of is anderszins verkeerd vervormd. |  |
|           | unauthorized_client | De client is niet bevoegd om een toegangstoken aan te vragen met behulp van deze methode. | Veroorzaakt door een verzoek dat geen gebruik heeft gemaakt van lokale terugloopback om de extensie aan te roepen, of op een VM die geen beheerde identiteiten heeft voor Azure-resources die correct zijn geconfigureerd. Zie [Beheerde identiteiten configureren voor Azure-resources op een VM met behulp van de Azure-portal](qs-configure-portal-windows-vm.md) als u hulp nodig hebt bij vm-configuratie. |
|           | access_denied | De broneigenaar of autorisatieserver heeft de aanvraag geweigerd. |  |
|           | unsupported_response_type | De autorisatieserver ondersteunt geen toegangstoken met deze methode. |  |
|           | invalid_scope | Het gevraagde bereik is ongeldig, onbekend of misvormd. |  |
| 500 interne serverfout | unknown | Kan token niet ophalen uit de Active directory. Zie logboeken in * \<bestandspad voor meer informatie\>* | Controleer of beheerde identiteiten voor Azure-resources zijn ingeschakeld op de VM. Zie [Beheerde identiteiten configureren voor Azure-resources op een VM met behulp van de Azure-portal](qs-configure-portal-windows-vm.md) als u hulp nodig hebt bij vm-configuratie.<br><br>Controleer ook of uw HTTP GET-aanvraag URI correct is opgemaakt, met name de resource URI die is opgegeven in de querytekenreeks. Zie de voorbeeldaanvraag in de vorige REST-sectie voor een voorbeeld of [Azure-services die Azure AD-verificatie ondersteunen](services-support-msi.md) voor een lijst met services en hun respectieve bron-id's.

## <a name="retry-guidance"></a>Richtlijnen opnieuw proberen 

Het wordt aanbevolen om opnieuw te proberen als u een foutcode van 404, 429 of 5xx ontvangt (zie [Foutafhandeling](#error-handling) hierboven).

Beperkingslimieten zijn van toepassing op het aantal oproepen naar het IMDS-eindpunt. Wanneer de beperkingsdrempel wordt overschreden, beperkt IMDS-eindpunt eventuele verdere aanvragen terwijl de gashendel van kracht is. Tijdens deze periode retourneert het IMDS-eindpunt de HTTP-statuscode 429 ('Te veel aanvragen') en mislukken de aanvragen. 

Voor het opnieuw proberen raden we de volgende strategie aan: 

| **Strategie voor opnieuw proberen** | **Instellingen** | **Waarden** | **Hoe het werkt** |
| --- | --- | --- | --- |
|ExponentialBackoff |Aantal pogingen<br />Min. uitstel<br />Max. uitstel<br />Delta-uitstel<br />Eerste snelle poging |5<br />0 sec.<br />60 sec.<br />2 sec.<br />false |Poging 1, vertraging 0 sec.<br />Poging 2, vertraging ~2 sec.<br />Poging 3, vertraging ~6 sec.<br />Poging 4, vertraging ~14 sec.<br />Poging 5, vertraging ~30 sec. |

## <a name="resource-ids-for-azure-services"></a>Bron-iD's voor Azure-services

Zie [Azure-services die Azure AD-verificatie ondersteunen](services-support-msi.md) voor een lijst met bronnen die Azure AD ondersteunen en zijn getest met beheerde identiteiten voor Azure-resources en hun respectieve bron-id's.


## <a name="next-steps"></a>Volgende stappen

- Zie [Beheerde identiteiten configureren voor Azure-resources op een vm configureren met behulp van de Azure-portal](qs-configure-portal-windows-vm.md)als u beheerde identiteiten voor Azure-resources op een Azure-vm wilt inschakelen.








