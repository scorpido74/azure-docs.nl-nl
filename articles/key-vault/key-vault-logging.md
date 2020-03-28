---
title: Azure Key Vault-logboekregistratie | Microsoft Documenten
description: Deze zelfstudie helpt u op weg met de logboekregistratie van Azure Sleutelkluis.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 8915970cd4c70228fad3b49921f4c81d6d90aa72
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78195325"
---
# <a name="azure-key-vault-logging"></a>Logboekregistratie voor Azure Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nadat u een of meer sleutelkluizen hebt gemaakt, wilt u waarschijnlijk controleren hoe en wanneer uw sleutelkluizen worden geopend en door wie. U dit doen door logboekregistratie in te schakelen voor Azure Key Vault, waarmee informatie wordt opgeslagen in een Azure-opslagaccount dat u opgeeft. Er wordt automatisch een nieuwe container met de naam **insights-logs-auditevent** gemaakt voor uw opgegeven opslagaccount. U dit zelfde opslagaccount gebruiken voor het verzamelen van logboeken voor meerdere sleutelkluizen.

U heeft 10 minuten na de sleutelkluisbewerking toegang tot uw logboekgegevens. In de meeste gevallen gaat het echter veel sneller.  Het is aan u om uw logboeken in uw opslagaccount te beheren:

* Gebruik standaardmethoden van Azure voor toegangsbeheer om uw logboeken te beveiligen door het aantal gebruikers te beperken dat toegang heeft tot de logboeken.
* Verwijder de logboeken die u niet meer in uw opslagaccount wilt bewaren.

Deze zelfstudie helpt u op weg met de logboekregistratie van Azure Sleutelkluis. U maakt een opslagaccount aan, schakelt logboekregistratie in en interpreteert de verzamelde logboekgegevens.  

> [!NOTE]
> Deze zelfstudie bevat geen instructies voor het maken van sleutelkluizen, sleutels of geheimen. Zie Wat is Azure Key Vault voor deze [informatie?](key-vault-overview.md). Of voor azure cli-instructies voor meerdere platforms, [raadpleegdeze gelijkwaardige zelfstudie](key-vault-manage-with-cli2.md).
>
> In dit artikel worden Azure PowerShell-instructies gegeven voor het bijwerken van diagnostische logboekregistratie. U diagnostische logboekregistratie ook bijwerken met Azure Monitor in de sectie **Diagnostische logboeken** van de Azure-portal. 
>

Zie Wat is Azure [Key Vault voor](key-vault-overview.md)overzichtsinformatie over Key Vault? Zie de [prijspagina](https://azure.microsoft.com/pricing/details/key-vault/)voor informatie over waar Key Vault beschikbaar is.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een bestaande sleutelkluis die u hebt gebruikt.  
* Azure PowerShell, minimale versie van 1.0.0. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) om Azure PowerShell te installeren en te koppelen aan uw Azure-abonnement. Als u Azure PowerShell al hebt geïnstalleerd en de versie niet kent, voert u vanaf de Azure PowerShell-console . `$PSVersionTable.PSVersion`  
* Voldoende opslagruimte op Azure voor uw Sleutelkluis-logboeken.

## <a name="connect-to-your-key-vault-subscription"></a><a id="connect"></a>Verbinding maken met uw key vault-abonnement

De eerste stap bij het instellen van key logging is om Azure PowerShell te richten op de sleutelkluis die u wilt registreren.

Start een Azure PowerShell-sessie en meld u aan bij uw Azure-account met de volgende opdracht:  

```powershell
Connect-AzAccount
```

Voer in het pop-upvenster in de browser uw gebruikersnaam en wachtwoord voor uw Azure-account in. Azure PowerShell krijgt alle abonnementen die aan dit account zijn gekoppeld. PowerShell gebruikt standaard de eerste.

Mogelijk moet u het abonnement opgeven dat u hebt gebruikt om uw sleutelkluis te maken. Voer de volgende opdracht in om de abonnementen voor uw account te bekijken:

