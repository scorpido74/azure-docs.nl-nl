---
title: Azure Storage-firewalls en virtuele netwerken configureren | Microsoft Docs
description: Gelaagde netwerkbeveiliging voor uw storage-account configureren.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 90f064ce5d6dc7ffa6b4c532ac30d9b4dd60e13f
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69981144"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Azure Storage-firewalls en virtuele netwerken configureren

Azure Storage biedt een gelaagd beveiligingsmodel. Dit model kunt u voor het beveiligen van uw storage-accounts naar een specifieke set ondersteunde netwerken. Als het netwerkregels zijn geconfigureerd, toegang alleen toepassingen waarbij gegevens uit meer dan de opgegeven set netwerken worden gevraagd een opslagaccount.

Een toepassing die toegang heeft tot een storage-account wanneer het netwerkregels zijn van kracht is juiste verificatie bij de aanvraag vereist. Autorisatie wordt ondersteund met Azure Active Directory-referenties (Azure AD) voor blobs en wacht rijen, met een geldige account toegangs sleutel of met een SAS-token.

> [!IMPORTANT]
> Firewall-regels voor uw storage-account inschakelen binnenkomende aanvragen voor gegevens standaard geblokkeerd, tenzij de aanvragen afkomstig zijn van een service die wordt uitgevoerd binnen een Azure Virtual Network (VNet). Aanvragen die zijn geblokkeerd omvatten die van andere Azure-services vanuit de Azure-portal van logboekregistratie en metrische gegevens over services, enzovoort.
>
> U kunt toegang verlenen tot Azure-services die worden uitgevoerd van binnen een VNet doordat het subnet van het service-exemplaar. Inschakelen van een beperkt aantal scenario's via de [uitzonderingen](#exceptions) mechanisme dat wordt beschreven in de volgende sectie. Voor toegang tot de Azure-portal, moet u zich op een computer in de vertrouwde grens (IP- of VNet) die u hebt ingesteld.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenario's

Opslagaccounts voor het weigeren van toegang tot verkeer via alle netwerken (met inbegrip van internetverkeer) standaard configureren. Vervolgens toegang verlenen aan verkeer van specifieke VNets. Deze configuratie kunt u een beveiligde netwerkgrens voor uw toepassingen te bouwen. U kunt ook toegang verlenen tot openbare internet IP-adresbereiken, het inschakelen van verbindingen met specifieke on-premises of internet-clients.

Netwerkregels worden toegepast op alle netwerkprotocollen naar Azure-opslag, met inbegrip van REST en SMB. Voor toegang tot de gegevens met hulpprogramma's als Azure portal, Storage Explorer en AZCopy, zijn de expliciete netwerkregels vereist.

U kunt netwerkregels toepassen op bestaande opslagaccounts of bij het maken van nieuwe storage-accounts.

Zodra het netwerkregels worden toegepast, zijn ze afgedwongen voor alle aanvragen. SAS-tokens die toegang tot een specifiek IP-adres verlenen dienen om de toegang van de token houder te beperken, maar niet verlenen aan nieuwe toegang buiten de geconfigureerde regels.

VM-schijfverkeer (inclusief koppelen en ontkoppelen van bewerkingen en schijf-i/o-) wordt niet beïnvloed door netwerkregels. REST-toegang tot pagina-blobs is beveiligd door netwerkregels.

Klassieke opslagaccounts bieden geen ondersteuning voor firewalls en virtuele netwerken.

U kunt niet-beheerde schijven in opslagaccounts met netwerkregels toegepast op back-up en herstel-VM's met het maken van een uitzondering. Dit proces wordt beschreven in de [uitzonderingen](#exceptions) sectie van dit artikel. Firewall-uitzonderingen zijn niet van toepassing met beheerde schijven als ze al worden beheerd door Azure.

## <a name="change-the-default-network-access-rule"></a>Wijzigen van de standaardtoegangsregel voor netwerk

Standaard accepteren opslagaccounts verbindingen van clients op een netwerk. Als u wilt beperken de toegang tot geselecteerde netwerken, moet u eerst de standaardactie wijzigen.

