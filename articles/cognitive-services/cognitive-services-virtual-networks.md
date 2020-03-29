---
title: Virtuele netwerken
titleSuffix: Azure Cognitive Services
description: Een gelaagde netwerkbeveiliging configureren voor uw cognitive services-bronnen.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 0988c8154c63bb408493edf3243078e625c80d53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371219"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Virtuele netwerken van Azure Cognitive Services configureren

Azure Cognitive Services biedt een gelaagd beveiligingsmodel. Met dit model u uw Cognitive Services-accounts beveiligen voor een specifieke subset van netwerken. Wanneer netwerkregels zijn geconfigureerd, hebben alleen toepassingen die gegevens aanvragen via de opgegeven set netwerken toegang tot het account. U de toegang tot uw bronnen beperken met het filteren van aanvragen. Alleen aanvragen toestaan die afkomstig zijn van opgegeven IP-adressen, IP-bereiken of uit een lijst met subnetten in [Azure Virtual Networks](../virtual-network/virtual-networks-overview.md). Als je geïnteresseerd bent in dit aanbod, moet je [preview-toegang aanvragen.](https://aka.ms/cog-svc-vnet-signup)

Een toepassing die toegang heeft tot een Cognitive Services-bron wanneer netwerkregels van kracht zijn, vereist autorisatie. Autorisatie wordt ondersteund met [Azure Active Directory-referenties](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) of met een geldige API-sleutel.

> [!IMPORTANT]
> Als u firewallregels inschakelt voor uw Cognitive Services-account, worden binnenkomende aanvragen voor gegevens standaard geblokkeerd. Om aanvragen door te laten gaan, moet aan een van de volgende voorwaarden worden voldaan:
> * De aanvraag moet afkomstig zijn van een service die werkt binnen een Azure Virtual Network (VNet) op de toegestane subnetlijst van het doel-Cognitive Services-account. Het eindpunt in aanvragen die afkomstig zijn van VNet moet worden ingesteld als het [aangepaste subdomein](cognitive-services-custom-subdomains.md) van uw Cognitive Services-account.
> * Of het verzoek moet afkomstig zijn van een toegestane lijst met IP-adressen.
>
> Aanvragen die worden geblokkeerd, zijn ook die van andere Azure-services, van de Azure-portal, van logboekregistratie- en metrische services, enzovoort.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenario's

Als u uw cognitive services-bron wilt beveiligen, moet u eerst een regel configureren om standaard toegang tot verkeer van alle netwerken (inclusief internetverkeer) te weigeren. Vervolgens moet u regels configureren die toegang verlenen tot verkeer van specifieke VNets. Met deze configuratie u een beveiligde netwerkgrens voor uw toepassingen bouwen. U ook regels configureren om toegang te verlenen tot verkeer uit bepaalde IP-adresbereiken van openbare internet, waardoor verbindingen van specifieke internet- of on-premises clients kunnen worden ingeschakeld.

Netwerkregels worden afgedwongen op alle netwerkprotocollen voor Azure Cognitive Services, waaronder REST en WebSocket. Om toegang te krijgen tot gegevens met behulp van hulpprogramma's zoals de Azure-testconsoles, moeten expliciete netwerkregels worden geconfigureerd. U netwerkregels toepassen op bestaande bronnen voor Cognitive Services of wanneer u nieuwe bronnen voor cognitieve services maakt. Zodra netwerkregels zijn toegepast, worden ze afgedwongen voor alle aanvragen.

## <a name="supported-regions-and-service-offerings"></a>Ondersteunde regio's en serviceaanbiedingen

Virtuele netwerkondersteuning voor cognitieve diensten die hieronder worden vermeld, is beperkt tot de *regio's Central US EUAP*, *South Central US*, East *US* *, West US 2,* *North Europe*, South *Africa North*, *West-Europe*, *Central India*, Australia *East*, *West US*en US *Gov Virginia* Azure. Als het serviceaanbod hier niet wordt vermeld, biedt het geen ondersteuning voor virtuele netwerken.

> [!div class="checklist"]
> * [Anomaly Detector](./anomaly-detector/index.yml)
> * [Computer Visie](./computer-vision/index.yml)
> * [Content Moderator](./content-moderator/index.yml)
> * [Custom Vision](./custom-vision-service/index.yml)
> * [Face](./face/index.yml)
> * [Form Recognizer](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Personalizer](./personalizer/index.yml)
> * [Tekstanalyse](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)

Virtuele netwerkondersteuning voor cognitieve services die hieronder worden vermeld, is beperkt tot de *regio's Central US EUAP*, *South Central US*, East *US,* *West US 2*, *Global*en US *Gov Virginia* Azure.
> [!div class="checklist"]
> * [Translator Text](./translator/index.yml)

## <a name="service-tags"></a>Servicetags
Naast het ondersteunen van eindpunten voor virtuele netwerkservices voor de bovenstaande services, ondersteunt Cognitive Services ook een servicetag voor uitgaande netwerkregelsconfiguratie. De volgende services zijn opgenomen in de CognitiveServicesManagement servicetag.
> [!div class="checklist"]
> * [Anomaly Detector](./anomaly-detector/index.yml)
> * [Computer Visie](./computer-vision/index.yml)
> * [Content Moderator](./content-moderator/index.yml)
> * [Custom Vision](./custom-vision-service/index.yml)
> * [Face](./face/index.yml)
> * [Form Recognizer](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Personalizer](./personalizer/index.yml)
> * [Tekstanalyse](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)
> * [Translator Text](./translator/index.yml)
> * [Spraakservice](./speech-service/index.yml)

## <a name="change-the-default-network-access-rule"></a>Standaardregel voor netwerktoegang wijzigen

Standaard accepteren cognitive services-bronnen verbindingen van clients in elk netwerk. Als u de toegang wilt beperken tot bepaalde netwerken, moet u eerst de standaardactie wijzigen.

> [!WARNING]
> Het aanbrengen van wijzigingen in netwerkregels kan van invloed zijn op de mogelijkheid van uw toepassingen om verbinding te maken met Azure Cognitive Services. Als u de standaardnetwerkregel instelt om alle toegang tot de gegevens te **weigeren,** wordt alle toegang tot de gegevens geblokkeerd, tenzij ook specifieke netwerkregels worden toegepast die toegang **verlenen.** Zorg ervoor dat u alleen toegang verleent tot netwerken die gebruikmaken van netwerkregels voordat u de standaardregel wijzigt om de toegang te weigeren. Als u IP-adressen voor uw on-premises netwerk toestaat, moet u alle mogelijke uitgaande openbare IP-adressen toevoegen van uw on-premises netwerk.

### <a name="managing-default-network-access-rules"></a>Standaardregels voor netwerktoegang beheren

U standaardregels voor netwerktoegang voor resources voor Cognitive Services beheren via de Azure-portal, PowerShell of Azure CLI.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Ga naar de resource Cognitive Services die u wilt beveiligen.

1. Selecteer het menu **RESOURCEBEHEER** met de naam **Virtueel netwerk**.

   ![Optie virtueel netwerk](media/vnet/virtual-network-blade.png)

1. Als u de toegang standaard wilt weigeren, kiest u ervoor om toegang toe te staan **vanuit geselecteerde netwerken**. Met de instelling **Geselecteerde netwerken** alleen, zonder begeleiding van geconfigureerde **virtuele netwerken** of **adresbereiken** , wordt alle toegang effectief geweigerd. Wanneer alle toegang wordt geweigerd, zijn aanvragen die de bron Cognitive Services proberen te gebruiken, niet toegestaan. De Azure-portal, Azure PowerShell of Azure CLI kan nog steeds worden gebruikt om de bron Voor Cognitieve Services te configureren.
1. Als u verkeer van alle netwerken wilt toestaan, verleent u toegang vanaf **Alle netwerken**.

   ![Virtuele netwerken ontkennen](media/vnet/virtual-network-deny.png)

1. Klik op **Opslaan** om uw wijzigingen toe te passen.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Installeer de [Azure PowerShell](/powershell/azure/install-az-ps) en [meld u aan](/powershell/azure/authenticate-azureps)of selecteer **Proberen**.

1. Geef de status weer van de standaardregel voor de resource Cognitive Services.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Stel de standaardregel in om netwerktoegang standaard te weigeren.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Stel de standaardregel in om standaard netwerktoegang toe te staan.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Installeer de [Azure CLI](/cli/azure/install-azure-cli) en meld [u aan](/cli/azure/authenticate-azure-cli)of selecteer Probeer **het**.

1. Geef de status weer van de standaardregel voor de resource Cognitive Services.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Stel de standaardregel in om netwerktoegang standaard te weigeren.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Stel de standaardregel in om standaard netwerktoegang toe te staan.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>Toegang verlenen vanuit een virtueel netwerk

U resources voor Cognitive Services configureren om alleen toegang te verlenen vanuit specifieke subnetten. De toegestane subnetten kunnen behoren tot een VNet in hetzelfde abonnement of in een ander abonnement, inclusief abonnementen die behoren tot een andere Azure Active Directory-tenant.

Schakel een [serviceeindpunt](../virtual-network/virtual-network-service-endpoints-overview.md) in voor Azure Cognitive Services binnen het VNet. Het eindpunt van de service leidt het verkeer van het VNet door een optimaal pad naar de Azure Cognitive Services-service. De identiteiten van het subnet en het virtuele netwerk worden ook bij elk verzoek verzonden. Beheerders kunnen vervolgens netwerkregels configureren voor de resource Cognitive Services waarmee aanvragen kunnen worden ontvangen van specifieke subnetten in een VNet. Clients die toegang hebben gekregen via deze netwerkregels, moeten blijven voldoen aan de autorisatievereisten van de Cognitive Services-bron om toegang te krijgen tot de gegevens.

Elke Cognitive Services-bron ondersteunt maximaal 100 virtuele netwerkregels, die kunnen worden gecombineerd met [IP-netwerkregels.](#grant-access-from-an-internet-ip-range)

### <a name="required-permissions"></a>Vereiste machtigingen

Als u een virtuele netwerkregel wilt toepassen op een resource voor Cognitive Services, moet de gebruiker over de juiste machtigingen beschikken voor de subnetten die worden toegevoegd. De vereiste machtiging is de *standaardrol inzender* of de rol *Medewerker Cognitieve services.* Vereiste machtigingen kunnen ook worden toegevoegd aan aangepaste roldefinities.

Cognitive Services-bron en de virtuele netwerken die toegang krijgen, kunnen in verschillende abonnementen zijn, waaronder abonnementen die deel uitmaken van een andere Azure AD-tenant.

> [!NOTE]
> Configuratie van regels die toegang verlenen tot subnetten in virtuele netwerken die deel uitmaken van een andere Azure Active Directory-tenant, wordt momenteel alleen ondersteund via Powershell-, CLI- en REST-API's. Dergelijke regels kunnen niet worden geconfigureerd via de Azure-portal, hoewel ze kunnen worden bekeken in de portal.

### <a name="managing-virtual-network-rules"></a>Virtuele netwerkregels beheren

U virtuele netwerkregels voor bronnen voor Cognitive Services beheren via de Azure-portal, PowerShell of Azure CLI.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Ga naar de resource Cognitive Services die u wilt beveiligen.

1. Selecteer het menu **RESOURCEBEHEER** met de naam **Virtueel netwerk**.

1. Controleer of u hebt geselecteerd om toegang toe te staan **vanuit geselecteerde netwerken**.

1. Als u toegang wilt verlenen tot een virtueel netwerk met een bestaande netwerkregel, selecteert u onder **Virtuele netwerken**de optie Bestaand virtueel **netwerk toevoegen**.

   ![Bestaande vNet toevoegen](media/vnet/virtual-network-add-existing.png)

1. Selecteer de opties **Virtuele netwerken** en **subnetten** en selecteer **Inschakelen**.

   ![Bestaande vNet-details toevoegen](media/vnet/virtual-network-add-existing-details.png)

1. Als u een nieuw virtueel netwerk wilt maken en toegang wilt verlenen, selecteert u **Nieuw virtueel netwerk toevoegen**.

   ![Nieuwe vNet toevoegen](media/vnet/virtual-network-add-new.png)

1. Geef de informatie die nodig is om het nieuwe virtuele netwerk te maken en selecteer **Vervolgens Maken**.

   ![VNet maken](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Als een serviceeindpunt voor Azure Cognitive Services niet eerder is geconfigureerd voor de geselecteerde virtuele netwerk- en subnetten, u het configureren als onderdeel van deze bewerking.
    >
    > Momenteel worden alleen virtuele netwerken die tot dezelfde Azure Active Directory-tenant behoren, weergegeven voor selectie tijdens het maken van regels. Als u toegang wilt verlenen tot een subnet in een virtueel netwerk van een andere tenant, gebruikt u Powershell-, CLI- of REST-API's.

1. Als u een virtuele netwerk- of subnetregel wilt verwijderen, selecteert u **...** om het contextmenu voor het virtuele netwerk of subnet te openen en selecteert **u Verwijderen**.

   ![VNet verwijderen](media/vnet/virtual-network-remove.png)

1. Klik op **Opslaan** om uw wijzigingen toe te passen.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Installeer de [Azure PowerShell](/powershell/azure/install-az-ps) en [meld u aan](/powershell/azure/authenticate-azureps)of selecteer **Proberen**.

1. Lijst virtuele netwerkregels.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Serviceeindpunt inschakelen voor Azure Cognitive Services op een bestaand virtueel netwerk en subnet.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. Voeg een netwerkregel toe voor een virtueel netwerk en subnet.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > Als u een netwerkregel wilt toevoegen voor een subnet in een VNet dat behoort tot een andere Azure AD-tenant, gebruikt u een volledig gekwalificeerde **parameter VirtualNetworkResourceId** in de vorm "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnet-name".

1. Een netwerkregel voor een virtueel netwerk en subnet verwijderen.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Installeer de [Azure CLI](/cli/azure/install-azure-cli) en meld [u aan](/cli/azure/authenticate-azure-cli)of selecteer Probeer **het**.

1. Lijst virtuele netwerkregels.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Serviceeindpunt inschakelen voor Azure Cognitive Services op een bestaand virtueel netwerk en subnet.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. Voeg een netwerkregel toe voor een virtueel netwerk en subnet.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > Als u een regel wilt toevoegen voor een subnet in een VNet dat behoort tot een andere Azure AD-tenant, gebruikt u een volledig gekwalificeerde subnet-id in de vorm "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnetnaam".
    > 
    > U de **parameter abonnement** gebruiken om de subnet-id op te halen voor een VNet van een andere Azure AD-tenant.

1. Een netwerkregel voor een virtueel netwerk en subnet verwijderen.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```
***

> [!IMPORTANT]
> Zorg ervoor dat [u de standaardregel instelt](#change-the-default-network-access-rule) op **weigeren**of netwerkregels hebben geen effect.

## <a name="grant-access-from-an-internet-ip-range"></a>Toegang verlenen vanuit een IP-internetbereik

U resources voor Cognitive Services configureren om toegang te krijgen vanuit specifieke IP-adresbereiken van openbare internet. Deze configuratie verleent toegang tot specifieke diensten en on-premises netwerken, waardoor het algemene internetverkeer effectief wordt geblokkeerd.

Geef toegestane internetadresbereiken op met [CIDR-notatie](https://tools.ietf.org/html/rfc4632) in het formulier `16.17.18.0/24` of als afzonderlijke IP-adressen zoals `16.17.18.19`.

   > [!Tip]
   > Kleine adresbereiken met de voorvoegselgroottes "/31" of "/32" worden niet ondersteund. Deze bereiken moeten worden geconfigureerd met behulp van afzonderlijke IP-adresregels.

IP-netwerkregels zijn alleen toegestaan voor **openbare ip-adressen op internet.** IP-adresbereiken die zijn gereserveerd voor particuliere netwerken (zoals gedefinieerd in [RFC 1918)](https://tools.ietf.org/html/rfc1918#section-3)zijn niet toegestaan in IP-regels. Particuliere netwerken omvatten adressen `10.*`die `172.16.*`  -  `172.31.*`beginnen `192.168.*`met , en .

   > [!NOTE]
   > IP-netwerkregels hebben geen effect op aanvragen die afkomstig zijn uit dezelfde Azure-regio als de bron Cognitive Services. Gebruik [virtuele netwerkregels](#grant-access-from-a-virtual-network) om aanvragen in dezelfde regio toe te staan.

Op dit moment worden alleen IPV4-adressen ondersteund. Elke Cognitive Services-bron ondersteunt maximaal 100 IP-netwerkregels, die kunnen worden gecombineerd met [virtuele netwerkregels.](#grant-access-from-a-virtual-network)

### <a name="configuring-access-from-on-premises-networks"></a>Toegang configureren vanaf on-premises netwerken

Als u toegang wilt verlenen vanuit uw on-premises netwerken tot uw Cognitive Services-bron met een IP-netwerkregel, moet u de IP-adressen identificeren die door uw netwerk worden gebruikt. Neem contact op met uw netwerkbeheerder voor hulp.

Als u [ExpressRoute](../expressroute/expressroute-introduction.md) on-premises gebruikt voor openbare peering of Microsoft-peering, moet u de NAT IP-adressen identificeren. Voor openbaar peering gebruikt elk ExpressRoute-circuit standaard twee NAT IP-adressen. Elk wordt toegepast op Azure-serviceverkeer wanneer het verkeer de Microsoft Azure-netwerkbackbone binnenkomt. Voor Microsoft-peering worden de NAT-IP-adressen die worden gebruikt, opgegeven of door de serviceprovider geleverd. Voor toegang tot uw serviceresources moet u deze openbare IP-adressen toestaan in de instelling voor IP-firewall voor de resource. Wanneer u op zoek bent naar de IP-adressen van uw ExpressRoute-circuit voor openbare peering, opent u [een ondersteuningsticket met ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via de Azure-portal. Meer informatie over [NAT voor openbare peering en Microsoft-peering met ExpressRoute.](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>IP-netwerkregels beheren

U IP-netwerkregels voor bronnen voor Cognitive Services beheren via de Azure-portal, PowerShell of Azure CLI.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Ga naar de resource Cognitive Services die u wilt beveiligen.

1. Selecteer het menu **RESOURCEBEHEER** met de naam **Virtueel netwerk**.

1. Controleer of u hebt geselecteerd om toegang toe te staan **vanuit geselecteerde netwerken**.

1. Als u toegang wilt verlenen tot een IP-internetbereik, voert u het IP-adres- of adresbereik (in [CIDR-indeling)](https://tools.ietf.org/html/rfc4632)in onder > **Firewall-adresbereik**. **Firewall** Alleen geldige openbare IP-adressen (niet-gereserveerde) adressen worden geaccepteerd.

   ![IP-bereik toevoegen](media/vnet/virtual-network-add-ip-range.png)

1. Als u een IP-netwerkregel <span class="docon docon-delete x-hidden-focus"></span> wilt verwijderen, selecteert u het pictogram prullenbak naast het adresbereik.

   ![IP-bereik verwijderen](media/vnet/virtual-network-delete-ip-range.png)

1. Klik op **Opslaan** om uw wijzigingen toe te passen.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Installeer de [Azure PowerShell](/powershell/azure/install-az-ps) en [meld u aan](/powershell/azure/authenticate-azureps)of selecteer **Proberen**.

1. Ip-netwerkregels weergeven.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. Voeg een netwerkregel toe voor een individueel IP-adres.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Voeg een netwerkregel toe voor een IP-adresbereik.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Een netwerkregel voor een individueel IP-adres verwijderen.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Een netwerkregel voor een IP-adresbereik verwijderen.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Installeer de [Azure CLI](/cli/azure/install-azure-cli) en meld [u aan](/cli/azure/authenticate-azure-cli)of selecteer Probeer **het**.

1. Ip-netwerkregels weergeven.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. Voeg een netwerkregel toe voor een individueel IP-adres.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Voeg een netwerkregel toe voor een IP-adresbereik.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. Een netwerkregel voor een individueel IP-adres verwijderen.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Een netwerkregel voor een IP-adresbereik verwijderen.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> Zorg ervoor dat [u de standaardregel instelt](#change-the-default-network-access-rule) op **weigeren**of netwerkregels hebben geen effect.

## <a name="next-steps"></a>Volgende stappen

* Ontdek de verschillende [Azure Cognitive Services](welcome.md)
* Meer informatie over [Azure Virtual Network Service-eindpunten](../virtual-network/virtual-network-service-endpoints-overview.md)