---
title: Azure Key Vault-oplossing in Azure Monitor | Microsoft Documenten
description: U de Azure Key Vault-oplossing in Azure Monitor gebruiken om Azure Key Vault-logboeken te bekijken.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/27/2019
ms.openlocfilehash: 7a2becf8cb43568383c324bb9f4f5b2e7b844268
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667140"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Azure Key Vault Analytics-oplossing in Azure Monitor

![Symbool Sleutelkluis](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

U de Azure Key Vault-oplossing in Azure Monitor gebruiken om Azure Key Vault AuditEvent-logboeken te bekijken.

Als u de oplossing wilt gebruiken, moet u logboekregistratie van Azure Key Vault-diagnoses inschakelen en de diagnose naar een Log Analytics-werkruimte leiden. Het is niet nodig om de logboeken naar Azure Blob-opslag te schrijven.

> [!NOTE]
> In januari 2017 is de ondersteunde manier van verzenden van logboeken van Key Vault naar Log Analytics gewijzigd. Als de Key Vault-oplossing die u gebruikt shows *(afgeschaft)* in de titel, verwijzen we u naar [het migreren van de oude Key Vault-oplossing](#migrating-from-the-old-key-vault-solution) voor stappen die u moet volgen.
>
>

## <a name="install-and-configure-the-solution"></a>De oplossing installeren en configureren
Gebruik de volgende instructies om de Azure Key Vault-oplossing te installeren en te configureren:

1. Gebruik het proces dat is beschreven in [Azure Monitor-oplossingen toevoegen vanuit de Galerie Oplossingen](../../azure-monitor/insights/solutions.md) om de Azure Key Vault-oplossing toe te voegen aan uw Log Analytics-werkruimte.
2. Diagnostische logboekregistratie inschakelen voor de Key Vault-resources om te controleren, met behulp van de [portal](#enable-key-vault-diagnostics-in-the-portal) of [PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Key Vault-diagnose inschakelen in de portal

1. Navigeer in de Azure-portal naar de Key Vault-bron om te controleren
2. Diagnostische *instellingen selecteren* om de volgende pagina te openen

   ![afbeelding van azure key vault-tegel](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Klik *op Diagnostische gegevens inschakelen* om de volgende pagina te openen

   ![afbeelding van azure key vault-tegel](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Geef een naam aan de diagnostische instelling.
5. Klik op het selectievakje *Verzenden naar logboekanalyse*
6. Selecteer een bestaande Log Analytics-werkruimte of maak een werkruimte
7. Als u *Logboeken van AuditEvent* wilt inschakelen, klikt u op het selectievakje onder Logboek
8. Klik *op Opslaan* om de logboekregistratie van diagnostische gegevens in te schakelen in de werkruimte Log Analytics.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Key Vault-diagnose inschakelen met PowerShell
Het volgende PowerShell-script geeft een `Set-AzDiagnosticSetting` voorbeeld van hoe u resources gebruiken om bronlogboekregistratie voor Key Vault in te schakelen:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Details van azure Key Vault-gegevensverzameling controleren
Azure Key Vault-oplossing verzamelt diagnostische logboeken rechtstreeks uit de Key Vault.
Het is niet nodig om de logboeken naar Azure Blob-opslag te schrijven en er is geen agent vereist voor het verzamelen van gegevens.

In de volgende tabel worden methoden voor het verzamelen van gegevens en andere details weergegeven over de manier waarop gegevens worden verzameld voor Azure Key Vault.

| Platform | Direct agent | Systems Center Operations Manager-agent | Azure | Operations Manager vereist? | Operations Manager agent gegevens verzonden via management groep | Verzamelingsfrequentie |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | bij aankomst |

## <a name="use-azure-key-vault"></a>Azure Key Vault gebruiken
Nadat u [de oplossing hebt geïnstalleerd,](https://azuremarketplace.microsoft.com/en-usrketplace/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview)bekijkt u de Key Vault-gegevens door op de tegel **Key Vault Analytics** te klikken op de pagina Overzicht van **Azure-monitor.** Open deze pagina in het menu **Azure Monitor** door te klikken op **Meer** onder de sectie **Inzichten.** 

![afbeelding van azure key vault-tegel](media/azure-key-vault/log-analytics-keyvault-tile.png)

Nadat u op de tegel **Key Vault Analytics** hebt geklikt, u samenvattingen van uw logboeken bekijken en vervolgens inzoomen op details voor de volgende categorieën:

* Volume van alle belangrijke kluisbewerkingen in de loop van de tijd
* Mislukte bewerkingsvolumes in de loop van de tijd
* Gemiddelde operationele latentie per bewerking
* Kwaliteit van de dienstverlening voor bewerkingen met het aantal bewerkingen dat meer dan 1000 ms inneemt en een lijst met bewerkingen die meer dan 1000 ms in nemen

![afbeelding van azure key vault-dashboard](media/azure-key-vault/log-analytics-keyvault01.png)

![afbeelding van azure key vault-dashboard](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Details voor elke bewerking weergeven
1. Klik **op** de pagina Overzicht op de tegel **Key Vault Analytics.**
2. Bekijk op het **Azure Key Vault-dashboard** de overzichtsgegevens in een van de bladen en klik er vervolgens op om gedetailleerde informatie erover te bekijken op de zoekpagina van het logboek.

    Op een van de zoekpagina's van het logboek u resultaten bekijken op tijd, gedetailleerde resultaten en uw logboekzoekgeschiedenis. U ook filteren op facetten om de resultaten te beperken.

## <a name="azure-monitor-log-records"></a>Logboekrecords voor Azure Monitor
De Azure Key Vault-oplossing analyseert records met een type **KeyVaults** die zijn verzameld uit [AuditEvent-logboeken](../../key-vault/key-vault-logging.md) in Azure Diagnostics.  Eigenschappen voor deze records staan in de volgende tabel:  

| Eigenschap | Beschrijving |
|:--- |:--- |
| `Type` |*AzureDiagnostics* |
| `SourceSystem` |*Azure* |
| `CallerIpAddress` |IP-adres van de klant die het verzoek heeft ingediend |
| `Category` | *AuditEvent* |
| `CorrelationId` |Een optionele GUID die de client kan doorgeven om de logboeken aan de clientzijde te relateren aan (Sleutelkluis-)logboeken aan de servicezijde. |
| `DurationMs` |De tijd die nodig was om de REST-API-aanvraag af te handelen in milliseconden. Deze keer is geen netwerklatentie opgenomen, dus de tijd die u aan de clientzijde meet, komt mogelijk niet overeen met deze tijd. |
| `httpStatusCode_d` |HTTP-statuscode geretourneerd door de aanvraag (bijvoorbeeld *200*) |
| `id_s` |Unieke ID van het verzoek |
| `identity_claim_appid_g` | GUID voor de toepassings-id |
| `OperationName` |Naam van de bewerking, zoals gedocumenteerd in [Azure Key Vault Logging](../../key-vault/key-vault-logging.md) |
| `OperationVersion` |REST API-versie aangevraagd door de client (bijvoorbeeld *2015-06-01*) |
| `requestUri_s` |Uri van het verzoek |
| `Resource` |Naam van de sleutelkluis |
| `ResourceGroup` |Resourcegroep van de sleutelkluis |
| `ResourceId` |Azure Resource Manager-resource-id. Voor Key Vault-logboeken is dit de Key Vault-bron-id. |
| `ResourceProvider` |*Microsoft. KEYVAULT* |
| `ResourceType` | *Kluizen* |
| `ResultSignature` |HTTP-status (bijvoorbeeld *OK)* |
| `ResultType` |Resultaat van REST API-aanvraag (bijvoorbeeld *Succes*) |
| `SubscriptionId` |Azure-abonnements-ID van het abonnement dat de Key Vault bevat |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migreren van de oude Key Vault-oplossing
In januari 2017 is de ondersteunde manier van verzenden van logboeken van Key Vault naar Log Analytics gewijzigd. Deze wijzigingen bieden de volgende voordelen:
+ Logboeken worden rechtstreeks naar een Log Analytics-werkruimte geschreven zonder dat u een opslagaccount hoeft te gebruiken
+ Minder latentie vanaf het moment dat logboeken worden gegenereerd voor hen die beschikbaar zijn in Log Analytics
+ Minder configuratiestappen
+ Een algemene indeling voor alle typen Azure-diagnostiek

Ga als beste de perfecte oplossing gebruiken:

1. [Diagnostische gegevens configureren om rechtstreeks naar een Log Analytics-werkruimte te worden verzonden vanuit Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. De Azure Key Vault-oplossing inschakelen met behulp van het proces dat is beschreven in [Azure-monitoroplossingen toevoegen vanuit de Galerie Oplossingen](../../azure-monitor/insights/solutions.md)
3. Opgeslagen query's, dashboards of waarschuwingen bijwerken om het nieuwe gegevenstype te gebruiken
   + Type is change from: KeyVaults to AzureDiagnostics. U het ResourceType gebruiken om te filteren op Key Vault-logboeken.
   + In plaats `KeyVaults`van: , gebruik`AzureDiagnostics | where ResourceType'=="VAULTS"`
   + Velden: (Veldnamen zijn hoofdlettergevoelig)
   + Voor elk veld met een \_achtervoegsel van s, \_d of \_g in de naam, wijzigt u het eerste teken in kleine letters
   + Voor elk veld met een \_achtervoegsel van o in naam worden de gegevens opgesplitst in afzonderlijke velden op basis van de geneste veldnamen. De UPN van de beller wordt bijvoorbeeld opgeslagen in een veld`identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   + VeldbellerIpAdres gewijzigd in CallerIPAddress
   + Field RemoteIPCountry is niet meer aanwezig
4. Verwijder de *Key Vault Analytics-oplossing (Deprecated)* Als u PowerShell gebruikt, gebruikt u`Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Gegevens die vóór de wijziging zijn verzameld, zijn niet zichtbaar in de nieuwe oplossing. U deze gegevens blijven opvragen met de oude namen Type en veld.

## <a name="troubleshooting"></a>Problemen oplossen
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Volgende stappen
* Gebruik [Logboekquery's in Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) om gedetailleerde Azure Key Vault-gegevens weer te geven.
