---
title: Zelf test-client voor het vooraf valideren van een virtuele machine | Azure Marketplace
description: Een self-test-client maken voor het vooraf valideren van een installatie kopie van een virtuele machine voor Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, Virtual Machine
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: pabutler
ms.openlocfilehash: 46923ecd33a054a36aa6900a415d0b563e5afff0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163255"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Een zelf test-client maken om een installatie kopie van een virtuele Azure-machine vooraf te valideren

Gebruik dit artikel als richt lijn voor het maken van een client service die gebruikmaakt van de zelf test-API. U kunt de zelf test-API gebruiken om een virtuele machine (VM) vooraf te valideren om te controleren of deze voldoet aan de meest recente publicatie vereisten voor Azure Marketplace. Met deze client service kunt u een virtuele machine testen voordat u uw aanbieding voor micro soft-certificering verzendt.

## <a name="development-and-testing-overview"></a>Overzicht van ontwikkelen en testen

Als onderdeel van het zelf test proces maakt u een lokale client die verbinding maakt met Azure Marketplace voor het valideren van een virtuele machine die wordt uitgevoerd in uw Azure-abonnement. Op de VM kan het Windows-of Linux-besturings systeem worden uitgevoerd.

De lokale client voert een script uit dat wordt geverifieerd met de API zelf test, verbindings gegevens verzendt en test resultaten ontvangt.

De stappen op hoog niveau voor het maken van een self-test-client zijn:

1. Kies de Azure Active Directory (AD)-Tenant voor uw toepassing.
2. Registreer de client-app.
3. Maak een token voor de Azure AD-client-app.
4. Geef het token door aan de zelf test-API.

Nadat u de-client hebt gemaakt, kunt u deze testen op uw virtuele machine.

### <a name="self-test-client-authorization"></a>Zelf test-client autorisatie

In het volgende diagram ziet u hoe autorisatie werkt voor service-naar-service-aanroepen met behulp van client referenties (gedeeld geheim of certificaat.)

