---
title: Een Azure Automation-runbook starten met een webhook
description: Een webhook waarmee een client een runbook kan starten in Azure Automation vanuit een HTTP-gesprek.  In dit artikel wordt beschreven hoe u een webhook maakt en hoe u er een aanroepen om een runbook te starten.
services: automation
ms.subservice: process-automation
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 8cb641f95e7327e80f42df86a56eba8c34e7e598
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367020"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Een Azure Automation-runbook starten met een webhook

Met een webhook kan een externe service een bepaalde runbook starten in Azure Automation via één HTTP-aanvraag. Externe services omvatten Azure DevOps Services, GitHub, Azure Monitor-logboeken en aangepaste toepassingen. Een dergelijke service kan een webhook gebruiken om een runbook te starten zonder een volledige oplossing te implementeren met behulp van de Azure Automation API. U webhooks vergelijken met andere methoden voor het starten van een runbook in [Het starten van een runbook in Azure Automation.](automation-starting-a-runbook.md)

> [!NOTE]
> Het gebruik van een webhook om een Python-runbook te starten wordt niet ondersteund.

![WebhooksOverzicht](media/automation-webhooks/webhook-overview-image.png)

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="webhook-properties"></a>Webhook-eigenschappen

In de volgende tabel worden de eigenschappen beschreven die u moet configureren voor een webhook.

| Eigenschap | Beschrijving |
|:--- |:--- |
| Name |Naam van de webhook. U elke gewenste naam opgeven, omdat deze niet wordt blootgesteld aan de client. Het wordt alleen gebruikt om de runbook in Azure Automation te identificeren. Als een best practice, moet u de webhook een naam met betrekking tot de client die het gebruikt. |
| URL |URL van de webhook. Dit is het unieke adres dat een client aanroept met een HTTP-POST om het runbook te starten dat is gekoppeld aan de webhook. Het wordt automatisch gegenereerd wanneer u de webhook maakt. U geen aangepaste URL opgeven. <br> <br> De URL bevat een beveiligingstoken waarmee een systeem van derden het runbook kan aanroepen zonder verdere verificatie. Om deze reden moet u de URL behandelen als een wachtwoord. Om veiligheidsredenen u de URL in de Azure-portal alleen bekijken bij het maken van de webhook. Let op de URL op een veilige locatie voor toekomstig gebruik. |
| Vervaldatum | Vervaldatum van de webhook, waarna het niet meer kan worden gebruikt. U de vervaldatum wijzigen nadat de webhook is gemaakt, zolang de webhook niet is verlopen. |
| Ingeschakeld | Stel in dat u aangeeft of de webhook standaard is ingeschakeld wanneer deze wordt gemaakt. Als u deze eigenschap instelt op Uitgeschakeld, kan geen enkele client de webhook gebruiken. U deze eigenschap instellen wanneer u de webhook of een andere tijd na de creatie maakt. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Parameters die worden gebruikt wanneer de webhook een runbook start

Een webhook kan waarden definiëren voor runbook-parameters die worden gebruikt wanneer het runbook wordt gestart. De webhook moet waarden bevatten voor alle verplichte runbook-parameters en kan waarden bevatten voor optionele parameters. Een parameterwaarde die is geconfigureerd voor een webhook kan zelfs na het maken van een webhook worden gewijzigd. Meerdere webhooks die aan één runbook zijn gekoppeld, kunnen elk verschillende parameterwaarden voor runbook gebruiken. Wanneer een client een runbook start met een webhook, kan deze de parameterwaarden die in de webhook zijn gedefinieerd, niet overschrijven.

Als u gegevens van de client wilt ontvangen, ondersteunt het runbook één parameter genaamd `WebhookData`. Deze parameter definieert een object met gegevens die de client opneemt in een POST-aanvraag.

![WebhookData-eigenschappen](media/automation-webhooks/webhook-data-properties.png)

De `WebhookData` parameter heeft de volgende eigenschappen:

| Eigenschap | Beschrijving |
|:--- |:--- |
| `WebhookName` | Naam van de webhook. |
| `RequestHeader` | Hashtable met de headers van het binnenkomende POST-verzoek. |
| `RequestBody` | Lichaam van de inkomende POST verzoek. Deze hoofdtekst behoudt alle gegevensopmaak, zoals tekenreeks, JSON, XML of vormgecodeerd. Het runbook moet worden geschreven om te werken met de verwachte gegevensindeling. |

Er is geen configuratie van de webhook vereist om de `WebhookData` parameter te ondersteunen, en het runbook is niet vereist om het te accepteren. Als de runbook de parameter niet definieert, worden alle details van de aanvraag die van de client wordt verzonden, genegeerd.

