---
title: Logboek registratie voor Azure Key Vault | Microsoft Docs
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195325"
---
# <a name="azure-key-vault-logging"></a>Logboekregistratie voor Azure Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nadat u een of meer sleutel kluizen hebt gemaakt, wilt u waarschijnlijk controleren hoe en wanneer uw sleutel kluizen toegankelijk zijn, en door wie. U kunt dit doen door logboek registratie in te scha kelen voor Azure Key Vault, waarmee gegevens worden opgeslagen in een Azure Storage-account dat u opgeeft. Er wordt automatisch een nieuwe container met de naam **Insights-logs-audit event** gemaakt voor het opgegeven opslag account. U kunt dit opslag account gebruiken voor het verzamelen van Logboeken voor meerdere sleutel kluizen.

U kunt de logboek gegevens 10 minuten (Maxi maal) openen na de sleutel kluis bewerking. In de meeste gevallen gaat het echter veel sneller.  Het is aan u om uw logboeken in uw opslagaccount te beheren:

* Gebruik standaardmethoden van Azure voor toegangsbeheer om uw logboeken te beveiligen door het aantal gebruikers te beperken dat toegang heeft tot de logboeken.
* Verwijder de logboeken die u niet meer in uw opslagaccount wilt bewaren.

Deze zelfstudie helpt u op weg met de logboekregistratie van Azure Sleutelkluis. U maakt een opslag account, schakelt logboek registratie in en interpreteert de verzamelde logboek gegevens.  

> [!NOTE]
> Deze zelfstudie bevat geen instructies voor het maken van sleutelkluizen, sleutels of geheimen. Zie [Wat is Azure Key Vault?](key-vault-overview.md)voor deze informatie. Voor meerdere platformen van Azure CLI, Zie [Deze equivalente zelf studie](key-vault-manage-with-cli2.md).
>
> Dit artikel bevat Azure PowerShell instructies voor het bijwerken van de diagnostische logboek registratie. U kunt Diagnostische logboek registratie ook bijwerken met behulp van Azure Monitor in de sectie **Diagnostische logboeken** van de Azure Portal. 
>

Zie [Wat is Azure Key Vault?](key-vault-overview.md)voor overzichts informatie over Key Vault. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/key-vault/)voor meer informatie over waar Key Vault beschikbaar is.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze zelfstudie te voltooien:

* Een bestaande sleutelkluis die u hebt gebruikt.  
* Azure PowerShell, minimale versie van 1.0.0. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) om Azure PowerShell te installeren en te koppelen aan uw Azure-abonnement. Als u Azure PowerShell al hebt geïnstalleerd en de versie niet weet, voert u `$PSVersionTable.PSVersion`in de Azure PowerShell-console in.  
* Voldoende opslagruimte op Azure voor uw Sleutelkluis-logboeken.

## <a id="connect"></a>Verbinding maken met uw sleutel kluis-abonnement

De eerste stap bij het instellen van sleutel registratie is het verwijzen van Azure PowerShell naar de sleutel kluis die u wilt registreren.

Start een Azure PowerShell-sessie en meld u aan bij uw Azure-account met behulp van de volgende opdracht:  

```powershell
Connect-AzAccount
```

Voer in het pop-upvenster in de browser uw gebruikersnaam en wachtwoord voor uw Azure-account in. Azure PowerShell worden alle abonnementen opgehaald die aan dit account zijn gekoppeld. Power Shell maakt standaard gebruik van de eerste.

Mogelijk moet u het abonnement opgeven dat u hebt gebruikt om de sleutel kluis te maken. Voer de volgende opdracht in om de abonnementen voor uw account weer te geven:

```powershell
Get-AzSubscription
```

Voer vervolgens het volgende in om het abonnement op te geven dat is gekoppeld aan de sleutel kluis:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

Power shell is een belang rijke stap, met name als er meerdere abonnementen aan uw account zijn gekoppeld. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) voor meer informatie over het configureren van Azure PowerShell.

## <a id="storage"></a>Een opslag account maken voor uw logboeken

Hoewel u een bestaand opslag account voor uw logboeken kunt gebruiken, maakt u een opslag account dat is toegewezen aan Key Vault Logboeken. Voor het gemak dat we later moeten opgeven, slaan we de details op in een variabele met de naam **sa**.

Voor een extra beheer gemak gebruiken we ook dezelfde resource groep als die waarin de sleutel kluis is opgenomen. Vanuit de [aan de slag-zelf studie](key-vault-get-started.md)heeft deze resource groep de naam **ContosoResourceGroup**en gaan we de Azië-Oost locatie blijven gebruiken. Vervang deze waarden door uw eigen waarde, zoals van toepassing:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Als u besluit om een bestaand opslag account te gebruiken, moet het hetzelfde abonnement gebruiken als uw sleutel kluis. En het moet het Azure Resource Manager-implementatie model gebruiken, in plaats van het klassieke implementatie model.
>
>

