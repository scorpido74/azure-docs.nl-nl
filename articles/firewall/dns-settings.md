---
title: DNS-instellingen Azure Firewall (preview-versie)
description: U kunt Azure Firewall configureren met DNS-server-en DNS-proxy-instellingen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: efe608437f350a29147cf10989cdb6c17a23196d
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397991"
---
# <a name="azure-firewall-dns-settings-preview"></a>DNS-instellingen Azure Firewall (preview-versie)

> [!IMPORTANT]
> Azure Firewall zijn DNS-instellingen momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

U kunt een aangepaste DNS-server configureren en DNS-proxy inschakelen voor Azure Firewall. U kunt deze instellingen configureren wanneer u de firewall of hoger op de pagina **DNS-instellingen** implementeert.

## <a name="dns-servers"></a>DNS-servers

Een DNS-server onderhoudt en verhelpt domein namen naar IP-adressen. Azure Firewall maakt standaard gebruik van Azure DNS voor naam omzetting. Met de **DNS-server** instelling kunt u uw eigen DNS-servers configureren voor Azure firewall naam omzetting. U kunt één of meerdere servers configureren.

> [!NOTE]
> Voor Azure-firewalls die worden beheerd met Azure Firewall Manager, worden de DNS-instellingen geconfigureerd in het bijbehorende Azure Firewall-beleid.

### <a name="configure-custom-dns-servers-preview---azure-portal"></a>Aangepaste DNS-servers configureren (preview)-Azure Portal

1. Selecteer onder Azure Firewall **instellingen** de optie **DNS-instellingen**.
2. Onder **DNS-servers** kunt u bestaande DNS-servers die eerder zijn opgegeven in uw Virtual Network typen of toevoegen.
3. Selecteer **Opslaan**.
4. De firewall stuurt nu DNS-verkeer naar de opgegeven DNS-server (s) voor naam omzetting.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="DNS-servers":::

### <a name="configure-custom-dns-servers-preview---azure-cli"></a>Aangepaste DNS-servers configureren (preview)-Azure CLI

In het volgende voor beeld worden de Azure Firewall met aangepaste DNS-servers met behulp van Azure CLI bijgewerkt.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> Voor de opdracht moet `az network firewall` de Azure cli-extensie `azure-firewall` worden geïnstalleerd. Het kan worden geïnstalleerd met behulp van de opdracht `az extension add --name azure-firewall` . 

### <a name="configure-custom-dns-servers-preview---azure-powershell"></a>Aangepaste DNS-servers (preview-versie) configureren-Azure PowerShell

In het volgende voor beeld worden de Azure Firewall met aangepaste DNS-servers met behulp van Azure PowerShell bijgewerkt.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy-preview"></a>DNS-proxy (preview-versie)

U kunt Azure Firewall configureren om te fungeren als een DNS-proxy. Een DNS-proxy fungeert als een intermediair voor DNS-aanvragen van virtuele client machines naar een DNS-server. Als u een aangepaste DNS-server configureert, moet u DNS-proxy inschakelen om te voor komen dat DNS-omzetting niet overeenkomen en FQDN-filtering inschakelen in netwerk regels.

Als u DNS-proxy niet inschakelt, kunnen DNS-aanvragen van de client op een ander tijdstip naar een DNS-server worden getransporteerd of een andere reactie retour neren vergeleken met de firewall. De DNS-proxy plaatst Azure Firewall in het pad van de client aanvragen om inconsistentie te voor komen.

De DNS-proxy configuratie vereist drie stappen:
1. Schakel DNS-proxy in Azure Firewall DNS-instellingen in.
2. Configureer eventueel uw aangepaste DNS-server of gebruik de standaard waarde.
3. Ten slotte moet u het privé-IP-adres van de Azure Firewall configureren als een aangepast DNS-adres in de instellingen van de DNS-server van uw virtuele netwerk. Dit zorgt ervoor dat DNS-verkeer wordt omgeleid naar Azure Firewall.

### <a name="configure-dns-proxy-preview---azure-portal"></a>DNS-proxy configureren (preview)-Azure Portal

Als u een DNS-proxy wilt configureren, moet u de instelling van de DNS-servers voor het virtuele netwerk configureren voor gebruik van het privé IP-adres van de firewall Schakel vervolgens DNS-proxy in Azure Firewall **DNS-instellingen** in.

#### <a name="configure-virtual-network-dns-servers"></a>DNS-servers voor het virtuele netwerk configureren 

1. Selecteer het virtuele netwerk waarin het DNS-verkeer wordt doorgestuurd via de Azure Firewall.
2. Selecteer onder **Instellingen** de optie **DNS-servers**.
3. Selecteer **aangepast** onder **DNS-servers**.
4. Voer het privé IP-adres van de firewall in.
5. Selecteer **Opslaan**.
6. Start de virtuele machines die zijn verbonden met het virtuele netwerk opnieuw op, zodat deze de nieuwe DNS-server instellingen krijgen. Vm's blijven hun huidige DNS-instellingen gebruiken totdat ze opnieuw zijn opgestart.

#### <a name="enable-dns-proxy-preview"></a>DNS-proxy inschakelen (preview-versie)

1. Selecteer uw Azure Firewall.
2. Selecteer **DNS-instellingen** onder **instellingen**.
3. **DNS-proxy** is standaard uitgeschakeld. Wanneer deze functie is ingeschakeld, luistert de firewall op poort 53 en stuurt DNS-aanvragen door naar de geconfigureerde DNS-servers.
4. Controleer de configuratie van de **DNS-servers** om ervoor te zorgen dat de instellingen geschikt zijn voor uw omgeving.
5. Selecteer **Opslaan**.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="DNS-proxy":::

### <a name="configure-dns-proxy-preview---azure-cli"></a>DNS-proxy configureren (preview)-Azure CLI

Het configureren van DNS-proxy-instellingen in Azure Firewall en het bijwerken van VNets voor gebruik van Azure Firewall als DNS-server kan worden uitgevoerd met behulp van Azure CLI.

#### <a name="configure-virtual-network-dns-servers"></a>DNS-servers voor het virtuele netwerk configureren

In dit voor beeld wordt het VNet zo geconfigureerd dat Azure Firewall als DNS-server wordt gebruikt.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

#### <a name="enable-dns-proxy-preview"></a>DNS-proxy inschakelen (preview-versie)

In dit voor beeld wordt de functie DNS-proxy in Azure Firewall ingeschakeld.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

### <a name="configure-dns-proxy-preview---azure-powershell"></a>DNS-proxy (preview-versie) configureren-Azure PowerShell

Het configureren van DNS-proxy-instellingen en het bijwerken van VNets voor gebruik van Azure Firewall als DNS-server kan worden uitgevoerd met behulp van Azure PowerShell.

#### <a name="configure-virtual-network-dns-servers"></a>DNS-servers voor het virtuele netwerk configureren

 In dit voor beeld wordt het VNet zo geconfigureerd dat Azure Firewall als DNS-server wordt gebruikt.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

#### <a name="enable-dns-proxy-preview"></a>DNS-proxy inschakelen (preview-versie)

In dit voor beeld wordt de functie DNS-proxy in Azure Firewall ingeschakeld.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Volgende stappen

[FQDN-filtering in netwerk regels](fqdn-filtering-network-rules.md)
