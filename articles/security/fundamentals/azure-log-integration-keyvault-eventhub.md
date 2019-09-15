---
title: Logboeken van Azure Key Vault integreren met behulp van Event Hubs | Microsoft Docs
description: Een zelf studie met de benodigde stappen om Key Vault logboeken beschikbaar te maken voor een SIEM met behulp van Azure Log Integration
services: security
author: barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 05/28/2019
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 15223c59f270dc562e521697186cfaf7f30073b9
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71004108"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Azure Log Integration zelf studie: Azure Key Vault gebeurtenissen verwerken door gebruik te maken van Event Hubs

>[!IMPORTANT]
> De Azure-functie voor logboek integratie wordt afgeschaft door 06/15/2019. AzLog-down loads zijn op 27 juni 2018 uitgeschakeld. Raadpleeg voor meer informatie over wat u verder kunt doen met [Azure monitor om te integreren met Siem-hulpprogram ma's](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

U kunt Azure Log Integration gebruiken om geregistreerde gebeurtenissen op te halen en deze beschikbaar te maken voor uw SIEM-systeem (Security Information and Event Management). In deze zelf studie ziet u een voor beeld van hoe Azure Log Integration kunnen worden gebruikt voor het verwerken van logboeken die zijn verkregen via Azure Event Hubs.

De aanbevolen methode voor het integreren van Azure-Logboeken is door gebruik te maken van de Azure Monitor-connector van uw SIEM-leverancier en deze [instructies](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md)te volgen. Als uw SIEM-leverancier echter geen connector biedt om Azure Monitor, kunt u Azure Log Integration gebruiken als tijdelijke oplossing (als uw SIEM wordt ondersteund door Azure Log Integration) totdat een dergelijke connector beschikbaar is.

 
Gebruik deze zelf studie om vertrouwd te raken met de manier waarop Azure Log Integration en Event Hubs samen werken door de voorbeeld stappen te volgen en te weten hoe elke stap de oplossing ondersteunt. Vervolgens kunt u uw eigen stappen voor het maken van de unieke vereisten van uw bedrijf door nemen die u hier hebt geleerd.

> [!WARNING]
> De stappen en opdrachten in deze zelf studie zijn niet bedoeld om te worden gekopieerd en geplakt. Ze zijn alleen voor beelden. Gebruik niet de Power shell-opdrachten zoals in uw live omgeving. U moet ze aanpassen op basis van uw unieke omgeving.


In deze zelf studie wordt u begeleid bij het nemen van Azure Key Vault activiteit die is geregistreerd in een Event Hub en deze beschikbaar maken als JSON-bestanden voor uw SIEM-systeem. U kunt vervolgens uw SIEM-systeem configureren om de JSON-bestanden te verwerken.

>[!NOTE]
>Voor de meeste stappen in deze zelf studie moet u sleutel kluizen, opslag accounts en Event hubs configureren. Aan het einde van deze zelf studie vindt u de specifieke Azure Log Integration stappen. Voer deze stappen niet uit in een productie omgeving. Ze zijn alleen bedoeld voor een test omgeving. U moet de stappen aanpassen voordat u ze in de productie omgeving gebruikt.

De informatie die wordt verstrekt, helpt u bij het begrijpen van de redenen achter elke stap. Koppelingen naar andere artikelen geven u meer details over bepaalde onderwerpen.

Zie voor meer informatie over de services die in deze zelf studie worden vermeld: 

- [Azure Key Vault](/azure/key-vault/key-vault-overview)
- [Azure Event Hubs](/azure/event-hubs/event-hubs-what-is-event-hubs)
- [Azure Log Integration](azure-log-integration-overview.md)


## <a name="initial-setup"></a>Eerste installatie

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Voordat u de stappen in dit artikel kunt uitvoeren, hebt u het volgende nodig:

* Een Azure-abonnement en een account voor dat abonnement met beheerders rechten. Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)maken.
 
* Een systeem met toegang tot Internet dat voldoet aan de vereisten voor het installeren van Azure Log Integration. Het systeem kan zich op een Cloud service bevinden of lokaal worden gehost.

