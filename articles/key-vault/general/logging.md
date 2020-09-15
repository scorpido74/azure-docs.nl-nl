---
title: Logboekregistratie voor Azure Key Vault | Microsoft Docs
description: Meer informatie over het bewaken van de toegang van uw sleutelkluizen door logboekregistratie voor Azure Key Vault in te schakelen. Hierbij wordt de informatie opgeslagen in een Azure-opslagaccount dat u opgeeft.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 0ed50b8d128386008a73eb4d1a8b412a42fdb945
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89485452"
---
# <a name="azure-key-vault-logging"></a>Logboekregistratie voor Azure Key Vault

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Nadat u een of meer sleutelkluizen hebt gemaakt, wilt u wellicht controleren hoe en wanneer uw sleutelkluizen toegankelijk zijn en voor wie. U kunt dit doen door logboekregistratie voor Azure Key Vault in te schakelen. Hierbij wordt de informatie opgeslagen in een Azure-opslagaccount dat u opgeeft. Er wordt automatisch een nieuwe container genaamd **insights-logs-auditevent** gemaakt voor uw opgegeven opslagaccount. U kunt ditzelfde opslagaccount gebruiken om logboeken voor meerdere sleutelkluizen te verzamelen.

U kunt uw logboekgegevens (maximaal) tien minuten nadat de sleutelkluisbewerking is uitgevoerd, bekijken. In de meeste gevallen gaat het echter veel sneller.  Het is aan u om uw logboeken in uw opslagaccount te beheren:

* Gebruik standaardmethoden van Azure voor toegangsbeheer om uw logboeken te beveiligen door het aantal gebruikers te beperken dat toegang heeft tot de logboeken.
* Verwijder de logboeken die u niet meer in uw opslagaccount wilt bewaren.

Deze zelfstudie helpt u op weg met de logboekregistratie van Azure Sleutelkluis. U zult een opslagaccount maken, logboekregistratie inschakelen en de verzamelde logboekgegevens interpreteren.  

> [!NOTE]
> Deze zelfstudie bevat geen instructies voor het maken van sleutelkluizen, sleutels of geheimen. Voor die informatie moet u [Wat is Azure Key Vault?](overview.md) bekijken. Zie [deze equivalente zelfstudie](manage-with-cli2.md) voor instructies over de platformonafhankelijke Azure CLI.
>
> Dit artikel biedt Azure PowerShell-instructies voor het bijwerken van diagnostische logboeken. U kunt diagnostische logboeken ook bijwerken door Azure Monitor te gebruiken in de sectie **Diagnostische logboeken** van de Azure-portal. 
>