> [!NOTE]
> Wanneer u een webhook aanroept, moet de client altijd parameterwaarden opslaan voor het geval de aanroep mislukt. Als er een netwerkstoring of verbindingsprobleem is, kan de toepassing mislukte webhook-oproepen niet ophalen.

Als u een `WebhookData` waarde opgeeft voor bij het maken van webhooks, wordt deze overschreven wanneer de webhook het runbook start met de gegevens van de post-aanvraag van de client. Dit gebeurt zelfs als de toepassing geen gegevens in de aanvraaginstantie bevat. 

Als u een runbook `WebhookData` start dat definieert met een ander mechanisme `WebhookData` dan een webhook, u een waarde bieden voor dat de runbook herkent. Deze waarde moet een object [properties](#webhook-properties) zijn `WebhookData` met dezelfde eigenschappen als de parameter, zodat de `WebhookData` runbook ermee kan werken, net zoals het werkt met werkelijke objecten die door een webhook worden doorgegeven.

Als u bijvoorbeeld de volgende runbook start vanuit de Azure-portal en een aantal voorbeeldwebhook-gegevens wilt doorgeven voor het testen, moet u de gegevens in JSON doorgeven in de gebruikersinterface.

![WebhookData parameter van UI](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Voor het volgende voorbeeld van runbook definiëren `WebhookData`we de volgende eigenschappen voor:

* **WebhookName**: MyWebhook
* **RequestBody**:`*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Nu passeren we het volgende JSON-object `WebhookData` in de gebruikersinterface voor de parameter. Dit voorbeeld, met vervoerretouren en nieuwelingen, komt overeen met de indeling die wordt doorgegeven vanuit een webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![WebhookData-parameter starten vanuit de gebruikersinterface](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure Automation registreert de waarden van alle invoerparameters met de runbook-taak. Zo wordt elke input van de client in de webhook-aanvraag geregistreerd en beschikbaar voor iedereen die toegang heeft tot de automatiseringstaak. Om deze reden moet u voorzichtig zijn met het opnemen van gevoelige informatie in webhook-gesprekken.

## <a name="webhook-security"></a>Webhook-beveiliging

De beveiliging van een webhook is afhankelijk van de privacy van de URL, die een beveiligingstoken bevat waarmee de webhook kan worden aangeroepen. Azure Automation voert geen verificatie uit op een aanvraag zolang deze naar de juiste URL wordt gemaakt. Daarom mogen uw clients geen webhooks gebruiken voor runbooks die zeer gevoelige bewerkingen uitvoeren zonder een alternatief middel te gebruiken om de aanvraag te valideren.

U logica opnemen in een runbook om te bepalen of deze wordt aangeroepen door een webhook. Laat het runbook `WebhookName` de `WebhookData` eigenschap van de parameter controleren. Het runbook kan verdere validatie uitvoeren door `RequestHeader` `RequestBody` te zoeken naar bepaalde informatie in de en eigenschappen.

Een andere strategie is om het runbook een validatie van een externe aandoening te laten uitvoeren wanneer het een webhook-verzoek ontvangt. Denk bijvoorbeeld aan een runbook die door GitHub wordt aangeroepen wanneer er een nieuwe commit is voor een GitHub-repository. De runbook maakt mogelijk verbinding met GitHub om te valideren dat er een nieuwe commit is opgetreden voordat deze wordt voortgezet.

## <a name="creating-a-webhook"></a>Een webhook maken

Gebruik de volgende procedure om een nieuwe webhook te maken die is gekoppeld aan een runbook in de Azure-portal.

1. Klik op de pagina Runbooks in de Azure-portal op het runbook waarmee de webhook de details van de runbook begint te bekijken. Controleer of het **veld** status van het runbook is ingesteld op **Gepubliceerd**.
2. Klik boven aan de pagina op **Webhook** om de webhook-pagina toevoegen te openen.
3. Klik **op Nieuwe webhook maken** om de pagina Webhook maken te openen.
4. Vul de velden **Naam** en **vervaldatum** in voor de webhook en geef op of deze moet worden ingeschakeld. Zie [Webhook-eigenschappen](#webhook-properties) voor meer informatie over deze eigenschappen.
5. Klik op het kopieerpictogram en druk op Ctrl+C om de URL van de webhook te kopiëren. Neem het dan op een veilige plaats op. 

    > [!NOTE]
    > Zodra u de webhook hebt gemaakt, u de URL niet meer ophalen.

   ![Webhook-URL](media/automation-webhooks/copy-webhook-url.png)

1. Klik **op Parameters** om waarden voor de runbookparameters op te geven. Als het runbook verplichte parameters heeft, u de webhook niet maken, tenzij u waarden opgeeft.
1. Klik op **Maken** om de webhook te maken.

## <a name="using-a-webhook"></a>Een webhook gebruiken

Als u een webhook wilt gebruiken nadat deze `POST` is gemaakt, moet uw client een HTTP-aanvraag met de URL voor de webhook opgeven. De syntaxis is:

```http
http://<Webhook Server>/token?=<Token Value>
```

De klant ontvangt een van de `POST` volgende retourcodes van het verzoek.

| Code | Tekst | Beschrijving |
|:--- |:--- |:--- |
| 202 |Geaccepteerd |De aanvraag is geaccepteerd en het runbook is in de wachtrij geplaatst. |
| 400 |Onjuiste aanvraag |Het verzoek werd om een van de volgende redenen niet aanvaard: <ul> <li>De webhook is verlopen.</li> <li>De webhook is uitgeschakeld.</li> <li>Het token in de URL is ongeldig.</li>  </ul> |
| 404 |Niet gevonden |Het verzoek werd om een van de volgende redenen niet aanvaard: <ul> <li>De webhook is niet gevonden.</li> <li>Het runbook is niet gevonden.</li> <li>Het account is niet gevonden.</li>  </ul> |
| 500 |Interne serverfout |De URL was geldig, maar er is een fout opgetreden. Gelieve het verzoek opnieuw in te dienen. |

Ervan uitgaande dat de aanvraag succesvol is, bevat het webhook-antwoord de taak-ID in JSON-indeling zoals hieronder weergegeven. Het bevat een enkele taak-ID, maar de JSON-indeling zorgt voor potentiële toekomstige verbeteringen.

```json
{"JobIds":["<JobId>"]}
```

De client kan niet bepalen wanneer de runbook-taak is voltooid of de voltooiingsstatus van de webhook. Het kan deze informatie achterhalen met behulp van de taak-ID met een ander mechanisme, zoals [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) of de [Azure Automation API.](/rest/api/automation/job)

## <a name="renewing-a-webhook"></a><a name="renew-webhook"></a>Een webhook vernieuwen

Wanneer een webhook wordt gemaakt, heeft deze een geldigheidsduur van tien jaar, waarna deze automatisch verloopt. Zodra een webhook is verlopen, u deze niet meer opnieuw activeren. U het alleen verwijderen en vervolgens opnieuw maken. 

U een webhook verlengen die zijn vervaldatum nog niet heeft bereikt. Ga als lid van het andere over op een webhook:

1. Navigeer naar het runbook met de webhook. 
2. Selecteer **Webhooks** onder **Resources**. 
3. Klik op de webhook die u wilt uitbreiden. 
4. Kies op de pagina Webhook een nieuwe vervaldatum en -tijd en klik op **Opslaan**.

## <a name="sample-runbook"></a>Voorbeeld van runbook

De volgende voorbeeldrunbook accepteert de webhookgegevens en start de virtuele machines die zijn opgegeven in de aanvraaginstantie. Als u dit runbook wilt testen, klikt u in uw Automatiseringsaccount onder **Runbooks**op **Een runbook maken**. Zie [Een runbook maken](automation-quickstart-create-runbook.md)als u niet weet hoe u een runbook maakt.

> [!NOTE]
> Voor niet-grafische PowerShell-runbooks `Add-AzAccount` en `Add-AzureRMAccount` zijn aliassen voor [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). U deze cmdlets gebruiken of u [uw modules](automation-update-azure-modules.md) in uw Automation-account updaten naar de nieuwste versies. Mogelijk moet u uw modules bijwerken, zelfs als u zojuist een nieuw Automation-account hebt gemaakt.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VMs from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal."
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="testing-the-sample"></a>Het monster testen

In het volgende voorbeeld wordt Windows PowerShell gebruikt om een runbook met een webhook te starten. Elke taal die een HTTP-aanvraag kan indienen, kan een webhook gebruiken. Windows PowerShell wordt hier als voorbeeld gebruikt.

Het runbook verwacht een lijst van virtuele machines geformatteerd in JSON in de hoofdtekst van het verzoek. Het runbook valideert ook dat de kopteksten een gedefinieerd bericht bevatten om te valideren dat de webhook-beller geldig is.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

In het volgende voorbeeld ziet u de hoofdtekst van `RequestBody` het `WebhookData`verzoek dat beschikbaar is voor het runbook in de eigenschap van . Deze waarde is opgemaakt in JSON om compatibel te zijn met het formaat dat is opgenomen in de hoofdtekst van het verzoek.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

In de volgende afbeelding ziet u het verzoek dat wordt verzonden vanuit Windows PowerShell en het resulterende antwoord. De taak-ID wordt uit het antwoord gehaald en omgezet in een tekenreeks.

![Knop Webhooks](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Een waarschuwing gebruiken om een Azure Automation-runbook te activeren](automation-create-alert-triggered-runbook.md)voor meer informatie over het gebruik van Azure Automation om actie te ondernemen voor Azure Automation.
