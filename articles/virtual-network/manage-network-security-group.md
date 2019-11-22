---
title: Een Azure-netwerk beveiligings groep maken, wijzigen of verwijderen
titlesuffix: Azure Virtual Network
description: Meer informatie over het maken, wijzigen of verwijderen van een netwerk beveiligings groep.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2018
ms.author: kumud
ms.openlocfilehash: afbe4aae0ac1296bfc4b2011069f9d81afed977f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273685"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Een netwerk beveiligings groep maken, wijzigen of verwijderen

Met beveiligings regels in netwerk beveiligings groepen kunt u het type netwerk verkeer filteren dat in en uit de subnetten van het virtuele netwerk en netwerk interfaces kan stromen. Als u niet bekend bent met netwerk beveiligings groepen, raadpleegt u overzicht van de [netwerk beveiligings groep](security-overview.md) voor meer informatie en voltooit u de zelf studie [netwerk verkeer filteren](tutorial-filter-network-traffic.md) om zo ervaring te krijgen met netwerk beveiligings groepen.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voer de volgende taken uit voordat u de stappen in een van de secties van dit artikel uitvoert:

- Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proef account](https://azure.microsoft.com/free).
- Als u de portal gebruikt, opent u https://portal.azure.comen meldt u zich aan met uw Azure-account.
- Als u Power shell-opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/powershell)of Power shell uitvoeren vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Voor deze zelf studie is de Azure PowerShell module versie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.
- Als u Azure-opdracht regel interface opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/bash)of door de CLI vanaf uw computer uit te voeren. Voor deze zelf studie is de Azure CLI-versie 2.0.28 of hoger vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Als u de Azure CLI lokaal uitvoert, moet u ook `az login` uitvoeren om een verbinding te maken met Azure.

