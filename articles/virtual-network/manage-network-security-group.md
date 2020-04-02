---
title: Een Azure-netwerkbeveiligingsgroep maken, wijzigen of verwijderen
titlesuffix: Azure Virtual Network
description: Meer informatie over het maken, wijzigen of verwijderen van een netwerkbeveiligingsgroep.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 9ed4ce6befda76069e965501a320dc110129a024
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521034"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken, wijzigen of verwijderen

Met beveiligingsregels in netwerkbeveiligingsgroepen u het type netwerkverkeer filteren dat in en uit virtuele netwerksubnetten en netwerkinterfaces kan stromen. Zie Overzicht van [netwerkbeveiligingsgroepen](security-overview.md)voor meer informatie over netwerkbeveiligingsgroepen. Voer vervolgens de zelfstudie [Voor netwerkverkeer filteren](tutorial-filter-network-traffic.md) in om enige ervaring op te doen met netwerkbeveiligingsgroepen.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u er geen hebt, stelt u een Azure-account in met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Voltooi een van deze taken voordat u aan de rest van dit artikel begint:

- **Portalgebruikers:** Meld u aan bij de [Azure-portal](https://portal.azure.com) met uw Azure-account.

- **PowerShell-gebruikers**: Voer de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/powershell)of voer PowerShell uit vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Zoek op het tabblad Azure Cloud Shell de vervolgkeuzelijst **Voor de omgeving selecteren** en kies **PowerShell** als deze nog niet is geselecteerd.

    Als u PowerShell lokaal gebruikt, gebruikt u Azure PowerShell-moduleversie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az.Network` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Voer `Connect-AzAccount` uit om een verbinding op te zetten met Azure.

- **CLI-gebruikers (Azure Command-line interface)** uitvoeren: voer de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/bash)of voer de CLI uit vanaf uw computer. Gebruik Azure CLI-versie 2.0.28 of hoger als u de Azure CLI lokaal uitvoert. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). Voer `az login` uit om een verbinding op te zetten met Azure.

Het account waarmee u zich aanmeldt of verbinding maakt met Azure, moet worden toegewezen aan de [rol netwerkbijdrage raan](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in [Machtigingen](#permissions).

## <a name="work-with-network-security-groups"></a>Met netwerkbeveiligingsgroepen werken

U [een](#view-all-network-security-groups)netwerkbeveiligingsgroep maken, alle [weergavesgegevens van,](#view-details-of-a-network-security-group) [wijzigen](#change-a-network-security-group)en [verwijderen.](#delete-a-network-security-group) U een netwerkbeveiligingsgroep ook [koppelen of](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) scheiden van een netwerkinterface of subnet.

### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Er is een limiet aan het aantal netwerkbeveiligingsgroepen dat u maken voor elke Azure-locatie en -abonnement. Zie [Azure-abonnements- en servicelimieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)voor meer informatie.

1. Selecteer in het menu [azure portal](https://portal.azure.com) of op **de** startpagina de optie Een **bron maken**.

2. Selecteer **Netwerken**en selecteer **vervolgens de beveiligingsgroep Netwerk**.

3. Stel op de pagina **Netwerkbeveiliging maken** onder het tabblad **Basisbeginselen** waarden in voor de volgende instellingen:

    | Instelling | Actie |
    | --- | --- |
    | **Abonnement** | Kies uw abonnement. |
    | **Resourcegroep** | Kies een bestaande resourcegroep of selecteer **Nieuw maken** om een nieuwe resourcegroep te maken. |
    | **Naam** | Voer een unieke tekenreeks in een resourcegroep in. |
    | **Regio** | Kies de gewenste locatie. |

4. Selecteer **Controleren + maken**.

5. Nadat u het **doorgegeven bericht Validatie hebt gezien,** selecteert u **Maken**.

#### <a name="commands"></a>Opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Alle netwerkbeveiligingsgroepen weergeven

Ga naar de [Azure-portal](https://portal.azure.com) om uw netwerkbeveiligingsgroepen te bekijken. Zoeken naar en selecteer **netwerkbeveiligingsgroepen**. De lijst met netwerkbeveiligingsgroepen wordt weergegeven voor uw abonnement.

#### <a name="commands"></a>Opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az netwerk nsg lijst](/cli/azure/network/nsg#az-network-nsg-list) |
| PowerShell | [Get-azNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Details van een netwerkbeveiligingsgroep weergeven

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw netwerkbeveiligingsgroepen te bekijken. Zoeken naar en selecteer **netwerkbeveiligingsgroepen**.

2. Selecteer de naam van uw netwerkbeveiligingsgroep.

In de menubalk van de netwerkbeveiligingsgroep u onder **Instellingen**de **binnenkomende beveiligingsregels,** **uitgaande beveiligingsregels,** **netwerkinterfaces**en **subnetten** bekijken waaraan de netwerkbeveiligingsgroep is gekoppeld.

Onder **Controle**u **diagnostische instellingen**in- of uitschakelen. Onder **Ondersteuning + probleemoplossing**u **effectieve beveiligingsregels**bekijken. Zie [Diagnostische logboekregistratie voor een netwerkbeveiligingsgroep](virtual-network-nsg-manage-log.md) en [Diagnose van een probleem met de vm-netwerkverkeersfilter](diagnose-network-traffic-filter-problem.md)voor meer informatie.

Zie de volgende artikelen voor meer informatie over de algemene Azure-instellingen:

- [Activiteitenlogboek](../azure-monitor/platform/platform-logs-overview.md)
- [Toegangsbeheer (IAM)](../role-based-access-control/overview.md)
- [Tags](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Sloten](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automation-script](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az netwerk nsg show](/cli/azure/network/nsg#az-network-nsg-show) |
| PowerShell | [Get-azNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Een netwerkbeveiligingsgroep wijzigen

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw netwerkbeveiligingsgroepen te bekijken. Zoeken naar en selecteer **netwerkbeveiligingsgroepen**.

2. Selecteer de naam van de netwerkbeveiligingsgroep die u wilt wijzigen.

De meest voorkomende wijzigingen zijn het toevoegen van [een beveiligingsregel,](#create-a-security-rule) [het verwijderen van een regel](#delete-a-security-rule)en het koppelen of koppelen van een [netwerkbeveiligingsgroep aan of van een subnet- of netwerkinterface](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

#### <a name="commands"></a>Opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [update AZ Network NSG](/cli/azure/network/nsg#az-network-nsg-update) |
| PowerShell | [Set-azNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Een netwerkbeveiligingsgroep koppelen aan of scheiden van een subnet of netwerkinterface

Als u een netwerkbeveiligingsgroep wilt koppelen aan of een netwerkbeveiligingsgroep wilt loskoppelen van een netwerkinterface, raadpleegt u [Een netwerkbeveiligingsgroep koppelen aan of distantiëren van een netwerkbeveiligingsgroep van een netwerkinterface](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Zie [Subnetinstellingen wijzigen](virtual-network-manage-subnet.md#change-subnet-settings)als u een netwerkbeveiligingsgroep wilt koppelen aan of een netwerkbeveiligingsgroep wilt loskoppelen van een subnet.

### <a name="delete-a-network-security-group"></a>Een netwerkbeveiligingsgroep verwijderen

Als een netwerkbeveiligingsgroep is gekoppeld aan subnetten of netwerkinterfaces, kan deze niet worden verwijderd. Een netwerkbeveiligingsgroep scheiden van alle subnetten en netwerkinterfaces voordat u deze probeert te verwijderen.

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw netwerkbeveiligingsgroepen te bekijken. Zoeken naar en selecteer **netwerkbeveiligingsgroepen**.

2. Selecteer de naam van de netwerkbeveiligingsgroep die u wilt verwijderen.

3. Selecteer op de werkbalk van de netwerkbeveiligingsgroep de optie **Verwijderen**. Selecteer vervolgens **Ja** in het bevestigingsdialoogvenster.

#### <a name="commands"></a>Opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az-netwerk nsg verwijderen](/cli/azure/network/nsg#az-network-nsg-delete) |
| PowerShell | [Gegevens verwijderen-azNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Werken met beveiligingsregels

Een netwerkbeveiligingsgroep bevat nul of meer beveiligingsregels. U [een](#view-all-security-rules)beveiligingsregel maken, alle [weergavesgegevens van,](#view-details-of-a-security-rule) [wijzigen](#change-a-security-rule)en [verwijderen.](#delete-a-security-rule)

### <a name="create-a-security-rule"></a>Een beveiligingsregel maken

Er is een limiet aan het aantal regels per netwerkbeveiligingsgroep dat u maken voor elke Azure-locatie en -abonnement. Zie [Azure-abonnements- en servicelimieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)voor meer informatie.

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw netwerkbeveiligingsgroepen te bekijken. Zoeken naar en selecteer **netwerkbeveiligingsgroepen**.

2. Selecteer de naam van de netwerkbeveiligingsgroep waaraan u een beveiligingsregel wilt toevoegen.

3. Kies in de menubalk van de netwerkbeveiligingsgroep **inkomende beveiligingsregels** of **uitgaande beveiligingsregels**.

    Er worden verschillende bestaande regels weergegeven, waaronder enkele die u mogelijk niet hebt toegevoegd. Wanneer u een netwerkbeveiligingsgroep maakt, worden er verschillende standaardbeveiligingsregels in gemaakt. Zie [standaardbeveiligingsregels](security-overview.md#default-security-rules)voor meer informatie .  U de standaardbeveiligingsregels niet verwijderen, maar u ze wel overschrijven met regels met een hogere prioriteit.

4. <a name="security-rule-settings"></a>Selecteer **Toevoegen**. Selecteer of voeg waarden toe voor de volgende instellingen en selecteer **OK:**

    | Instelling | Waarde | Details |
    | ------- | ----- | ------- |
    | **Bron** | Een van:<ul><li>**Alle**</li><li>**IP-adressen**</li><li>**Servicetag** (binnenkomende beveiligingsregel) of **VirtualNetwork** (uitgaande beveiligingsregel)</li><li>**Toepassingsbeveiligingsgroep&nbsp;&nbsp;**</li></ul> | <p>Als u **IP-adressen**kiest, moet u ook **IP-adressen/CIDR-bereiken**opgeven.</p><p>Als u **Servicetag**kiest, u ook een **bronservicetag**kiezen.</p><p>Als u **de beveiligingsgroep Toepassing**kiest, moet u ook een bestaande toepassingsbeveiligingsgroep kiezen. Als u **de beveiligingsgroep Toepassing** voor zowel **Bron** als **Bestemming**kiest, moeten de netwerkinterfaces binnen beide toepassingsbeveiligingsgroepen zich in hetzelfde virtuele netwerk bevinden.</p> |
    | **Bron-IP-adressen/CIDR-bereiken** | Een door komma's afgebakende lijst met IP-adressen en CIDR-bereiken (Classless Interdomain Routing) | <p>Deze instelling wordt weergegeven als u **Bron** wijzigt in **IP-adressen**. U moet één waarde of door komma's gescheiden lijst met meerdere waarden opgeven. Een voorbeeld van `10.0.0.0/16, 192.188.1.1`meerdere waarden is . Er zijn grenzen aan het aantal waarden dat u opgeven. Zie [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)voor meer informatie.</p><p>Als het IP-adres dat u opgeeft is toegewezen aan een Azure VM, geeft u het privé-IP-adres op, niet het openbare IP-adres. Azure verwerkt beveiligingsregels nadat het openbare IP-adres is vertaald naar een privé-IP-adres voor binnenkomende beveiligingsregels, maar voordat het een privé-IP-adres vertaalt naar een openbaar IP-adres voor uitgaande regels. Zie [IP-adrestypen](virtual-network-ip-addresses-overview-arm.md)voor meer informatie over openbare en private IP-adressen in Azure.</p> |
    | **Bronservicetag** | Een servicetag uit de vervolgkeuzelijst | Deze optionele instelling wordt **Source** weergegeven als u Source-to-servicetag instelt voor een binnenkomende beveiligingsregel. **Service Tag** Een servicetag is een vooraf gedefinieerde id voor een categorie IP-adressen. Zie [Servicetags](security-overview.md#service-tags)voor meer informatie over beschikbare servicetags en wat elke tag vertegenwoordigt. |
    | **Beveiligingsgroep Brontoepassingen** | Een bestaande toepassingsbeveiligingsgroep | Deze instelling wordt weergegeven als u de beveiligingsgroep **Bron** **inApplication instelt**. Selecteer een toepassingsbeveiligingsgroep die bestaat in hetzelfde gebied als de netwerkinterface. Meer informatie over het [maken van een toepassingsbeveiligingsgroep](#create-an-application-security-group). |
    | **Poortbereiken van bron** | Een van:<ul><li>Eén poort, zoals`80`</li><li>Een reeks poorten, zoals`1024-65535`</li><li>Een door komma's gescheiden lijst met afzonderlijke poorten en/of poortbereiken, zoals`80, 1024-65535`</li><li>Een sterretje (`*`) om het verkeer op elke haven mogelijk te maken</li></ul> | Met deze instelling geeft u de poorten op waarop de regel verkeer toestaat of weigert. Er zijn grenzen aan het aantal poorten dat u opgeven. Zie [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)voor meer informatie. |
    | **Bestemming** | Een van:<ul><li>**Alle**</li><li>**IP-adressen**</li><li>**Servicetag** (uitgaande beveiligingsregel) of **VirtualNetwork** (binnenkomende beveiligingsregel)</li><li>**Toepassingsbeveiligingsgroep&nbsp;&nbsp;**</li></ul> | <p>Als u **IP-adressen**kiest, geeft u ook **IP-adressen/CIDR-bereiken op bestemming**.</p><p>Als u **VirtualNetwork**kiest, is verkeer toegestaan naar alle IP-adressen binnen de adresruimte van het virtuele netwerk. **VirtualNetwork** is een servicetag.</p><p>Als u **de beveiligingsgroep Toepassing**selecteert, moet u vervolgens een bestaande toepassingsbeveiligingsgroep selecteren. Meer informatie over het [maken van een toepassingsbeveiligingsgroep](#create-an-application-security-group).</p> |
    | **Ip-adressen van bestemming/CIDR-bereiken** | Een door komma's afgebakende lijst met IP-adressen en CIDR-bereiken | <p>Deze instelling wordt weergegeven als u **Bestemming** wijzigt in **IP-adressen**. Net als **bij ip-adressen** bron en **bron/CIDR-bereiken**u enkele of meerdere adressen of bereiken opgeven. Er zijn grenzen aan het nummer dat u opgeven. Zie [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)voor meer informatie.</p><p>Als het IP-adres dat u opgeeft is toegewezen aan een Azure VM, moet u ervoor zorgen dat u het privé-IP-adres opgeeft, niet het openbare IP-adres. Azure verwerkt beveiligingsregels nadat het openbare IP-adres is vertaald naar een privé-IP-adres voor binnenkomende beveiligingsregels, maar voordat Azure een privé-IP-adres vertaalt naar een openbaar IP-adres voor uitgaande regels. Zie [IP-adrestypen](virtual-network-ip-addresses-overview-arm.md)voor meer informatie over openbare en private IP-adressen in Azure.</p> |
    | **Doelservicetag** | Een servicetag uit de vervolgkeuzelijst | Deze optionele instelling wordt weergegeven als u **bestemming** wijzigt in **servicetag** voor een uitgaande beveiligingsregel. Een servicetag is een vooraf gedefinieerde id voor een categorie IP-adressen. Zie [Servicetags](security-overview.md#service-tags)voor meer informatie over beschikbare servicetags en wat elke tag vertegenwoordigt. |
    | **Beveiligingsgroep bestemmingstoepassingen** | Een bestaande toepassingsbeveiligingsgroep | Deze instelling wordt weergegeven als u de **beveiligingsgroep** **Bestemming** instelt op toepassing . Selecteer een toepassingsbeveiligingsgroep die bestaat in hetzelfde gebied als de netwerkinterface. Meer informatie over het [maken van een toepassingsbeveiligingsgroep](#create-an-application-security-group). |
    | **Bestemmingspoortbereiken** | Een van:<ul><li>Eén poort, zoals`80`</li><li>Een reeks poorten, zoals`1024-65535`</li><li>Een door komma's gescheiden lijst met afzonderlijke poorten en/of poortbereiken, zoals`80, 1024-65535`</li><li>Een sterretje (`*`) om het verkeer op elke haven mogelijk te maken</li></ul> | Net als bij **bronpoortbereiken**u enkele of meerdere poorten en bereiken opgeven. Er zijn grenzen aan het nummer dat u opgeven. Zie [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)voor meer informatie. |
    | **Protocol** | **Elke**, **TCP,** **UDP**of **ICMP** | U de regel beperken tot het Transmission Control Protocol (TCP), User Datagram Protocol (UDP) of Internet Control Message Protocol (ICMP). De standaardregel is dat de regel van toepassing is op alle protocollen. |
    | **Actie** | **Toestaan** of **weigeren** | Met deze instelling bepaalt u of deze regel de toegang voor de meegeleverde bron- en doelconfiguratie toestaat of weigert. |
    | **Prioriteit** | Een waarde tussen 100 en 4096 die uniek is voor alle beveiligingsregels binnen de netwerkbeveiligingsgroep | Azure verwerkt beveiligingsregels in prioriteitsvolgorde. Hoe lager het getal, hoe hoger de prioriteit. We raden u aan een kloof tussen prioriteitsnummers achter te laten wanneer u regels maakt, zoals 100, 200 en 300. Het weglaten van hiaten maakt het gemakkelijker om regels toe te voegen in de toekomst, zodat u ze een hogere of lagere prioriteit geven dan bestaande regels. |
    | **Naam** | Een unieke naam voor de regel binnen de netwerkbeveiligingsgroep | De naam kan maximaal 80 tekens zijn. Het moet beginnen met een letter of nummer, en het moet eindigen met een letter, nummer, of onderstrepen. De naam mag alleen letters, cijfers, onderdoelpunten, perioden of koppeltekens bevatten. |
    | **Beschrijving** | Een tekstbeschrijving | U optioneel een tekstbeschrijving opgeven voor de beveiligingsregel. |

#### <a name="commands"></a>Opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Alle beveiligingsregels weergeven

Een netwerkbeveiligingsgroep bevat nul of meer regels. Zie Overzicht van de [netwerkbeveiligingsgroep](security-overview.md)voor meer informatie over de informatie die wordt vermeld bij het weergeven van regels.

1. Ga naar de [Azure-portal](https://portal.azure.com) om de regels van een netwerkbeveiligingsgroep weer te geven. Zoeken naar en selecteer **netwerkbeveiligingsgroepen**.

2. Selecteer de naam van de netwerkbeveiligingsgroep waarvoor u de regels wilt weergeven.

3. Kies in de menubalk van de netwerkbeveiligingsgroep **inkomende beveiligingsregels** of **uitgaande beveiligingsregels**.

De lijst bevat alle regels die u hebt gemaakt en de [standaardbeveiligingsregels](security-overview.md#default-security-rules)van de netwerkbeveiligingsgroep.

#### <a name="commands"></a>Opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Details van een beveiligingsregel weergeven

1. Ga naar de [Azure-portal](https://portal.azure.com) om de regels van een netwerkbeveiligingsgroep weer te geven. Zoeken naar en selecteer **netwerkbeveiligingsgroepen**.

2. Selecteer de naam van de netwerkbeveiligingsgroep waarvoor u de details van een regel wilt weergeven.

3. Kies in de menubalk van de netwerkbeveiligingsgroep **inkomende beveiligingsregels** of **uitgaande beveiligingsregels**.

4. Selecteer de regel waarvoor u details wilt weergeven. Zie [Instellingen voor beveiligingsregels](#security-rule-settings)voor een uitleg van alle instellingen.

    > [!NOTE]
    > Deze procedure is alleen van toepassing op een aangepaste beveiligingsregel. Het werkt niet als u een standaardbeveiligingsregel kiest.

#### <a name="commands"></a>Opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az netwerk nsg regel show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Een beveiligingsregel wijzigen

1. Voer de stappen uit in [De details van een beveiligingsregel weergeven](#view-details-of-a-security-rule).

2. Wijzig de instellingen indien nodig en selecteer **Opslaan**. Zie [Instellingen voor beveiligingsregels](#security-rule-settings)voor een uitleg van alle instellingen.

    > [!NOTE]
    > Deze procedure is alleen van toepassing op een aangepaste beveiligingsregel. Het is niet toegestaan een standaardbeveiligingsregel te wijzigen.

#### <a name="commands"></a>Opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Een beveiligingsregel verwijderen

1. Voer de stappen uit in [De details van een beveiligingsregel weergeven](#view-details-of-a-security-rule).

2. Selecteer **Verwijderen**en selecteer vervolgens **Ja**.

    > [!NOTE]
    > Deze procedure is alleen van toepassing op een aangepaste beveiligingsregel. Het is niet toegestaan een standaardbeveiligingsregel te verwijderen.

#### <a name="commands"></a>Opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [nsg-regel AZ-netwerk verwijderen](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete) |
| PowerShell | [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Werken met toepassingsbeveiligingsgroepen

Een toepassingsbeveiligingsgroep bevat nul of meer netwerkinterfaces. Zie [toepassingsbeveiligingsgroepen](security-overview.md#application-security-groups)voor meer informatie. Alle netwerkinterfaces in een toepassingsbeveiligingsgroep moeten in hetzelfde virtuele netwerk bestaan. Zie [Een netwerkinterface toevoegen aan een toepassingsbeveiligingsgroep](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)voor meer informatie over het toevoegen van een netwerkinterface aan een toepassingsbeveiligingsgroep.

### <a name="create-an-application-security-group"></a>Een toepassingsbeveiligingsgroep maken

1. Selecteer in het menu [azure portal](https://portal.azure.com) of op **de** startpagina de optie Een **bron maken**.

2. Voer in het zoekvak *de groep Toepassingsbeveiliging*in .

3. Selecteer op de groep **Toepassingsbeveiliging** de optie **Maken**.

4. Stel op de pagina Een groep **toepassingsbeveiliging maken** onder het tabblad **Basisbeginselen** waarden in voor de volgende instellingen:

    | Instelling | Actie |
    | --- | --- |
    | **Abonnement** | Kies uw abonnement. |
    | **Resourcegroep** | Kies een bestaande resourcegroep of selecteer **Nieuw maken** om een nieuwe resourcegroep te maken. |
    | **Naam** | Voer een unieke tekenreeks in een resourcegroep in. |
    | **Regio** | Kies de gewenste locatie. |

5. Selecteer **Controleren + maken**.

6. Selecteer onder het tabblad **Controleren + maken** de optie **Maken**. **Validation passed**

#### <a name="commands"></a>Opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az netwerk asg maken](/cli/azure/network/asg#az-network-asg-create) |
| PowerShell | [Nieuwe azApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Alle toepassingsbeveiligingsgroepen weergeven

Ga naar de [Azure-portal](https://portal.azure.com) om uw toepassingsbeveiligingsgroepen te bekijken. Zoeken naar en selecteer **toepassingsbeveiligingsgroepen**. De Azure-portal geeft een lijst weer met uw toepassingsbeveiligingsgroepen.

#### <a name="commands"></a>Opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az netwerk asg lijst](/cli/azure/network/asg#az-network-asg-list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Details van een specifieke toepassingsbeveiligingsgroep weergeven

1. Ga naar de [Azure-portal](https://portal.azure.com) om een toepassingsbeveiligingsgroep weer te geven. Zoeken naar en selecteer **toepassingsbeveiligingsgroepen**.

2. Selecteer de naam van de toepassingsbeveiligingsgroep waarvan u de details wilt weergeven.

#### <a name="commands"></a>Opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az netwerk asg show](/cli/azure/network/asg#az-network-asg-show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Een toepassingsbeveiligingsgroep wijzigen

1. Ga naar de [Azure-portal](https://portal.azure.com) om een toepassingsbeveiligingsgroep weer te geven. Zoeken naar en selecteer **toepassingsbeveiligingsgroepen**.

2. Selecteer de naam van de toepassingsbeveiligingsgroep die u wilt wijzigen.

3. Selecteer **wijzigen** naast de instelling die u wilt wijzigen. U bijvoorbeeld **tags**toevoegen of verwijderen of u de **resourcegroep** of **-abonnement**wijzigen.

    > [!NOTE]
    > Je de locatie niet wijzigen.

    In de menubalk u ook **Toegangsbeheer (IAM)** selecteren. Op de pagina **Toegangsbesturingselement (IAM)** u machtigingen toewijzen of verwijderen aan de beveiligingsgroep van de toepassing.

#### <a name="commands"></a>Opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az netwerk asg update](/cli/azure/network/asg#az-network-asg-update) |
| PowerShell | Geen PowerShell-cmdlet |

### <a name="delete-an-application-security-group"></a>Een toepassingsbeveiligingsgroep verwijderen

U een beveiligingsgroep voor toepassingen niet verwijderen als deze netwerkinterfaces bevat. Als u alle netwerkinterfaces uit de toepassingsbeveiligingsgroep wilt verwijderen, wijzigt u de instellingen van de netwerkinterface of verwijdert u de netwerkinterfaces. Zie [Toevoegen aan of verwijderen uit toepassingsbeveiligingsgroepen](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) of Een [netwerkinterface verwijderen](virtual-network-network-interface.md#delete-a-network-interface)voor meer informatie.

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw toepassingsbeveiligingsgroepen te beheren. Zoeken naar en selecteer **toepassingsbeveiligingsgroepen**.

2. Selecteer de naam van de toepassingsbeveiligingsgroep die u wilt verwijderen.

3. Selecteer **Verwijderen**en selecteer **Vervolgens Ja** om de groep toepassingsbeveiliging te verwijderen.

#### <a name="commands"></a>Opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az-netwerk asg verwijderen](/cli/azure/network/asg#az-network-asg-delete) |
| PowerShell | [Beveiligingsgroep verwijderen-azApplication](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Machtigingen

Als u taken wilt uitvoeren in netwerkbeveiligingsgroepen, beveiligingsregels en toepassingsbeveiligingsgroepen, moet uw account worden toegewezen aan de rol [netwerkinzender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste machtigingen zijn toegewezen zoals vermeld in de volgende tabellen:

### <a name="network-security-group"></a>Netwerkbeveiligingsgroep

| Actie                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Netwerkbeveiligingsgroep openen                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Netwerkbeveiligingsgroep maken of bijwerken                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Netwerkbeveiligingsgroep verwijderen                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Een netwerkbeveiligingsgroep koppelen aan een subnet of netwerkinterface 

### <a name="network-security-group-rule"></a>Code voor netwerkbeveiligingsgroepen

| Actie                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   Regel krijgen                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Regel maken of bijwerken                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Regel verwijderen                                                         |

### <a name="application-security-group"></a>Toepassingsbeveiligingsgroep

| Actie                                                                     | Name                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Een IP-configuratie deelnemen aan een toepassingsbeveiligingsgroep|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Een beveiligingsregel deelnemen aan een toepassingsbeveiligingsgroep    |
| Microsoft.Network/applicationSecurityGroups/read                           | Een beveiligingsgroep voor toepassingen openen                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Een toepassingsbeveiligingsgroep maken of bijwerken           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Een toepassingsbeveiligingsgroep verwijderen                     |

## <a name="next-steps"></a>Volgende stappen

- Een netwerk- of toepassingsbeveiligingsgroep maken met [PowerShell-](powershell-samples.md) of [Azure CLI-voorbeeldscripts](cli-samples.md) of Azure [Resource Manager-sjablonen](template-samples.md)
- [Azure-beleid](policy-samples.md) maken en toepassen voor virtuele netwerken
