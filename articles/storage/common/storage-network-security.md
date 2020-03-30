---
title: Azure Storage-firewalls en virtuele netwerken configureren | Microsoft Documenten
description: Een gelaagde netwerkbeveiliging configureren voor uw opslagaccount.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 77ad8579f31ce900a67e2ba3ddc53a5b034b6d42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454666"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Azure Storage-firewalls en virtuele netwerken configureren

Azure Storage biedt een gelaagd beveiligingsmodel. Met dit model u het toegangsniveau tot uw opslagaccounts beveiligen en beheren dat uw toepassingen en bedrijfsomgevingen vereisen, op basis van het type en de subset van gebruikte netwerken. Wanneer netwerkregels zijn geconfigureerd, hebben alleen toepassingen die gegevens aanvragen via de opgegeven set netwerken toegang tot een opslagaccount. U de toegang tot uw opslagaccount beperken tot aanvragen die afkomstig zijn van opgegeven IP-adressen, IP-bereiken of uit een lijst met subnetten in een Virtual Network (Azure Network).

Opslagaccounts hebben een openbaar eindpunt dat toegankelijk is via het internet. U ook [privéeindpunten voor uw opslagaccount](storage-private-endpoints.md)maken, dat een privé-IP-adres van uw VNet toewijst aan het opslagaccount en al het verkeer tussen uw VNet en het opslagaccount via een privékoppeling beveiligt. De Azure-opslagfirewall biedt toegangsbeheertoegang voor het openbare eindpunt van uw opslagaccount. U de firewall ook gebruiken om alle toegang via het openbare eindpunt te blokkeren bij het gebruik van privéeindpunten. Met uw configuratie van uw opslagfirewall kunnen ook bepaalde vertrouwde Azure-platformservices veilig toegang krijgen tot het opslagaccount.

Een toepassing die toegang heeft tot een opslagaccount wanneer netwerkregels van kracht zijn, vereist nog steeds de juiste autorisatie voor de aanvraag. Autorisatie wordt ondersteund met Azure Active Directory-referenties (Azure AD) voor blobs en wachtrijen, met een geldige accounttoegangssleutel of met een SAS-token.

> [!IMPORTANT]
> Het inschakelen van firewallregels voor uw opslagaccount blokkeert standaard binnenkomende aanvragen voor gegevens, tenzij de aanvragen afkomstig zijn van een service die actief is binnen een Azure Virtual Network (VNet) of van toegestane openbare IP-adressen. Aanvragen die worden geblokkeerd, zijn ook die van andere Azure-services, van de Azure-portal, van logboekregistratie- en metrische services, enzovoort.
>
> U toegang verlenen tot Azure-services die vanuit een VNet werken door verkeer toe te staan vanuit het subnet dat de service-instantie host. U ook een beperkt aantal scenario's inschakelen via het onderstaande [exceptions-mechanisme.](#exceptions) Als u via het Azure-portaal toegang wilt krijgen tot gegevens van het opslagaccount, moet u zich op een machine bevinden binnen de vertrouwde grens (IP of VNet) die u hebt ingesteld.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenario's

Als u uw opslagaccount wilt beveiligen, moet u eerst standaard een regel configureren om toegang tot verkeer van alle netwerken (inclusief internetverkeer) op het openbare eindpunt te weigeren. Vervolgens moet u regels configureren die toegang verlenen tot verkeer van specifieke VNets. U ook regels configureren om toegang te verlenen tot verkeer uit bepaalde IP-adresbereiken van openbare internet, waardoor verbindingen van specifieke internet- of on-premises clients kunnen worden ingeschakeld. Met deze configuratie u een beveiligde netwerkgrens voor uw toepassingen bouwen.

U firewallregels combineren die toegang bieden vanuit specifieke virtuele netwerken en vanaf openbare IP-adresbereiken op hetzelfde opslagaccount. Opslagfirewallregels kunnen worden toegepast op bestaande opslagaccounts of bij het maken van nieuwe opslagaccounts.

Opslagfirewallregels zijn van toepassing op het openbare eindpunt van een opslagaccount. U hebt geen firewalltoegangsregels nodig om verkeer toe te staan voor privéeindpunten van een opslagaccount. Het proces van goedkeuring van de creatie van een privéeindpunt verleent impliciete toegang tot verkeer van het subnet dat het privéeindpunt host.