Het account waarmee u zich aanmeldt of verbinding maakt met Azure met, moet worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in [machtigingen](#permissions).

## <a name="work-with-network-security-groups"></a>Met netwerkbeveiligingsgroepen werken

U kunt alle [gegevens van](#view-details-of-a-network-security-group)een netwerk beveiligings groep maken, [weer geven, bekijken](#view-all-network-security-groups), [wijzigen](#change-a-network-security-group)en [verwijderen](#delete-a-network-security-group) . U kunt ook een netwerk beveiligings groep [koppelen aan of loskoppelen](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) van een netwerk interface of subnet.

### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Er is een limiet aan het aantal netwerk beveiligings groepen dat u kunt maken per Azure-locatie en-abonnement. Zie [Netwerkenlimieten](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor meer informatie.

1. Selecteer in het menu Azure Portal of op de **Start** pagina de optie **een resource maken**.
2. Selecteer **netwerken**en selecteer vervolgens **netwerk beveiligings groep**.
3. Voer een **naam** in voor de netwerk beveiligings groep, selecteer uw **abonnement**, maak een nieuwe **resource groep**of selecteer een bestaande resource groep, selecteer een **locatie**en selecteer vervolgens **maken**.

**Opdrachten**

- Azure CLI: [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)
- Power shell: [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Alle netwerk beveiligings groepen weer geven

In het zoekvak boven aan de portal voert u *netwerk beveiligings groepen*in. Wanneer **netwerk beveiligings groepen** worden weer gegeven in de zoek resultaten, selecteert u deze. De netwerk beveiligings groepen die in uw abonnement aanwezig zijn, worden weer gegeven.

**Opdrachten**

- Azure CLI: [AZ Network NSG List](/cli/azure/network/nsg#az-network-nsg-list)
- Power shell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>Details van een netwerk beveiligings groep weer geven

1. In het zoekvak boven aan de portal voert u *netwerk beveiligings groepen*in. Wanneer **netwerk beveiligings groepen** worden weer gegeven in de zoek resultaten, selecteert u deze.
2. Selecteer de netwerk beveiligings groep in de lijst waarvoor u details wilt weer geven. Onder **instellingen** kunt u de regels voor **binnenkomende** en **uitgaande beveiliging**bekijken, de **netwerk interfaces** en **subnetten** waaraan de netwerk beveiligings groep is gekoppeld. U kunt **Diagnostische logboeken** ook in-of uitschakelen en de **juiste beveiligings regels**weer geven. Zie [Diagnostische logboeken](virtual-network-nsg-manage-log.md) en de [juiste beveiligings regels weer geven](diagnose-network-traffic-filter-problem.md)voor meer informatie.
3. Zie de volgende artikelen voor meer informatie over de algemene Azure-instellingen die worden weer gegeven:
    *   [Activiteitenlogboek](../azure-monitor/platform/activity-logs-overview.md)
    *   [Toegangs beheer (IAM)](../role-based-access-control/overview.md)
    *   [Tags](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Vergren delingen](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Automatiserings script](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Opdrachten**

- Azure CLI: [AZ Network NSG show](/cli/azure/network/nsg#az-network-nsg-show)
- Power shell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Een netwerk beveiligings groep wijzigen

1. Voer in het zoekvak boven aan de portal *netwerk beveiligings groepen* in het zoekvak in. Wanneer **netwerk beveiligings groepen** worden weer gegeven in de zoek resultaten, selecteert u deze.
2. Selecteer de netwerk beveiligings groep die u wilt wijzigen. De meest voorkomende wijzigingen zijn het [toevoegen](#create-a-security-rule) of [verwijderen](#delete-a-security-rule) van beveiligings regels en [het koppelen of loskoppelen van een netwerk beveiligings groep van een subnet of netwerk interface](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

**Opdrachten**

- Azure CLI: [AZ Network NSG update](/cli/azure/network/nsg#az-network-nsg-update)
- Power shell: [set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Een netwerk beveiligings groep koppelen aan of loskoppelen van een subnet of netwerk interface

Zie [een netwerk beveiligings groep koppelen aan of een netwerk beveiligings groep loskoppelen van een netwerk interface](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)om een netwerk beveiligings groep aan te koppelen aan of om een netwerk beveiligings groep te loskoppelen van een netwerk interface. Zie [subnet-instellingen wijzigen](virtual-network-manage-subnet.md#change-subnet-settings)als u een netwerk beveiligings groep aan wilt koppelen of een netwerk beveiligings groep wilt loskoppelen van een subnet.

### <a name="delete-a-network-security-group"></a>Een netwerk beveiligings groep verwijderen

Als een netwerk beveiligings groep is gekoppeld aan subnetten of netwerk interfaces, kan deze niet worden verwijderd. Verbreken van een netwerk beveiligings groep van alle subnetten en netwerk interfaces voordat u deze verwijdert.

1. Voer in het zoekvak boven aan de portal *netwerk beveiligings groepen* in het zoekvak in. Wanneer **netwerk beveiligings groepen** worden weer gegeven in de zoek resultaten, selecteert u deze.
2. Selecteer de netwerk beveiligings groep die u wilt verwijderen uit de lijst.
3. Selecteer **verwijderen**en selecteer vervolgens **Ja**.

**Opdrachten**

- Azure CLI: [AZ Network NSG delete](/cli/azure/network/nsg#az-network-nsg-delete)
- Power shell: [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup)

## <a name="work-with-security-rules"></a>Werken met beveiligings regels

Een netwerk beveiligings groep bevat geen of meer beveiligings regels. U kunt alle regels maken, [weer geven](#view-all-security-rules), [Details van](#view-details-of-a-security-rule)een beveiligings regel bekijken, [wijzigen](#change-a-security-rule)en [verwijderen](#delete-a-security-rule) .

### <a name="create-a-security-rule"></a>Een beveiligings regel maken

Er geldt een limiet voor het aantal regels per netwerk beveiligings groep dat per Azure-locatie en-abonnement kan worden gemaakt. Zie [Netwerkenlimieten](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor meer informatie.

1. Voer in het zoekvak boven aan de portal *netwerk beveiligings groepen* in het zoekvak in. Wanneer **netwerk beveiligings groepen** worden weer gegeven in de zoek resultaten, selecteert u deze.
2. Selecteer de netwerk beveiligings groep in de lijst waaraan u een beveiligings regel wilt toevoegen.
3. Selecteer **binnenkomende beveiligings regels** onder **instellingen**. Er worden verschillende bestaande regels vermeld. Sommige van de regels die u mogelijk niet hebt toegevoegd. Wanneer er een netwerk beveiligings groep wordt gemaakt, worden er diverse standaard beveiligings regels in gemaakt. Zie [standaard beveiligings regels](security-overview.md#default-security-rules)voor meer informatie.  Het is niet mogelijk om standaard beveiligings regels te verwijderen, maar u kunt ze vervangen door regels met een hogere prioriteit.
4. <a name = "security-rule-settings"></a>Selecteer **+ toevoegen**.  Selecteer of voeg waarden toe voor de volgende instellingen en selecteer vervolgens **OK**:
    
    |Instelling  |Waarde  |Details  |
    |---------|---------|---------|
    |Bron     | Selecteer **een wille keurige**, **toepassings beveiligings groep**, **IP-adressen**of **servicetag voor inkomende** beveiligings regels. Als u een uitgaande beveiligings regel maakt, zijn de opties hetzelfde als de opties die worden weer gegeven voor **doel**.       | Als u **toepassings beveiligings groep**selecteert, selecteert u een of meer bestaande toepassings beveiligings groepen die zich in dezelfde regio bevinden als de netwerk interface. Meer informatie over het [maken van een toepassings beveiligings groep](#create-an-application-security-group). Als u **toepassings beveiligings groep** voor zowel de **bron** als de **bestemming**selecteert, moeten de netwerk interfaces binnen beide toepassings beveiligings groepen zich in hetzelfde virtuele netwerk bevallen. Als u **IP-adressen**selecteert, geeft u **IP-adressen/CIDR-bereiken**voor de bron op. U kunt een enkele waarde of een door komma's gescheiden lijst met meerdere waarden opgeven. Een voor beeld van meerdere waarden is 10.0.0.0/16, 192.188.1.1. Er zijn limieten voor het aantal waarden dat u kunt opgeven. Zie [Azure-limieten](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor meer informatie. Als u **service label**selecteert, selecteert u vervolgens één servicetag. Een servicetag is een vooraf gedefinieerde id voor een categorie met IP-adressen. Zie [service Tags](security-overview.md#service-tags)voor meer informatie over de beschik bare service tags en wat elke tag vertegenwoordigt. Als het door u opgegeven IP-adres is toegewezen aan een virtuele machine van Azure, moet u ervoor zorgen dat u het privé-IP-adres opgeeft, niet het open bare IP-adressen dat aan de virtuele machine is toegewezen. Beveiligings regels worden verwerkt nadat Azure het open bare IP-adres heeft omgezet in een privé-IP-adres voor binnenkomende beveiligings regels, en voordat Azure een privé-IP-adres vertaalt naar een openbaar IP-adres voor uitgaande regels. Zie [IP-adres typen](virtual-network-ip-addresses-overview-arm.md)voor meer informatie over open bare en privé-IP-adressen in Azure.        |
    |Poortbereiken van bron     | Geef één poort op, zoals 80, een bereik van poorten, zoals 1024-65535, of een door komma's gescheiden lijst met afzonderlijke poorten en/of poortbereiken, zoals 80, 1024-65535. Voer een asterisk in om verkeer toe te staan op een wille keurige poort. | De poorten en bereiken geven aan welk poort verkeer wordt toegestaan of geweigerd door de regel. Er zijn limieten voor het aantal poorten dat u kunt opgeven. Zie [Azure-limieten](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor meer informatie.  |
    |Bestemming     | Selecteer **een wille keurige**, **toepassings beveiligings groep**, **IP-adressen**of **Virtual Network** voor uitgaande beveiligings regels. Als u een binnenkomende beveiligings regel maakt, zijn de opties hetzelfde als de opties die worden weer gegeven voor de **bron**.        | Als u **toepassings beveiligings groep** selecteert, moet u vervolgens een of meer bestaande toepassings beveiligings groepen selecteren die zich in dezelfde regio bevinden als de netwerk interface. Meer informatie over het [maken van een toepassings beveiligings groep](#create-an-application-security-group). Als u **toepassings beveiligings groep**selecteert, selecteert u vervolgens een bestaande toepassings beveiligings groep die in dezelfde regio als de netwerk interface voor komt. Als u **IP-adressen**selecteert, geeft u **doel-IP-adressen/CIDR-bereiken**op. Net als **bron** **-en bron-IP-adressen/CIDR-bereiken**, kunt u één of meerdere adressen of bereiken opgeven, en er gelden limieten voor het aantal dat u kunt opgeven. Het selecteren van een **virtueel netwerk**, een servicetag, betekent dat verkeer is toegestaan voor alle IP-adressen binnen de adres ruimte van het virtuele netwerk. Als het door u opgegeven IP-adres is toegewezen aan een virtuele machine van Azure, moet u ervoor zorgen dat u het privé-IP-adres opgeeft, niet het open bare IP-adressen dat aan de virtuele machine is toegewezen. Beveiligings regels worden verwerkt nadat Azure het open bare IP-adres heeft omgezet in een privé-IP-adres voor binnenkomende beveiligings regels, en voordat Azure een privé-IP-adres vertaalt naar een openbaar IP-adres voor uitgaande regels. Zie [IP-adres typen](virtual-network-ip-addresses-overview-arm.md)voor meer informatie over open bare en privé-IP-adressen in Azure.        |
    |Poortbereiken van doel     | Geef een enkele waarde of een door komma's gescheiden lijst met waarden op. | Net als bij de bronbereiken van de **bron**, kunt u één of meerdere poorten en bereiken opgeven, en er gelden beperkingen voor het aantal dat u kunt opgeven. |
    |Protocol     | Selecteer **wille keurig**, **TCP**, **UDP** of **ICMP**.        |         |
    |Actie     | Selecteer **toestaan** of **weigeren**.        |         |
    |Prioriteit     | Voer een waarde in tussen 100-4096 die uniek is voor alle beveiligings regels in de netwerk beveiligings groep. |Regels worden in volg orde van prioriteit verwerkt. Hoe lager het getal, des te hoger de prioriteit. Het is raadzaam om een hiaat tussen prioriteits nummers te laten bij het maken van regels, zoals 100, 200, 300. Als u hiaten hebt, is het eenvoudiger om in de toekomst regels toe te voegen die u mogelijk hoger of lager dan bestaande regels moet maken.         |
    |Naam     | Een unieke naam voor de regel binnen de netwerk beveiligings groep.        |  De naam mag Maxi maal 80 tekens lang zijn. De naam moet beginnen met een letter of cijfer, eindigen met een letter, cijfer of onderstrepings teken en mag alleen letters, cijfers, onderstrepings tekens, punten of afbreek streepjes bevatten.       |
    |Beschrijving     | Een optionele beschrijving.        |         |

**Opdrachten**

- Azure CLI: [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- Power shell: [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Alle beveiligings regels weer geven

Een netwerk beveiligings groep bevat nul of meerdere regels. Zie [overzicht van netwerk beveiligings groepen](security-overview.md)voor meer informatie over de informatie die wordt weer gegeven wanneer regels worden weer gegeven.

1. In het zoekvak boven aan de portal voert u *netwerk beveiligings groepen*in. Wanneer **netwerk beveiligings groepen** worden weer gegeven in de zoek resultaten, selecteert u deze.
2. Selecteer de netwerk beveiligings groep in de lijst waarvoor u regels wilt weer geven.
3. Selecteer **binnenkomende beveiligings regels** of **uitgaande beveiligings regels** onder **instellingen**.

De lijst bevat de regels die u hebt gemaakt en de [standaard beveiligings regels](security-overview.md#default-security-rules)voor de netwerk beveiligings groep.

**Opdrachten**

- Azure CLI: [AZ Network NSG Rule List](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- Power shell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Details van een beveiligingsregel weergeven

1. In het zoekvak boven aan de portal voert u *netwerk beveiligings groepen*in. Wanneer **netwerk beveiligings groepen** worden weer gegeven in de zoek resultaten, selecteert u deze.
2. Selecteer de netwerk beveiligings groep waarvoor u de details van een beveiligings regel wilt weer geven.
3. Selecteer **binnenkomende beveiligings regels** of **uitgaande beveiligings regels** onder **instellingen**.
4. Selecteer de regel waarvan u de details wilt weer geven. Zie [beveiligings regel instellingen](#security-rule-settings)voor een gedetailleerde uitleg van alle instellingen.

**Opdrachten**

- Azure CLI: [AZ Network NSG Rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- Power shell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Een beveiligings regel wijzigen

1. Volg de stappen in [Details van een beveiligings regel weer geven](#view-details-of-a-security-rule).
2. Wijzig de instellingen naar wens en selecteer vervolgens **Opslaan**. Zie [beveiligings regel instellingen](#security-rule-settings)voor een gedetailleerde uitleg van alle instellingen.

**Opdrachten**

- Azure CLI: [AZ Network NSG Rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- Power shell: [set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Een beveiligings regel verwijderen

1. Volg de stappen in [Details van een beveiligings regel weer geven](#view-details-of-a-security-rule).
2. Selecteer **verwijderen**en selecteer vervolgens **Ja**.

**Opdrachten**

- Azure CLI: [AZ Network NSG regel delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- Power shell: [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig)

## <a name="work-with-application-security-groups"></a>Werken met toepassingsbeveiligingsgroepen

Een toepassings beveiligings groep bevat geen of meer netwerk interfaces. Zie [toepassings beveiligings groepen](security-overview.md#application-security-groups)voor meer informatie. Alle netwerk interfaces in een toepassings beveiligings groep moeten zich in hetzelfde virtuele netwerk bevinden. Zie [een netwerk interface toevoegen aan een toepassings beveiligings groep](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)voor meer informatie over het toevoegen van een netwerk interface aan een toepassings beveiligings groep.

### <a name="create-an-application-security-group"></a>Een toepassings beveiligings groep maken

1. Selecteer **+ Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Voer **Toepassingsbeveiligingsgroep** in het vak *In Marketplace zoeken* in. Wanneer **Toepassingsbeveiligingsgroep** wordt weergegeven in de zoekresultaten, selecteert u deze optie. Vervolgens selecteert u onder **Alles** opnieuw **Toepassingsbeveiligingsgroep** en selecteert u **Maken**.
3. Voer de volgende gegevens in of selecteer deze, en selecteer **Maken**:

    | Instelling        | Waarde                                                   |
    | ---            | ---                                                     |
    | Naam           | De naam moet uniek zijn binnen de resourcegroep.        |
    | Abonnement   | Selecteer uw abonnement.                               |
    | Resourcegroep | Selecteer een bestaande resource groep of maak een nieuwe. |
    | Locatie       | Selecteer een locatie                                       |

**Opdrachten**

- Azure CLI: [AZ Network ASG Create](/cli/azure/network/asg#az-network-asg-create)
- Power shell: [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Alle toepassings beveiligings groepen weer geven

1. Selecteer **alle services** in de linkerbovenhoek van de Azure Portal.
2. Voer *toepassings beveiligings groepen* in het vak **alle services filter** in en selecteer vervolgens **toepassings beveiligings groepen** wanneer deze worden weer gegeven in de zoek resultaten.

**Opdrachten**

- Azure CLI: [AZ Network ASG List](/cli/azure/network/asg#az-network-asg-list)
- Power shell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Details van een specifieke toepassings beveiligings groep weer geven

1. Selecteer **alle services** in de linkerbovenhoek van de Azure Portal.
2. Voer *toepassings beveiligings groepen* in het vak **alle services filter** in en selecteer vervolgens **toepassings beveiligings groepen** wanneer deze worden weer gegeven in de zoek resultaten.
3. Selecteer de toepassings beveiligings groep waarvan u de details wilt weer geven.

**Opdrachten**

- Azure CLI: [AZ Network ASG show](/cli/azure/network/asg#az-network-asg-show)
- Power shell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Een toepassings beveiligings groep wijzigen

1. Selecteer **alle services** in de linkerbovenhoek van de Azure Portal.
2. Voer *toepassings beveiligings groepen* in het vak **alle services filter** in en selecteer vervolgens **toepassings beveiligings groepen** wanneer deze worden weer gegeven in de zoek resultaten.
3. Selecteer de toepassings beveiligings groep waarvan u de instellingen wilt wijzigen. U kunt Tags toevoegen of verwijderen, of machtigingen toewijzen of verwijderen voor de toepassings beveiligings groep.

- Azure CLI: [AZ Network ASG update](/cli/azure/network/asg#az-network-asg-update)
- Power shell: geen Power shell-cmdlet.

### <a name="delete-an-application-security-group"></a>Een toepassings beveiligings groep verwijderen

U kunt een toepassings beveiligings groep niet verwijderen als deze netwerk interfaces bevat. Verwijder alle netwerk interfaces uit de toepassings beveiligings groep door ofwel de instellingen van de netwerk interface te wijzigen of de netwerk interfaces te verwijderen. Zie [toevoegen aan of verwijderen van een netwerk interface uit toepassings beveiligings groepen](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) of [een netwerk interface verwijderen](virtual-network-network-interface.md#delete-a-network-interface)voor meer informatie.

1. Selecteer **alle services** in de linkerbovenhoek van de Azure Portal.
2. Voer *toepassings beveiligings groepen* in het vak **alle services filter** in en selecteer vervolgens **toepassings beveiligings groepen** wanneer deze worden weer gegeven in de zoek resultaten.
3. Selecteer de toepassings beveiligings groep die u wilt verwijderen.
4. Selecteer **verwijderen**en selecteer vervolgens **Ja** om de toepassings beveiligings groep te verwijderen.

**Opdrachten**

- Azure CLI: [AZ Network ASG delete](/cli/azure/network/asg#az-network-asg-delete)
- Power shell: [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup)

## <a name="permissions"></a>Machtigingen

Als u taken wilt uitvoeren voor netwerk beveiligings groepen, beveiligings regels en toepassings beveiligings groepen, moet uw account worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste machtigingen zijn toegewezen in de volgende tabellen:

### <a name="network-security-group"></a>Netwerkbeveiligingsgroep

| Actie                                                        |   Naam                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Netwerk beveiligings groep ophalen                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Een netwerk beveiligings groep maken of bijwerken                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Netwerk beveiligings groep verwijderen                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Een netwerk beveiligings groep aan een subnet of netwerk interface koppelen 


### <a name="network-security-group-rule"></a>Regel voor netwerk beveiligings groep

| Actie                                                        |   Naam                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   Regel ophalen                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Regel maken of bijwerken                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Regel verwijderen                                                         |

### <a name="application-security-group"></a>Toepassings beveiligings groep

| Actie                                                                     | Naam                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Een IP-configuratie toevoegen aan een toepassings beveiligings groep|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Een beveiligings regel toevoegen aan een toepassings beveiligings groep    |
| Microsoft.Network/applicationSecurityGroups/read                           | Een toepassings beveiligings groep ophalen                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Een toepassings beveiligings groep maken of bijwerken           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Een toepassings beveiligings groep verwijderen                     |

## <a name="next-steps"></a>Volgende stappen

- Een netwerk-of toepassings beveiligings groep maken met behulp van [Power shell](powershell-samples.md) of [Azure cli](cli-samples.md) -voorbeeld scripts of met behulp van Azure [Resource Manager-sjablonen](template-samples.md)
- [Azure-beleid](policy-samples.md) maken en Toep assen voor virtuele netwerken