## <a id="identify"></a>De sleutelkluis voor uw logboeken identificeren

In de [zelf studie aan](key-vault-get-started.md)de slag is de naam van de sleutel kluis **ContosoKeyVault**. We blijven die naam gebruiken en slaan de details op in een variabele met de naam **KV**:

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>Logboekregistratie inschakelen

Voor het inschakelen van logboek registratie voor Key Vault gebruiken we de cmdlet **set-AzDiagnosticSetting** , samen met de variabelen die we hebben gemaakt voor het nieuwe opslag account en de sleutel kluis. U kunt de vlag **-enabled** ook instellen op **$True** en de categorie instellen op **audit event** (de enige categorie voor Key Vault logboek registratie):

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

Met deze uitvoer wordt bevestigd dat logboek registratie nu is ingeschakeld voor uw sleutel kluis en dat er gegevens worden opgeslagen in uw opslag account.

U kunt desgewenst een Bewaar beleid instellen voor uw logboeken, zodat oudere logboeken automatisch worden verwijderd. Stel bijvoorbeeld het Bewaar beleid in door de **-RetentionEnabled-** vlag in te stellen op **$True**en stel de para meter **-RetentionInDays** in op **90** zodat logboeken ouder zijn dan 90 dagen automatisch worden verwijderd.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Wat wordt in het logboek vastgelegd?

* Alle geverifieerde REST API aanvragen, met inbegrip van mislukte aanvragen als gevolg van toegangs machtigingen, systeem fouten of ongeldige aanvragen.
* Bewerkingen op de sleutel kluis zelf, waaronder het maken, verwijderen, instellen van het toegangs beleid voor sleutel kluizen en het bijwerken van sleutel kluis kenmerken, zoals Tags.
* Bewerkingen voor sleutels en geheimen in de sleutel kluis, waaronder:
  * Deze sleutels of geheimen maken, wijzigen of verwijderen.
  * Ondertekenen, controleren, versleutelen, ontsleutelen, decoderen en inpakken van sleutels, geheimen ophalen en sleutels en geheimen weer geven (en hun versie).
* Niet-geverifieerde aanvragen die in een 401-respons resulteren. Voor beelden zijn aanvragen die geen Bearer-token hebben, die ongeldig of verlopen zijn of die een ongeldig token hebben.  

## <a id="access"></a>Toegang tot uw logboeken

Key Vault-logboeken worden opgeslagen in de container **Insights-logs-audit event** in het opslag account dat u hebt ingevoerd. Als u de logboeken wilt weer geven, moet u blobs downloaden.

Maak eerst een variabele voor de containernaam. U gebruikt deze variabele in de rest van de procedure.

```powershell
$container = 'insights-logs-auditevent'
```

Als u alle blobs in deze container wilt weer geven, voert u het volgende in:

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

Zoals u in deze uitvoer kunt zien, hebben de blobs de volgende naamgevings regels: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

De datum- en tijdwaarden maken gebruik van UTC.

Omdat u hetzelfde opslag account kunt gebruiken voor het verzamelen van Logboeken voor meerdere resources, is de volledige Resource-ID in de BLOB-naam handig voor het openen of downloaden van alleen de blobs die u nodig hebt. Maar eerst laten we zien hoe u alle blobs kunt downloaden.

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

