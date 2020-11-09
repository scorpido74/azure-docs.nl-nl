---
title: DNS-instellingen Azure Firewall
description: U kunt Azure Firewall configureren met DNS-server-en DNS-proxy-instellingen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: ad0ac040b510783656617ddbf2063cd94c80aae7
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380943"
---
# <a name="azure-firewall-dns-settings"></a>DNS-instellingen Azure Firewall

U kunt een aangepaste DNS-server configureren en DNS-proxy inschakelen voor Azure Firewall. U kunt deze instellingen configureren wanneer u de firewall of hoger op de pagina **DNS-instellingen** implementeert.

## <a name="dns-servers"></a>DNS-servers

Een DNS-server onderhoudt en verhelpt domein namen naar IP-adressen. Azure Firewall maakt standaard gebruik van Azure DNS voor naam omzetting. Met de **DNS-server** instelling kunt u uw eigen DNS-servers configureren voor Azure firewall naam omzetting. U kunt één of meerdere servers configureren.

> [!NOTE]
> Voor Azure-firewalls die worden beheerd met Azure Firewall Manager, worden de DNS-instellingen geconfigureerd in het bijbehorende Azure Firewall-beleid.

### <a name="configure-custom-dns-servers---azure-portal"></a>Aangepaste DNS-servers configureren-Azure Portal

1. Selecteer onder Azure Firewall **instellingen** de optie **DNS-instellingen**.
2. Onder **DNS-servers** kunt u bestaande DNS-servers die eerder zijn opgegeven in uw Virtual Network typen of toevoegen.
3. Selecteer **Opslaan**.
4. De firewall stuurt nu DNS-verkeer naar de opgegeven DNS-server (s) voor naam omzetting.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="DNS-servers":::

### <a name="configure-custom-dns-servers---azure-cli"></a>Aangepaste DNS-servers configureren-Azure CLI

In het volgende voor beeld worden de Azure Firewall met aangepaste DNS-servers met behulp van Azure CLI bijgewerkt.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> Voor de opdracht moet `az network firewall` de Azure cli-extensie `azure-firewall` worden geïnstalleerd. Het kan worden geïnstalleerd met behulp van de opdracht `az extension add --name azure-firewall` . 

### <a name="configure-custom-dns-servers---azure-powershell"></a>Aangepaste DNS-servers configureren-Azure PowerShell

In het volgende voor beeld worden de Azure Firewall met aangepaste DNS-servers met behulp van Azure PowerShell bijgewerkt.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy"></a>DNS-proxy

U kunt Azure Firewall configureren om te fungeren als een DNS-proxy. Een DNS-proxy fungeert als een intermediair voor DNS-aanvragen van virtuele client machines naar een DNS-server. Als u een aangepaste DNS-server configureert, moet u DNS-proxy inschakelen om te voor komen dat DNS-omzetting niet overeenkomen en FQDN-filtering inschakelen in netwerk regels.

Als u DNS-proxy niet inschakelt, kunnen DNS-aanvragen van de client op een ander tijdstip naar een DNS-server worden getransporteerd of een andere reactie retour neren vergeleken met de firewall. De DNS-proxy plaatst Azure Firewall in het pad van de client aanvragen om inconsistentie te voor komen.

Er zijn twee typen cache functies die plaatsvinden wanneer Azure Firewall een DNS-proxy is:

- Positieve cache – DNS-omzetting is geslaagd. De firewall gebruikt de TTL (time-to-Live) van het pakket of het object. 

- Negatieve cache – DNS-omzetting resulteert in geen reactie of geen oplossing. De firewall slaat deze informatie één uur in de cache op.

De DNS-proxy slaat alle opgeloste IP-adressen op uit de FQDN-waarden in netwerk regels. Gebruik als best practice FQDN-adressen die worden omgezet in één IP-adres.  

### <a name="dns-proxy-configuration"></a>Configuratie van DNS-proxy

De DNS-proxy configuratie vereist drie stappen:
1. Schakel DNS-proxy in Azure Firewall DNS-instellingen in.
2. Configureer eventueel uw aangepaste DNS-server of gebruik de standaard waarde.
3. Ten slotte moet u het privé-IP-adres van de Azure Firewall configureren als een aangepast DNS-adres in de instellingen van de DNS-server van uw virtuele netwerk. Dit zorgt ervoor dat DNS-verkeer wordt omgeleid naar Azure Firewall.

#### <a name="configure-dns-proxy---azure-portal"></a>DNS-proxy-Azure Portal configureren

Als u een DNS-proxy wilt configureren, moet u de instelling van de DNS-servers voor het virtuele netwerk configureren voor gebruik van het privé IP-adres van de firewall Schakel vervolgens DNS-proxy in Azure Firewall **DNS-instellingen** in.

##### <a name="configure-virtual-network-dns-servers"></a>DNS-servers voor het virtuele netwerk configureren 

1. Selecteer het virtuele netwerk waarin het DNS-verkeer wordt doorgestuurd via de Azure Firewall.
2. Selecteer onder **Instellingen** de optie **DNS-servers**.
3. Selecteer **aangepast** onder **DNS-servers**.
4. Voer het privé IP-adres van de firewall in.
5. Selecteer **Opslaan**.
6. Start de virtuele machines die zijn verbonden met het virtuele netwerk opnieuw op, zodat deze de nieuwe DNS-server instellingen krijgen. Vm's blijven hun huidige DNS-instellingen gebruiken totdat ze opnieuw zijn opgestart.

##### <a name="enable-dns-proxy"></a>DNS-proxy inschakelen

1. Selecteer uw Azure Firewall.
2. Selecteer **DNS-instellingen** onder **instellingen**.
3. **DNS-proxy** is standaard uitgeschakeld. Wanneer deze functie is ingeschakeld, luistert de firewall op poort 53 en stuurt DNS-aanvragen door naar de geconfigureerde DNS-servers.
4. Controleer de configuratie van de **DNS-servers** om ervoor te zorgen dat de instellingen geschikt zijn voor uw omgeving.
5. Selecteer **Opslaan**.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="DNS-proxy":::

#### <a name="configure-dns-proxy---azure-cli"></a>DNS-proxy configureren-Azure CLI

U kunt Azure CLI gebruiken voor het configureren van DNS-proxy-instellingen in Azure Firewall en het bijwerken van virtuele netwerken om Azure Firewall te gebruiken als de DNS-server.

##### <a name="configure-virtual-network-dns-servers"></a>DNS-servers voor het virtuele netwerk configureren

In dit voor beeld wordt het VNet zo geconfigureerd dat Azure Firewall als DNS-server wordt gebruikt.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### <a name="enable-dns-proxy"></a>DNS-proxy inschakelen

In dit voor beeld wordt de functie DNS-proxy in Azure Firewall ingeschakeld.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### <a name="configure-dns-proxy---azure-powershell"></a>DNS-proxy-Azure PowerShell configureren

U kunt Azure PowerShell gebruiken om DNS-proxy-instellingen te configureren in Azure Firewall en virtuele netwerken bij te werken om Azure Firewall als de DNS-server te gebruiken.

##### <a name="configure-virtual-network-dns-servers"></a>DNS-servers voor het virtuele netwerk configureren

 In dit voor beeld wordt het VNet zo geconfigureerd dat Azure Firewall als DNS-server wordt gebruikt.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### <a name="enable-dns-proxy"></a>DNS-proxy inschakelen

In dit voor beeld wordt de functie DNS-proxy in Azure Firewall ingeschakeld.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Volgende stappen

[FQDN-filtering in netwerk regels](fqdn-filtering-network-rules.md)