![Client autorisatie proces](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>De zelf test-client-API

De zelf test-API bevat een enkel eind punt dat alleen de POST-methode ondersteunt.  Het heeft de volgende structuur.

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
|  Autorisatie     |  De teken reeks ' Bearer XXXX-XXXX-XXXX-xxxxx ' bevat het Azure Active Directory (AD) client token, dat kan worden gemaakt met behulp van Power shell.          |
|  DNSName           |  De DNS-naam van de virtuele machine die u wilt testen    |
|  Gebruiker              |  Gebruikers naam voor aanmelding bij de virtuele machine         |
|  Wachtwoord          |  Wacht woord voor aanmelding bij de virtuele machine          |
|  Besturingssysteem                |  Het besturings systeem van de virtuele machine: een `Linux` of `Windows`          |
|  PortNo            |  Open het poort nummer om verbinding te maken met de virtuele machine. Het poort nummer is doorgaans `22` voor Linux en `5986` voor Windows.          |
|  |  |

## <a name="consuming-the-api"></a>De API gebruiken

U kunt de zelf test-API gebruiken met Power shell of krul.

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Power shell gebruiken voor het verbruiken van de API op het Linux-besturings systeem

Voer de volgende stappen uit om de API aan te roepen in Power shell:

1. Gebruik de `Invoke-WebRequest` opdracht om de API aan te roepen.
2. De methode is post en het inhouds type is JSON, zoals wordt weer gegeven in het volgende code voorbeeld en scherm opname.
3. Geef de hoofd tekst parameters op in JSON-indeling.

Het volgende code voorbeeld toont een Power shell-aanroep van de API.

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
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```
In de volgende scherm opname ziet u een voor beeld van het aanroepen van de API in Power shell.

![API aanroepen met Power shell voor Linux-besturings systeem](./media/stclient-call-api-ps-linuxvm.png)

In het vorige voor beeld kunt u de JSON ophalen en parseren om de volgende details op te halen:

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

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

De volgende scherm opname, waarin `$res.Content`wordt weer gegeven, geeft u de details van de test resultaten in JSON-indeling.

![JSON-resultaten van Power shell-aanroep naar Linux](./media/stclient-pslinux-rescontent-json.png)

In de volgende scherm opname ziet u een voor beeld van resultaten van JSON-testen die zijn bekeken in een online JSON-Viewer (bijvoorbeeld [code beautify](https://codebeautify.org/jsonviewer) of [JSON Viewer](https://jsonformatter.org/json-viewer)).

![JSON-resultaten van Power shell-aanroep naar Linux VM](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Power shell gebruiken voor het verbruik van de API op het Windows-besturings systeem

Voer de volgende stappen uit om de API aan te roepen in Power shell:

1. Gebruik de `Invoke-WebRequest` opdracht om de API aan te roepen.
2. De methode is post en het inhouds type is JSON, zoals wordt weer gegeven in het volgende code voorbeeld en scherm opname.
3. Maak de hoofd tekst-para meters in JSON-indeling.

Het volgende code voorbeeld toont een Power shell-aanroep van de API.

```powershell
$accesstoken = “Get token for your Client AAD App”
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
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

In de volgende scherm opname ziet u een voor beeld van het aanroepen van de API in Power shell.

![API aanroepen met Power shell voor Windows VM](./media/stclient-call-api-ps-windowsvm.png)

In het vorige voor beeld kunt u de JSON ophalen en parseren om de volgende details op te halen:

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

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

De volgende scherm opname, waarin `$res.Content`wordt weer gegeven, geeft u de details van de test resultaten in JSON-indeling.

![JSON-resultaten van Power shell-aanroepen naar Windows](./media/stclient-pswindows-rescontent-json.png)

In de volgende scherm opname ziet u de test resultaten die worden weer gegeven in een online JSON-viewer.
(bijvoorbeeld [code beautify](https://codebeautify.org/jsonviewer), JSON- [Viewer](https://jsonformatter.org/json-viewer))

![JSON-resultaten van Power shell-aanroep naar Windows VM](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Krul gebruiken voor het gebruik van de API op het Linux-besturings systeem

Voer de volgende stappen uit om de API met krul aan te roepen:

1. Gebruik de krul opdracht om de API aan te roepen.
2. De methode is post en het inhouds type is JSON, zoals wordt weer gegeven in het volgende code fragment.

```
CURL POST -H "Content-Type:application/json"
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX"
https://isvapp.azurewebsites.net/selftest-vm
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'
```

In het volgende scherm ziet u een voor beeld van het gebruik van krul om de API aan te roepen.

![API aanroepen met de opdracht krul](./media/stclient-consume-api-curl.png)

In de volgende scherm opname ziet u de JSON-resultaten van de krul aanroep.

![JSON-resultaten van krul oproep](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>De Azure AD-Tenant voor de app kiezen

Gebruik de volgende stappen om de Azure AD-Tenant te kiezen waar u uw toepassing wilt maken.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer in de bovenste menu balk uw account en kies in de lijst met mappen de Active Directory Tenant waar u de toepassing wilt registreren. Of selecteer het pictogram voor het adres van de map en het **abonnement** om het globale abonnements filter weer te geven. In de volgende scherm opname ziet u een voor beeld van dit filter.

   ![Het abonnements filter selecteren](./media/stclient-subscription-filter.png)

3. Selecteer **alle services** op de navigatie balk aan de linkerkant en selecteer vervolgens **Azure Active Directory**.

   In de volgende stappen hebt u mogelijk de Tenant naam (of mapnaam) of de Tenant-ID (of directory-ID) nodig.

   **Tenant gegevens ophalen:**

   In **Azure Active Directory overzicht**zoekt u naar ' Eigenschappen ' en selecteert u vervolgens **Eigenschappen**. Gebruik de volgende scherm opname als voor beeld:

   - **Naam** : de naam van de Tenant of de Directory
   - **Map** -id: de Tenant-id of directory-id of gebruik de schuif balk om eigenschappen te vinden.

   ![Pagina eigenschappen van Azure Active Directory](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>De client-app registreren

Gebruik de volgende stappen om de client-app te registreren.

1. Selecteer **alle services** op de navigatie balk aan de linkerkant en selecteer vervolgens **app-registraties**.
2. Selecteer onder **app-registraties** **+ nieuwe toepassing registreren**.
3. Geef onder **maken**de informatie op die vereist is voor de volgende velden:

   - **Naam** : Voer een beschrijvende naam in voor de app. Bijvoorbeeld ' SelfTestClient '.
   - **Toepassings type** : Selecteer **Web-app/API**
   - **Aanmeldings-URL** : type ' https:\//isvapp.azurewebsites.net/selftest-VM '

4. Selecteer **Maken**.
5. Kopieer de **toepassings-id**onder **app-registraties** of **geregistreerde app**.

   ![De toepassings-ID ophalen](./media/stclient-app-id.png)

6. Selecteer in de werk balk geregistreerde app de optie **instellingen**.
7. Selecteer de **vereiste machtigingen** om machtigingen voor uw toepassing te configureren.
8. Selecteer onder **vereiste machtigingen** **+ toevoegen**.
9. Onder **API-toegang toevoegen** **selecteert u een API**.
10. Onder **Selecteer een API**typt u ' Windows Azure Classic Deployment model ' om te zoeken naar de API.
11. Kies in de zoek resultaten het **klassieke Windows Azure-implementatie model** en klik vervolgens op **selecteren**.

    ![Multi tenant voor app configureren](./media/stclient-select-api.png)

12. Kies onder **API-toegang toevoegen**de **optie machtigingen selecteren**.
13. Selecteer **toegang tot Windows Azure Service Management-API**.

    ![API-toegang voor de app inschakelen](./media/stclient-enable-api-access.png)

14. Klik op **Selecteren**.
15. Selecteer **Done**.
16. Selecteer onder **Alle instellingen** de optie **Eigenschappen**.
17. Onder **Eigenschappen**schuift u omlaag naar **multi-tenant**. Selecteer **Ja**.

    ![Multi tenant voor app configureren](./media/stclient-yes-multitenant.png)

18. Selecteer **Opslaan**.
19. Selecteer onder **instellingen**de optie **sleutels**.
20. Maak een geheime sleutel door het tekstvak sleutel **Beschrijving** te selecteren. Configureer de volgende velden:

    - Typ een sleutel naam. Bijvoorbeeld ' selftestclient '
    - Selecteer in de vervolg keuzelijst **Expires** de optie ' in 1 jaar '.
    - Selecteer **Opslaan** om de sleutel te genereren.
    - Kopieer onder **waarde**de sleutel.

      >[!Important]
      >U kunt de sleutel waarde niet zien nadat u het formulier **sleutels** hebt afgesloten.

    ![Formulier voor sleutel waarde](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>Het token voor de client-app maken

U kunt elk van de volgende Program ma's gebruiken om een token te maken en op te halen met behulp van de OAuth-REST API:

- Postman
- Krul in Linux
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Een token maken en ophalen met behulp van postman

 Als u Auth0 wilt stellen voor tokens voor een van uw geautoriseerde toepassingen, moet u een POST-bewerking uitvoeren op het [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) -eind punt met een nettolading in de volgende indeling:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```

Geef de volgende para meters door in de hoofd tekst van de aanvraag:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

Geef de volgende para meters in de aanvraag header door:

```
Content-Type: application/x-www-form-urlencoded
```

In de volgende scherm opname ziet u een voor beeld van het gebruik van Postman om een token op te halen.

![Token ophalen met postman](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Een token maken en ophalen met behulp van krul in Linux

Als u Auth0 wilt stellen voor tokens voor een van uw geautoriseerde toepassingen, moet u een POST-bewerking uitvoeren op het [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) -eind punt met een nettolading in de volgende indeling:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

In de volgende scherm opname ziet u een voor beeld van het gebruik van de krul opdracht om een token op te halen.

![Token ophalen met de opdracht krul](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Een token maken en ophalen met C&#35;

Als u Auth0 wilt stellen voor tokens voor een van uw geautoriseerde toepassingen, voert u een POST-bewerking uit naar het https:\//soamtenant.auth0.com/oauth/token-eind punt met een nettolading in de volgende indeling:

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

### <a name="to-create-and-get-a-token-using-powershell"></a>Een token maken en ophalen met behulp van Power shell

Als u Auth0 wilt stellen voor tokens voor een van uw geautoriseerde toepassingen, voert u een POST-bewerking uit naar het https:\//soamtenant.auth0.com/oauth/token-eind punt met een nettolading in de volgende indeling:

```powershell
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP “
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

## <a name="pass-the-client-app-token-to-the-api"></a>Het token van de client-app door geven aan de API

Geef het token door aan de zelf test-API met behulp van de volgende code in de autorisatie-header:

```powershell
$redirectUri = ‘https://isvapp.azurewebsites.net/selftest-vm’
$accesstoken = ‘place your token here’

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

## <a name="test-your-self-test-client"></a>Uw Self-Test client testen

Voer de volgende stappen uit om de client te testen:

1. Implementeer de virtuele machine die u wilt testen.
2. Roep de zelf test-API aan met behulp van uw client-app-token voor autorisatie.
3. De test resultaten in JSON-indeling ophalen.

### <a name="test-result-examples"></a>Voor beelden van test resultaten

In de volgende fragmenten worden test resultaten in JSON-indeling weer gegeven.

**Test resultaten voor een Windows-VM:**

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

**Test resultaten voor een virtuele Linux-machine:**

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

Nadat u uw virtuele Azure-machine hebt getest, kunt u [de aanbieding publiceren](./cpp-publish-offer.md).
