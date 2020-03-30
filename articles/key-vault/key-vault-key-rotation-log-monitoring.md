---
title: Azure Key Vault instellen met end-to-end sleutelrotatie en -controle | Microsoft Documenten
description: Gebruik deze handleiding om u te helpen bij het instellen van sleutelrotatie en het bewaken van sleutelkluislogboeken.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218406"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Azure Key Vault instellen met sleutelrotatie en controle

## <a name="introduction"></a>Inleiding

Nadat u een sleutelkluis hebt, u deze gebruiken om sleutels en geheimen op te slaan. Uw toepassingen hoeven uw sleutels of geheimen niet langer voort te duren, maar kunnen ze indien nodig uit de kluis aanvragen. Met een sleutelkluis u sleutels en geheimen bijwerken zonder het gedrag van uw toepassing te beïnvloeden, wat een breed scala aan mogelijkheden voor uw sleutel- en geheim beheer opent.

In dit artikel wordt uitgelegd hoe u een geplande rotatie van opslagaccountsleutels implementeert, de logboeken voor de controle van de sleutelkluis controleert en waarschuwingen verzamelen wanneer onverwachte aanvragen worden gedaan. 

U moet eerst een sleutelkluis maken met de methode van uw keuze:

- [een geheim uit Azure Key Vault instellen en ophalen met behulp van Azure CLI](quick-create-cli.md)
- [Een geheim instellen en ophalen uit Azure Key Vault met Azure PowerShell](quick-create-powershell.md)
- [Een geheim instellen en ophalen uit Azure Key Vault met Azure Portal](quick-create-portal.md)


## <a name="store-a-secret"></a>Bewaar een geheim

Als u een toepassing wilt inschakelen om een geheim uit Key Vault op te halen, moet u eerst het geheim maken en uploaden naar uw kluis.

Start een Azure PowerShell-sessie en gebruik de volgende opdracht om u aan te melden bij uw Azure-account:

```powershell
Connect-AzAccount
```

Voer in het pop-upbrowservenster de gebruikersnaam en het wachtwoord voor uw Azure-account in. PowerShell krijgt alle abonnementen die aan dit account zijn gekoppeld. PowerShell gebruikt standaard de eerste.

Als u meerdere abonnementen hebt, moet u mogelijk opgeven welke is gebruikt om uw sleutelkluis te maken. Voer het volgende in om de abonnementen voor uw account te bekijken:

```powershell
Get-AzSubscription
```

Voer het volgende in om het abonnement op te geven dat is gekoppeld aan de sleutelkluis die u registreert:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Omdat in dit artikel wordt aangetoond dat een opslagaccountsleutel als geheim wordt opgeslagen, moet u die opslagaccountsleutel krijgen.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Nadat u uw geheim hebt opgehaald (in dit geval uw opslagaccountsleutel), moet u die sleutel converteren naar een beveiligde tekenreeks en vervolgens een geheim maken met die waarde in uw sleutelkluis.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Vervolgens krijgt u de URI voor het geheim dat u hebt gemaakt. Je hebt deze URI in een latere stap nodig om de sleutelkluis te bellen en je geheim op te halen. Voer de volgende PowerShell-opdracht uit en noteer de ID-waarde, de URI van het geheim:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>De toepassing instellen

Nu u een geheim hebt opgeslagen, u code gebruiken om deze op te halen en te gebruiken nadat u nog een paar stappen hebt uitgevoerd.

Eerst moet u uw toepassing registreren bij Azure Active Directory. Vertel Key Vault vervolgens uw toepassingsgegevens, zodat aanvragen van uw toepassing kunnen worden toegestaan.

> [!NOTE]
> Uw toepassing moet worden gemaakt op dezelfde Azure Active Directory-tenant als uw sleutelkluis.

1. **Open Azure Active Directory**.
2. Selecteer **App-registraties**. 
3. Selecteer **Nieuwe toepassingsregistratie** om een toepassing toe te voegen aan Azure Active Directory.

    ![Toepassingen openen in Azure Active Directory](./media/keyvault-keyrotation/azure-ad-application.png)

4. Onder **Maken**verlaat u het toepassingstype als **web-app / API** en geef u uw toepassing een naam. Geef uw aanvraag een **aanmeldings-URL**. Deze URL kan alles zijn wat je wilt voor deze demo.

    ![Toepassingsregistratie maken](./media/keyvault-keyrotation/create-app.png)

5. Nadat de toepassing is toegevoegd aan Azure Active Directory, wordt de toepassingspagina geopend. Selecteer **Instellingen**en selecteer **Eigenschappen**. Kopieer de waarde **van de toepassings-id.** Je hebt het nodig in latere stappen.