Zie [Wat is Azure Key Vault?](overview.md) voor algemene informatie over Key Vault. Bekijk de [pagina met prijzen](https://azure.microsoft.com/pricing/details/key-vault/) voor informatie over waar Key Vault beschikbaar is. Zie [Azure Monitor voor Key Vault](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview) voor informatie over het gebruik ervan.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een bestaande sleutelkluis die u hebt gebruikt.  
* Azure PowerShell, minimaal versie 1.0.0. Zie [Azure PowerShell installeren en configureren](/powershell/azure/) om Azure PowerShell te installeren en te koppelen aan uw Azure-abonnement. Als u Azure PowerShell al hebt geïnstalleerd, maar niet weet welke versie u hebt, typt u `$PSVersionTable.PSVersion` in de Azure PowerShell-console.  
* Voldoende opslagruimte op Azure voor uw Sleutelkluis-logboeken.

## <a name="connect-to-your-key-vault-subscription"></a><a id="connect"></a>Verbinding maken met uw sleutelkluisabonnement

De eerste stap in het instellen van logboekregistratie is Azure PowerShell te laten wijzen naar de sleutelkluis waarvoor u logboekregistratie wilt inschakelen.

Start een Azure PowerShell-sessie en gebruik de volgende opdracht om u aan te melden bij uw Azure-account:  

```powershell
Connect-AzAccount
```

Voer in het pop-upvenster in de browser uw gebruikersnaam en wachtwoord voor uw Azure-account in. Azure PowerShell haalt alle abonnementen op die zijn gekoppeld aan dit account. PowerShell gebruikt standaard het eerste abonnement.

U moet mogelijk het abonnement opgeven dat u hebt gebruikt om uw sleutelkluis te maken. Voer de volgende opdracht in als u de abonnementen voor uw account wilt zien:

```powershell
Get-AzSubscription
```

Geef vervolgens op welk abonnement is gekoppeld aan de sleutelkluis waarvoor u logboekregistratie wilt inschakelen. Typ:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

PowerShell laten wijzen naar het juiste abonnement is een belangrijke stap, vooral als er meerdere abonnementen zijn gekoppeld aan uw account. Zie [Azure PowerShell installeren en configureren](/powershell/azure/) voor meer informatie over het configureren van Azure PowerShell.

## <a name="create-a-storage-account-for-your-logs"></a><a id="storage"></a>Een opslagaccount voor uw logboeken maken

Hoewel u een bestaand opslagaccount voor uw logboeken kunt gebruiken, maken we hier een opslagaccount speciaal voor Key Vault-logboeken. Voor het gemak slaan we de details op in een variabele met de naam **sa**. Deze moeten we later namelijk opgeven.

En om het ons nog gemakkelijker te maken, gebruiken we de resourcegroep die de sleutelkluis bevat. In de [zelfstudie Aan de slag](../secrets/quick-create-cli.md) heeft deze resourcegroep de naam **ContosoResourceGroup** en we gebruiken hier ook de locatie Azië - oost. Vervang deze waarden door uw eigen, indien van toepassing:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Als u besluit een bestaand opslagaccount te gebruiken, moet dat hetzelfde abonnement gebruiken als uw sleutelkluis. En het moet het Azure Resource Manager-implementatiemodel gebruiken in plaats van het klassieke implementatiemodel.
>
>

## <a name="identify-the-key-vault-for-your-logs"></a><a id="identify"></a>De sleutelkluis voor uw logboeken identificeren

In de [zelfstudie Aan de slag](../secrets/quick-create-cli.md) was de naam van de sleutelkluis **ContosoKeyVault**. We zullen die naam blijven gebruiken en de gegevens opslaan in een variabele genaamd **kv**:

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a name="enable-logging-using-azure-powershell"></a><a id="enable"></a>Logboekregistratie inschakelen met Azure PowerShell

Als u logboekregistratie wilt inschakelen voor Key Vault, gebruikt u de cmdlet **Set-AzDiagnosticSetting**, samen met de variabelen die we voor het nieuwe opslagaccount en de sleutelkluis hebben gemaakt. We moeten de vlag **-Enabled** instellen op **$true** en de categorie instellen op `AuditEvent` (de enige categorie voor logboekregistratie van Key Vault):

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

De uitvoer ziet er als volgt uit:

```output
StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccountContosoKeyVaultLogs
ServiceBusRuleId   :
StorageAccountName :
    Logs
    Enabled           : True
    Category          : AuditEvent
    RetentionPolicy
    Enabled : False
    Days    : 0
```

Deze uitvoer bevestigt dat logboekregistratie nu is ingeschakeld voor uw sleutelkluis, en slaat informatie op in uw opslagaccount.

U kunt eventueel een retentiebeleid instellen voor uw logboeken, zodat oudere logboeken automatisch worden verwijderd. Stel bijvoorbeeld een retentiebeleid in door de vlag **-RetentionEnabled** in te stellen op **$true**, en stel de parameter **-RetentionInDays** in op **90**, zodat logboeken die ouder zijn dan 90 dagen automatisch worden verwijderd.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Wat wordt in het logboek vastgelegd?

* Alle geverifieerde REST-API-aanvragen, ook mislukte aanvragen als gevolg van toegangsmachtigingen, systeemfouten of ongeldige aanvragen.
* Bewerkingen voor de sleutelkluis zelf, zoals het maken, verwijderen en instellen van het toegangsbeleid voor sleutelkluizen, en het bijwerken van de kenmerken van sleutelkluizen, zoals tags.
* Bewerkingen van sleutels en geheimen in de sleutelkluis, waaronder:
  * Het maken, wijzigen of verwijderen van die sleutels of geheimen.
  * Het ondertekenen, verifiëren, versleutelen, ontsleutelen, verpakken en uitpakken van sleutels, het ophalen van geheimen en het vermelden van sleutels en geheimen (en hun versies).
* Niet-geverifieerde aanvragen die in een 401-respons resulteren. Voorbeelden daarvan zijn aanvragen die geen Bearer-token hebben, die ongeldig of verlopen zijn, of die een ongeldig token hebben.  

## <a name="enable-logging-using-azure-cli"></a>Logboekregistratie inschakelen met CLI

```azurecli
az login

az account set --subscription {AZURE SUBSCRIPTION ID}

az provider register -n Microsoft.KeyVault

az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="access-your-logs"></a><a id="access"></a>Toegang tot uw logboeken

Key Vault-logboeken worden opgeslagen in de container **insights-logs-auditevent** in het opslagaccount dat u hebt opgegeven. Als u de logboeken wilt bekijken, moet u blobs downloaden.

Maak eerst een variabele voor de containernaam. U zult deze variabele in de rest van de walkthrough gebruiken.

```powershell
$container = 'insights-logs-auditevent'
```

Als u alle blobs in deze container wilt weergeven, typt u:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

De uitvoer ziet er ongeveer als volgt uit:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

---
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Zoals u in deze uitvoer kunt zien, hebben de blobs de volgende naamgevingsregels: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

De datum- en tijdwaarden maken gebruik van UTC.

Aangezien u hetzelfde opslagaccount kunt gebruiken voor het verzamelen van logboeken voor meerdere resources, is de volledige resource-id in de blobnaam handig voor toegang tot of het downloaden van alleen de blobs die u nodig hebt. Maar eerst laten we zien hoe u alle blobs kunt downloaden.

Maak een map waarin u de blobs wilt downloaden. Bijvoorbeeld:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Haal vervolgens een lijst met alle blobs op:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Sluis deze lijst via **Get-AzStorageBlobContent** door om de blobs naar de doelmap te downloaden:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Wanneer u deze tweede opdracht uitvoert, maakt het scheidingsteken **/** in de blobnamen een volledige mapstructuur onder de doelmap. U gebruikt deze structuur voor het downloaden en opslaan van de blobs als bestanden.

Als u alleen specifieke blobs wilt downloaden, moet u jokertekens gebruiken. Bijvoorbeeld:

* Als u meerdere sleutelkluizen hebt en het logboek voor slechts één sleutelkluis wilt downloaden met de naam CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Als u meerdere resourcegroepen hebt en logboeken voor slechts één resourcegroep wilt downloaden, gebruikt u `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Als u alle logboeken voor de maand januari 2019 wilt downloaden, gebruikt u `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

We gaan zo kijken wat er precies in de logboeken staat. Maar eerst moet u nog twee opdrachten kennen:

* De status van diagnostische instellingen voor uw sleutelkluisresource opvragen: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Logboekregistratie voor uw sleutelkluisresource uitschakelen: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`


## <a name="interpret-your-key-vault-logs"></a><a id="interpret"></a>De Key Vault-logboeken interpreteren

Afzonderlijke blobs worden opgeslagen als tekst, die is opgemaakt als een JSON-blob. Laten we eens naar een voorbeeld van een logboekvermelding kijken. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

De volgende tabel bevat de namen en beschrijvingen van velden:

| Veldnaam | Beschrijving |
| --- | --- |
| **time** |Datum en tijd in UTC. |
| **resourceId** |Azure Resource Manager-resource-id. Voor Key Vault-logboeken is dit altijd de Key Vault-resource-id. |
| **operationName** |Naam van de bewerking, zoals beschreven in de volgende tabel. |
| **operationVersion** |REST-API-versie die door de client is aangevraagd. |
| **category** |Type resultaat. Voor Key Vault-logboeken is `AuditEvent` de enige beschikbare waarde. |
| **resultType** |Resultaat van de REST-API-aanvraag. |
| **resultSignature** |HTTP-status. |
| **resultDescription** |Extra beschrijving van het resultaat, indien beschikbaar. |
| **durationMs** |De tijd die nodig was om de REST-API-aanvraag af te handelen in milliseconden. Hierbij wordt geen rekening gehouden met de netwerklatentie, zodat de tijd die u aan de clientzijde meet mogelijk niet overeenkomt met de tijd die hier wordt weergegeven. |
| **callerIpAddress** |IP-adres van de client die de aanvraag heeft ingediend. |
| **correlationId** |Een optionele GUID die de client kan doorgeven om de logboeken aan de clientzijde te relateren aan (Sleutelkluis-)logboeken aan de servicezijde. |
| **identity** |De identiteit van het token dat is opgegeven in de REST-API-aanvraag. Dit is meestal ‘user’, ‘service principal’ of de combinatie ‘user+appId’, bijvoorbeeld bij een aanvraag via een Azure PowerShell-cmdlet. |
| **properties** |Gegevens die variëren op basis van de bewerking (**operationName**). In de meeste gevallen bevat dit veld clientgegevens (de useragent-tekenreeks die door de client wordt doorgegeven), de exacte URI voor de REST-API-aanvraag en de HTTP-statuscode. Als een object wordt geretourneerd als gevolg van een aanvraag (bijvoorbeeld **KeyCreate** of **VaultGet**), bevat dit veld ook de sleutel-URI (als `id`), kluis-URI of geheim-URI. |

De veldwaarden voor **operationName** hebben de *ObjectVerb*-indeling. Bijvoorbeeld:

* Alle sleutelkluisbewerkingen hebben de `Vault<action>`-indeling, zoals `VaultGet` en `VaultCreate`.
* Alle sleutelbewerkingen hebben de `Key<action>`-indeling, zoals `KeySign` en `KeyList`.
* Alle geheimbewerkingen hebben de `Secret<action>`-indeling, zoals `SecretGet` en `SecretListVersions`.

De volgende tabel bevat de **operationName**-waarden en de bijbehorende REST-API-opdrachten:

| operationName | REST-API-opdracht |
| --- | --- |
| **Verificatie** |Verifiëren via het Azure Active Directory-eindpunt |
| **VaultGet** |[Informatie over een sleutelkluis ophalen](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Een sleutelkluis maken of bijwerken](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Een sleutelkluis verwijderen](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Een sleutelkluis bijwerken](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Alle sleutelkluizen in een resourcegroep weergeven](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Een sleutel maken](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Informatie over een sleutel ophalen](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Een sleutel in een kluis importeren](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Een back-up van een sleutel maken](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Een sleutel verwijderen](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[Een sleutel herstellen](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Aanmelden met een sleutel](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Verifiëren met een sleutel](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Een sleutel inpakken](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Een sleutel uitpakken](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Versleutelen met een sleutel](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Ontsleutelen met een sleutel](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Een sleutel bijwerken](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[De sleutels in een kluis weergeven](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[De versies van een sleutel weergeven](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Een geheim maken](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Een geheim ophalen](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Een geheim bijwerken](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Een geheim verwijderen](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Geheimen in een kluis weergeven](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Versies van een geheim weergeven](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a name="use-azure-monitor-logs"></a><a id="loganalytics"></a>Azure Monitor-logboeken gebruiken

Met de Key Vault-oplossing in Azure Monitor-logboeken kunt u de `AuditEvent`-logboeken van Key Vault controleren. In Azure Monitor-logboeken kunt u logboekquery’s gebruiken om gegevens te analyseren en de informatie op te halen die u nodig hebt. 

Zie [Azure Key Vault in Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md) voor meer informatie.

## <a name="next-steps"></a><a id="next"></a>Volgende stappen

Zie [Azure Key Vault in een webtoepassing gebruiken](tutorial-net-create-vault-azure-web-app.md) voor een zelfstudie over het gebruik van Azure Key Vault in een .NET-webtoepassing.

Zie de [Ontwikkelaarshandleiding voor Azure Key Vault](developers-guide.md) voor het programmeren van verwijzingen.

Zie [Cmdlets voor Azure Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault) voor een lijst met Azure PowerShell 1.0-cmdlets voor Azure Key Vault.
