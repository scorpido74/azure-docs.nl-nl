---
title: Zelftestclient om een virtuele machine vooraf te valideren | Azure Marketplace
description: Een zelftestclient maken voor het vooraf valideren van een virtuele machineafbeelding voor de Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: dsindona
ms.openlocfilehash: fb568400cb60f108303909353bfa703e98ab6157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286418"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Een zelftestclient maken om een azure-afbeelding voor virtuele machines vooraf te valideren

Gebruik dit artikel als een handleiding voor het maken van een clientservice die de zelftest-API verbruikt. U de zelftest-API gebruiken om een virtuele machine (VM) vooraf te valideren om ervoor te zorgen dat deze voldoet aan de nieuwste publicatievereisten voor Azure Marketplace. Met deze clientservice u een virtuele machine testen voordat u uw aanbieding voor Microsoft-certificering indient.

## <a name="development-and-testing-overview"></a>Overzicht van ontwikkeling en testen

Als onderdeel van het zelftestproces maakt u een lokale client die verbinding maakt met Azure Marketplace om een vm te valideren die wordt uitgevoerd in uw Azure-abonnement. De VM kan het Windows- of Linux-besturingssysteem gebruiken.

De lokale client voert een script uit dat verifieert met de zelftest-API, verbindingsgegevens verzendt en testresultaten ontvangt.

De stappen op hoog niveau voor het maken van een zelftestclient zijn:

1. Kies de Azure Active Directory (AD)-tenant voor uw toepassing.
2. Registreer de client-app.
3. Maak een token voor de Azure AD-client-app.
4. Geef het token door aan de zelftest-API.

Nadat u de client hebt gemaakt, u deze testen op uw vm.

### <a name="self-test-client-authorization"></a>Clientautorisatie zelf testen

In het volgende diagram ziet u hoe autorisatie werkt voor service-to-servicegesprekken met behulp van clientreferenties (gedeeld geheim of certificaat.)