```powershell
Get-AzSubscription
```

Voer vervolgens het volgende in om het abonnement op te geven dat is gekoppeld aan de sleutelkluis die u registreert:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

PowerShell naar het juiste abonnement wijzen is een belangrijke stap, vooral als u meerdere abonnementen aan uw account hebt gekoppeld. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) voor meer informatie over het configureren van Azure PowerShell.

## <a name="create-a-storage-account-for-your-logs"></a><a id="storage"></a>Een opslagaccount voor uw logboeken maken

Hoewel u een bestaand opslagaccount voor uw logboeken gebruiken, maken we een opslagaccount dat is toegewezen aan Key Vault-logboeken. Voor het gemak voor wanneer we dit later moeten specificeren, slaan we de details op in een variabele met de naam **sa**.

Voor extra beheergemak gebruiken we ook dezelfde resourcegroep als de groep die de sleutelkluis bevat. Vanuit de [zelfstudie aan](key-vault-get-started.md)de slag, deze resourcegroep heet **ContosoResourceGroup**en we blijven de locatie Oost-Azië gebruiken. Vervang deze waarden door uw eigen, indien van toepassing:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Als u besluit een bestaand opslagaccount te gebruiken, moet het hetzelfde abonnement gebruiken als uw sleutelkluis. En het moet het Azure Resource Manager-implementatiemodel gebruiken, in plaats van het klassieke implementatiemodel.
>
>

## <a name="identify-the-key-vault-for-your-logs"></a><a id="identify"></a>De sleutelkluis voor uw logboeken identificeren

In de [get-started tutorial](key-vault-get-started.md), de belangrijkste kluis naam was **ContosoKeyVault**. We blijven die naam gebruiken en slaan de gegevens op in een variabele met de naam **kv:**

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a name="enable-logging"></a><a id="enable"></a>Logboekregistratie inschakelen

Om logboekregistratie voor Key Vault mogelijk te maken, gebruiken we de cmdlet **Set-AzDiagnosticSetting,** samen met de variabelen die we hebben gemaakt voor het nieuwe opslagaccount en de sleutelkluis. We stellen ook de **vlag ingeschakeld in op** **$true** en stellen de categorie in op **AuditEvent** (de enige categorie voor key vault logging):

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

De uitvoer ziet er als volgt uit:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

Deze uitvoer bevestigt dat logboekregistratie nu is ingeschakeld voor uw sleutelkluis en dat informatie wordt opgeslagen in uw opslagaccount.

Optioneel u een bewaarbeleid voor uw logboeken zo instellen dat oudere logboeken automatisch worden verwijderd. Stel bijvoorbeeld het bewaarbeleid in door de vlag **-RetentionEnabled** in te stellen op **$true**en stel de parameter **-RetentionInDays** in op **90,** zodat logboeken ouder dan 90 dagen automatisch worden verwijderd.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Wat wordt in het logboek vastgelegd?

* Alle geverifieerde REST API-aanvragen, inclusief mislukte aanvragen als gevolg van toegangsmachtigingen, systeemfouten of slechte aanvragen.
* Bewerkingen op de sleutelkluis zelf, inclusief maken, verwijderen, toegangsbeleid voor sleutelkluizen instellen en belangrijke vault-kenmerken zoals tags bijwerken.
* Bewerkingen op sleutels en geheimen in de sleutelkluis, waaronder:
  * Deze sleutels of geheimen maken, wijzigen of verwijderen.
  * Tekenen, verifiëren, versleutelen, ontsleutelen, inpakken en uitpakken van sleutels, geheimen ophalen en sleutels en geheimen (en hun versies) vermelden.
* Niet-geverifieerde aanvragen die in een 401-respons resulteren. Voorbeelden zijn aanvragen die geen token aan toonder hebben, die misvormd zijn of verlopen of die een ongeldig token hebben.  

