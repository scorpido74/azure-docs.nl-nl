---
title: Het pakketfilter van FreeBSD gebruiken om een firewall in Azure te maken
description: Meer informatie over het implementeren van een NAT-firewall met PF van FreeBSD in Azure.
author: KylieLiang
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 9b78c0d93b57a3e3f4963088d0b93f121f57483c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945113"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Het pakketfilter van FreeBSD gebruiken om een beveiligde firewall in Azure te maken
In dit artikel wordt uitgelegd hoe u een NAT-firewall implementeert met het Packer-filter van FreeBSD via de Azure Resource Manager-sjabloon voor het scenario van de algemene webserver.

## <a name="what-is-pf"></a>Wat is PF?
PF (Packet Filter, ook geschreven pf) is een BSD licentie stateful packet filter, een centraal stukje software voor firewalling. PF is sindsdien snel geëvolueerd en heeft nu verschillende voordelen ten opzichte van andere beschikbare firewalls. Network Address Translation (NAT) is vanaf dag één in PF, waarna packet scheduler en active queue management zijn geïntegreerd in PF, door de ALTQ te integreren en configureerbaar te maken via de configuratie van PF. Functies zoals pfsync en CARP voor failover en redundantie, authpf voor sessieauthenticatie en ftp-proxy om firewalls van het moeilijke FTP-protocol te vergemakkelijken, hebben pf ook uitgebreid. Kortom, PF is een krachtige en feature-rijke firewall. 

## <a name="get-started"></a>Aan de slag
Als u geïnteresseerd bent in het opzetten van een beveiligde firewall in de cloud voor uw webservers, laten we dan aan de slag. U ook de scripts toepassen die in deze Azure Resource Manager-sjabloon worden gebruikt om uw netwerktopologie in te stellen.
De sjabloon Azure Resource Manager heeft een FreeBSD-virtuele machine ingesteld die NAT /omleiding uitvoert met PF en twee Virtual-machines van FreeBSD, waarbij de Nginx-webserver is geïnstalleerd en geconfigureerd. Naast het uitvoeren van NAT voor de twee webservers uitgang verkeer, de NAT / omleiding virtuele machine onderschept HTTP-verzoeken en omleiden naar de twee webservers in round-robin mode. De VNet maakt gebruik van de private niet-routable IP-adresruimte 10.0.0.2/24 en u de parameters van de sjabloon wijzigen. De sjabloon Azure Resource Manager definieert ook een routetabel voor het hele VNet, een verzameling afzonderlijke routes die worden gebruikt om Azure-standaardroutes te overschrijven op basis van het doel-IP-adres. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Implementeren via Azure CLI
U moet de nieuwste [Azure CLI](/cli/azure/install-az-cli2) geïnstalleerd en ingelogd op een Azure-account met behulp van [az login](/cli/azure/reference-index). Maak een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voorbeeld `myResourceGroup` wordt `West US` een naam van de resourcegroep op de locatie gezoend.

```azurecli
az group create --name myResourceGroup --location westus
```

Implementeer vervolgens de [sjabloon pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) bij [het maken van de implementatie van az-groepen.](/cli/azure/group/deployment) Download [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) onder hetzelfde pad en definieer `adminPassword`uw `networkPrefix`eigen `domainNamePrefix`resourcewaarden, zoals , en . 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Na ongeveer vijf minuten krijgt u `"provisioningState": "Succeeded"`de informatie van . Vervolgens u ssh naar de frontend VM (NAT) of toegang tot Nginx webserver in een browser met behulp van het openbare IP-adres of FQDN van de frontend VM (NAT). In het volgende voorbeeld worden FQDN en openbaar IP-adres weergegeven `myResourceGroup` dat is toegewezen aan de frontend VM (NAT) in de resourcegroep. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Volgende stappen
Wilt u uw eigen NAT instellen in Azure? Open Source, gratis maar krachtig? Dan is PF een goede keuze. Door de [sjabloon pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup)te gebruiken, hebt u slechts vijf minuten nodig om een NAT-firewall in te stellen met round-robin load balancing met behulp van FreeBSD's PF in Azure voor veelvoorkomend webserverscenario. 

Als u het aanbieden van FreeBSD in Azure wilt leren, raadpleegt u [de inleiding tot FreeBSD op Azure.](freebsd-intro-on-azure.md)

Als u meer wilt weten over PF, raadpleegdan [freebsd handboek](https://www.freebsd.org/doc/handbook/firewalls-pf.html) of [PF-User's Guide](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
