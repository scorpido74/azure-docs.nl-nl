---
title: Verbindings-en netwerkproblemen
titleSuffix: Azure Cloud Services
description: In dit artikel worden de veelgestelde vragen over connectiviteit en netwerken voor Microsoft Azure Cloud Services weergegeven.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/23/2018
ms.author: genli
ms.openlocfilehash: 7caeba0e88f63106eae80f7142b5d65463f8d7a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019397"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Verbindings- en netwerkproblemen voor Azure Cloud Services: veelgestelde vragen (veelgestelde vragen)

Dit artikel bevat veelgestelde vragen over verbindings- en netwerkproblemen voor [Azure Cloud Services.](https://azure.microsoft.com/services/cloud-services) Zie de pagina [VM-grootte van Cloud Services voor](cloud-services-sizes-specs.md)informatie over de grootte van de grootte van Cloud Services .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Ik kan geen IP reserveren in een multi-VIP cloud service.
Zorg er eerst voor dat de virtuele machine-instantie waarvoor u het IP-adres probeert te reserveren, is ingeschakeld. Ten tweede moet u ervoor zorgen dat u gereserveerde IP's gebruikt voor zowel de faserings- als productie-implementaties. *Wijzig* de instellingen niet tijdens het upgraden van de implementatie.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Hoe gebruik ik Extern bureaublad als ik een NSG heb?
Voeg regels toe aan de NSG die verkeer op havens **3389** en **20000**mogelijk maken. Extern bureaublad maakt gebruik van poort **3389**. Cloudservice-exemplaren zijn load balanced, dus u niet direct bepalen met welke instantie u verbinding wilt maken. De *RemoteForwarder-* en *RemoteAccess-agents* beheren RDP-verkeer (Remote Desktop Protocol) en stellen de client in staat om een RDP-cookie te verzenden en een afzonderlijke instantie op te geven waarmee verbinding moet worden gemaakt. De *RemoteForwarder-* en *RemoteAccess-agents* vereisen dat poort **20000** is geopend, die kan worden geblokkeerd als u een NSG hebt.

## <a name="can-i-ping-a-cloud-service"></a>Kan ik een cloudservice pingen?

Nee, niet met behulp van het normale "ping"/ICMP protocol. Het ICMP-protocol is niet toegestaan via de Azure load balancer.

Om de connectiviteit te testen, raden we u aan een poortping te doen. Terwijl Ping.exe ICMP gebruikt, u andere hulpprogramma's, zoals PSPing, Nmap en telnet, gebruiken om de connectiviteit met een specifieke TCP-poort te testen.

Zie [Poortpingen gebruiken in plaats van ICMP gebruiken om Azure VM-connectiviteit te testen](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/)voor meer informatie.

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Hoe voorkom ik het ontvangen van duizenden hits van onbekende IP-adressen die kunnen wijzen op een kwaadaardige aanval op de cloudservice?
Azure implementeert een meerlaagse netwerkbeveiliging om zijn platformservices te beschermen tegen ddos-aanvallen (distributed denial-of-service). Het Azure DDoS-beveiligingssysteem maakt deel uit van het continue bewakingsproces van Azure, dat voortdurend wordt verbeterd door middel van penetratietests. Dit DDoS-verdedigingssysteem is ontworpen om niet alleen aanvallen van buitenaf te weerstaan, maar ook van andere Azure-tenants. Zie [Azure-netwerkbeveiliging](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf)voor meer informatie.

U ook een opstarttaak maken om bepaalde specifieke IP-adressen selectief te blokkeren. Zie [Een specifiek IP-adres blokkeren](cloud-services-startup-tasks-common.md#block-a-specific-ip-address)voor meer informatie .

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Wanneer ik rdp naar mijn cloudservice-instantie probeer te maken, krijg ik het bericht 'Het gebruikersaccount is verlopen'.
Mogelijk krijgt u het foutbericht 'Dit gebruikersaccount is verlopen' wanneer u de vervaldatum omzeilt die is geconfigureerd in uw RDP-instellingen. U de vervaldatum van de portal wijzigen door de volgende stappen te volgen:

1. Meld u aan bij de [Azure-portal,](https://portal.azure.com)ga naar uw cloudservice en selecteer het tabblad **Extern bureaublad.**

2. Selecteer de **implementatiesleuf Productie** **of Fasering.**

3. Wijzig de **datum verloopt op** en sla de configuratie op.

U moet nu in staat zijn om RDP naar uw machine.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Waarom wordt het verkeer niet gelijkmatig verdeeld met Azure Load Balancer?
Zie de nieuwe distributiemodus van [Azure Load Balancer](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/)voor informatie over hoe een interne load balancer werkt.

Het gebruikte distributiealgoritme is een 5-tuple (bron-IP, bronpoort, bestemmings-IP, doelpoort en protocoltype) hash om verkeer naar beschikbare servers in kaart te brengen. Het biedt plakkerigheid alleen binnen een transportsessie. Pakketten in dezelfde TCP- of UDP-sessie worden naar hetzelfde DATACENTER IP-exemplaar (DIP) geleid achter het load-balanced endpoint. Wanneer de client de verbinding sluit en heropent of een nieuwe sessie start vanaf hetzelfde bron-IP, verandert de bronpoort en wordt het verkeer naar een ander DIP-eindpunt gebracht.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Hoe kan ik binnenkomend verkeer omleiden naar de standaard-URL van mijn cloudservice naar een aangepaste URL?

De URL Rewrite-module van IIS kan worden gebruikt om verkeer dat naar \*de standaard-URL voor de cloudservice (bijvoorbeeld cloudapp.net) komt, om te leiden naar een aangepaste naam/URL. Omdat de URL Rewrite-module standaard is ingeschakeld op webrollen en de regels ervan zijn geconfigureerd in de web.config van de toepassing, is deze altijd beschikbaar op de VM, ongeacht het opnieuw opstarten/opnieuw afbeeldingen. Zie voor meer informatie:

- [Herschrijfregels maken voor de MODULE URL Herschrijven](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Een standaardkoppeling verwijderen](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Hoe kan ik binnenkomend verkeer blokkeren/uitschakelen naar de standaard-URL van mijn cloudservice?

U binnenkomend verkeer naar de standaard URL/naam van \*uw cloudservice voorkomen (bijvoorbeeld .cloudapp.net). Stel de hostkop op een aangepaste DNS-naam (bijvoorbeeld www\.MyCloudService.com) in onder sitebindingsconfiguratie in het bestand van de cloudservicedefinitie (*.csdef), zoals aangegeven:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
    <WebRole name="MyWebRole" vmsize="Small">
        <Sites>
            <Site name="Web">
            <Bindings>
                <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" />
            </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
</ServiceDefinition>
```

Omdat deze hostheaderbinding wordt afgedwongen via het csdef-bestand, is de service alleen toegankelijk via de aangepaste naam 'www.MyCloudService.com'. Alle binnenkomende aanvragen voor het domein "*.cloudapp.net" mislukken altijd. Als u een aangepaste SLB-sonde of een interne load balancer in de service gebruikt, kan het blokkeren van de standaard URL/naam van de service het indringende gedrag verstoren.

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Hoe kan ik ervoor zorgen dat het openbare IP-adres van een cloudservice nooit verandert?

Om ervoor te zorgen dat het openbare IP-adres van uw cloudservice (ook wel VIP genoemd) nooit verandert, zodat het gewoonlijk op de witte lijst kan worden gebracht door een paar specifieke clients, raden we u aan er een gereserveerd IP-adres aan te hebben. Anders wordt het virtuele IP-adres van Azure vanuit uw abonnement verdeeld als u de implementatie verwijdert. Voor een succesvolle VIP-swapbewerking hebt u afzonderlijke gereserveerde IP's nodig voor zowel productie- als faseringsruimten. Zonder hen mislukt de swapoperatie. Zie de volgende artikelen om een IP-adres te reserveren en te koppelen aan uw cloudservice:

- [Het IP-adres van een bestaande cloudservice reserveren](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
- [Een gereserveerd IP koppelen aan een cloudservice met behulp van een serviceconfiguratiebestand](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

Als u meer dan één exemplaar voor uw rollen hebt, mag het koppelen van RIP aan uw cloudservice geen downtime veroorzaken. U ook het IP-bereik van uw Azure-datacenter toevoegen aan een lijst met toegestane services. U alle Azure IP-bereiken vinden in het [Microsoft Download Center.](https://www.microsoft.com/en-us/download/details.aspx?id=41653)

Dit bestand bevat de IP-adresbereiken (inclusief compute, SQL en opslagbereiken) die worden gebruikt in Azure-datacenters. Er wordt wekelijks een bijgewerkt bestand geplaatst dat de momenteel geïmplementeerde bereiken en eventuele komende wijzigingen in de IP-bereiken weergeeft. Nieuwe bereiken die in het bestand worden weergegeven, worden gedurende ten minste één week niet in de datacenters gebruikt. Download het nieuwe .xml-bestand elke week en voer de nodige wijzigingen op uw site uit om de services die in Azure worden uitgevoerd, correct te identificeren. Azure ExpressRoute-gebruikers kunnen er rekening mee houden dat dit bestand wordt gebruikt om de BGP-advertentie van Azure-ruimte in de eerste week van elke maand bij te werken.

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Hoe kan ik virtuele netwerken van Azure Resource Manager gebruiken met cloudservices?

Cloudservices kunnen niet worden geplaatst in virtuele azure-netwerken voor Azure Resource Manager. Virtual Networks van Resource Manager en virtuele netwerken met klassieke implementatie kunnen worden verbonden via peering. Zie [Virtueel netwerkpeeren](../virtual-network/virtual-network-peering-overview.md)voor meer informatie.


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Hoe krijg ik de lijst met openbare IP's die door mijn Cloud Services worden gebruikt?

U het volgende PS-script gebruiken om de lijst met openbare IP's voor cloudservices onder uw abonnement te krijgen

```powershell
$services = Get-AzureService  | Group-Object -Property ServiceName

foreach ($service in $services)
{
    "Cloud Service '$($service.Name)'"

    $deployment = Get-AzureDeployment -ServiceName $service.Name
    "VIP - " +  $deployment.VirtualIPs[0].Address
    "================================="
}
```