Wanneer u deze tweede opdracht uitvoert, maakt het **/** scheidings teken in de naam van de BLOB een volledige mapstructuur onder de doelmap. U gebruikt deze structuur om de blobs te downloaden en op te slaan als bestanden.

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

We gaan zo kijken wat er precies in de logboeken staat. Maar voordat we verdergaan, moet u nog twee opdrachten kennen:

* De status van diagnostische instellingen voor uw sleutelkluisresource opvragen: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Logboekregistratie voor uw sleutelkluisresource uitschakelen: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>De Key Vault-logboeken interpreteren

Afzonderlijke blobs worden opgeslagen als tekst, die is opgemaakt als een JSON-blob. We bekijken een voor beeld van een logboek vermelding. Voer deze opdracht uit:

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Er wordt een logboek vermelding geretourneerd die er ongeveer als volgt uitziet:

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

De volgende tabel bevat de veld namen en beschrijvingen:

| Veldnaam | Beschrijving |
| --- | --- |
| **tegelijk** |De datum en tijd in UTC. |
| **resourceId** |Azure Resource Manager resource-ID. Voor Key Vault-Logboeken is dit altijd de Key Vault Resource-ID. |
| **operationName** |Naam van de bewerking, zoals beschreven in de volgende tabel. |
| **operationVersion** |REST API versie die door de client is aangevraagd. |
| **rubriek** |Type resultaat. Voor Key Vault-Logboeken is **audit event** de enige beschik bare waarde. |
| **resultType** |Resultaat van de REST API aanvraag. |
| **resultSignature** |HTTP-status. |
| **resultDescription** |Extra beschrijving van het resultaat, indien beschikbaar. |
| **durationMs** |De tijd die nodig was om de REST-API-aanvraag af te handelen in milliseconden. Hierbij wordt geen rekening gehouden met de netwerklatentie, zodat de tijd die u aan de clientzijde meet mogelijk niet overeenkomt met de tijd die hier wordt weergegeven. |
| **callerIpAddress** |Het IP-adres van de client die de aanvraag heeft ingediend. |
| **Correlatie** |Een optionele GUID die de client kan doorgeven om de logboeken aan de clientzijde te relateren aan (Sleutelkluis-)logboeken aan de servicezijde. |
| **persoon** |Identiteit van het token dat is aangeboden in de REST API aanvraag. Dit is meestal een ' gebruiker ', een ' Service-Principal ' of de combi natie ' gebruiker + appId ', zoals in het geval van een aanvraag die resulteert van een Azure PowerShell-cmdlet. |
| **eigenschappen** |Informatie die varieert op basis van de bewerking (**operationname**). In de meeste gevallen bevat dit veld client informatie (de teken reeks van de gebruikers agent die is door gegeven door de client), de exacte REST API aanvraag-URI en de HTTP-status code. Als er een object wordt geretourneerd als gevolg van een aanvraag (bijvoorbeeld **VaultGet** **of een** service), bevat het ook de sleutel-URI (als id), kluis-URI of geheime URI. |

De waarde van het veld **operationname** bevindt zich in de *ObjectVerb* -indeling. Bijvoorbeeld:

* Alle sleutel kluis bewerkingen hebben de `Vault<action>` indeling, zoals `VaultGet` en `VaultCreate`.
* Alle sleutel bewerkingen hebben de `Key<action>` indeling, zoals `KeySign` en `KeyList`.
* Alle geheime bewerkingen hebben de `Secret<action>` indeling, zoals `SecretGet` en `SecretListVersions`.

De volgende tabel geeft een lijst van de **operationname** waarden en de bijbehorende rest API opdrachten:

| operationName | REST API opdracht |
| --- | --- |
| **Verificatie** |Verifiëren via Azure Active Directory-eind punt |
| **VaultGet** |[Informatie over een sleutelkluis ophalen](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Een sleutelkluis maken of bijwerken](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Een sleutelkluis verwijderen](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Een sleutelkluis bijwerken](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Alle sleutelkluizen in een resourcegroep weergeven](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **Maken** |[Een sleutel maken](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Informatie over een sleutel ophalen](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **Importeren** |[Een sleutel in een kluis importeren](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **Back-up maken** |[Een back-up van een sleutel maken](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **Verwijderen** |[Een sleutel verwijderen](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **Herstellen** |[Een sleutel herstellen](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **Hekje** |[Aanmelden met een sleutel](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **Controleren** |[Verifiëren met een sleutel](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **Tekst terugloop** |[Een sleutel inpakken](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Een sleutel uitpakken](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **Sleutel codering** |[Versleutelen met een sleutel](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **Sleutel decoderen** |[Ontsleutelen met een sleutel](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **Bijwerken** |[Een sleutel bijwerken](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **Lijst met handelingen** |[De sleutels in een kluis weergeven](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[De versies van een sleutel weergeven](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **Geheimset** |[Een geheim maken](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Een geheim ophalen](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Een geheim bijwerken](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Een geheim verwijderen](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Geheimen in een kluis weergeven](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Versies van een geheim weergeven](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Azure Monitor-Logboeken gebruiken

U kunt de Key Vault oplossing in Azure Monitor Logboeken gebruiken om Key Vault **audit event** -logboeken te controleren. In Azure Monitor-Logboeken kunt u logboek query's gebruiken om gegevens te analyseren en de benodigde informatie op te halen. 

Zie [Azure Key Vault-oplossing in azure monitor-logboeken](../azure-monitor/insights/azure-key-vault.md)voor meer informatie over hoe u dit kunt instellen. Dit artikel bevat ook instructies voor het migreren van de oude Key Vault-oplossing die werd aangeboden tijdens de preview-versie van Azure Monitor logboeken, waar u uw logboeken voor het eerst naar een Azure-opslag account hebt gerouteerd en Azure Monitor Logboeken hebt geconfigureerd om daar te lezen.

## <a id="next"></a>Volgende stappen

Zie [Azure Key Vault gebruiken in een webtoepassing](tutorial-net-create-vault-azure-web-app.md)voor een zelf studie over het gebruik van Azure Key Vault in een .NET-webtoepassing.

Zie de [Ontwikkelaarshandleiding voor Azure Key Vault](key-vault-developers-guide.md) voor het programmeren van verwijzingen.

Zie [Azure Key Vault-cmdlets](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)voor een lijst met Azure PowerShell 1,0-cmdlets voor Azure Key Vault.

Zie [Key Vault met end-to-end-draaiing en controle instellen](key-vault-key-rotation-log-monitoring.md)voor een zelf studie over de belangrijkste draaiing en logboek controle met Azure Key Vault.