Netwerkregels worden afgedwongen op alle netwerkprotocollen voor Azure-opslag, inclusief REST en SMB. Om toegang te krijgen tot gegevens met behulp van hulpprogramma's zoals de Azure-portal, Storage Explorer en AZCopy, moeten expliciete netwerkregels worden geconfigureerd.

Zodra netwerkregels zijn toegepast, worden ze afgedwongen voor alle aanvragen. SAS-tokens die toegang verlenen tot een specifiek IP-adres dienen om de toegang van de tokenhouder te beperken, maar verlenen geen nieuwe toegang buiten geconfigureerde netwerkregels.

Schijfverkeer voor virtuele machines (inclusief het monteren en ontkoppelen van bewerkingen en schijf-IO) wordt niet beïnvloed door netwerkregels. REST-toegang tot paginablobs wordt beschermd door netwerkregels.

Klassieke opslagaccounts ondersteunen geen firewalls en virtuele netwerken.

U onbeheerde schijven gebruiken in opslagaccounts met netwerkregels die worden toegepast op het back-upwerk en het herstellen van VM's door een uitzondering te maken. Dit proces wordt gedocumenteerd in het gedeelte [Uitzonderingen](#exceptions) van dit artikel. Firewalluitzonderingen zijn niet van toepassing op beheerde schijven, omdat ze al worden beheerd door Azure.

## <a name="change-the-default-network-access-rule"></a>Standaardregel voor netwerktoegang wijzigen

Standaard accepteren opslagaccounts verbindingen van clients in elk netwerk. Als u de toegang wilt beperken tot bepaalde netwerken, moet u eerst de standaardactie wijzigen.

> [!WARNING]
> Als u wijzigingen aanbrengt in de netwerkregels, kan dit van invloed zijn op de mogelijkheid van uw toepassingen om verbinding te maken met Azure Storage. Als u de standaardnetwerkregel instelt om alle toegang tot de gegevens te **weigeren,** wordt alle toegang tot de gegevens geblokkeerd, tenzij ook specifieke netwerkregels worden toegepast die toegang **verlenen.** Zorg ervoor dat u alleen toegang verleent tot netwerken die gebruikmaken van netwerkregels voordat u de standaardregel wijzigt om de toegang te weigeren.

### <a name="managing-default-network-access-rules"></a>Standaardregels voor netwerktoegang beheren

U standaardregels voor netwerktoegang voor opslagaccounts beheren via de Azure-portal, PowerShell of CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Ga naar het opslagaccount dat u wilt beveiligen.

1. Klik op het instellingenmenu genaamd **Firewalls en virtuele netwerken.**

1. Als u de toegang standaard wilt weigeren, kiest u ervoor om toegang toe te staan **vanuit geselecteerde netwerken**. Als u verkeer van alle netwerken wilt toestaan, verleent u toegang vanaf **Alle netwerken**.

1. Klik op **Opslaan** om uw wijzigingen toe te passen.

#### <a name="powershell"></a>PowerShell

1. Installeer de [Azure PowerShell](/powershell/azure/install-Az-ps) en [meld u aan](/powershell/azure/authenticate-azureps).

1. Geef de status van de standaardregel voor het opslagaccount weer.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Stel de standaardregel in om netwerktoegang standaard te weigeren.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Stel de standaardregel in om standaard netwerktoegang toe te staan.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2 CLIv2

1. Installeer de [Azure CLI](/cli/azure/install-azure-cli) en meld [u aan](/cli/azure/authenticate-azure-cli).

1. Geef de status van de standaardregel voor het opslagaccount weer.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Stel de standaardregel in om netwerktoegang standaard te weigeren.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Stel de standaardregel in om standaard netwerktoegang toe te staan.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Toegang verlenen vanuit een virtueel netwerk

U opslagaccounts zo configureren dat ze alleen toegang hebben vanuit specifieke subnetten. De toegestane subnetten kunnen behoren tot een VNet in hetzelfde abonnement, of die in een ander abonnement, inclusief abonnementen die behoren tot een andere Azure Active Directory-tenant.

Een [serviceeindpunt](/azure/virtual-network/virtual-network-service-endpoints-overview) voor Azure Storage inschakelen binnen het VNet. Het eindpunt van de service leidt het verkeer van het VNet door een optimaal pad naar de Azure Storage-service. De identiteiten van het subnet en het virtuele netwerk worden ook bij elk verzoek verzonden. Beheerders kunnen vervolgens netwerkregels configureren voor het opslagaccount waarmee aanvragen kunnen worden ontvangen van specifieke subnetten in een VNet. Clients die toegang krijgen via deze netwerkregels moeten blijven voldoen aan de autorisatievereisten van het opslagaccount om toegang te krijgen tot de gegevens.