* Azure Log Integration geïnstalleerd. Installeren:

   a. Gebruik Extern bureaublad om verbinding te maken met het systeem dat wordt vermeld in stap 2.   
   b. Kopieer het Azure Log Integration-installatie programma naar het systeem. c. Start het installatie programma en accepteer de licentie voorwaarden voor micro soft-software.

* Als u telemetrie-informatie gaat geven, moet u het selectie vakje ingeschakeld laten. Als u liever geen gebruiks gegevens naar micro soft verzendt, schakelt u het selectie vakje uit.

   Zie [Azure log Integration with Azure Diagnostics Logging and Windows Event forwarding](azure-log-integration-get-started.md)(Engelstalig) voor meer informatie over Azure log Integration en hoe u deze kunt installeren.

* De meest recente Power shell-versie.

   Als Windows Server 2016 op uw computer is geïnstalleerd, hebt u ten minste Power shell 5,0. Als u een andere versie van Windows Server gebruikt, hebt u mogelijk een eerdere versie van Power shell geïnstalleerd. U kunt de versie controleren door in ```get-host``` te voeren in een Power shell-venster. Als Power shell 5,0 niet is geïnstalleerd, kunt u [het downloaden](https://www.microsoft.com/download/details.aspx?id=50395).

   Nadat u ten minste Power shell 5,0 hebt, kunt u door gaan met de installatie van de meest recente versie door de instructies in [install Azure PowerShell](/powershell/azure/install-az-ps)te volgen.


## <a name="create-supporting-infrastructure-elements"></a>Ondersteunende infrastructuur elementen maken

1. Open een Power shell-venster met verhoogde bevoegdheden en ga naar **C:\Program Files\Microsoft Azure log Integration**.
1. Importeer de AzLog-cmdlets door het script LoadAzLogModule. ps1 uit te voeren. Voer de `.\LoadAzLogModule.ps1` opdracht in. (Let op de ". \" in die opdracht.) Deze lijst ziet er ongeveer zo uit:</br>

   ![Lijst met geladen modules](./media/azure-log-integration-keyvault-eventhub/loaded-modules.png)

1. Voer de `Connect-AzAccount` opdracht in. Voer in het venster aanmelden de referentie gegevens in voor het abonnement dat u wilt gebruiken voor deze zelf studie.

   >[!NOTE]
   >Als dit de eerste keer is dat u zich aanmeldt bij Azure vanaf deze computer, wordt er een bericht weer gegeven over het toestaan van micro soft om Power shell-gebruiks gegevens te verzamelen. U wordt aangeraden deze gegevens verzameling in te scha kelen omdat deze wordt gebruikt om Azure PowerShell te verbeteren.

1. Na een geslaagde verificatie bent u aangemeld. Noteer de abonnements-ID en abonnements naam, want u hebt deze nodig om latere stappen te volt ooien.

1. Variabelen maken om waarden op te slaan die later zullen worden gebruikt. Voer de volgende Power shell-regels in. Mogelijk moet u de waarden aanpassen zodat deze overeenkomen met uw omgeving.
    - ```$subscriptionName = 'Visual Studio Ultimate with MSDN'```(De naam van uw abonnement kan afwijken. U kunt deze weer geven als onderdeel van de uitvoer van de vorige opdracht.)
    - ```$location = 'West US'```(Deze variabele wordt gebruikt om de locatie op te geven waar resources moeten worden gemaakt. U kunt deze variabele wijzigen naar een wille keurige locatie van uw keuze.)
    - ```$random = Get-Random```
    - ```$name = 'azlogtest' + $random```(De naam kan wille keurig zijn, maar mag alleen kleine letters en cijfers bevatten.)
    - ```$storageName = $name```(Deze variabele wordt gebruikt voor de naam van het opslag account.)
    - ```$rgname = $name```(Deze variabele wordt gebruikt voor de naam van de resource groep.)
    - ```$eventHubNameSpaceName = $name```(Dit is de naam van de Event Hub naam ruimte.)
1. Geef het abonnement op waarmee u wilt werken:
    
    ```Select-AzSubscription -SubscriptionName $subscriptionName```
1. Een resourcegroep maken:
    
    ```$rg = New-AzResourceGroup -Name $rgname -Location $location```
    
   Als u op `$rg` dit punt typt, ziet u uitvoer die vergelijkbaar is met deze scherm afbeelding:

   ![Uitvoer na het maken van een resource groep](./media/azure-log-integration-keyvault-eventhub/create-rg.png)
1. Maak een opslag account dat wordt gebruikt om status informatie bij te houden:
    
    ```$storage = New-AzStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
1. Maak de Event Hub naam ruimte. Dit is vereist om een Event Hub te maken.
    
    ```$eventHubNameSpace = New-AzEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
1. Haal de regel-ID op die wordt gebruikt met de Insights-provider:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey'```
1. Haal alle mogelijke Azure-locaties op en voeg de namen toe aan een variabele die in een latere stap kan worden gebruikt:
    
    a. ```$locationObjects = Get-AzLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Als u op `$locations` dit punt typt, ziet u de locatie namen zonder de aanvullende gegevens die worden geretourneerd door Get-AzLocation.
1. Een Azure Resource Manager-logboek profiel maken: 
    
    ```Add-AzLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Zie [overzicht van het Azure-activiteiten logboek](../../azure-monitor/platform/activity-logs-overview.md)voor meer informatie over het Azure-logboek profiel.

> [!NOTE]
> Er wordt mogelijk een fout bericht weer gegeven wanneer u probeert een logboek profiel te maken. U kunt vervolgens de documentatie bekijken voor Get-AzLogProfile en Remove-AzLogProfile. Als u Get-AzLogProfile uitvoert, ziet u informatie over het logboek profiel. U kunt het bestaande logboek profiel verwijderen door de ```Remove-AzLogProfile -name 'Log Profile Name'``` opdracht in te voeren.
>
>![Fout in het Resource Manager-profiel](./media/azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

1. De sleutel kluis maken:

   ```$kv = New-AzKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location```

1. Logboek registratie configureren voor de sleutel kluis:

   ```Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true```

## <a name="generate-log-activity"></a>Logboek activiteit genereren

Er moeten aanvragen naar Key Vault worden verzonden om de logboek activiteit te genereren. Acties zoals het genereren van sleutels, het opslaan van geheimen of het lezen van geheimen van Key Vault, maken logboek vermeldingen.

1. De huidige opslag sleutels weer geven:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Genereer een nieuwe **Key2**:
    
   ```New-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
1. De sleutels opnieuw weer geven en zien dat **Key2** een andere waarde bevat:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Stel een geheim in en lees dit om aanvullende logboek vermeldingen te genereren:
    
   a. ```Set-AzKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Geretourneerd geheim](./media/azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Azure Log Integration configureren

Nu u alle vereiste elementen hebt geconfigureerd om Key Vault logboek registratie bij een Event Hub, moet u Azure Log Integration configureren:

1. ```$storage = Get-AzStorageAccount -ResourceGroupName $rgname -Name $storagename```
1. ```$eventHubKey = Get-AzEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
1. ```$storagekeys = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
1. ```$storagekey = $storagekeys[0].Value```

Voer de opdracht AzLog uit voor elke Event Hub:

1. ```$eventhubs = Get-AzEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
1. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Na een minuut of voor het uitvoeren van de laatste twee opdrachten ziet u dat JSON-bestanden worden gegenereerd. U kunt bevestigen dat door de Directory- **C:\users\AzLog\EventHubJson**te bewaken.

## <a name="next-steps"></a>Volgende stappen

- [Veelgestelde vragen over Azure Log Integration](azure-log-integration-faq.md)
- [Aan de slag met Azure Log Integration](azure-log-integration-get-started.md)
- [Logboeken van Azure-resources integreren in uw SIEM-systemen](azure-log-integration-overview.md)