## <a name="access-your-logs"></a><a id="access"></a>Toegang tot uw logboeken

Key Vault-logboeken worden opgeslagen in de container **insights-logs-auditevent** in het opslagaccount dat u hebt opgegeven. Om de logboeken te bekijken, moet je blobs downloaden.

Maak eerst een variabele voor de containernaam. Je gebruikt deze variabele gedurende de rest van de walkthrough.

```powershell
$container = 'insights-logs-auditevent'
```

Voer het als volgt op:

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

Zoals u zien in deze uitvoer, volgen de blobs een naamgevingsconventie:`resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

De datum- en tijdwaarden maken gebruik van UTC.

Omdat u hetzelfde opslagaccount gebruiken om logboeken voor meerdere bronnen te verzamelen, is de volledige bron-id in de blobnaam handig om alleen de blobs die u nodig hebt te openen of te downloaden. Maar eerst laten we zien hoe u alle blobs kunt downloaden.

Maak een map om de blobs te downloaden. Bijvoorbeeld:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Haal vervolgens een lijst met alle blobs op:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Pipe deze lijst via **Get-AzStorageBlobContent** om de blobs te downloaden naar de doelmap:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Wanneer u deze tweede **/** opdracht uitvoert, maakt de scheidingsteken in de blobnamen een volledige mapstructuur onder de doelmap. U gebruikt deze structuur om de blobs te downloaden en op te slaan als bestanden.

Als u alleen specifieke blobs wilt downloaden, moet u jokertekens gebruiken. Bijvoorbeeld:

* Als u meerdere sleutelkluizen hebt en het logboek voor slechts één sleutelkluis wilt downloaden met de naam CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Als u meerdere resourcegroepen hebt en logboeken voor slechts één resourcegroep wilt downloaden, gebruikt u `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Als u alle logboeken voor de maand januari 2019 wilt downloaden, gebruikt u: `-Blob '*/year=2019/m=01/*'`

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

We gaan zo kijken wat er precies in de logboeken staat. Maar voordat we verder gaan met dat, moet je nog twee commando's weten:

* De status van diagnostische instellingen voor uw sleutelkluisresource opvragen: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Logboekregistratie voor uw sleutelkluisresource uitschakelen: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a name="interpret-your-key-vault-logs"></a><a id="interpret"></a>De Key Vault-logboeken interpreteren

Afzonderlijke blobs worden opgeslagen als tekst, die is opgemaakt als een JSON-blob. Laten we eens kijken naar een voorbeeld log entry. Voer deze opdracht uit:

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Het retourneert een log vermelding vergelijkbaar met deze:

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

In de volgende tabel worden de veldnamen en -beschrijvingen weergegeven:

| Veldnaam | Beschrijving |
| --- | --- |
| **Tijd** |Datum en tijd in UTC. |
| **Resourceid** |Azure Resource Manager-bron-id. Voor Key Vault-logboeken is dit altijd de Key Vault-bron-id. |
| **operationName** |Naam van de bewerking, zoals beschreven in de volgende tabel. |
| **operationVersion** |REST API-versie gevraagd door de client. |
| **Categorie** |Type resultaat. Voor Key Vault-logboeken is **AuditEvent** de enige beschikbare waarde. |
| **resultType** |Resultaat van de REST API-aanvraag. |
| **resultSignature** |HTTP-status. |
| **resultDescription** |Extra beschrijving van het resultaat, indien beschikbaar. |
| **durationMs** |De tijd die nodig was om de REST-API-aanvraag af te handelen in milliseconden. Hierbij wordt geen rekening gehouden met de netwerklatentie, zodat de tijd die u aan de clientzijde meet mogelijk niet overeenkomt met de tijd die hier wordt weergegeven. |
| **callerIpAddress** |IP-adres van de klant die het verzoek heeft ingediend. |
| **correlationId** |Een optionele GUID die de client kan doorgeven om de logboeken aan de clientzijde te relateren aan (Sleutelkluis-)logboeken aan de servicezijde. |
| **Identiteit** |Identiteit van het token dat is gepresenteerd in de REST API-aanvraag. Dit is meestal een 'gebruiker', een 'serviceprincipal' of de combinatie 'user+appId', zoals in het geval van een verzoek dat voortvloeit uit een Azure PowerShell-cmdlet. |
| **Eigenschappen** |Informatie die varieert op basis van de bewerking **(operationName).** In de meeste gevallen bevat dit veld clientgegevens (de tekenreeks van de gebruikersagent die door de client is doorgegeven), de exacte REST API-aanvraag URI en de HTTP-statuscode. Bovendien, wanneer een object wordt geretourneerd als gevolg van een aanvraag (bijvoorbeeld **KeyCreate** of **VaultGet),** bevat het ook de sleutel URI (als "id"), vault URI of secret URI. |