Genereer vervolgens een sleutel voor uw toepassing, zodat deze kan communiceren met Azure Active Directory. Als u een sleutel wilt maken, selecteert u **Toetsen** onder **Instellingen**. Noteer de nieuw gegenereerde sleutel voor uw Azure Active Directory-toepassing. U hebt deze in een latere stap nodig. De sleutel is niet beschikbaar nadat u deze sectie hebt verlaten. 

![Azure Active Directory-app-sleutels](./media/keyvault-keyrotation/create-key.png)

Voordat u oproepen van uw toepassing in de sleutelkluis vaststelt, moet u de sleutelkluis vertellen over uw toepassing en de machtigingen ervan. Met de volgende opdracht worden de kluisnaam en de toepassings-id van uw Azure Active Directory-app gebruikt om de **toepassing** Toegang te verlenen tot uw sleutelkluis.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

U bent nu klaar om te beginnen met het bouwen van uw sollicitatiegesprekken. In uw toepassing moet u de NuGet-pakketten installeren die nodig zijn om te communiceren met Azure Key Vault en Azure Active Directory. Voer in de Visual Studio Package Manager-console de volgende opdrachten in. Bij het schrijven van dit artikel is de huidige versie van het Azure Active Directory-pakket 3.10.305231913, dus bevestig de nieuwste versie en update indien nodig.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Maak in uw toepassingscode een klasse om de methode voor uw Azure Active Directory-verificatie vast te houden. In dit voorbeeld wordt die klasse **Utils**genoemd. Voeg de `using` volgende instructie toe:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Voeg vervolgens de volgende methode toe om het JWT-token op te halen uit Azure Active Directory. Voor onderhoud u de hardgecodeerde tekenreekswaarden verplaatsen naar uw web- of toepassingsconfiguratie.

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

Voeg de benodigde code toe om Key Vault te bellen en uw geheime waarde op te halen. Eerst moet u de `using` volgende instructie toevoegen:

```csharp
using Microsoft.Azure.KeyVault;
```

Voeg de methodeaanroepen toe om Key Vault aan te roepen en je geheim op te halen. Met deze methode geeft u de geheime URI die u in een vorige stap hebt opgeslagen. Let op het gebruik van de **GetToken-methode** van de klasse **Utils** die u eerder hebt gemaakt.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Wanneer u uw toepassing uitvoert, moet u nu authenticeren naar Azure Active Directory en vervolgens uw geheime waarde ophalen uit Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Sleutelrotatie met Azure Automation

> [!IMPORTANT]
> Azure Automation-runbooks vereisen nog `AzureRM` steeds het gebruik van de module.

U bent nu klaar om een rotatiestrategie op te zetten voor de waarden die u opslaat als Key Vault-geheimen. Geheimen kunnen op verschillende manieren worden gedraaid:

- Als onderdeel van een handmatig proces
- Programmatisch met API-aanroepen
- Via een Azure Automation-script

Voor de toepassing van dit artikel gebruikt u PowerShell in combinatie met Azure Automation om de toegangssleutel van een Azure-opslagaccount te wijzigen. Je werkt dan een sleutelkluisgeheim bij met die nieuwe sleutel.

Als u Azure Automation toestaat geheime waarden in uw sleutelkluis in te stellen, moet u de client-id voor de verbinding met de naam **AzureRunAsConnection**krijgen. Deze verbinding is gemaakt toen u uw Azure Automation-exemplaar instelde. Als u deze id wilt vinden, selecteert u **Elementen** in uw Azure Automation-exemplaar. Selecteer van daaruit **Verbindingen**en selecteer vervolgens de principal van de **AzureRunAsConnection-service.** Noteer de waarde **ApplicationId.**

