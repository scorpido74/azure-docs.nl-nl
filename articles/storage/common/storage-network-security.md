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
ms.openlocfilehash: 00e69d9222444e3b700fca10e3f15b4b110e0c60
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241743"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Azure Storage-firewalls en virtuele netwerken configureren

Azure Storage biedt een gelaagd beveiligingsmodel. Met dit model kunt u uw opslag accounts beveiligen naar een specifieke subset van netwerken. Wanneer netwerk regels zijn geconfigureerd, hebben alleen toepassingen die gegevens aanvragen via de opgegeven set netwerken toegang tot een opslag account. U kunt de toegang tot uw opslag account beperken tot aanvragen die afkomstig zijn van opgegeven IP-adressen, IP-adresbereiken of uit een lijst met subnetten in azure Virtual Networks.

Een toepassing die toegang heeft tot een opslag account wanneer netwerk regels van kracht zijn, moet de aanvraag correct worden geautoriseerd. Autorisatie wordt ondersteund met Azure Active Directory-referenties (Azure AD) voor blobs en wacht rijen, met een geldige account toegangs sleutel of met een SAS-token.

> [!IMPORTANT]
> Als u firewall regels inschakelt voor uw opslag account, worden binnenkomende aanvragen voor gegevens standaard geblokkeerd, tenzij de aanvragen afkomstig zijn van een service die binnen een Azure Virtual Network (VNet) actief is. Aanvragen die zijn geblokkeerd omvatten die van andere Azure-services vanuit de Azure-portal van logboekregistratie en metrische gegevens over services, enzovoort.
>
> U kunt toegang verlenen tot Azure-Services die binnen een VNet worden bediend door verkeer van het subnet dat als host fungeert voor het service-exemplaar toe te staan. U kunt ook een beperkt aantal scenario's inschakelen met behulp van het mechanisme [uitzonde ringen](#exceptions) dat in de volgende sectie wordt beschreven. Als u toegang wilt krijgen tot gegevens van het opslag account via de Azure Portal, moet u zich op een computer bevindt binnen de vertrouwde grens (ofwel IP of VNet) die u hebt ingesteld.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenario's

Als u uw opslag account wilt beveiligen, moet u eerst een regel configureren om de toegang tot verkeer van alle netwerken (met inbegrip van Internet verkeer) standaard te weigeren. Vervolgens configureert u regels die toegang verlenen tot verkeer van specifieke VNets. Deze configuratie kunt u een beveiligde netwerkgrens voor uw toepassingen te bouwen. U kunt ook regels configureren voor het verlenen van toegang tot verkeer van open bare IP-adresbereiken voor het Internet, het inschakelen van verbindingen van specifieke internet-of lokale clients.

Netwerkregels worden toegepast op alle netwerkprotocollen naar Azure-opslag, met inbegrip van REST en SMB. Om toegang te krijgen tot gegevens met behulp van hulpprogram ma's zoals de Azure Portal, Storage Explorer en AZCopy, moeten expliciete netwerk regels worden geconfigureerd.

U kunt netwerkregels toepassen op bestaande opslagaccounts of bij het maken van nieuwe storage-accounts.

Zodra het netwerkregels worden toegepast, zijn ze afgedwongen voor alle aanvragen. SAS-tokens die toegang tot een specifiek IP-adres verlenen dienen om de toegang van de token houder te beperken, maar niet verlenen aan nieuwe toegang buiten de geconfigureerde regels.

VM-schijfverkeer (inclusief koppelen en ontkoppelen van bewerkingen en schijf-i/o-) wordt niet beïnvloed door netwerkregels. REST-toegang tot pagina-blobs is beveiligd door netwerkregels.

Klassieke opslagaccounts bieden geen ondersteuning voor firewalls en virtuele netwerken.

U kunt niet-beheerde schijven in opslagaccounts met netwerkregels toegepast op back-up en herstel-VM's met het maken van een uitzondering. Dit proces wordt beschreven in de [uitzonderingen](#exceptions) sectie van dit artikel. Firewall-uitzonderingen zijn niet van toepassing met beheerde schijven als ze al worden beheerd door Azure.

## <a name="change-the-default-network-access-rule"></a>Wijzigen van de standaardtoegangsregel voor netwerk

Standaard accepteren opslagaccounts verbindingen van clients op een netwerk. Als u wilt beperken de toegang tot geselecteerde netwerken, moet u eerst de standaardactie wijzigen.

> [!WARNING]
> Wijzigingen aanbrengen in netwerkregels kan invloed hebben op uw toepassingen kunnen verbinding maken met Azure Storage. Als u de standaard netwerk regel instelt op **weigeren** , wordt alle toegang tot de gegevens geblokkeerd, tenzij specifieke netwerk regels die toegang **verlenen** , ook worden toegepast. Zorg ervoor dat toegang verlenen tot een toegestane netwerken met behulp van netwerkregels voordat u de standaardregel voor het weigeren van toegang wijzigen.

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

U kunt opslag accounts configureren om alleen toegang toe te staan vanaf specifieke subnetten. De toegestane subnetten kunnen deel uitmaken van een VNet in hetzelfde abonnement of in een ander abonnement, met inbegrip van abonnementen die horen bij een andere Azure Active Directory Tenant.

