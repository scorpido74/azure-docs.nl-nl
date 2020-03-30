---
title: Diagnose van een probleem met het netwerkfilter van een virtuele machine | Microsoft Documenten
description: Meer informatie over het diagnosticeren van een probleem met het filterprobleem van een virtuele machinenetwerkfilter door de effectieve beveiligingsregels voor een virtuele machine te bekijken.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: kumud
ms.openlocfilehash: 6939ea2497a9f12321e1a6dfb9bf9fbb353bc7db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240781"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Diagnose van een probleem met het filterprobleem van het netwerkvan een virtuele machine

In dit artikel leert u hoe u een probleem met netwerkverkeerfilter diagnosticeren door de beveiligingsregels van de netwerkbeveiligingsgroep (NSG) te bekijken die effectief zijn voor een virtuele machine (VM).

Met NSG's u de typen verkeer beheren die in en uit een VM stromen. U een NSG koppelen aan een subnet in een virtueel Azure-netwerk, een netwerkinterface die is gekoppeld aan een vm of beide. De effectieve beveiligingsregels die op een netwerkinterface worden toegepast, zijn een aggregatie van de regels die bestaan in de NSG die zijn gekoppeld aan een netwerkinterface en het subnet waarin de netwerkinterface zich bevindt. Regels in verschillende NSG's kunnen soms met elkaar in conflict komen en van invloed zijn op de netwerkconnectiviteit van een VM. U alle effectieve beveiligingsregels van NSG's bekijken die worden toegepast op de netwerkinterfaces van uw VM. Zie Overzicht van virtuele [netwerknetwerken,](virtual-networks-overview.md) [netwerknetwerken](virtual-network-network-interface.md)en netwerkbeveiligingsgroepen als u niet bekend bent met virtuele netwerk-, netwerkinterface- of [NSG-concepten.](security-overview.md)

## <a name="scenario"></a>Scenario

