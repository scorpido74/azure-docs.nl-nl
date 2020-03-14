---
title: Azure Key Vault met end-to-end sleutel rotatie en-controle instellen | Microsoft Docs
description: Gebruik deze hand leiding om u te helpen bij het instellen van de belangrijkste draai-en sleutel kluis Logboeken.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 6962a264787bd8a55b6f6a2ebdb6eeb615c33d5a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218406"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Azure Key Vault instellen met de functie voor het draaien en controleren van sleutels

## <a name="introduction"></a>Inleiding

Nadat u een sleutel kluis hebt, kunt u deze gebruiken om sleutels en geheimen op te slaan. Uw toepassingen hoeven uw sleutels of geheimen niet langer op te slaan, maar kunnen ze naar wens aanvragen bij de kluis. Met een sleutel kluis kunt u sleutels en geheimen bijwerken zonder dat dit van invloed is op het gedrag van uw toepassing, waardoor er een breed scala aan mogelijkheden voor uw sleutel en uw geheime beheer wordt geopend.

In dit artikel vindt u instructies voor het implementeren van een geplande rotatie van sleutels voor opslag accounts, het controleren van de audit logboeken van de sleutel kluis en het activeren van waarschuwingen wanneer er onverwachte aanvragen worden gedaan. 

U moet eerst een sleutel kluis maken met behulp van de methode van uw keuze:

- [Een geheim van Azure Key Vault instellen en ophalen met behulp van Azure CLI](quick-create-cli.md)
- [Een geheim instellen en ophalen uit Azure Key Vault met behulp van Azure PowerShell](quick-create-powershell.md)
- [Een geheim instellen en ophalen uit Azure Key Vault met behulp van Azure Portal](quick-create-portal.md)


## <a name="store-a-secret"></a>Een geheim opslaan

Als u een toepassing wilt inschakelen voor het ophalen van een geheim van Key Vault, moet u eerst het geheim maken en dit uploaden naar uw kluis.

Start een Azure PowerShell-sessie en meld u aan bij uw Azure-account met de volgende opdracht:

```powershell
Connect-AzAccount
```

Voer in het pop-upvenster browser de gebruikers naam en het wacht woord in voor uw Azure-account. Power shell haalt alle abonnementen op die aan dit account zijn gekoppeld. Power Shell maakt standaard gebruik van de eerste.

Als u meerdere abonnementen hebt, moet u mogelijk het account opgeven dat is gebruikt voor het maken van uw sleutel kluis. Voer het volgende in om de abonnementen voor uw account weer te geven:

```powershell
Get-AzSubscription
```

Als u het abonnement wilt opgeven dat is gekoppeld aan de sleutel kluis, voert u de volgende gegevens in:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Omdat in dit artikel wordt gedemonstreerd hoe u een sleutel van een opslag account als geheim opslaat, moet u die sleutel voor het opslag account ophalen.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Nadat u uw geheim hebt opgehaald (in dit geval de sleutel van uw opslag account), moet u die sleutel converteren naar een beveiligde teken reeks en vervolgens een geheim maken met die waarde in uw sleutel kluis.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Haal vervolgens de URI op voor het geheim dat u hebt gemaakt. U hebt deze URI in een latere stap nodig om de sleutel kluis aan te roepen en uw geheim op te halen. Voer de volgende Power shell-opdracht uit en noteer de ID-waarde. Dit is de URI van het geheim:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>De toepassing instellen

Nu u een geheim hebt opgeslagen, kunt u code gebruiken om deze op te halen en te gebruiken nadat u nog een paar stappen hebt uitgevoerd.

Eerst moet u uw toepassing registreren bij Azure Active Directory. Vertel vervolgens Key Vault uw toepassings gegevens zodat deze aanvragen van uw toepassing kunnen toestaan.

> [!NOTE]
> Uw toepassing moet worden gemaakt op dezelfde Azure Active Directory Tenant als uw sleutel kluis.

1. Open **Azure Active Directory**.
2. Selecteer **App-registraties**. 
3. Selecteer **nieuwe toepassing registreren** om een toepassing toe te voegen aan Azure Active Directory.

    ![Toepassingen openen in Azure Active Directory](./media/keyvault-keyrotation/azure-ad-application.png)

4. Laat het toepassings type onder **maken**staan als **Web-app/API** en geef uw toepassing een naam. Geef uw toepassing een **aanmeldings-URL**. Deze URL kan alles zijn wat u wilt voor deze demo.

    ![Toepassings registratie maken](./media/keyvault-keyrotation/create-app.png)

5. Nadat de toepassing is toegevoegd aan Azure Active Directory, wordt de pagina toepassing geopend. Selecteer **instellingen**en selecteer vervolgens **Eigenschappen**. Kopieer de waarde van de **toepassings-id** . U hebt deze nodig in latere stappen.