> [!WARNING]
> Wijzigingen aanbrengen in netwerkregels kan invloed hebben op uw toepassingen kunnen verbinding maken met Azure Storage. Als u de standaardregel voor het netwerk op **weigeren** blokkeert de toegang tot de gegevens, tenzij specifieke regels worden **verlenen** toegang worden ook toegepast. Zorg ervoor dat toegang verlenen tot een toegestane netwerken met behulp van netwerkregels voordat u de standaardregel voor het weigeren van toegang wijzigen.

### <a name="managing-default-network-access-rules"></a>Standaardregels voor netwerktoegang beheren

U kunt de standaardregels voor netwerktoegang voor storage-accounts via Azure portal, PowerShell of CLIv2 beheren.

#### <a name="azure-portal"></a>Azure Portal

1. Ga naar het opslagaccount dat u wilt beveiligen.

1. Klik op het instellingenmenu met de naam **Firewalls en virtuele netwerken**.

1. Kies voor het weigeren van toegang standaard, zodat toegang vanaf **geselecteerde netwerken**. Kies als u wilt dat verkeer via alle netwerken, zodat toegang vanaf **alle netwerken**.

1. Klik op **opslaan** de wijzigingen worden toegepast.

#### <a name="powershell"></a>PowerShell

1. Installeer de [Azure PowerShell](/powershell/azure/install-Az-ps) en [aanmelden](/powershell/azure/authenticate-azureps).

1. De status van de standaardregel voor de storage-account weergeven.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. De standaardregel voor het weigeren van toegang tot het netwerk standaard instellen.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. De standaardregel waarmee toegang tot het netwerk standaard instellen.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. Installeer de [Azure CLI](/cli/azure/install-azure-cli) en [aanmelden](/cli/azure/authenticate-azure-cli).

1. De status van de standaardregel voor de storage-account weergeven.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. De standaardregel voor het weigeren van toegang tot het netwerk standaard instellen.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. De standaardregel waarmee toegang tot het netwerk standaard instellen.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Toegang verlenen vanuit een virtueel netwerk

U kunt storage-accounts voor toegang alleen vanaf specifieke vnet's configureren.

Schakel een [Service-eindpunt](/azure/virtual-network/virtual-network-service-endpoints-overview) voor Azure Storage binnen het VNet. Dit eindpunt biedt verkeer een optimale route naar de Azure Storage-service. De identiteit van het virtuele netwerk en het subnet worden ook verzonden bij elke aanvraag. Beheerders kunnen vervolgens configureren voor netwerkregels voor het opslagaccount waarmee aanvragen worden ontvangen van specifieke subnetten in het VNet. Clients krijgen toegang via deze netwerkregels moet blijven om te voldoen aan de autorisatievereisten van het storage-account voor toegang tot de gegevens.