U probeert verbinding te maken met een VM over poort 80 vanaf het internet, maar de verbinding mislukt. Als u wilt bepalen waarom u geen toegang hebt tot poort 80 vanaf internet, u de effectieve beveiligingsregels voor een netwerkinterface bekijken via de [Azure-portal,](#diagnose-using-azure-portal) [PowerShell](#diagnose-using-powershell)of azure [CLI.](#diagnose-using-azure-cli)

De volgende stappen gaan ervan uit dat u een bestaande VM hebt om de effectieve beveiligingsregels voor te bekijken. Als u geen bestaande VM hebt, implementeert u eerst een [Linux-](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Windows-VM](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) om de taken in dit artikel te voltooien. De voorbeelden in dit artikel zijn voor een VM met de naam *myVM* met een netwerkinterface met de naam *myVMVMNic*. De VM- en netwerkinterface bevinden zich in een resourcegroep met de naam *myResourceGroup*en bevinden zich in de regio *Oost-VS.* Wijzig de waarden in de stappen, indien van toepassing, voor de VM waarvoor u het probleem diagnosticeert.

## <a name="diagnose-using-azure-portal"></a>Diagnose stellen met Azure-portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een Azure-account met de [benodigde machtigingen.](virtual-network-network-interface.md#permissions)
2. Voer boven aan de Azure-portal de naam van de VM in het zoekvak in. Wanneer de naam van de virtuele machine wordt weergegeven in de zoekresultaten, selecteert u deze.
3. Selecteer **onder INSTELLINGEN**de optie **Netwerken**, zoals in de volgende afbeelding wordt weergegeven:

   ![Beveiligingsregels weergeven](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   De regels die u in de vorige afbeelding ziet, zijn voor een netwerkinterface met de naam **myVMVMNic**. U ziet dat er **inbound PORT-regels** zijn voor de netwerkinterface van twee verschillende netwerkbeveiligingsgroepen:
   
   - **mySubnetNSG**: Gekoppeld aan het subnet waarin de netwerkinterface zich bevindt.
   - **myVMNSG**: Gekoppeld aan de netwerkinterface in de VM met de naam **myVMVMNic**.

   De regel met de naam **DenyAllInBound** is wat inkomende communicatie naar de VM via poort 80 verhindert, vanaf het internet, zoals beschreven in het [scenario.](#scenario) De regel bevat *0.0.0.0/0* voor **SOURCE**, waaronder het internet. Geen enkele andere regel met een hogere prioriteit (lager getal) staat poort 80 inkomende toe. Zie [Een probleem oplossen](#resolve-a-problem)om poort 80 toe te staan die vanaf internet naar de vm wordt toegekoppeld. Zie [Netwerkbeveiligingsgroepen](security-overview.md)voor meer informatie over beveiligingsregels en hoe Azure deze toepast.

   Onder aan de foto ziet u ook **UITGAANDE POORTREGELS.** Daaronder gelden de uitgaande poortregels voor de netwerkinterface. Hoewel de afbeelding slechts vier binnenkomende regels voor elke NSG weergeeft, kunnen uw NSG's veel meer dan vier regels hebben. Op de afbeelding ziet u **VirtualNetwork** onder **BRON** en **BESTEMMING** en **AzureLoadBalancer** onder **SOURCE**. **VirtualNetwork** en **AzureLoadBalancer** zijn [servicetags.](security-overview.md#service-tags) Servicetags vertegenwoordigen een groep IP-adresvoorvoegsels om de complexiteit voor het maken van beveiligingsregels te minimaliseren.

4. Controleer of de vm zich in de uitvoeringsstatus bevindt en selecteer vervolgens **Effectieve beveiligingsregels**, zoals in de vorige afbeelding wordt weergegeven, om de effectieve beveiligingsregels in de volgende afbeelding weer te geven:

   ![Effectieve beveiligingsregels weergeven](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   De genoemde regels zijn hetzelfde als je zag in stap 3, maar er zijn verschillende tabbladen voor de NSG gekoppeld aan de netwerkinterface en het subnet. Zoals u zien op de foto, worden alleen de eerste 50 regels weergegeven. Als u een CSV-bestand wilt downloaden dat alle regels bevat, selecteert u **Downloaden**.

   Als u wilt zien welke voorvoegsels elke servicetag vertegenwoordigt, selecteert u een regel, zoals de regel met de naam **AllowAzureLoadBalancerInbound**. In de volgende afbeelding worden de voorvoegsels voor de **AzureLoadBalancer-servicetag** weergegeven:

   ![Effectieve beveiligingsregels weergeven](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Hoewel de **AzureLoadBalancer-servicetag** slechts één voorvoegsel vertegenwoordigt, vertegenwoordigen andere servicetags verschillende voorvoegsels.

5. De vorige stappen toonden de beveiligingsregels voor een netwerkinterface genaamd **myVMVMNic,** maar je hebt ook een netwerkinterface met de naam **myVMVMNic2** gezien in sommige van de vorige foto's. De VM in dit voorbeeld heeft twee netwerkinterfaces aan verbonden. De effectieve beveiligingsregels kunnen voor elke netwerkinterface verschillend zijn.

   Als u de regels voor de **myVMVMNic2-netwerkinterface** wilt bekijken, selecteert u deze. Zoals te zien is in de afbeelding die volgt, heeft de netwerkinterface dezelfde regels die aan het subnet zijn gekoppeld als de **myVMVMNic-netwerkinterface,** omdat beide netwerkinterfaces zich in hetzelfde subnet bevinden. Wanneer u een NSG aan een subnet koppelt, worden de regels toegepast op alle netwerkinterfaces in het subnet.

   ![Beveiligingsregels weergeven](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   In tegenstelling tot de **myVMVMNic-netwerkinterface** heeft de **myVMVMNic2-netwerkinterface** geen netwerkbeveiligingsgroep. Elke netwerkinterface en subnet kan nul, of één, NSG verbonden aan het hebben. De NSG die is gekoppeld aan elke netwerkinterface of subnet kan hetzelfde zijn, of anders. U dezelfde netwerkbeveiligingsgroep koppelen aan zoveel netwerkinterfaces en subnetten als u wilt.

Hoewel effectieve beveiligingsregels via de VM zijn bekeken, u ook effectieve beveiligingsregels bekijken via een individu:
- **Netwerkinterface:** meer informatie over het [weergeven van een netwerkinterface.](virtual-network-network-interface.md#view-network-interface-settings)
- **NSG**: Meer informatie over het [bekijken van een NSG.](manage-network-security-group.md#view-details-of-a-network-security-group)

## <a name="diagnose-using-powershell"></a>Diagnose stellen met PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U de opdrachten uitvoeren die volgen in de [Azure Cloud Shell](https://shell.azure.com/powershell)of door PowerShell vanaf uw computer uit te voeren. De Azure Cloud Shell is een gratis interactieve shell. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u PowerShell vanaf uw computer uitvoert, hebt u de Azure PowerShell-module, versie 1.0.0 of hoger, nodig. Voer `Get-Module -ListAvailable Az` uit op uw computer om de geïnstalleerde versie te vinden. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet `Connect-AzAccount` u ook uitvoeren om u aan te melden bij Azure met een account dat de [benodigde machtigingen](virtual-network-network-interface.md#permissions)heeft].

Profiteer van de effectieve beveiligingsregels voor een netwerkinterface met [Get-AzEffectiveNetworkSecurityGroup.](/powershell/module/az.network/get-azeffectivenetworksecuritygroup) In het volgende voorbeeld worden de effectieve beveiligingsregels voor een netwerkinterface met de naam *myVMVMNic*, die zich in een resourcegroep met de naam *myResourceGroup bevindt:*

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

De uitvoer wordt geretourneerd in json-indeling. Zie [Opdrachtuitvoer interpreteren](#interpret-command-output)om de uitvoer te begrijpen.
Uitvoer wordt alleen geretourneerd als een NSG is gekoppeld aan de netwerkinterface, het subnet waarin de netwerkinterface zich bevindt of beide. De VM moet in de loopstatus zijn. Een VM kan meerdere netwerkinterfaces met verschillende NSG's toepassen. Voer bij het oplossen van problemen de opdracht uit voor elke netwerkinterface.

Als u nog steeds een verbindingsprobleem hebt, raadpleegt u [aanvullende diagnose](#additional-diagnosis) en [overwegingen.](#considerations)

Als u de naam van een netwerkinterface niet kent, maar wel de naam weet van de VM waaraan de netwerkinterface is gekoppeld, worden de iD's van alle netwerkinterfaces die aan een VM zijn gekoppeld, als volgt teruggegeven:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

U ontvangt uitvoer die vergelijkbaar is met het volgende voorbeeld:

```output
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

In de vorige uitvoer is de naam van de netwerkinterface *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnose stellen met Azure CLI

Als u CLI-opdrachten (Azure Command-line interface) gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/bash)of door de CLI vanaf uw computer uit te voeren. Dit artikel vereist de Azure CLI-versie 2.0.32 of hoger. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). Als u de Azure CLI lokaal uitvoert, `az login` moet u ook Azure uitvoeren en aanmelden met een account dat over de [vereiste machtigingen beschikt.](virtual-network-network-interface.md#permissions)

Haal de effectieve beveiligingsregels voor een netwerkinterface met [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg). In het volgende voorbeeld worden de effectieve beveiligingsregels voor een netwerkinterface met de naam *myVMVMNic* gekregen die zich in een resourcegroep met de naam *myResourceGroup bevindt:*

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

De uitvoer wordt geretourneerd in json-indeling. Zie [Opdrachtuitvoer interpreteren](#interpret-command-output)om de uitvoer te begrijpen.
Uitvoer wordt alleen geretourneerd als een NSG is gekoppeld aan de netwerkinterface, het subnet waarin de netwerkinterface zich bevindt of beide. De VM moet in de loopstatus zijn. Een VM kan meerdere netwerkinterfaces met verschillende NSG's toepassen. Voer bij het oplossen van problemen de opdracht uit voor elke netwerkinterface.

Als u nog steeds een verbindingsprobleem hebt, raadpleegt u [aanvullende diagnose](#additional-diagnosis) en [overwegingen.](#considerations)

Als u de naam van een netwerkinterface niet kent, maar wel de naam weet van de VM waaraan de netwerkinterface is gekoppeld, worden de iD's van alle netwerkinterfaces die aan een VM zijn gekoppeld, als volgt teruggegeven:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

Binnen de geretourneerde uitvoer ziet u informatie die vergelijkbaar is met het volgende voorbeeld:

```output
"networkProfile": {
    "additionalProperties": {},
    "networkInterfaces": [
      {
        "additionalProperties": {},
        "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
        "primary": true,
        "resourceGroup": "myResourceGroup"
      },
```

In de vorige uitvoer is de naam van de netwerkinterface *de myVMVMNic-interface.*

## <a name="interpret-command-output"></a>Opdrachtuitvoer interpreteren

Ongeacht of u de [PowerShell](#diagnose-using-powershell)of de [Azure CLI](#diagnose-using-azure-cli) hebt gebruikt om het probleem te diagnosticeren, u ontvangt uitvoer die de volgende informatie bevat:

- **NetworkSecurityGroup**: De ID van de netwerkbeveiligingsgroep.
- **Koppeling**: Of de netwerkbeveiligingsgroep is gekoppeld aan een *NetworkInterface* of *Subnet*. Als een NSG aan beide is gekoppeld, wordt de uitvoer geretourneerd met **NetworkSecurityGroup,** **Association**en **EffectiveSecurityRules**voor elke NSG. Als de NSG wordt gekoppeld of losgekoppeld voordat de opdracht wordt uitgevoerd om de effectieve beveiligingsregels weer te geven, moet u mogelijk een paar seconden wachten voordat de wijziging wordt weergegeven in de opdrachtuitvoer.
- **EffectiveSecurityRules**: Een uitleg van elke eigenschap wordt beschreven in [Een beveiligingsregel maken.](manage-network-security-group.md#create-a-security-rule) Regelnamen die voorafgaan aan *defaultSecurityRules/* zijn standaardbeveiligingsregels die in elke NSG bestaan. Regelnamen die voorafgaan aan *securityRules/* zijn regels die u hebt gemaakt. Regels die een [servicetag](security-overview.md#service-tags)opgeven , zoals **Internet**, **VirtualNetwork**en **AzureLoadBalancer** voor de eigenschappen **destinationAddressPrefix** of **sourceAddressPrefix,** bevatten ook waarden voor de eigenschap **expandedDestinationAddressPrefix.** De eigenschap **expandedDestinationAddressPrefix** bevat alle adresvoorvoegsels die worden weergegeven door de servicetag.

Als u dubbele regels in de uitvoer ziet, is dit omdat een NSG is gekoppeld aan zowel de netwerkinterface als het subnet. Beide NSG's hebben dezelfde standaardregels en kunnen aanvullende dubbele regels hebben als u uw eigen regels hebt gemaakt die in beide NSG's hetzelfde zijn.

De regel met de naam **defaultSecurityRules/DenyAllInBound** is wat inkomende communicatie naar de VM via poort 80 verhindert, vanaf het internet, zoals beschreven in het [scenario.](#scenario) Geen enkele andere regel met een hogere prioriteit (lager getal) maakt poort 80 binnenkomend vanaf het internet mogelijk.

## <a name="resolve-a-problem"></a>Een probleem oplossen

Of u nu de [Azure-portal,](#diagnose-using-azure-portal) [PowerShell](#diagnose-using-powershell)of de [Azure CLI](#diagnose-using-azure-cli) gebruikt om het probleem te diagnosticeren dat in het [scenario](#scenario) in dit artikel wordt gepresenteerd, de oplossing is het maken van een netwerkbeveiligingsregel met de volgende eigenschappen:

| Eigenschap                | Waarde                                                                              |
|---------                |---------                                                                           |
| Bron                  | Alle                                                                                |
| Poortbereiken van bron      | Alle                                                                                |
| Doel             | Het IP-adres van de VM, een reeks IP-adressen of alle adressen in het subnet. |
| Poortbereiken van doel | 80                                                                                 |
| Protocol                | TCP                                                                                |
| Actie                  | Toestaan                                                                              |
| Prioriteit                | 100                                                                                |
| Name                    | Allow-HTTP-All                                                                     |

Nadat u de regel hebt gemaakt, is poort 80 toegestaan om van internet te worden opgenomen, omdat de prioriteit van de regel hoger is dan de standaardbeveiligingsregel met de naam *DenyAllInBound*, die het verkeer weigert. Meer informatie over het [maken van een beveiligingsregel](manage-network-security-group.md#create-a-security-rule). Als er verschillende NSG's zijn gekoppeld aan zowel de netwerkinterface als het subnet, moet u in beide NSG's dezelfde regel maken.

Wanneer Azure binnenkomend verkeer verwerkt, worden regels verwerkt in de NSG die aan het subnet zijn gekoppeld (als er een bijbehorende NSG is) en verwerkt het vervolgens de regels in de NSG die zijn gekoppeld aan de netwerkinterface. Als er een NSG is gekoppeld aan de netwerkinterface en het subnet, moet de poort in beide NSG's geopend zijn, om het verkeer de VM te kunnen bereiken. Om administratieve en communicatieproblemen te verlichten, raden we u aan een NSG te koppelen aan een subnet, in plaats van aan individuele netwerkinterfaces. Als VM's binnen een subnet andere beveiligingsregels nodig hebben, u de netwerkinterfaces lid maken van een toepassingsbeveiligingsgroep (ASG) en een ASG opgeven als bron en bestemming van een beveiligingsregel. Meer informatie over [toepassingsbeveiligingsgroepen](security-overview.md#application-security-groups).

Zie [Overwegingen](#considerations) en aanvullende diagnose als u nog steeds communicatieproblemen hebt.

## <a name="considerations"></a>Overwegingen

Houd rekening met de volgende punten bij het oplossen van verbindingsproblemen:

* Standaardbeveiligingsregels blokkeren binnenkomende toegang vanaf het internet en staan alleen binnenkomend verkeer van het virtuele netwerk toe. Als u binnenkomend verkeer van internet wilt toestaan, voegt u beveiligingsregels toe met een hogere prioriteit dan standaardregels. Meer informatie over [standaardbeveiligingsregels](security-overview.md#default-security-rules)of het [toevoegen van een beveiligingsregel](manage-network-security-group.md#create-a-security-rule).
* Als u virtuele netwerken hebt peered, wordt de **VIRTUAL_NETWORK** servicetag standaard automatisch uitgebreid met voorvoegsels voor virtuele netwerken met peered. Als u problemen met virtuele netwerkpeering wilt oplossen, u de voorvoegsels weergeven in de lijst **ExpandedAddressPrefix.** Meer informatie over [virtuele netwerkpeering](virtual-network-peering-overview.md) en [servicetags](security-overview.md#service-tags).
* Effectieve beveiligingsregels worden alleen weergegeven voor een netwerkinterface als er een NSG is gekoppeld aan de netwerkinterface van de VM en of subnet of als de VM in de uitvoeringsstatus is.
* Als er geen NSG's zijn gekoppeld aan de netwerkinterface of het subnet en u een [openbaar IP-adres](virtual-network-public-ip-address.md) hebt toegewezen aan een VM, zijn alle poorten open voor inkomende toegang van en uitgaande toegang tot overal. Als de VM een openbaar IP-adres heeft, raden we u aan een NSG toe te passen op het subnet van de netwerkinterface.

## <a name="additional-diagnosis"></a>Aanvullende diagnose

* Als u een snelle test wilt uitvoeren om te bepalen of verkeer van of naar een vm is toegestaan, gebruikt u de [IP-stroomverificatiemogelijkheid](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) van Azure Network Watcher. Ip-stroomcontrole geeft aan of verkeer is toegestaan of geweigerd. Als geweigerd wordt, geeft IP-stroomverificatie aan welke beveiligingsregel het verkeer wordt geweigerd.
* Als er geen beveiligingsregels zijn waardoor de netwerkverbinding van een VM mislukt, kan het probleem te wijten zijn aan:
  * Firewallsoftware die binnen het besturingssysteem van de VM wordt uitgevoerd
  * Routes geconfigureerd voor virtuele apparaten of on-premises verkeer. Internetverkeer kan via [geforceerde tunneling](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)naar uw on-premises netwerk worden doorgestuurd. Als u tunnelinternet naar een virtueel apparaat of on-premises forceert, u mogelijk geen verbinding maken met de vm via internet. Zie Een probleem met het routeren van [virtuele machines diagnosticeren](diagnose-network-routing-problem.md)voor meer informatie over het diagnosticeren van routeproblemen die de doorstroming van het verkeer uit de VM kunnen belemmeren.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over alle taken, eigenschappen en instellingen voor een [netwerkbeveiligingsgroep](manage-network-security-group.md#work-with-network-security-groups) en [beveiligingsregels](manage-network-security-group.md#work-with-security-rules).
- Meer informatie over [standaardbeveiligingsregels,](security-overview.md#default-security-rules) [servicetags](security-overview.md#service-tags)en [hoe Azure beveiligingsregels voor binnenkomend en uitgaand verkeer](security-overview.md#network-security-groups) voor een virtuele machine verwerkt.