Genereer vervolgens een sleutel voor uw toepassing, zodat deze kan communiceren met Azure Active Directory. Als u een sleutel wilt maken, selecteert u **sleutels** onder **instellingen**. Noteer de zojuist gegenereerde sleutel voor uw Azure Active Directory-toepassing. U hebt deze in een latere stap nodig. De sleutel is niet beschikbaar nadat u deze sectie verlaat. 

![App-sleutels Azure Active Directory](./media/keyvault-keyrotation/create-key.png)

Voordat u aanroepen van uw toepassing in de sleutel kluis tot stand brengt, moet u de sleutel kluis informeren over uw toepassing en de bijbehorende machtigingen. De volgende opdracht maakt gebruik van de naam van de kluis en de toepassings-ID van uw Azure Active Directory-app om de **toepassing toegang te geven tot uw** sleutel kluis.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

U kunt nu beginnen met het bouwen van uw toepassings aanroepen. In uw toepassing moet u de NuGet-pakketten installeren die nodig zijn om te communiceren met Azure Key Vault en Azure Active Directory. Voer de volgende opdrachten uit in de Visual Studio Package Manager-console. Bij het schrijven van dit artikel is de huidige versie van het Azure Active Directory pakket 3.10.305231913, dus bevestig de meest recente versie en werk deze indien nodig bij.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Maak in de toepassings code een klasse om de methode voor uw Azure Active Directory-verificatie te bewaren. In dit voor beeld heet die klasse **hulppr**. Voeg de volgende `using`-instructie toe:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Voeg vervolgens de volgende methode toe om de JWT-token op te halen uit Azure Active Directory. Voor onderhoud kunt u de in code vastgelegde teken reeks waarden verplaatsen naar uw web-of toepassings configuratie.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Voeg de benodigde code toe om Key Vault aan te roepen en uw geheime waarde op te halen. Eerst moet u de volgende `using`-instructie toevoegen:

```csharp
using Microsoft.Azure.KeyVault;
```

Voeg de methode aanroepen toe om Key Vault aan te roepen en uw geheim op te halen. In deze methode geeft u de geheime URI op die u in een vorige stap hebt opgeslagen. Let op het gebruik van de methode **GetToken** van de klasse **hulppr** . die u eerder hebt gemaakt.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Wanneer u de toepassing uitvoert, moet u zich nu verifiëren voor Azure Active Directory en vervolgens uw geheime waarde ophalen van Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>De draaiing van sleutels met Azure Automation

> [!IMPORTANT]
> Azure Automation runbooks vereisen nog steeds het gebruik van de module `AzureRM`.

U bent nu klaar om een rotatie strategie in te stellen voor de waarden die u als Key Vault geheimen opslaat. Geheimen kunnen op verschillende manieren worden gedraaid:

- Als onderdeel van een hand matig proces
- Programmatisch met behulp van API-aanroepen
- Via een Azure Automation script

Voor de doel einden van dit artikel gebruikt u Power shell in combi natie met Azure Automation om de toegangs sleutel van een Azure Storage-account te wijzigen. Vervolgens werkt u een sleutel kluis geheim bij met de nieuwe sleutel.

Als u wilt toestaan dat Azure Automation geheime waarden in uw sleutel kluis in te stellen, moet u de client-ID ophalen voor de verbinding met de naam **AzureRunAsConnection**. Deze verbinding is gemaakt toen u uw Azure Automation-exemplaar hebt gemaakt. Als u deze ID wilt vinden, selecteert u **assets** van uw Azure Automation-exemplaar. Selecteer vervolgens **verbindingen**en selecteer vervolgens de Service-Principal **AzureRunAsConnection** . Noteer de **ApplicationId** -waarde.