![Clientautorisatieproces](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>De API voor zelftestclient

De zelftest-API bevat één eindpunt dat alleen de POST-methode ondersteunt.  Het heeft de volgende structuur.

```
Uri:             https://isvapp.azurewebsites.net/selftest-vm
Method:          Post
Request Header:  Content-Type: "application/json"
Authorization:   "Bearer xxxx-xxxx-xxxx-xxxxx"
Request body:    The Request body parameters should use the following JSON format:
                 {
                   "DNSName":"XXXX.westus.cloudapp.azure.com",
                   "User":"XXX",
                   "Password":"XXX@1234567",
                   "OS":"XXX",
                   "PortNo":"22",
                   "CompanyName":"ABCD",
                 }
```

In de volgende tabel worden de API-velden beschreven.


|      Veld         |    Beschrijving    |
|  ---------------   |  ---------------  |
|  Autorisatie     |  De tekenreeks 'Xxxx-xxxx-xxxx-xxxxx aan touw trekster' bevat het Azure Active Directory-clienttoken (AD), dat kan worden gemaakt met PowerShell.          |
|  DNSName           |  DNS-naam van de VM die moet worden getest    |
|  Gebruiker              |  Gebruikersnaam voor het aanmelden bij de VM         |
|  Wachtwoord          |  Wachtwoord voor het aanmelden bij de VM          |
|  OS                |  Besturingssysteem van de `Linux` VM: een van beide of`Windows`          |
|  PortNo            |  Open poortnummer voor verbinding maken met de VM. Het poortnummer is `22` meestal `5986` voor Linux en voor Windows.          |
|  |  |

## <a name="consuming-the-api"></a>De API consumeren

U de zelftest-API gebruiken met PowerShell of cURL.

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>PowerShell gebruiken om de API op het Linux-besturingssysteem te gebruiken

Voer de volgende stappen uit om de API in PowerShell aan te roepen:

1. Gebruik `Invoke-WebRequest` de opdracht om de API aan te roepen.
2. De methode is Post en het inhoudstype is JSON, zoals wordt weergegeven in het volgende codevoorbeeld en schermopname.
3. Geef de hoofdparameters op in de JSON-indeling.

In het volgende codevoorbeeld ziet u een PowerShell-aanroep naar de API.

```powershell
$accesstoken = "Get token for your Client AAD App"
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Linux"
    "PortNo" = "22"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" -Headers $headers;
$Content = $res | ConvertFrom-Json
```
De volgende schermopname toont een voorbeeld voor het aanroepen van de API in PowerShell.

![Call API met PowerShell voor Linux OS](./media/stclient-call-api-ps-linuxvm.png)

Met behulp van het vorige voorbeeld u de JSON ophalen en deze ontleden om de volgende details te krijgen:

```powershell
$testresult = ConvertFrom-Json -InputObject (ConvertFrom-Json -InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

De volgende schermopname, `$res.Content`die wordt weergegeven, geeft u de details van uw testresultaten in JSON-formaat.

![JSON resultaten van PowerShell call to Linux](./media/stclient-pslinux-rescontent-json.png)

De volgende schermopname toont een voorbeeld van JSON-testresultaten die worden bekeken in een online JSON-viewer (bijvoorbeeld [Code Beautify](https://codebeautify.org/jsonviewer) of [JSON Viewer](https://jsonformatter.org/json-viewer)).

![JSON results from PowerShell call to Linux VM](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>PowerShell gebruiken om de API op het Windows-besturingssysteem te gebruiken

Voer de volgende stappen uit om de API in PowerShell aan te roepen:

1. Gebruik `Invoke-WebRequest` de opdracht om de API aan te roepen.
2. De methode is Post en het inhoudstype is JSON, zoals wordt weergegeven in het volgende codevoorbeeld en schermopname.
3. Maak de parameters van het lichaam in JSON-indeling.

In het volgende codevoorbeeld ziet u een PowerShell-aanroep naar de API.

```powershell
$accesstoken = "Get token for your Client AAD App"
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Windows"
    "PortNo" = "5986"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" -Headers $headers;
$Content = $res | ConvertFrom-Json
```

De volgende schermopname toont een voorbeeld voor het aanroepen van de API in PowerShell.

![Call API met PowerShell voor Windows VM](./media/stclient-call-api-ps-windowsvm.png)

Met behulp van het vorige voorbeeld u de JSON ophalen en deze ontleden om de volgende details te krijgen:

```powershell
$testresult = ConvertFrom-Json -InputObject (ConvertFrom-Json -InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

De volgende schermopname, `$res.Content`die wordt weergegeven, geeft u de details van uw testresultaten in JSON-formaat.

![JSON resultaten van PowerShell call to Windows](./media/stclient-pswindows-rescontent-json.png)

De volgende schermopname toont testresultaten die worden bekeken in een online JSON-viewer.
(bijvoorbeeld [Code Beautify](https://codebeautify.org/jsonviewer), [JSON Viewer)](https://jsonformatter.org/json-viewer)

![JSON-resultaten van PowerShell-oproep naar Windows VM](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Gebruik cURL om de API op het Linux-besturingssysteem te gebruiken

Voer de volgende stappen uit om de API met cURL aan te roepen:

1. Gebruik de opdracht Krul om de API aan te roepen.
2. De methode is Post en het inhoudstype is JSON, zoals in het volgende codefragment wordt weergegeven.

```
CURL POST -H "Content-Type:application/json"
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX"
https://isvapp.azurewebsites.net/selftest-vm
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'
```

In het volgende scherm ziet u een voorbeeld van het gebruik van krul om de API aan te roepen.

![Aanroepen API met de opdracht Krul](./media/stclient-consume-api-curl.png)

De volgende schermopname toont de JSON-resultaten van de kruloproep.

![JSON resultaten van krul oproep](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>De Azure AD-tenant voor de app kiezen

Gebruik de volgende stappen om de Azure AD-tenant te kiezen waar u uw toepassing wilt maken.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer op de bovenste menubalk uw account en kies onder de lijst Directory de Active Directory-tenant waar u uw toepassing wilt registreren. Of selecteer het pictogram **Directory + Abonnement** om het filter Globaal abonnement weer te geven. De volgende schermopname toont een voorbeeld van dit filter.

   ![Het abonnementsfilter selecteren](./media/stclient-subscription-filter.png)

3. Selecteer op de navigatiebalk aan de linkerkant **Alle services** en selecteer vervolgens Azure **Active Directory**.

   In de volgende stappen hebt u mogelijk de tenantnaam (of mapnaam) of de tenant-id (of directory-id) nodig.

   **Ga als u op de informatie over huurders:**

   Zoek in **Azure Active Directory-overzicht**naar 'Eigenschappen' en selecteer **Eigenschappen**. Als voorbeeld de volgende schermopname gebruiken:

   - **Naam** - De tenantnaam of mapnaam
   - **Directory-id** : de tenant-id of de map-id of gebruik de schuifbalk om eigenschappen te zoeken.

   ![Pagina Eigenschappen van Azure Active Directory](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>De client-app registreren

Gebruik de volgende stappen om de client-app te registreren.

1. Selecteer op de navigatiebalk aan de linkerkant **Alle services** en selecteer **vervolgens App-registraties**.
2. Selecteer **onder App-registraties** **+ Nieuwe toepassingsregistratie**.
3. Geef **onder Maken**de vereiste informatie op voor de volgende velden:

   - **Naam** - Voer een vriendelijke naam in voor de app. Bijvoorbeeld "SelfTestClient".
   - **Toepassingstype** - **Web-app/API selecteren**
   - **Aanmeldings-URL** - Typ\/"https: /isvapp.azurewebsites.net/selftest-vm"

4. Selecteer **Maken**.
5. Kopieer onder **App-registraties** of **geregistreerde app**de **toepassings-id**.

   ![De toepassings-id ophalen](./media/stclient-app-id.png)

6. Selecteer op de werkbalk geregistreerde app de optie **Instellingen**.
7. Selecteer **Vereiste machtigingen** om machtigingen voor uw toepassing te configureren.
8. Selecteer **onder Vereiste machtigingen**de optie + **Toevoegen**.
9. Kies **onder API-toegang toevoegen**de optie Een API **selecteren**.
10. Typ **onder Een API selecteren**het type 'Windows Azure classic deployment model' om naar de API te zoeken.
11. Kies in de zoekresultaten **het klassieke implementatiemodel van Windows Azure** en klik op **Selecteren**.

    ![Meerdere tenant configureren voor app](./media/stclient-select-api.png)

12. Kies **Onder API-toegang toevoegen**de optie Machtigingen **selecteren**.
13. Selecteer **Access "Windows Azure Service Management API".**

    ![API-toegang voor app inschakelen](./media/stclient-enable-api-access.png)

14. Klik **op Selecteren**.
15. Selecteer **Done**.
16. Selecteer onder **Alle instellingen** de optie **Eigenschappen**.
17. Schuif **onder Eigenschappen**omlaag naar **Multi-tenant**. Selecteer **Ja**.

    ![Meerdere tenant configureren voor app](./media/stclient-yes-multitenant.png)

18. Selecteer **Opslaan**.
19. Selecteer **Toetsen**onder **Instellingen**.
20. Maak een geheime sleutel door het tekstvak **Sleutelbeschrijving te** selecteren. Configureer de volgende velden:

    - Typ een sleutelnaam in. Bijvoorbeeld, "selftestclient"
    - Selecteer **in** de vervolgkeuzelijst VERLOOPT de optie 'In 1 jaar'.
    - Selecteer **Opslaan** om de sleutel te genereren.
    - Kopieer de sleutel **onder WAARDE.**

      >[!Important]
      >U de sleutelwaarde niet meer zien nadat u het **formulier Sleutels hebt** verlaten.

    ![Formulier voor de waardewaarde van de sleutel](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>Het token voor de client-app maken

U een van de volgende programma's gebruiken om een token te maken en te krijgen met behulp van de OAuth REST API:

- Postman
- cURL in Linux
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Een token maken en krijgen met Postman

 Als u Auth0 om tokens wilt vragen voor een van [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) uw geautoriseerde toepassingen, voert u een POST-bewerking uit naar het eindpunt met een payload in de volgende indeling:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```

Geef de volgende parameters door in de instantie Aanvraag:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

Geef de volgende parameters door in de koptekst Verzoek:

```
Content-Type: application/x-www-form-urlencoded
```

De volgende schermopname toont een voorbeeld van het gebruik van Postman om een token te krijgen.

![Token krijgen bij Postbode](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Een token maken en ophalen met behulp van cURL in Linux

Als u Auth0 om tokens wilt vragen voor een van [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) uw geautoriseerde toepassingen, voert u een POST-bewerking uit naar het eindpunt met een payload in de volgende indeling:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

De volgende schermopname toont een voorbeeld van het gebruik van de opdracht krul om een token te krijgen.

![Token krijgen met opdracht Krul](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Een token maken en krijgen met C-&#35;

Als u Auth0 om tokens wilt vragen voor een van uw\/geautoriseerde toepassingen, voert u een POST-bewerking uit naar het https: /soamtenant.auth0.com/oauth/token eindpunt met een payload in de volgende indeling:

```csharp
string clientId = "Your Application Id";
string clientSecret = "Your Application Secret";
string audience = "https://management.core.windows.net";
string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, "https://login.microsoftonline.com/common/oauth2/token");
string grantType = "client_credentials";
var client = new RestClient(authority);
var request = new RestRequest(Method.POST);
       request.AddHeader("content-type", "application/x-www-form- urlencoded");

string requestBody = "grant_type=" + grantType + "&" + "client_id=" +   clientId + "&" + "client_secret=" + clientSecret + "&" + "resource=" +  audience;

request.AddParameter("application/x-www-form-urlencoded", requestBody,  ParameterType.RequestBody);

IRestResponse response = client.Execute(request);
var content = response.Content;
var token = JObject.Parse(content)["access_token"];
```

### <a name="to-create-and-get-a-token-using-powershell"></a>Een token maken en krijgen met PowerShell

Als u Auth0 om tokens wilt vragen voor een van uw\/geautoriseerde toepassingen, voert u een POST-bewerking uit naar het https: /soamtenant.auth0.com/oauth/token eindpunt met een payload in de volgende indeling:

```powershell
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP "
$audience = "https://management.core.windows.net";
$authority = "https://login.microsoftonline.com/common/oauth2/token"
$grantType = "client_credentials";

$requestBody = "grant_type=" + $grantType + "&" + "client_id=" +  $clientId   + "&" + "client_secret=" + $clientSecret + "&" + "resource=" + $audience;

$headers = New-Object  "System.Collections.Generic.Dictionary[[String],[String]]"
             $headers.Add("ContentType", "application/x-www-form-urlencoded")
resp = Invoke-WebRequest -Method Post -Uri $authority -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $requestBody

$token = $resp.Content | ConvertFrom-Json
$token.AccessToken
```

## <a name="pass-the-client-app-token-to-the-api"></a>Het token van de client-app doorgeven aan de API

Geef het token door aan de zelftest-API met de volgende code in de autorisatiekop:

```powershell
$redirectUri = 'https://isvapp.azurewebsites.net/selftest-vm'
$accesstoken = 'place your token here'

$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body =
@{
      'DNSName'="XXXX.cloudapp.azure.com";
      'User'="XXX";
      'Password'="XXXX@12345";
      'OS'="Linux";
      'PortNo'="22"
  } | ConvertTo-Json

$result=Invoke-WebRequest -Method Post -Uri $redirectUri -Headers $headers -ContentType 'application/json' -Body $Body
$result
Write-Output 'Test Results:'
$result.Content
```

## <a name="test-your-self-test-client"></a>Test uw zelftestclient

Voer de volgende stappen uit om de client te testen:

1. Implementeer de VM die u wilt testen.
2. Bel de zelftest-API met behulp van uw client-app-token voor autorisatie.
3. Haal de testresultaten op in JSON-indeling.

### <a name="test-result-examples"></a>Voorbeelden van testresultaten

De volgende fragmenten tonen testresultaten in JSON-indeling.

**Testresultaten voor een Windows-vm:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Microsoft Windows Server 2016 Datacenter",
  "OSVersion": "10.0.14393",
  "CreatedDate": "06/01/2018 07:48:04",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "Tes tID": "1.1.4",
      "TestCaseName": "OS Architecture",
      "Description": "Azure supports 64bit Operating System Only.",
      "Result": "Passed",
      "ActualValue": "64 Bit",
      "Re quiredValue": "OS Should be 64 bit"
    },
    {
      "TestID": "1.1.5",
      "TestCaseName": "User account dependency",
      "Description": "Application execution should not have de pendency on administrator account.",
      "Result": "Passed",
      "ActualValue": "isv",
      "RequiredValue": "Logged in user should not be an administrator"
    },
    {
      "TestID": " 1.1.6",
      "TestCaseName": "Failover Cluster",
      "Description": "Windows Server Failover Clustering feature is not yet supported, Application should not have dependency on this feature.",
      "Result": "Passed",
      "ActualValue": "Disabled",
      "RequiredValue": "Failover Clustering feature is disabled"
    },
```

**Testresultaten voor een Linux VM:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Ubuntu  16.04",
  "OSVersion": "16.04",
  "CreatedDate": "06/01/2018 07:04:24",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "TestID": "48",
      "TestCaseName": "Bash Hi story",
      "Description": "Bash history files should be cleared before creating the VM image.",
      "Result": "Passed",
      "ActualValue": "No file Exist",
      "RequiredVal ue": "1024"
    },
    {
      "TestID": "39",
      "TestCaseName": "Linux Agent Version",
      "Description": "Azure Linux Agent 2.2.10 and above should be installed. ",
      "Result": "Pas sed",
      "ActualValue": "2.2.20",
      "RequiredValue": "2.2.10"
    },
    {
      "TestID": "40",
      "TestCaseName": "Required Kernel Parameters",
      "Description": "Verifies the following  kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300",
      "Result": "Passed",
      "ActualValue": "Matched Parameter: console=ttyS0,earlypri ntk=ttyS0,rootdelay=300",
      "RequiredValue": "console=ttyS0#earlyprintk=ttyS0#rootdelay=300"
    },
```

## <a name="next-steps"></a>Volgende stappen

Nadat u uw virtuele Azure-machine hebt getest, u [de aanbieding publiceren.](./cpp-publish-offer.md)