Schakel een [Service-eindpunt](/azure/virtual-network/virtual-network-service-endpoints-overview) voor Azure Storage binnen het VNet. Het service-eind punt routeert verkeer van het VNet via een optimaal pad naar de Azure Storage-service. De identiteiten van het subnet en het virtuele netwerk worden ook met elke aanvraag verzonden. Beheerders kunnen vervolgens netwerk regels configureren voor het opslag account waarmee aanvragen kunnen worden ontvangen van specifieke subnetten in een VNet. Clients krijgen toegang via deze netwerkregels moet blijven om te voldoen aan de autorisatievereisten van het storage-account voor toegang tot de gegevens.

Elk opslagaccount biedt ondersteuning voor maximaal 100 regels voor virtueel netwerk, die kunnen worden gecombineerd met [IP-netwerkregels](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Beschikbaar virtueel netwerk regio 's

Service-eindpunten werken in het algemeen tussen virtuele netwerken en service-exemplaren in dezelfde Azure-regio. Wanneer u service-eindpunten met Azure Storage, dit bereik vergroot om op te nemen de [gekoppelde regio](/azure/best-practices-availability-paired-regions). Service-eindpunten kunt bedrijfscontinuïteit tijdens een regionale failover en toegang tot exemplaren van de alleen-lezen geografisch redundante opslag (RA-GRS). Netwerkregels die toegang vanuit een virtueel netwerk naar een opslagaccount verlenen ook verlenen toegang tot een RA-GRS-exemplaar.

Bij het plannen voor herstel na noodgevallen tijdens een regionale storing, moet u van tevoren de vnet's maken in de gekoppelde regio. Service-eindpunten inschakelen voor Azure Storage, aan de netwerkregels van het toegang verlenen vanuit deze alternatieve virtuele netwerken. Deze regels toepassen op uw geografisch redundante opslag-accounts.

> [!NOTE]
> Service-eindpunten zijn niet van toepassing op het verkeer buiten de regio van het virtuele netwerk en het paar aangewezen regio. U kunt alleen regels voor het verlenen van toegang van virtuele netwerken voor storage-accounts in de primaire regio van een opslagaccount of in de aangewezen gekoppelde regio toepassen.

### <a name="required-permissions"></a>Vereiste machtigingen

Als u wilt een virtueel netwerk-regel van toepassing op een storage-account, moet de gebruiker de juiste machtigingen voor de subnetten die worden toegevoegd. De machtiging die nodig is *Service koppelen aan een Subnet* en is opgenomen in de *Inzender voor Opslagaccounts* ingebouwde rol. Het kan ook worden toegevoegd aan de aangepaste roldefinities.

Het opslag account en de virtuele netwerken waartoe toegang wordt verleend, kunnen zich in verschillende abonnementen bevinden, met inbegrip van abonnementen die deel uitmaken van een andere Azure AD-Tenant.

> [!NOTE]
> Het configureren van regels die toegang verlenen tot subnetten in virtuele netwerken die deel uitmaken van een andere Azure Active Directory Tenant, worden momenteel alleen ondersteund via Power shell, CLI en REST Api's. Deze regels kunnen niet worden geconfigureerd via de Azure Portal, maar kunnen wel worden weer gegeven in de portal.

### <a name="managing-virtual-network-rules"></a>Virtual network-regels beheren

U kunt virtuele-netwerkregels voor storage-accounts via Azure portal, PowerShell of CLIv2 beheren.

#### <a name="azure-portal"></a>Azure Portal

1. Ga naar het opslagaccount dat u wilt beveiligen.

1. Klik op het instellingenmenu met de naam **Firewalls en virtuele netwerken**.

1. Controleer of u hebt geselecteerd zodat toegang vanaf **geselecteerde netwerken**.

1. Toegang verlenen tot een virtueel netwerk met een nieuwe regel voor het netwerk, onder **virtuele netwerken**, klikt u op **bestaand virtueel netwerk toevoegen**, selecteer **virtuele netwerken** en **Subnetten** opties en klik vervolgens op **toevoegen**. Voor het maken van een nieuw virtueel netwerk en het toegang geven, klikt u op **nieuw virtueel netwerk toevoegen**. Geef de informatie die nodig zijn voor de nieuw virtueel netwerk maken en klik vervolgens op **maken**.

    > [!NOTE]
    > Als een service-eindpunt voor Azure Storage is niet eerder hebt geconfigureerd voor de geselecteerde virtuele netwerk en subnetten, kunt u deze configureren als onderdeel van deze bewerking.
    >
    > Momenteel worden alleen virtuele netwerken die deel uitmaken van dezelfde Azure Active Directory Tenant weer gegeven voor selectie tijdens het maken van de regel. Als u toegang wilt verlenen tot een subnet in een virtueel netwerk dat deel uitmaakt van een andere Tenant, gebruikt u Power shell, CLI of REST Api's.

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

    > [!TIP]
    > Als u een netwerk regel wilt toevoegen voor een subnet in een VNet dat deel uitmaakt van een andere Azure AD-Tenant, gebruikt u een volledig gekwalificeerde **VirtualNetworkResourceId** -para meter in de notatie "/Subscriptions/Subscription-id/resourceGroups/resourceGroup-name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".

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

    > [!TIP]
    > Als u een regel wilt toevoegen voor een subnet in een VNet dat deel uitmaakt van een andere Azure AD-Tenant, gebruikt u een volledig gekwalificeerde subnet-ID in de notatie '/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name '.
    > 
    > U kunt de para meter **abonnement** gebruiken om de subnet-id op te halen voor een VNet dat deel uitmaakt van een andere Azure AD-Tenant.

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

Als u bepaalde services wilt gebruiken, moet u een subset van vertrouwde micro soft-Services toestaan om de netwerk regels over te slaan. Deze services wordt sterke verificatie gebruikt voor toegang tot het opslagaccount.

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