![Azure Automation-client-id](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Selecteer **Modules**in **assets**. Selecteer **Galerij**en zoek bijgewerkte versies van elk van de volgende modules en importeer deze:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Bij het schrijven van dit artikel moesten alleen de eerder genoteerde modules worden bijgewerkt voor het volgende script. Als uw automatiseringstaak mislukt, bevestigt u dat u alle benodigde modules en hun afhankelijkheden hebt geïmporteerd.

Nadat u de toepassings-id voor uw Azure Automation-verbinding hebt opgehaald, moet u uw sleutelkluis vertellen dat deze toepassing toestemming heeft om geheimen in uw kluis bij te werken. Gebruik de volgende PowerShell-opdracht:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Selecteer vervolgens **Runbooks** onder de instantie Azure Automation en selecteer **Vervolgens Runbook toevoegen**. Selecteer **Snelle invoer**. Geef uw runbook een naam en selecteer **PowerShell** als runbook-type. U een beschrijving toevoegen. Selecteer tot slot **Maken**.

![Runbook maken](./media/keyvault-keyrotation/Create_Runbook.png)

Plak het volgende PowerShell-script in het editorvenster voor uw nieuwe runbook:

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

Selecteer in het editorvenster **het deelvenster Testen** om uw script te testen. Nadat het script foutloos is uitgevoerd, u **Publiceren**selecteren en vervolgens een planning voor de runbook toepassen in het configuratievenster van de runbook.

## <a name="key-vault-auditing-pipeline"></a>Key Vault-controlepijplijn

Wanneer u een sleutelkluis instelt, u controle inschakelen om logboeken te verzamelen over toegangsverzoeken die zijn gedaan in de sleutelkluis. Deze logboeken worden opgeslagen in een aangewezen Azure-opslagaccount en kunnen worden uitgetrokken, gecontroleerd en geanalyseerd. In het volgende scenario worden Azure-functies, Azure-logische apps en controlelogboeken voor sleutelkluizen gebruikt om een pijplijn te maken die een e-mail verzendt wanneer een app die niet overeenkomt met de app-id van de web-app, geheimen uit de kluis ophaalt.

Eerst moet u het inloggen op uw sleutelkluis inschakelen. Gebruik de volgende PowerShell-opdrachten. (U de volledige details in [dit artikel over key-vault-logging](key-vault-logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Nadat logboekregistratie is ingeschakeld, worden controlelogboeken opgeslagen in het aangewezen opslagaccount. Deze logboeken bevatten gebeurtenissen over hoe en wanneer uw sleutelkluizen worden geopend en door wie.

> [!NOTE]
> U hebt 10 minuten na de sleutelkluisbewerking toegang tot uw logboekgegevens. Het zal vaak eerder beschikbaar zijn dan dat.

De volgende stap is het [maken van een Azure Service Bus-wachtrij.](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) In deze wachtrij worden controlelogboeken voor sleutelkluizen gepusht. Wanneer de controlelogboekberichten in de wachtrij staan, pikt de logische app ze op en werkt ze erop. Een instantie servicebus maken met de volgende stappen:

1. Maak een naamruimte voor servicebus (als u er al een hebt die u wilt gebruiken, gaat u naar stap 2).
2. Blader naar de instantie Servicebus in de Azure-portal en selecteer de naamruimte waarin u de wachtrij wilt maken.
3. Selecteer **Een resource** > **Enterprise Integration** > **Service Bus maken**en voer de vereiste gegevens in.
4. Zoek de verbindingsgegevens van servicebus door de naamruimte te selecteren en vervolgens **Verbindingsgegevens te**selecteren . Je hebt deze informatie nodig voor de volgende sectie.

Maak vervolgens [een Azure-functie](../azure-functions/functions-create-first-azure-function.md) om de logboeken van de sleutelkluis binnen het opslagaccount te peilen en nieuwe gebeurtenissen op te halen. Deze functie wordt geactiveerd volgens een schema.

Als u een Azure-functie-app wilt maken, selecteert **u Een resource maken,** zoekt u op de marktplaats naar **functie-app**en selecteert u **Vervolgens Maken**. Tijdens het maken u een bestaand hostingplan gebruiken of een nieuw plan maken. U ook kiezen voor dynamische hosting. Zie [Azure-functies schalen](../azure-functions/functions-scale.md)voor meer informatie over de hostingopties voor Azure-functies.

Nadat de Azure-functie-app is gemaakt, gaat u naar de app En selecteert u het **timerscenario** en **C\# ** voor de taal. Selecteer vervolgens **Deze functie maken**.

![Startblad Azure-functies](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Vervang op het tabblad **Ontwikkelen** de run.csx-code door het volgende:

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
> Wijzig de variabelen in de vorige code om te verwijzen naar uw opslagaccount waar de sleutelkluislogboeken zijn geschreven, naar de instantie Servicebus die u eerder hebt gemaakt en naar het specifieke pad naar de opslaglogboeken voor sleutelkluizen.

De functie pikt het nieuwste logboekbestand op van het opslagaccount waar de sleutelkluislogboeken worden geschreven, pakt de laatste gebeurtenissen uit dat bestand en duwt ze naar een servicebuswachtrij. 

Omdat één bestand meerdere gebeurtenissen kan hebben, moet u een sync.txt-bestand maken waar de functie ook naar kijkt om de tijdstempel te bepalen van de laatste gebeurtenis die is opgehaald. Als u dit bestand gebruikt, wordt ervoor dat u niet meerdere keren dezelfde gebeurtenis pusht. 

Het bestand sync.txt bevat een tijdstempel voor de laatst aangetroffen gebeurtenis. Wanneer de logboeken worden geladen, moeten ze worden gesorteerd op basis van hun tijdstempels om ervoor te zorgen dat ze correct zijn besteld.

Voor deze functie verwijzen we naar een aantal extra bibliotheken die niet beschikbaar zijn in Azure-functies. Om deze bibliotheken op te nemen, hebben we Azure-functies nodig om ze op te halen met NuGet. Selecteer onder het vak **Code** de optie **Bestanden weergeven**.

![Optie Bestanden weergeven](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Voeg een bestand met de volgende inhoud toe, project.json:

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

Nadat u **Opslaan**hebt geselecteerd, worden de vereiste binaire bestanden gedownload van Azure Functions.

Ga naar het tabblad **Integreren** en geef de parameter timer een betekenisvolle naam om te gebruiken binnen de functie. In de voorgaande code verwacht de functie dat de timer *myTimer*wordt genoemd. Geef als volgt een [CRON-expressie](../app-service/webjobs-create.md#CreateScheduledCRON) voor de timer op: `0 * * * * *`. Deze expressie zorgt ervoor dat de functie eenmaal per minuut wordt uitgevoerd.

Voeg op hetzelfde tabblad **Integreren** een invoer toe van het type **Azure Blob-opslag**. Deze invoer wijst naar het bestand sync.txt dat de tijdstempel bevat van de laatste gebeurtenis die door de functie wordt bekeken. Deze invoer wordt binnen de functie geopend met behulp van de parameternaam. In de vorige code verwacht de Azure Blob-opslaginvoer dat de parameternaam *invoerblob*is. Selecteer het opslagaccount waar het sync.txt-bestand zich bevindt (het kan hetzelfde of een ander opslagaccount zijn). Geef in het padveld het pad naar `{container-name}/path/to/sync.txt`het bestand op in de indeling .

Voeg een uitvoer toe van het type **Azure Blob-opslag**. Deze uitvoer wijst naar het sync.txt-bestand dat u in de invoer hebt gedefinieerd. Deze uitvoer wordt door de functie gebruikt om de tijdstempel van de laatste gebeurtenis te schrijven. De voorgaande code verwacht dat deze parameter *outputBlob*wordt genoemd.

De functie is nu klaar. Zorg ervoor dat u terugschakelt naar het tabblad **Ontwikkelen** en de code opslaat. Controleer het uitvoervenster op eventuele compilatiefouten en corrigeer deze indien nodig. Als de code wordt gecompileerd, moet de code nu elke minuut de logboeken van de sleutelkluis controleren en nieuwe gebeurtenissen in de gedefinieerde wachtrij van servicebus duwen. U moet zien logging informatie uitschrijven naar het logboekvenster elke keer dat de functie wordt geactiveerd.

### <a name="azure-logic-app"></a>Azure-logica-app

Vervolgens moet u een Azure-logica-app maken die de gebeurtenissen oppikt die de functie naar de wachtrij van de servicebus pusht, de inhoud ontleedt en een e-mail verzendt op basis van een voorwaarde die wordt gekoppeld.

[Maak een logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md) door Een**logicalogische app** **voor bronintegratie** > **Integration** > maken te selecteren .

Nadat de logische app is gemaakt, gaat u naar de app en selecteert u **Bewerken**. Selecteer in de editor van de logische app de optie **ServiceBuswachtrij** en voer uw servicebusreferenties in om deze aan de wachtrij te koppelen.

![Azure Logic App-servicebus](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Selecteer **Een voorwaarde toevoegen**. Schakel in de voorwaarde over naar de geavanceerde editor en voer de volgende code in. Vervang *APP_ID* door de werkelijke app-id van uw web-app:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Deze expressie wordt in wezen **false** als de *appid* van de binnenkomende gebeurtenis (die het hoofd van het Service Bus-bericht is) niet de *appid* van de app is.

Nu, maak een actie onder **ALS NEE, NIETS DOEN**.

![Azure Logic Apps kiezen actie](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Selecteer voor de actie **Office 365 - stuur e-mail**. Vul de velden in om een e-mail te maken die u wilt verzenden wanneer de gedefinieerde voorwaarde **false retourneert.** Als u office 365 niet hebt, zoekt u naar alternatieven om dezelfde resultaten te bereiken.

U hebt nu een end-to-end pijplijn die één keer per minuut op zoek is naar nieuwe key-vault auditlogs. Het duwt nieuwe logboeken vindt het aan een rij van de Bus van de Dienst. De logische app wordt geactiveerd wanneer een nieuw bericht in de wachtrij terechtkomt. Als de *appid* in de gebeurtenis niet overeenkomt met de app-id van de aanroepende toepassing, stuurt deze een e-mail.