![Client-ID Azure Automation](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Selecteer in **assets** **modules**. Selecteer **Galerie**en zoek en importeer bijgewerkte versies van elk van de volgende modules:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Als dit artikel is geschreven, moeten alleen de eerder genoteerde modules voor het volgende script worden bijgewerkt. Als uw Automation-taak mislukt, controleert u of u alle benodigde modules en de bijbehorende afhankelijkheden hebt geïmporteerd.

Nadat u de toepassings-ID voor uw Azure Automation verbinding hebt opgehaald, moet u uw sleutel kluis vertellen dat deze toepassing is gemachtigd om geheimen bij te werken in uw kluis. Gebruik de volgende Power shell-opdracht:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Selecteer vervolgens **Runbooks** onder uw Azure Automation-exemplaar en selecteer vervolgens **Runbook toevoegen**. Selecteer **Snelle invoer**. Geef uw runbook een naam en selecteer **Power shell** als het type runbook. U kunt een beschrijving toevoegen. Selecteer tot slot **maken**.

![Runbook maken](./media/keyvault-keyrotation/Create_Runbook.png)

Plak het volgende Power shell-script in het deel venster Editor voor uw nieuwe runbook:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Optionally you can set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

Selecteer in het deel venster Editor de optie **test deel venster** om uw script te testen. Nadat het script zonder fouten is uitgevoerd, kunt u **publiceren**selecteren. vervolgens kunt u een planning voor het runbook Toep assen in het deel venster runbook Configuration.

## <a name="key-vault-auditing-pipeline"></a>Pijp lijn voor Key Vault controle

Wanneer u een sleutel kluis instelt, kunt u controle inschakelen voor het verzamelen van logboeken met toegangs aanvragen voor de sleutel kluis. Deze logboeken worden opgeslagen in een aangewezen Azure-opslag account en kunnen worden opgehaald, bewaakt en geanalyseerd. In het volgende scenario worden Azure functions, Azure Logic apps en sleutel-kluis audit logboeken gebruikt om een pijp lijn te maken waarmee een e-mail wordt verzonden wanneer een app die niet overeenkomt met de App-ID van de web-app, geheimen uit de kluis ophaalt.

Eerst moet u logboek registratie inschakelen voor uw sleutel kluis. Gebruik de volgende Power shell-opdrachten. (In dit artikel vindt u een overzicht van de volledige Details [over de sleutel kluis logboek registratie](key-vault-logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Nadat logboek registratie is ingeschakeld, worden audit Logboeken opgeslagen in het aangewezen opslag account. Deze logboeken bevatten gebeurtenissen over hoe en wanneer uw sleutel kluizen toegankelijk zijn, en door wie.

> [!NOTE]
> U kunt de logboek gegevens 10 minuten na de sleutel kluis gebruiken. Het is vaak eerder beschikbaar dan dat.

De volgende stap is het [maken van een Azure service bus wachtrij](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Deze wachtrij is de plaats waar sleutel kluis audit logboeken worden gepusht. Wanneer de berichten van het controle logboek zich in de wachtrij bevinden, haalt de logische app deze op en voert deze op deze taken uit. Maak een Service Bus-exemplaar met de volgende stappen:

1. Maak een Service Bus naam ruimte (als u er al een hebt die u wilt gebruiken, gaat u verder met stap 2).
2. Blader naar het Service Bus-exemplaar in de Azure Portal en selecteer de naam ruimte waarin u de wachtrij wilt maken.
3. Selecteer **een resource maken** > **bedrijfsintegratie** > **Service Bus**en voer vervolgens de vereiste gegevens in.
4. Zoek de Service Bus verbindings gegevens door de naam ruimte te selecteren en vervolgens **verbindings gegevens**te selecteren. U hebt deze informatie nodig voor de volgende sectie.

Maak vervolgens [een Azure-functie](../azure-functions/functions-create-first-azure-function.md) om de sleutel kluis logboeken te controleren binnen het opslag account en nieuwe gebeurtenissen op te halen. Deze functie wordt geactiveerd volgens een schema.

Als u een Azure function-app wilt maken, selecteert u **een resource maken**, zoekt u in de marketplace naar **functie-app**en selecteert u **maken**. Tijdens het maken kunt u een bestaand hosting plan gebruiken of een nieuw abonnement maken. U kunt er ook voor kiezen om dynamisch te hosten. Zie [Azure functions schalen](../azure-functions/functions-scale.md)voor meer informatie over de hosting opties voor Azure functions.

Nadat de Azure function-app is gemaakt, gaat u naar deze en selecteert u het **Timer** scenario en **C\#** voor de taal. Selecteer vervolgens **deze functie maken**.

![Blade Azure Functions starten](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Vervang op het tabblad **ontwikkelen** de code run. CSX door het volgende:

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didn't have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //Required to order by time as they might not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

> [!NOTE]
> Wijzig de variabelen in de voor gaande code zodat deze verwijzen naar het opslag account waar de sleutel kluis logboeken worden geschreven, naar het Service Bus exemplaar dat u eerder hebt gemaakt en naar het specifieke pad naar de sleutel kluis opslag Logboeken.

De functie haalt het meest recente logboek bestand op uit het opslag account waarin de sleutel kluis logboeken worden geschreven, plaatst de meest recente gebeurtenissen uit dat bestand en duwt ze naar een Service Bus wachtrij. 

Omdat één bestand meerdere gebeurtenissen kan hebben, moet u een Sync. txt-bestand maken dat door de functie wordt weer gegeven om het tijds tempel van de laatste gebeurtenis die is opgehaald te bepalen. Als u dit bestand gebruikt, zorgt u ervoor dat dezelfde gebeurtenis niet meerdere keren wordt gepusht. 

Het bestand sync. txt bevat een tijds tempel voor de laatst gevonden gebeurtenis. Wanneer de logboeken worden geladen, moeten ze worden gesorteerd op basis van hun tijds tempels om ervoor te zorgen dat ze correct worden geordend.

Voor deze functie verwijzen we naar een paar extra bibliotheken die niet beschikbaar zijn in het vak in Azure Functions. Om deze bibliotheken op te nemen, hebben we Azure Functions nodig om ze te kunnen ophalen met behulp van NuGet. Selecteer in het vak **code** de optie **bestanden weer geven**.

![Optie voor het weer geven van bestanden](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Voeg een bestand met de naam project. json toe met de volgende inhoud:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```

Nadat u **Opslaan**hebt geselecteerd, worden de vereiste binaire bestanden door Azure functions gedownload.

Ga naar het tabblad **integreren** en geef de para meter timer een duidelijke naam die u in de functie kunt gebruiken. In de voor gaande code verwacht de functie dat de timer *myTimer*wordt genoemd. Geef als volgt een [cron-expressie](../app-service/webjobs-create.md#CreateScheduledCRON) op voor de timer: `0 * * * * *`. Deze expressie zorgt ervoor dat de functie één keer per minuut wordt uitgevoerd.

Voeg op hetzelfde tabblad **integreren** een invoer van het type **Azure Blob-opslag**toe. Deze invoer verwijst naar het bestand sync. txt dat het tijds tempel bevat van de laatste gebeurtenis die door de functie is bekeken. Deze invoer wordt in de functie geopend met behulp van de parameter naam. In de voor gaande code verwacht de invoer van de Azure Blob-opslag de parameter naam *inputBlob*. Selecteer het opslag account waarin het bestand sync. txt zich bevindt (dit kan hetzelfde zijn of een ander opslag account zijn). Geef in het veld pad het pad naar het bestand op in de indeling `{container-name}/path/to/sync.txt`.

Voeg een uitvoer van het type **Azure Blob-opslag**toe. Deze uitvoer verwijst naar het bestand sync. txt dat u in de invoer hebt gedefinieerd. Deze uitvoer wordt gebruikt door de functie om het tijds tempel van de laatste gebeurtenis die is bekeken, te schrijven. De voor gaande code verwacht dat deze para meter *outputBlob*wordt genoemd.

De functie is nu gereed. Ga terug naar het tabblad **ontwikkelen** en sla de code op. Controleer het uitvoer venster voor compilatie fouten en corrigeer deze indien nodig. Als de code wordt gecompileerd, moet de code nu elke minuut de sleutel kluis logboeken controleren en nieuwe gebeurtenissen naar de gedefinieerde Service Bus wachtrij pushen. U ziet dat logboek gegevens naar het logboek venster schrijven wanneer de functie wordt geactiveerd.

### <a name="azure-logic-app"></a>Azure Logic-app

Vervolgens moet u een Azure Logic-app maken die de gebeurtenissen ophaalt die de functie naar de Service Bus wachtrij pusht, de inhoud parseert en een e-mail bericht verzendt op basis van een voor waarde die overeenkomt.

[Maak een logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md) door **een resource maken** > **Integration** > **Logic app**te selecteren.

Nadat de logische app is gemaakt, gaat u naar deze en selecteert u **bewerken**. Selecteer in de Logic app-editor **Service Bus wachtrij** en voer uw service bus referenties in om deze te verbinden met de wachtrij.

![Azure Logic App Service Bus](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Selecteer **een voor waarde toevoegen**. In de voor waarde gaat u naar de geavanceerde editor en voert u de volgende code in. Vervang *APP_ID* door de daad werkelijke app-id van uw web-app:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Deze expressie retourneert in feite **Onwaar** als de *AppID* van de binnenkomende gebeurtenis (de hoofd tekst van het service bus bericht) niet de *AppID* van de app is.

Maak nu een actie onder **als Nee, niets doen**.

![Actie Azure Logic Apps kiezen](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Selecteer voor de actie **Office 365-e-mail verzenden**. Vul de velden in om een e-mail bericht te maken dat u kunt verzenden wanneer de gedefinieerde voor waarde **Onwaar**retourneert. Als u geen Office 365 hebt, zoekt u naar alternatieven om dezelfde resultaten te krijgen.

U hebt nu een end-to-end-pijp lijn die een nieuwe sleutel kluis audit logboeken eenmaal per minuut zoekt. Er worden nieuwe logboeken gepusht die naar een Service Bus wachtrij worden gevonden. De logische app wordt geactiveerd wanneer er een nieuw bericht binnenkomt in de wachtrij. Als de *AppID* binnen het evenement niet overeenkomt met de app-id van de aanroepende toepassing, wordt een e-mail bericht verzonden.