De veldwaarden **van operationName** zijn in *objectverb-indeling.* Bijvoorbeeld:

* Alle belangrijke kluisbewerkingen hebben de `Vault<action>` indeling, zoals `VaultGet` en `VaultCreate`.
* Alle belangrijke bewerkingen hebben het `Key<action>` formaat, zoals `KeySign` en `KeyList`.
* Alle geheime bewerkingen hebben het `Secret<action>` formaat, zoals `SecretGet` en `SecretListVersions`.

In de volgende tabel worden de **bewerkingswaarden** en de bijbehorende REST API-opdrachten weergegeven:

| operationName | REST API, opdracht |
| --- | --- |
| **Verificatie** |Verifiëren via Azure Active Directory-eindpunt |
| **VaultGet** |[Informatie over een sleutelkluis ophalen](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Een sleutelkluis maken of bijwerken](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Een sleutelkluis verwijderen](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Een sleutelkluis bijwerken](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Alle sleutelkluizen in een resourcegroep weergeven](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Een sleutel maken](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Informatie over een sleutel ophalen](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Een sleutel in een kluis importeren](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Een back-up maken van een sleutel](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
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
| **SecretGet** |[Krijg een geheim](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Een geheim bijwerken](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Een geheim verwijderen](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Geheimen in een kluis weergeven](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Versies van een geheim weergeven](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a name="use-azure-monitor-logs"></a><a id="loganalytics"></a>Azure Monitor-logboeken gebruiken

U de Key Vault-oplossing in Azure Monitor-logboeken gebruiken om key vault **auditevent-logboeken** te bekijken. In Azure Monitor-logboeken gebruikt u logboekquery's om gegevens te analyseren en de informatie te krijgen die u nodig hebt. 

Zie [Azure Key Vault-oplossing in Azure Monitor-logboeken](../azure-monitor/insights/azure-key-vault.md)voor meer informatie, waaronder het instellen van dit. Dit artikel bevat ook instructies als u moet migreren van de oude Key Vault-oplossing die is aangeboden tijdens de preview van Azure Monitor-logboeken, waarbij u uw logboeken eerst hebt doorgestuurd naar een Azure-opslagaccount en azure-monitorlogboeken hebt geconfigureerd om van daaruit te lezen.

## <a name="next-steps"></a><a id="next"></a>Volgende stappen

Zie [Azure Key Vault gebruiken vanuit een webtoepassing](tutorial-net-create-vault-azure-web-app.md)voor een zelfstudie die Azure Key Vault in een .NET-webtoepassing gebruikt.

Zie de [Ontwikkelaarshandleiding voor Azure Key Vault](key-vault-developers-guide.md) voor het programmeren van verwijzingen.

Zie [Azure Key Vault-cmdlets](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)voor een lijst met Azure PowerShell 1.0-cmdlets voor Azure Key Vault.

Zie [Key Vault instellen met end-to-end sleutelrotatie en -controle](key-vault-key-rotation-log-monitoring.md)voor een zelfstudie over sleutelrotatie en logboekcontrole met Azure Key Vault.