Elk opslagaccount ondersteunt maximaal 100 virtuele netwerkregels, die kunnen worden gecombineerd met [IP-netwerkregels.](#grant-access-from-an-internet-ip-range)

### <a name="available-virtual-network-regions"></a>Beschikbare virtuele netwerkregio's

Over het algemeen werken serviceeindpunten tussen virtuele netwerken en service-exemplaren in dezelfde Azure-regio. Wanneer u serviceeindpunten gebruikt met Azure Storage, wordt dit bereik uitgebreid met het [gekoppelde gebied.](/azure/best-practices-availability-paired-regions) Serviceeindpunten bieden continuïteit tijdens een regionale failover en toegang tot alleen-lezen georedundante opslag (RA-GRS) exemplaren. Netwerkregels die toegang verlenen vanuit een virtueel netwerk tot een opslagaccount verlenen, verlenen ook toegang tot een RA-GRS-instantie.

Wanneer u een herstel van een ramp plant tijdens een regionale storing, moet u de VNets in de gekoppelde regio van tevoren maken. Schakel serviceeindpunten in voor Azure Storage, waarbij netwerkregels toegang verlenen vanuit deze alternatieve virtuele netwerken. Pas deze regels vervolgens toe op uw georedundante opslagaccounts.

> [!NOTE]
> Serviceeindpunten zijn niet van toepassing op verkeer buiten de regio van het virtuele netwerk en het aangewezen regiopaar. U alleen netwerkregels toepassen die toegang verlenen van virtuele netwerken tot opslagaccounts in het primaire gebied van een opslagaccount of in het aangewezen gekoppelde gebied.

### <a name="required-permissions"></a>Vereiste machtigingen

Als u een virtuele netwerkregel wilt toepassen op een opslagaccount, moet de gebruiker over de juiste machtigingen beschikken voor de subnetten die worden toegevoegd. De benodigde machtiging is *Join Service naar een subnet* en is opgenomen in de ingebouwde rol *Opslagaccountbijdrager.* Het kan ook worden toegevoegd aan aangepaste roldefinities.

Opslagaccount en de virtuele netwerken die toegang krijgen, kunnen in verschillende abonnementen zijn, waaronder abonnementen die deel uitmaken van een andere Azure AD-tenant.

> [!NOTE]
> Configuratie van regels die toegang verlenen tot subnetten in virtuele netwerken die deel uitmaken van een andere Azure Active Directory-tenant, wordt momenteel alleen ondersteund via Powershell-, CLI- en REST-API's. Dergelijke regels kunnen niet worden geconfigureerd via de Azure-portal, hoewel ze kunnen worden bekeken in de portal.

### <a name="managing-virtual-network-rules"></a>Virtuele netwerkregels beheren

U virtuele netwerkregels voor opslagaccounts beheren via de Azure-portal, PowerShell of CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Ga naar het opslagaccount dat u wilt beveiligen.

1. Klik op het instellingenmenu genaamd **Firewalls en virtuele netwerken.**

1. Controleer of u hebt geselecteerd om toegang toe te staan **vanuit geselecteerde netwerken**.

1. Als u toegang wilt verlenen tot een virtueel netwerk met een nieuwe netwerkregel, klikt u onder **Virtuele netwerken**op Bestaand virtueel **netwerk toevoegen,** selecteert u **Virtuele netwerken** en **subnettenopties** en klikt u vervolgens op **Toevoegen**. Als u een nieuw virtueel netwerk wilt maken en toegang wilt verlenen, klikt u op **Nieuw virtueel netwerk toevoegen**. Geef de informatie die nodig is om het nieuwe virtuele netwerk te maken en klik op **Maken**.

    > [!NOTE]
    > Als een serviceeindpunt voor Azure Storage niet eerder is geconfigureerd voor het geselecteerde virtuele netwerk en subnetten, u het configureren als onderdeel van deze bewerking.
    >
    > Momenteel worden alleen virtuele netwerken die tot dezelfde Azure Active Directory-tenant behoren, weergegeven voor selectie tijdens het maken van regels. Als u toegang wilt verlenen tot een subnet in een virtueel netwerk van een andere tenant, gebruikt u Powershell-, CLI- of REST-API's.

1. Als u een virtuele netwerk- of subnetregel wilt verwijderen, klikt u op **...** om het contextmenu voor het virtuele netwerk of subnet te openen en klikt u op **Verwijderen**.

1. Klik op **Opslaan** om uw wijzigingen toe te passen.

#### <a name="powershell"></a>PowerShell

1. Installeer de [Azure PowerShell](/powershell/azure/install-Az-ps) en [meld u aan](/powershell/azure/authenticate-azureps).

1. Lijst virtuele netwerkregels.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Serviceeindpunt inschakelen voor Azure Storage op een bestaand virtueel netwerk en subnet.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Voeg een netwerkregel toe voor een virtueel netwerk en subnet.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Als u een netwerkregel wilt toevoegen voor een subnet in een VNet dat behoort tot een andere Azure AD-tenant, gebruikt u een volledig gekwalificeerde **parameter VirtualNetworkResourceId** in de vorm "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnet-name".

1. Een netwerkregel voor een virtueel netwerk en subnet verwijderen.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Zorg ervoor dat [u de standaardregel instelt](#change-the-default-network-access-rule) op **weigeren**of netwerkregels hebben geen effect.

#### <a name="cliv2"></a>CLIv2 CLIv2

1. Installeer de [Azure CLI](/cli/azure/install-azure-cli) en meld [u aan](/cli/azure/authenticate-azure-cli).

1. Lijst virtuele netwerkregels.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Serviceeindpunt inschakelen voor Azure Storage op een bestaand virtueel netwerk en subnet.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Voeg een netwerkregel toe voor een virtueel netwerk en subnet.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Als u een regel voor een subnet wilt toevoegen in een VNet dat behoort tot een\<andere Azure\>AD-tenant, gebruikt u een volledig gekwalificeerde subnet-id in de vorm\<"/abonnementen/\>subscription-ID /resourceGroups/\<resourceGroup-Name\>/providers/Microsoft.Network/virtualNetworks/\<vNet-name\>/subnets/ subnetnaam".
    >
    > U de **parameter abonnement** gebruiken om de subnet-id op te halen voor een VNet van een andere Azure AD-tenant.

1. Een netwerkregel voor een virtueel netwerk en subnet verwijderen.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Zorg ervoor dat [u de standaardregel instelt](#change-the-default-network-access-rule) op **weigeren**of netwerkregels hebben geen effect.

## <a name="grant-access-from-an-internet-ip-range"></a>Toegang verlenen vanuit een IP-internetbereik

U opslagaccounts configureren om toegang te krijgen vanuit specifieke ip-adresbereiken van openbare internet. Deze configuratie verleent toegang tot specifieke internetdiensten en on-premises netwerken en blokkeert algemeen internetverkeer.

Geef toegestane internetadresbereiken op met [CIDR-notatie](https://tools.ietf.org/html/rfc4632) in het formulier *16.17.18.0/24* of als afzonderlijke IP-adressen zoals *16.17.18.19*.

   > [!NOTE]
   > Kleine adresbereiken met de voorvoegselgroottes "/31" of "/32" worden niet ondersteund. Deze bereiken moeten worden geconfigureerd met behulp van afzonderlijke IP-adresregels.

IP-netwerkregels zijn alleen toegestaan voor **openbare ip-adressen op internet.** IP-adresbereiken die zijn gereserveerd voor particuliere netwerken (zoals gedefinieerd in [RFC 1918)](https://tools.ietf.org/html/rfc1918#section-3)zijn niet toegestaan in IP-regels. Particuliere netwerken omvatten adressen die beginnen met _10.*_, _172.16.*_ - _172.31.*_ en _192.168.*_.

   > [!NOTE]
   > IP-netwerkregels hebben geen effect op aanvragen die afkomstig zijn uit hetzelfde Azure-gebied als het opslagaccount. Gebruik [virtuele netwerkregels](#grant-access-from-a-virtual-network) om aanvragen in dezelfde regio toe te staan.

  > [!NOTE]
  > Services die zijn geïmplementeerd in dezelfde regio als het opslagaccount, gebruiken privé-Azure-IP-adressen voor communicatie. U de toegang tot specifieke Azure-services dus niet beperken op basis van hun openbare inkomende IP-adresbereik.

Alleen IPV4-adressen worden ondersteund voor het configureren van opslagfirewallregels.

Elk opslagaccount ondersteunt maximaal 100 IP-netwerkregels.

### <a name="configuring-access-from-on-premises-networks"></a>Toegang configureren vanaf on-premises netwerken

Als u toegang wilt verlenen vanaf uw on-premises netwerken tot uw opslagaccount met een IP-netwerkregel, moet u de IP-adressen identificeren die door uw netwerk worden gebruikt. Neem contact op met uw netwerkbeheerder voor hulp.

Als u [ExpressRoute](/azure/expressroute/expressroute-introduction) gebruikt vanuit uw on-premises netwerk voor openbare peering of Microsoft-peering, moet u de NAT IP-adressen opgeven die worden gebruikt. Voor openbare peering gebruikt elk ExpressRoute-circuit standaard twee NAT IP-adressen. Deze worden toegepast op Azure-serviceverkeer wanneer het verkeer het Microsoft Azure-backbone-netwerk binnenkomt. Voor Microsoft-peering worden de gebruikte NAT-IP-adressen opgegeven door de klant of worden ze door de serviceprovider verstrekt. Voor toegang tot uw serviceresources moet u deze openbare IP-adressen toestaan in de instelling voor IP-firewall voor de resource. Wanneer u op zoek bent naar de IP-adressen van uw ExpressRoute-circuit voor openbare peering, opent u [een ondersteuningsticket met ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via de Azure-portal. Meer informatie over [NAT voor openbare peering en Microsoft-peering met ExpressRoute.](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>IP-netwerkregels beheren

U IP-netwerkregels voor opslagaccounts beheren via de Azure-portal, PowerShell of CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Ga naar het opslagaccount dat u wilt beveiligen.

1. Klik op het instellingenmenu genaamd **Firewalls en virtuele netwerken.**

1. Controleer of u hebt geselecteerd om toegang toe te staan **vanuit geselecteerde netwerken**.

1. Als u toegang wilt verlenen tot een IP-internetbereik, voert u het IP-adres- of adresbereik (in CIDR-indeling) in onder > **Firewall-adresbereik**. **Firewall**

1. Als u een IP-netwerkregel wilt verwijderen, klikt u op het pictogram prullenbak naast het adresbereik.

1. Klik op **Opslaan** om uw wijzigingen toe te passen.

#### <a name="powershell"></a>PowerShell

1. Installeer de [Azure PowerShell](/powershell/azure/install-Az-ps) en [meld u aan](/powershell/azure/authenticate-azureps).

1. Ip-netwerkregels weergeven.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Voeg een netwerkregel toe voor een individueel IP-adres.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Voeg een netwerkregel toe voor een IP-adresbereik.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Een netwerkregel voor een individueel IP-adres verwijderen.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Een netwerkregel voor een IP-adresbereik verwijderen.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Zorg ervoor dat [u de standaardregel instelt](#change-the-default-network-access-rule) op **weigeren**of netwerkregels hebben geen effect.

#### <a name="cliv2"></a>CLIv2 CLIv2

1. Installeer de [Azure CLI](/cli/azure/install-azure-cli) en meld [u aan](/cli/azure/authenticate-azure-cli).

1. Ip-netwerkregels weergeven.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Voeg een netwerkregel toe voor een individueel IP-adres.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Voeg een netwerkregel toe voor een IP-adresbereik.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Een netwerkregel voor een individueel IP-adres verwijderen.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Een netwerkregel voor een IP-adresbereik verwijderen.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Zorg ervoor dat [u de standaardregel instelt](#change-the-default-network-access-rule) op **weigeren**of netwerkregels hebben geen effect.

## <a name="exceptions"></a>Uitzonderingen

Netwerkregels helpen bij het creëren van een veilige omgeving voor verbindingen tussen uw toepassingen en uw gegevens voor de meeste scenario's. Sommige toepassingen zijn echter afhankelijk van Azure-services die niet uniek kunnen worden geïsoleerd via regels voor virtuele netwerken of IP-adres. Maar dergelijke diensten moeten worden verleend aan opslag om volledige toepassingsfunctionaliteit mogelijk te maken. In dergelijke situaties u de instelling ***Vertrouwde Microsoft-services toestaan...*** gebruiken om dergelijke services toegang te geven tot uw gegevens, logboeken of analyses.

### <a name="trusted-microsoft-services"></a>Vertrouwde Microsoft-services

Sommige Microsoft-services werken vanuit netwerken die niet kunnen worden opgenomen in uw netwerkregels. U een subset van dergelijke vertrouwde Microsoft-services toegang verlenen tot het opslagaccount, terwijl de netwerkregels voor andere apps worden gehandhaafd. Deze vertrouwde services gebruiken vervolgens sterke verificatie om veilig verbinding te maken met uw opslagaccount. We hebben twee manieren van vertrouwde toegang voor Microsoft-services ingeschakeld.

- Bronnen van sommige services, **wanneer ze zijn geregistreerd in uw abonnement,** hebben toegang tot uw opslagaccount in **hetzelfde abonnement** voor bepaalde bewerkingen, zoals het schrijven van logboeken of back-ups.
- Resources van sommige services kunnen expliciete toegang krijgen tot uw opslagaccount door **een RBAC-rol toe** te wijzen aan de door het systeem toegewezen beheerde identiteit.


Wanneer u de instelling **Vertrouwde Microsoft-services toestaan...** inschakelt, krijgen resources van de volgende services die zijn geregistreerd in hetzelfde abonnement als uw opslagaccount toegang voor een beperkte reeks bewerkingen zoals beschreven:

| Service                  | Naam resourceprovider     | Bewerkingen toegestaan                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | Back-ups en herstelvan onbeheerde schijven uitvoeren in virtuele IAAS-machines. (niet vereist voor beheerde schijven). [Meer informatie](/azure/backup/backup-introduction-to-azure-backup). |
| Azure Data Box           | Microsoft.DataBox          | Hiermee u gegevens importeren naar Azure gebruiken met Gegevensvak. [Meer informatie](/azure/databox/data-box-overview). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Aangepaste installatie van afbeeldingen en artefacten. [Meer informatie](/azure/devtest-lab/devtest-lab-overview). |
| Azure Event Grid         | Microsoft.EventGrid        | Schakel het publiceren van Blob Storage-gebeurtenissen in en laat gebeurtenisraster publiceren naar opslagwachtrijen. Meer informatie over [blob-opslaggebeurtenissen](/azure/event-grid/event-sources) en [publicatie in wachtrijen](/azure/event-grid/event-handlers). |
| Azure Event Hubs         | Microsoft.EventHub         | Archiveer gegevens met Event Hubs Capture. [Meer weten?](/azure/event-hubs/event-hubs-capture-overview) |
| Azure File Sync          | Microsoft.StorageSync      | Hiermee u uw on-prem-bestandsserver transformeren naar een cache voor Azure File-shares. Synchronisatie met meerdere plaatsen mogelijk, snel herstel van rampen en back-ups aan de cloudzijde. [Meer informatie](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Inrichten van de initiële inhoud van het standaardbestandssysteem voor een nieuw HDInsight-cluster. [Meer informatie](/azure/hdinsight/hdinsight-hadoop-use-blob-storage). |
| Azure Exporteren importeren      | Microsoft.ImportExport     | Hiermee u gegevens importeren naar Azure en gegevens exporteren vanuit Azure met behulp van de import/exportservice. [Meer informatie](/azure/storage/common/storage-import-export-service).  |
| Azure Monitor            | Microsoft.Insights         | Hiermee u bewakingsgegevens schrijven naar een beveiligde opslagaccount, inclusief diagnostische logboeken voor bronnen, aanmeldings- en controlelogboeken van Azure Active Directory en Microsoft Intune-logboeken. [Meer informatie](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). |
| Azure-netwerken         | Microsoft.Network          | Netwerkverkeerlogboeken opslaan en analyseren. [Meer informatie](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Replicatie inschakelen voor het herstel van Azure IaaS-virtuele machines wanneer u cache-, bron- of doelopslagaccounts gebruikt met firewall.  [Meer informatie](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication). |

Met de instelling **Vertrouwde Microsoft-services toestaan...** kan een bepaalde instantie van de onderstaande services ook toegang krijgen tot het opslagaccount, als u expliciet [een RBAC-rol toewijst](storage-auth-aad.md#assign-rbac-roles-for-access-rights) aan de [door het systeem toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) voor die broninstantie. In dit geval komt de toegangsruimte voor de instantie overeen met de RBAC-rol die is toegewezen aan de beheerde identiteit.

| Service                        | Naam resourceprovider                 | Doel            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure Cognitive Search         | Microsoft.Search/searchServices        | Hiermee kunnen cognitive search-services toegang krijgen tot opslagaccounts voor indexering, verwerking en query's. |
| Azure Container Registry Tasks | Microsoft.ContainerRegistry/registers | ACR Tasks heeft toegang tot opslagaccounts bij het maken van containerafbeeldingen. |
| Azure Data Factory             | Microsoft.DataFactory/fabrieken        | Hiermee u toegang krijgen tot opslagaccounts via de ADF-runtime. |
| Azure Data Share               | Microsoft.DataShare/accounts           | Hiermee u toegang krijgen tot opslagaccounts via Data Share. |
| Azure Logic Apps               | Microsoft.Logic/werkstromen              | Hiermee kunnen logische apps toegang krijgen tot opslagaccounts. [Meer informatie](/azure/logic-apps/create-managed-service-identity#authenticate-access-with-managed-identity). |
| Azure Machine Learning-service | Microsoft.MachineLearningServices      | Geautoriseerde Azure Machine Learning-werkruimten schrijven experimentuitvoer, -modellen en -logboeken naar Blob-opslag en lezen de gegevens. [Meer informatie](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace). | 
| Azure SQL Data Warehouse       | Microsoft.Sql                          | Hiermee u gegevens uit specifieke SQL Database-exemplaren importeren en exporteren met PolyBase. [Meer informatie](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview). |
| Azure Stream Analytics         | Microsoft.StreamAnalytics             | Hiermee kunnen gegevens van een streamingtaak worden geschreven naar Blob-opslag. Deze functie is momenteel beschikbaar als preview-product. [Meer informatie](/azure/stream-analytics/blob-output-managed-identity). |
| Azure Synapse Analytics        | Microsoft.Synapse/werkruimten          | Hiermee u toegang tot gegevens in Azure Storage van Synapse Analytics gebruiken. |


### <a name="storage-analytics-data-access"></a>Toegang tot gegevens voor opslaganalyse

In sommige gevallen is toegang tot diagnostische logboeken en statistieken vereist van buiten de netwerkgrens. Wanneer u vertrouwde services toegang tot het opslagaccount configureert, u leestoegang toestaan voor de logboekbestanden, metrische gegevenstabellen of beide. [Meer informatie over het werken met storage analytics.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Uitzonderingen beheren

U uitzonderingen op netwerkregels beheren via de Azure-portal, PowerShell of Azure CLI v2.

#### <a name="azure-portal"></a>Azure Portal

1. Ga naar het opslagaccount dat u wilt beveiligen.

1. Klik op het instellingenmenu genaamd **Firewalls en virtuele netwerken.**

1. Controleer of u hebt geselecteerd om toegang toe te staan **vanuit geselecteerde netwerken**.

1. Selecteer **onder Uitzonderingen**de uitzonderingen die u wilt toestaan.

1. Klik op **Opslaan** om uw wijzigingen toe te passen.

#### <a name="powershell"></a>PowerShell

1. Installeer de [Azure PowerShell](/powershell/azure/install-Az-ps) en [meld u aan](/powershell/azure/authenticate-azureps).

1. Geef de uitzonderingen weer voor de netwerkregels voor opslagaccount.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Configureer de uitzonderingen op de netwerkregels voor opslagaccount.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Verwijder de uitzonderingen op de netwerkregels voor opslagaccount.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Zorg ervoor dat [de standaardregel](#change-the-default-network-access-rule) wordt ingesteld om uitzonderingen **te weigeren**of uitzonderingen te verwijderen hebben geen effect.

#### <a name="cliv2"></a>CLIv2 CLIv2

1. Installeer de [Azure CLI](/cli/azure/install-azure-cli) en meld [u aan](/cli/azure/authenticate-azure-cli).

1. Geef de uitzonderingen weer voor de netwerkregels voor opslagaccount.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Configureer de uitzonderingen op de netwerkregels voor opslagaccount.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Verwijder de uitzonderingen op de netwerkregels voor opslagaccount.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Zorg ervoor dat [de standaardregel](#change-the-default-network-access-rule) wordt ingesteld om uitzonderingen **te weigeren**of uitzonderingen te verwijderen hebben geen effect.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over eindpunten van Azure Network-service in [serviceeindpunten](/azure/virtual-network/virtual-network-service-endpoints-overview).

Ga dieper in op azure storage-beveiliging in [azure storage-beveiligingshandleiding.](../blobs/security-recommendations.md)