Elk opslagaccount biedt ondersteuning voor maximaal 100 regels voor virtueel netwerk, die kunnen worden gecombineerd met [IP-netwerkregels](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Beschikbaar virtueel netwerk regio 's

Service-eindpunten werken in het algemeen tussen virtuele netwerken en service-exemplaren in dezelfde Azure-regio. Wanneer u service-eindpunten met Azure Storage, dit bereik vergroot om op te nemen de [gekoppelde regio](/azure/best-practices-availability-paired-regions). Service-eindpunten kunt bedrijfscontinuïteit tijdens een regionale failover en toegang tot exemplaren van de alleen-lezen geografisch redundante opslag (RA-GRS). Netwerkregels die toegang vanuit een virtueel netwerk naar een opslagaccount verlenen ook verlenen toegang tot een RA-GRS-exemplaar.

Bij het plannen voor herstel na noodgevallen tijdens een regionale storing, moet u van tevoren de vnet's maken in de gekoppelde regio. Service-eindpunten inschakelen voor Azure Storage, aan de netwerkregels van het toegang verlenen vanuit deze alternatieve virtuele netwerken. Deze regels toepassen op uw geografisch redundante opslag-accounts.

> [!NOTE]
> Service-eindpunten zijn niet van toepassing op het verkeer buiten de regio van het virtuele netwerk en het paar aangewezen regio. U kunt alleen regels voor het verlenen van toegang van virtuele netwerken voor storage-accounts in de primaire regio van een opslagaccount of in de aangewezen gekoppelde regio toepassen.

### <a name="required-permissions"></a>Vereiste machtigingen

Als u wilt een virtueel netwerk-regel van toepassing op een storage-account, moet de gebruiker de juiste machtigingen voor de subnetten die worden toegevoegd. De machtiging die nodig is *Service koppelen aan een Subnet* en is opgenomen in de *Inzender voor Opslagaccounts* ingebouwde rol. Het kan ook worden toegevoegd aan de aangepaste roldefinities.

Storage-account en de virtuele netwerken krijgen toegang mogelijk tot verschillende abonnementen behoren, maar deze abonnementen moeten deel uitmaken van dezelfde Azure AD-tenant.

### <a name="managing-virtual-network-rules"></a>Virtual network-regels beheren

U kunt virtuele-netwerkregels voor storage-accounts via Azure portal, PowerShell of CLIv2 beheren.

#### <a name="azure-portal"></a>Azure Portal

1. Ga naar het opslagaccount dat u wilt beveiligen.

1. Klik op het instellingenmenu met de naam **Firewalls en virtuele netwerken**.

1. Controleer of u hebt geselecteerd zodat toegang vanaf **geselecteerde netwerken**.

1. Toegang verlenen tot een virtueel netwerk met een nieuwe regel voor het netwerk, onder **virtuele netwerken**, klikt u op **bestaand virtueel netwerk toevoegen**, selecteer **virtuele netwerken** en **Subnetten** opties en klik vervolgens op **toevoegen**. Voor het maken van een nieuw virtueel netwerk en het toegang geven, klikt u op **nieuw virtueel netwerk toevoegen**. Geef de informatie die nodig zijn voor de nieuw virtueel netwerk maken en klik vervolgens op **maken**.

    > [!NOTE]
    > Als een service-eindpunt voor Azure Storage is niet eerder hebt geconfigureerd voor de geselecteerde virtuele netwerk en subnetten, kunt u deze configureren als onderdeel van deze bewerking.

1. Als u wilt een virtueel netwerk of subnet regel verwijderen, klikt u op **...**  opent u het snelmenu voor het virtuele netwerk of subnet en klik op **verwijderen**.

1. Klik op **opslaan** de wijzigingen worden toegepast.

#### <a name="powershell"></a>PowerShell

1. Installeer de [Azure PowerShell](/powershell/azure/install-Az-ps) en [aanmelden](/powershell/azure/authenticate-azureps).

1. Lijst met regels voor virtueel netwerk.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Service-eindpunt voor Azure Storage inschakelen op een bestaand virtueel netwerk en subnet.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Voeg een regel voor een virtueel netwerk en subnet toe.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

1. Verwijderen van een regel voor een virtueel netwerk en subnet.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Zorg ervoor dat u [de standaardregel instellen](#change-the-default-network-access-rule) naar **weigeren**, of netwerkregels hebben geen effect.

#### <a name="cliv2"></a>CLIv2

1. Installeer de [Azure CLI](/cli/azure/install-azure-cli) en [aanmelden](/cli/azure/authenticate-azure-cli).

1. Lijst met regels voor virtueel netwerk.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Service-eindpunt voor Azure Storage inschakelen op een bestaand virtueel netwerk en subnet.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Voeg een regel voor een virtueel netwerk en subnet toe.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

1. Verwijderen van een regel voor een virtueel netwerk en subnet.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Zorg ervoor dat u [de standaardregel instellen](#change-the-default-network-access-rule) naar **weigeren**, of netwerkregels hebben geen effect.

## <a name="grant-access-from-an-internet-ip-range"></a>Toegang verlenen vanuit een IP-bereik

U kunt storage-accounts voor toegang via internet voor specifieke openbare IP-adresbereiken. Deze configuratie verleent toegang tot specifieke internet gebaseerde services en on-premises netwerken en algemene internetverkeer wordt geblokkeerd.

Geef toegestane internet-adresbereiken met behulp van [CIDR-notatie](https://tools.ietf.org/html/rfc4632) in het formulier *16.17.18.0/24* of afzonderlijke IP-adressen, zoals *16.17.18.19*.

   > [!NOTE]
   > Kleine adresbereiken met behulp van '/ 31' of '/ 32' voorvoegsel grootten worden niet ondersteund. Deze bereiken moeten worden geconfigureerd met behulp van afzonderlijke regels voor IP-adres.

IP-netwerkregels zijn alleen toegestaan voor **openbare internet** IP-adressen. IP-adresbereiken die zijn gereserveerd voor particuliere netwerken (zoals gedefinieerd in [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) zijn niet toegestaan in de IP-regels. Particuliere netwerken bevatten adressen die met beginnen _10.*_ , _172.16. *_  - _172.31. *_ , en _192.168. *_ .

   > [!NOTE]
   > IP-netwerkregels hebben geen invloed op aanvragen die afkomstig zijn van dezelfde Azure-regio als het opslagaccount. Gebruik [virtuele-netwerkregels](#grant-access-from-a-virtual-network) waarmee aanvragen voor dezelfde regio.

Alleen IPV4-adressen worden ondersteund op dit moment.

Elk opslagaccount biedt ondersteuning voor maximaal 100 IP-netwerkregels, die kunnen worden gecombineerd met [virtuele-netwerkregels](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Toegang vanaf on-premises netwerken configureren

Om toegang te verlenen vanaf uw on-premises netwerken met uw opslagaccount met een regel voor IP-netwerk, moet u het internetgerichte IP-adressen die worden gebruikt door uw netwerk te identificeren. Neem contact op met de netwerkbeheerder voor hulp.

Als u [ExpressRoute](/azure/expressroute/expressroute-introduction) gebruikt vanuit uw on-premises netwerk voor openbare peering of Microsoft-peering, moet u de NAT IP-adressen opgeven die worden gebruikt. Voor openbare peering gebruikt elk ExpressRoute-circuit standaard twee NAT IP-adressen. Deze worden toegepast op Azure-serviceverkeer wanneer het verkeer het Microsoft Azure-backbone-netwerk binnenkomt. Voor Microsoft-peering worden de NAT IP-adressen die worden gebruikt opgegeven door de klant of de serviceprovider. Voor toegang tot uw serviceresources moet u deze openbare IP-adressen toestaan in de instelling voor IP-firewall voor de resource. Wanneer u op zoek bent naar de IP-adressen van uw ExpressRoute-circuit, opent u [een ondersteuningsticket met ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via de Azure Portal. Meer informatie over [NAT voor openbare peering en Microsoft-peering met ExpressRoute.](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>IP-netwerkregels beheren

U kunt IP-netwerkregels voor storage-accounts via Azure portal, PowerShell of CLIv2 beheren.

#### <a name="azure-portal"></a>Azure Portal

1. Ga naar het opslagaccount dat u wilt beveiligen.

1. Klik op het instellingenmenu met de naam **Firewalls en virtuele netwerken**.

1. Controleer of u hebt geselecteerd zodat toegang vanaf **geselecteerde netwerken**.

1. Om te verlenen toegang tot een internet-IP-bereik, voer het IP-adres of adresbereik (in CIDR-indeling) onder **Firewall** > **adresbereik**.

1. Als u wilt verwijderen van een regel voor IP-netwerk, klikt u op het prullenbakpictogram naast het adresbereik.

1. Klik op **opslaan** de wijzigingen worden toegepast.

#### <a name="powershell"></a>PowerShell

1. Installeer de [Azure PowerShell](/powershell/azure/install-Az-ps) en [aanmelden](/powershell/azure/authenticate-azureps).

1. Lijst met regels voor IP-netwerk.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Voeg een regel voor een afzonderlijk IP-adres toe.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Voeg een regel voor een IP-adresbereik toe.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Verwijderen van een regel voor een afzonderlijk IP-adres.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Een regel voor een IP-adresbereik verwijderen.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Zorg ervoor dat u [de standaardregel instellen](#change-the-default-network-access-rule) naar **weigeren**, of netwerkregels hebben geen effect.

#### <a name="cliv2"></a>CLIv2

1. Installeer de [Azure CLI](/cli/azure/install-azure-cli) en [aanmelden](/cli/azure/authenticate-azure-cli).

1. Lijst met regels voor IP-netwerk.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Voeg een regel voor een afzonderlijk IP-adres toe.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Voeg een regel voor een IP-adresbereik toe.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Verwijderen van een regel voor een afzonderlijk IP-adres.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Een regel voor een IP-adresbereik verwijderen.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Zorg ervoor dat u [de standaardregel instellen](#change-the-default-network-access-rule) naar **weigeren**, of netwerkregels hebben geen effect.

## <a name="exceptions"></a>Uitzonderingen

Netwerkregels kunnen zorgen ervoor dat de configuratie van een beveiligd netwerk voor de meeste scenario's. Er zijn echter enkele gevallen waarbij uitzonderingen moeten worden toegekend om in te schakelen van de volledige functionaliteit. U kunt storage-accounts configureren met uitzonderingen voor vertrouwde Microsoft-services, en voor toegang tot gegevens van storage analytics.

### <a name="trusted-microsoft-services"></a>Vertrouwde Microsoft-services

Sommige Microsoft-services die interactie met de storage-accounts hebben werken van netwerken die toegang via netwerkregels kunnen niet worden toegekend.

Om te helpen bij dit type service werken zoals bedoeld, kunt u de set vertrouwde Microsoft-services om de netwerkregels over te slaan. Deze services wordt sterke verificatie gebruikt voor toegang tot het opslagaccount.

Als u inschakelt de **vertrouwde Microsoft-services toestaan...**  uitzondering, de volgende services (indien geregistreerd in uw abonnement), krijgen toegang tot het opslagaccount:

| Service                  | Resource-providernaam     | Doel                                                                                                                                                                                                                                                                                                                      |
|:-------------------------|:---------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Backup             | Microsoft.RecoveryServices | Back-ups en herstelbewerkingen van niet-beheerde schijven in virtuele machines van IAAS uitvoeren. (niet vereist voor beheerde schijven). [Meer informatie](/azure/backup/backup-introduction-to-azure-backup).                                                                                                                                                     |
| Azure Data Box           | Microsoft.DataBox          | Hiermee kunt u gegevens importeren naar Azure met behulp van Data Box. [Meer informatie](/azure/databox/data-box-overview).                                                                                                                                                                                                                              |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Aangepaste installatiekopie maken en artefact installatie. [Meer informatie](/azure/devtest-lab/devtest-lab-overview).                                                                                                                                                                                                                      |
| Azure Event Grid         | Microsoft.EventGrid        | Gebeurtenispublicatie Blob-opslag inschakelen en toestaan van Event Grid om te publiceren naar storage-wachtrijen. Meer informatie over [blob-opslaggebeurtenissen](/azure/event-grid/event-sources) en [publiceren naar wachtrijen](/azure/event-grid/event-handlers).                                                                                                     |
| Azure Event Hubs         | Microsoft.EventHub         | Gegevens met Event Hubs Capture archiveren. [Meer informatie](/azure/event-hubs/event-hubs-capture-overview).                                                                                                                                                                                                                           |
| Azure File Sync          | Microsoft.StorageSync      | Hiermee kunt u uw on-premises Bestands server transformeren naar een cache voor Azure-bestands shares. Het toestaan van synchronisatie op meerdere locaties, snelle herstel na nood gevallen en back-ups aan de Cloud zijde. [Meer informatie](../files/storage-sync-files-planning.md)                                                                                                       |
| Azure HDInsight          | Microsoft.HDInsight        | Richt de oorspronkelijke inhoud in van het standaard bestandssysteem voor een nieuw HDInsight-cluster. [Meer informatie](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/).                                                                                                                                    |
| Azure Machine Learning-service | Microsoft.MachineLearningServices | Geautoriseerde Azure Machine Learning-werk ruimten schrijven experiment-uitvoer, modellen en logboeken naar Blob Storage. [Meer informatie](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace).                                                               
| Azure Monitor            | Microsoft.Insights         | Kan het schrijven van gegevens naar een beveiligde storage-account te controleren [meer](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security).                                                                                                                                                                        |
| Azure-netwerken         | Microsoft.Network          | Store en netwerk-verkeerslogboeken te analyseren. [Meer informatie](/azure/network-watcher/network-watcher-packet-capture-overview).                                                                                                                                                                                                        |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Herstel na noodgevallen configureren door het inschakelen van replicatie voor virtuele machines van Azure IaaS. Dit is vereist als u de cache-opslagaccount firewall is ingeschakeld of bron storage-account of doelopslagaccount.  [Meer informatie](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication). |
| Azure SQL Data Warehouse | Microsoft.Sql              | Hiermee kunt u scenario's voor het importeren en exporteren van specifieke SQL-data base-instanties met poly base. [Meer informatie](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).                                                                                                                                                 |
| Azure Stream Analytics   | Microsoft.StreamAnalytics  | Hiermee staat u toe dat gegevens van een streaming-taak naar de Blob-opslag worden geschreven. Deze functie is momenteel beschikbaar als preview-versie. [Meer informatie](../../stream-analytics/blob-output-managed-identity.md).                                                                                                                                        |

### <a name="storage-analytics-data-access"></a>Toegang tot gegevens van Storage analytics

In sommige gevallen is de toegang tot diagnostische logboeken en metrische gegevens lezen vereist van buiten de grens van het netwerk. U kunt uitzonderingen voor de netwerkregels waarmee leestoegang tot de logboekbestanden voor storage-account en/of de metrische gegevens over tabellen verlenen. [Meer informatie over het werken met opslaganalyse.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Uitzonderingen beheren

U kunt uitzonderingen op netwerk via Azure portal, PowerShell of Azure CLI beheren v2.

#### <a name="azure-portal"></a>Azure Portal

1. Ga naar het opslagaccount dat u wilt beveiligen.

1. Klik op het instellingenmenu met de naam **Firewalls en virtuele netwerken**.

1. Controleer of u hebt geselecteerd zodat toegang vanaf **geselecteerde netwerken**.

1. Onder **uitzonderingen**, selecteert u de uitzonderingen die u wilt verlenen.

1. Klik op **opslaan** de wijzigingen worden toegepast.

#### <a name="powershell"></a>PowerShell

1. Installeer de [Azure PowerShell](/powershell/azure/install-Az-ps) en [aanmelden](/powershell/azure/authenticate-azureps).

1. De uitzonderingen voor de storage-account-netwerkregels worden weergegeven.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. De uitzonderingen aan de storage-account network-regels configureren.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Verwijder de uitzonderingen voor de netwerkregels voor storage-account.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Zorg ervoor dat u [de standaardregel instellen](#change-the-default-network-access-rule) naar **weigeren**, of verwijder uitzonderingen hebben geen effect.

#### <a name="cliv2"></a>CLIv2

1. Installeer de [Azure CLI](/cli/azure/install-azure-cli) en [aanmelden](/cli/azure/authenticate-azure-cli).

1. De uitzonderingen voor de storage-account-netwerkregels worden weergegeven.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. De uitzonderingen aan de storage-account network-regels configureren.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Verwijder de uitzonderingen voor de netwerkregels voor storage-account.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Zorg ervoor dat u [de standaardregel instellen](#change-the-default-network-access-rule) naar **weigeren**, of verwijder uitzonderingen hebben geen effect.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure Network service-eindpunten in [Service-eindpunten](/azure/virtual-network/virtual-network-service-endpoints-overview).

Duik dieper in Azure Storage-beveiliging in [Azure Storage-beveiligingshandleiding](storage-security-guide.md).
