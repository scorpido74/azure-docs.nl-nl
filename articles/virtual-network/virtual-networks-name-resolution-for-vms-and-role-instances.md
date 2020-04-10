---
title: Naamomzetting voor resources in virtuele Azure-netwerken
titlesuffix: Azure Virtual Network
description: Scenario's voor naamomzetting voor Azure IaaS, hybride oplossingen, tussen verschillende cloudservices, Active Directory en het gebruik van uw eigen DNS-server.
services: virtual-network
documentationcenter: na
author: rohinkoul
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/2/2020
ms.author: rohink
ms.custom: fasttrack-edit
ms.openlocfilehash: d7d0699718642a7eb9f85b2e8a86623092c34365
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010559"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Naamomzetting voor resources in virtuele Azure-netwerken

Afhankelijk van hoe u Azure gebruikt om IaaS-, PaaS- en hybride oplossingen te hosten, moet u mogelijk toestaan dat de virtuele machines (VM's) en andere resources die in een virtueel netwerk worden geïmplementeerd, met elkaar communiceren. Hoewel u communicatie inschakelen met IP-adressen, is het veel eenvoudiger om namen te gebruiken die gemakkelijk kunnen worden onthouden en niet kunnen veranderen. 

Wanneer resources die in virtuele netwerken worden geïmplementeerd, domeinnamen moeten oplossen naar interne IP-adressen, kunnen ze een van de drie methoden gebruiken:

* [Azure DNS-privézones](../dns/private-dns-overview.md)
* [Azure-naamomzetting](#azure-provided-name-resolution)
* [Naamomzetting die uw eigen DNS-server gebruikt](#name-resolution-that-uses-your-own-dns-server) (die query's kan doorsturen naar de door Azure geleverde DNS-servers)

Welk type naamomzetting u gebruikt, is afhankelijk van hoe uw resources met elkaar moeten communiceren. In de volgende tabel worden scenario's en bijbehorende oplossingen voor naamsoplossing geïllustreerd:

> [!NOTE]
> Azure DNS private zones heeft de voorkeur en biedt u flexibiliteit bij het beheren van uw DNS-zones en -records. Zie voor meer informatie [Azure DNS gebruiken voor privédomeinen](../dns/private-dns-overview.md).

> [!NOTE]
> Als u Azure Provided DNS gebruikt, wordt het juiste DNS-achtervoegsel automatisch toegepast op uw virtuele machines. Voor alle andere opties moet u ofwel Volledig gekwalificeerde domeinnamen (FQDN) gebruiken of handmatig het juiste DNS-achtervoegsel toepassen op uw virtuele machines.

| **Scenario** | **Oplossing** | **DNS-achtervoegsel** |
| --- | --- | --- |
| Naamomzetting tussen VM's in hetzelfde virtuele netwerk of Azure Cloud Services-rolexemplaren in dezelfde cloudservice. | [Azure DNS-privézones](../dns/private-dns-overview.md) of [door Azure geleverde naamomzetting](#azure-provided-name-resolution) |Hostname of FQDN |
| Naamoplossing tussen VM's in verschillende virtuele netwerken of rolexemplaren in verschillende cloudservices. |[Azure DNS-privézones](../dns/private-dns-overview.md) of door de klant beheerde DNS-servers die query's doorsturen tussen virtuele netwerken voor oplossing door Azure (DNS-proxy). Zie [Naamomzetting met uw eigen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Alleen FQDN |
| Naamomzetting van een Azure App Service (Web App, Function of Bot) met behulp van virtuele netwerkintegratie naar rolexemplaren of VM's in hetzelfde virtuele netwerk. |Door de klant beheerde DNS-servers die query's doorsturen tussen virtuele netwerken voor oplossing door Azure (DNS-proxy). Zie [Naamomzetting met uw eigen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Alleen FQDN |
| Naamresolutie van App Service Web Apps naar VM's in hetzelfde virtuele netwerk. |Door de klant beheerde DNS-servers die query's doorsturen tussen virtuele netwerken voor oplossing door Azure (DNS-proxy). Zie [Naamomzetting met uw eigen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Alleen FQDN |
| Naamresolutie van App Service Web Apps in één virtueel netwerk naar VM's in een ander virtueel netwerk. |Door de klant beheerde DNS-servers die query's doorsturen tussen virtuele netwerken voor oplossing door Azure (DNS-proxy). Zie [Naamomzetting met uw eigen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Alleen FQDN |
| Oplossing voor on-premises computer- en servicenamen uit VM's of rolexemplaren in Azure. |Door de klant beheerde DNS-servers (on-premises domeincontroller, lokale alleen-lezen domeincontroller of een DNS-secundaire gesynchroniseerd met zoneoverdrachten, bijvoorbeeld). Zie [Naamomzetting met uw eigen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Alleen FQDN |
| Oplossing van Azure-hostnamen van on-premises computers. |Doorsturen van query's naar een door de klant beheerde DNS-proxyserver in het bijbehorende virtuele netwerk, stuurt de proxyserver query's door naar Azure voor oplossing. Zie [Naamomzetting met uw eigen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Alleen FQDN |
| Reverse DNS voor interne IP's. |[Azure DNS-privézones](../dns/private-dns-overview.md) of [door Azure geleverde naamomzetting](#azure-provided-name-resolution) of [naamomzetting met uw eigen DNS-server.](#name-resolution-that-uses-your-own-dns-server) |Niet van toepassing |
| Naamoplossing tussen VM's of rolexemplaren in verschillende cloudservices, niet in een virtueel netwerk. |Niet van toepassing. Connectiviteit tussen VM's en rolexemplaren in verschillende cloudservices wordt niet buiten een virtueel netwerk ondersteund. |Niet van toepassing|

## <a name="azure-provided-name-resolution"></a>Azure-naamomzetting

Azure provided name resolution biedt alleen elementaire gezaghebbende DNS-mogelijkheden. Als u deze optie gebruikt, worden de DNS-zonenamen en -records automatisch beheerd door Azure en u de DNS-zonenamen of de levenscyclus van DNS-records niet beheren. Als u een volledig uitgeruste DNS-oplossing voor uw virtuele netwerken nodig hebt, moet u [Azure DNS-privézones](../dns/private-dns-overview.md) of [door de klant beheerde DNS-servers](#name-resolution-that-uses-your-own-dns-server)gebruiken.

Samen met de resolutie van openbare DNS-namen biedt Azure interne naamomzetting voor VM's en rolexemplaren die zich binnen hetzelfde virtuele netwerk of dezelfde cloudservice bevinden. VM's en exemplaren in een cloudservice delen hetzelfde DNS-achtervoegsel, dus alleen de hostnaam is voldoende. Maar in virtuele netwerken die worden geïmplementeerd met behulp van het klassieke implementatiemodel, hebben verschillende cloudservices verschillende DNS-achtervoegsels. In deze situatie hebt u de FQDN nodig om namen tussen verschillende cloudservices op te lossen. In virtuele netwerken die zijn geïmplementeerd met behulp van het Azure Resource Manager-implementatiemodel, is het DNS-achtervoegsel consistent voor alle virtuele machines binnen een virtueel netwerk, zodat de FQDN niet nodig is. DNS-namen kunnen worden toegewezen aan zowel VM's als netwerkinterfaces. Hoewel voor azure-gebaseerde naamomzetting geen configuratie vereist is, is dit niet de juiste keuze voor alle implementatiescenario's, zoals beschreven in de vorige tabel.

> [!NOTE]
> Wanneer u web- en werknemersrollen van cloudservices gebruikt, hebt u ook toegang tot de interne IP-adressen van rolinstanties met behulp van de Azure Service Management REST API. Zie de [API-verwijzing servicebeheer rest](https://msdn.microsoft.com/library/azure/ee460799.aspx)voor meer informatie . Het adres is gebaseerd op de rolnaam en het instantienummer. 
>

### <a name="features"></a>Functies

Azure-naamomzetting bevat de volgende functies:
* Gebruiksgemak. Er is geen configuratie vereist.
* Hoge beschikbaarheid. U hoeft geen clusters van uw eigen DNS-servers te maken en te beheren.
* U de service gebruiken in combinatie met uw eigen DNS-servers om zowel on-premises als Azure-hostnamen op te lossen.
* U naamomzetting tussen VM's en rolinstanties binnen dezelfde cloudservice gebruiken, zonder dat er een FQDN nodig is.
* U naamomzetting gebruiken tussen VM's in virtuele netwerken die het Azure Resource Manager-implementatiemodel gebruiken, zonder dat er een FQDN nodig is. Virtuele netwerken in het klassieke implementatiemodel vereisen een FQDN wanneer u namen in verschillende cloudservices oplost. 
* U hostnamen gebruiken die uw implementaties het best beschrijven, in plaats van met automatisch gegenereerde namen te werken.

### <a name="considerations"></a>Overwegingen

Punten om rekening mee te houden wanneer u de door Azure geleverde naamresolutie gebruikt:
* Het door Azure gemaakte DNS-achtervoegsel kan niet worden gewijzigd.
* DNS-lookup is ingesteld op een virtueel netwerk. DNS-namen die voor één virtuele netwerk zijn gemaakt, kunnen niet worden opgelost vanuit andere virtuele netwerken.
* U uw eigen records niet handmatig registreren.
* WINS en NetBIOS worden niet ondersteund. U uw VM's niet zien in Windows Verkenner.
* Hostnamen moeten DNS-compatibel zijn. Namen mogen slechts 0-9, a-z en '-' gebruiken en kunnen niet beginnen of eindigen met een '-'.
* DNS-queryverkeer wordt voor elke vm beperkt. Beperking mag geen invloed hebben op de meeste toepassingen. Als de beperking van het verzoek wordt nageleefd, moet u ervoor zorgen dat caching aan clientzijde is ingeschakeld. Zie [DNS-clientconfiguratie](#dns-client-configuration)voor meer informatie.
* Alleen VM's in de eerste 180 cloudservices worden voor elk virtueel netwerk geregistreerd in een klassiek implementatiemodel. Deze limiet is niet van toepassing op virtuele netwerken in Azure Resource Manager.
* Het Azure DNS IP-adres is 168.63.129.16. Dit is een statisch IP-adres en zal niet veranderen.

### <a name="reverse-dns-considerations"></a>Omgekeerde DNS-overwegingen
Reverse DNS wordt ondersteund in alle arm gebaseerde virtuele netwerken. U omgekeerde DNS-query's (PTR-query's) uitgeven om IP-adressen van virtuele machines in kaart te brengen aan FQDN's van virtuele machines.
* Alle PTR-query's voor IP-adressen van virtuele machines \[retourneren FQDN's met formuliervmname\].internal.cloudapp.net
* Forward lookup on FQDNs of form \[vmname\].internal.cloudapp.net will resolve to IP address assigned to the virtual machine.
* Als het virtuele netwerk is gekoppeld aan een [Azure DNS-privézones](../dns/private-dns-overview.md) als een virtueel registratienetwerk, worden twee records met de omgekeerde DNS-query's teruggekeerd. Een record zal de \[van\]het formulier vmname . [priatednszonename] en andere zou \[van\]het formulier vmname zijn .internal.cloudapp.net
* Reverse DNS lookup is scoped naar een bepaald virtueel netwerk, zelfs als het is peered naar andere virtuele netwerken. Reverse DNS queries (PTR queries) voor IP-adressen van virtuele machines in peered virtuele netwerken retourneert NXDOMAIN.

> [!NOTE]
> Als u wilt dat reverse DNS lookup over virtueel netwerk heen gaat, u een reverse lookup zone (in-addr.arpa) [Azure DNS private zones](../dns/private-dns-overview.md) maken en deze aan meerdere virtuele netwerken verbinden. U moet echter wel handmatig de omgekeerde DNS-records voor de virtuele machines beheren.
>


## <a name="dns-client-configuration"></a>DNS-clientconfiguratie

Deze sectie behandelt caching aan clientzijde en herpogingen aan clientzijde.

### <a name="client-side-caching"></a>Caching aan clientzijde

Niet elke DNS-query hoeft over het netwerk te worden verzonden. Client-side caching helpt de latentie te verminderen en de veerkracht voor netwerkblips te verbeteren, door terugkerende DNS-query's uit een lokale cache op te lossen. DNS-records bevatten een time-to-live (TTL)-mechanisme, waarmee de cache de record zo lang mogelijk kan opslaan zonder dat dit gevolgen heeft voor de versheid van records. Zo is client-side caching geschikt voor de meeste situaties.

De standaard Windows DNS-client heeft een INgebouwde DNS-cache. Sommige Linux-distributies bevatten standaard geen caching. Als u merkt dat er nog geen lokale cache is, voegt u een DNS-cache toe aan elke Linux-vm.

Er zijn een aantal verschillende DNS caching pakketten beschikbaar (zoals dnsmasq). Zo installeer je dnsmasq op de meest voorkomende distributies:

* **Ubuntu (maakt gebruik van resolvconf)**:
  * Installeer het dnsmasq `sudo apt-get install dnsmasq`pakket met .
* **SUSE (maakt gebruik van netconf)**:
  * Installeer het dnsmasq `sudo zypper install dnsmasq`pakket met .
  * Schakel de dnsmasq-service in met `systemctl enable dnsmasq.service`. 
  * Start de dnsmasq-service met `systemctl start dnsmasq.service`. 
  * Bewerken **/etc/sysconfig/network/config**, en *verander NETCONFIG_DNS_FORWARDER=""* in *dnsmasq*.
  * Update resolv.conf `netconfig update`met , om de cache in te stellen als de lokale DNS resolver.
* **CentOS (maakt gebruik van NetworkManager)**:
  * Installeer het dnsmasq `sudo yum install dnsmasq`pakket met .
  * Schakel de dnsmasq-service in met `systemctl enable dnsmasq.service`.
  * Start de dnsmasq-service met `systemctl start dnsmasq.service`.
  * Voeg *prepend domeinnaam-servers 127.0.0.1 toe* aan **/etc/dhclient-eth0.conf**.
  * Start de netwerkservice opnieuw met `service network restart`, om de cache in te stellen als de lokale DNS-resolver.

> [!NOTE]
> Het dnsmasq-pakket is slechts een van de vele DNS-caches die beschikbaar zijn voor Linux. Controleer voordat u deze gebruikt de geschiktheid voor uw specifieke behoeften en controleert of er geen andere cache is geïnstalleerd.

    
### <a name="client-side-retries"></a>Herpogingen aan clientzijde

DNS is in de eerste plaats een UDP-protocol. Omdat het UDP-protocol geen garantie biedt voor de bezorging van berichten, wordt de logica opnieuw geprobeerd in het DNS-protocol zelf. Elke DNS-client (besturingssysteem) kan verschillende logica voor nieuwe technieken vertonen, afhankelijk van de voorkeur van de maker:

* Windows-besturingssystemen opnieuw proberen na een seconde, en dan weer na nog eens twee seconden, vier seconden, en nog eens vier seconden. 
* De standaard Linux setup wordt na vijf seconden opnieuw weergegeven. We raden aan om de specificaties van de nieuwe poging te wijzigen in vijf keer, met tussenpozen van één seconde.

Controleer de huidige instellingen op `cat /etc/resolv.conf`een Linux VM met . Kijk bijvoorbeeld naar de *optieregel:*

```bash
options timeout:1 attempts:5
```

Het bestand resolv.conf wordt meestal automatisch gegenereerd en mag niet worden bewerkt. De specifieke stappen voor het toevoegen van de *optieregel* verschillen per distributie:

* **Ubuntu** (maakt gebruik van resolvconf):
  1. Voeg de *optieregel* toe aan **/etc/resolvconf/resolv.conf.d/tail**.
  2. Uitvoeren `resolvconf -u` om bij te werken.
* **SUSE** (maakt gebruik van netconf):
  1. *Time-out:1-pogingen:5* toevoegen aan de parameter **NETCONFIG_DNS_RESOLVER_OPTIONS=""** in **/etc/sysconfig/network/config**.
  2. Uitvoeren `netconfig update` om bij te werken.
* **CentOS** (maakt gebruik van NetworkManager):
  1. Voeg *echo "options timeout:1 attempts:5"* toe aan **/etc/NetworkManager/dispatcher.d/11-dhclient**.
  2. Bijwerken `service network restart`met .

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Naamomzetting die uw eigen DNS-server gebruikt

Deze sectie behandelt VM's, rolinstanties en web-apps.

### <a name="vms-and-role-instances"></a>VM's en rolexemplaren

Uw behoeften aan naamomzetting gaan mogelijk verder dan de functies van Azure. Het kan bijvoorbeeld nodig zijn om Microsoft Windows Server Active Directory-domeinen te gebruiken, DNS-namen tussen virtuele netwerken op te lossen. Om deze scenario's te dekken, biedt Azure u de mogelijkheid om uw eigen DNS-servers te gebruiken.

DNS-servers binnen een virtueel netwerk kunnen DNS-query's doorsturen naar de recursieve resolvers in Azure. Hiermee u hostnamen binnen dat virtuele netwerk oplossen. Een dc-domeincontroller (DC) die in Azure wordt uitgevoerd, kan bijvoorbeeld reageren op DNS-query's voor zijn domeinen en alle andere query's doorsturen naar Azure. Met doorsturen van query's kunnen VM's zowel uw on-premises resources (via de DC) als de door Azure geleverde hostnamen (via de expediteur) zien. Toegang tot de recursieve resolvers in Azure wordt geboden via het virtuele IP 168.63.129.16.

DNS forwarding maakt ook DNS-resolutie tussen virtuele netwerken mogelijk en stelt uw on-premises machines in staat om door Azure geleverde hostnamen op te lossen. Om de hostnaam van een vm op te lossen, moet de VM van de DNS-server zich in hetzelfde virtuele netwerk bevinden en worden geconfigureerd om hostnaamquery's door te sturen naar Azure. Omdat het DNS-achtervoegsel in elk virtueel netwerk verschilt, u voorwaardelijke doorstuurregels gebruiken om DNS-query's naar het juiste virtuele netwerk te verzenden voor oplossing. De volgende afbeelding toont twee virtuele netwerken en een on-premises netwerk dat DNS-resolutie tussen virtuele netwerken doet, met behulp van deze methode. Een voorbeeld DNS-expediteur is beschikbaar in de [azure quickstartsjablonengalerie](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) en [GitHub.](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder)

> [!NOTE]
> Een rolinstantie kan naamresolutie van VM's uitvoeren binnen hetzelfde virtuele netwerk. Dit gebeurt door gebruik te maken van de FQDN, die bestaat uit de hostnaam van de VM en **internal.cloudapp.net** DNS-achtervoegsel. In dit geval is naamomzetting echter alleen succesvol als in de rolinstantie de VM-naam is gedefinieerd in het [rolschema (.cscfg-bestand).](https://msdn.microsoft.com/library/azure/jj156212.aspx)
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> Rolexemplaren die naamresolutie van VM's in een ander virtueel netwerk (FQDN met behulp van het **internal.cloudapp.net** achtervoegsel) moeten uitvoeren, moeten dit doen met behulp van de methode die in deze sectie wordt beschreven (aangepaste DNS-servers doorsturen tussen de twee virtuele netwerken).
>

![Diagram van DNS tussen virtuele netwerken](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Wanneer u de door Azure geleverde naamomzetting gebruikt, biedt Azure Dynamic Host Configuration Protocol (DHCP) elke vm een intern DNS-achtervoegsel (**internal.cloudapp.net**). Met dit achtervoegsel u de naamresolutie van de host maken omdat de hostnaamrecords zich in de **internal.cloudapp.net-zone** bevinden. Wanneer u uw eigen oplossing voor naamomzetting gebruikt, wordt dit achtervoegsel niet aan VM's geleverd omdat het andere DNS-architecturen (zoals scenario's met domeinbewerking) verstoort. In plaats daarvan biedt Azure een niet-werkende tijdelijke aanduiding *(reddog.microsoft.com).*

Indien nodig u het interne DNS-achtervoegsel bepalen met PowerShell of de API:

* Voor virtuele netwerken in Azure Resource Manager-implementatiemodellen is het achtervoegsel beschikbaar via de REST API voor [netwerkinterface,](https://docs.microsoft.com/rest/api/virtualnetwork/networkinterfaces)de cmdlet [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) PowerShell en de opdracht [AZ-netwerknic show](/cli/azure/network/nic#az-network-nic-show) Azure CLI.
* In klassieke implementatiemodellen is het achtervoegsel beschikbaar via de [get deployment API-aanroep](https://msdn.microsoft.com/library/azure/ee460804.aspx) of de cmdlet [Get-AzureVM -Debug.](/powershell/module/servicemanagement/azure/get-azurevm)

Als het doorsturen van query's naar Azure niet aan uw behoeften voldoet, moet u uw eigen DNS-oplossing bieden. Uw DNS-oplossing moet:

* Zorg voor de juiste hostnaamresolutie, bijvoorbeeld via [DDNS.](virtual-networks-name-resolution-ddns.md) Als u DDNS gebruikt, moet u mogelijk dns-recordopruiming uitschakelen. Azure DHCP-leases zijn lang en het opruimen kan DNS-records voortijdig verwijderen. 
* Zorg voor een passende recursieve oplossing om de oplossing van externe domeinnamen mogelijk te maken.
* Wees toegankelijk (TCP en UDP op poort 53) van de clients die het bedient en heb toegang tot het internet.
* Worden beveiligd tegen toegang vanaf het internet, om bedreigingen van externe agenten te beperken.

> [!NOTE]
> Voor de beste prestaties moet IPv6 worden uitgeschakeld wanneer u Azure VM's als DNS-servers gebruikt.

### <a name="web-apps"></a>Web-apps
Stel dat u naamresolutie moet uitvoeren vanuit uw web-app die is gebouwd met behulp van App Service, gekoppeld aan een virtueel netwerk, naar VM's in hetzelfde virtuele netwerk. Voer naast het instellen van een aangepaste DNS-server met een DNS-expediteer die query's doorstuurt naar Azure (virtueel IP 168.63.129.16), de volgende stappen uit:
1. Virtuele netwerkintegratie inschakelen voor uw web-app, zo niet al gedaan, zoals beschreven in [Uw app integreren met een virtueel netwerk.](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
2. Selecteer in de Azure-portal voor het App Service-abonnement dat de web-app host, de optie **Netwerk synchroniseren** onder **Netwerken**, Integratie van **virtuele netwerken**.

    ![Schermafbeelding van de omzetting van virtuele netwerknamen](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Als u naamomzetting moet uitvoeren vanuit uw web-app die is gebouwd met behulp van App Service, gekoppeld aan een virtueel netwerk, naar VM's in een ander virtueel netwerk, moet u aangepaste DNS-servers op beide virtuele netwerken als volgt gebruiken:

* Stel een DNS-server in uw virtuele doelnetwerk in, op een VM die ook query's kan doorsturen naar de recursieve resolver in Azure (virtueel IP 168.63.129.16). Een voorbeeld DNS-expediteur is beschikbaar in de [azure quickstartsjablonengalerie](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) en [GitHub.](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder) 
* Stel een DNS-expediteer in het virtuele bronnetwerk in op een vm. Configureer deze DNS-expediteer om query's door te sturen naar de DNS-server in uw virtuele doelnetwerk.
* Configureer uw bron-DNS-server in de instellingen van uw bron virtueel netwerk.
* Virtuele netwerkintegratie inschakelen voor uw web-app om te koppelen aan het virtuele bronnetwerk, volgens de instructies in [Uw app integreren met een virtueel netwerk.](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* Selecteer in de Azure-portal voor het App Service-abonnement dat de web-app host, de optie **Netwerk synchroniseren** onder **Netwerken**, Integratie van **virtuele netwerken**.

## <a name="specify-dns-servers"></a>DNS-servers opgeven
Wanneer u uw eigen DNS-servers gebruikt, biedt Azure de mogelijkheid om meerdere DNS-servers per virtueel netwerk op te geven. U kunt ook meerdere DNS-servers opgeven per netwerkinterface (voor Azure Resource Manager) of per cloudservice (voor het klassieke implementatiemodel). DNS-servers die zijn opgegeven voor een netwerkinterface of cloudservice krijgen voorrang op DNS-servers die zijn opgegeven voor het virtuele netwerk.

> [!NOTE]
> Netwerkverbindingseigenschappen, zoals IP-server-IP's van de DNS-server, mogen niet rechtstreeks binnen VM's worden bewerkt. Dit komt omdat ze kunnen krijgen gewist tijdens de dienst te genezen wanneer de virtuele netwerk adapter wordt vervangen. Dit geldt voor zowel Windows- als Linux VM's.

Wanneer u het implementatiemodel azure resource beheer gebruikt, u DNS-servers opgeven voor een virtueel netwerk en een netwerkinterface. Zie Een [virtueel netwerk beheren](manage-virtual-network.md) en [Een netwerkinterface beheren](virtual-network-network-interface.md)voor meer informatie.

> [!NOTE]
> Als u kiest voor een aangepaste DNS-server voor uw virtuele netwerk, moet u ten minste één IP-adres van de DNS-server opgeven. Anders negeert het virtuele netwerk de configuratie en gebruikt het in plaats daarvan door Azure verstrekte DNS.

Wanneer u het klassieke implementatiemodel gebruikt, u DNS-servers opgeven voor het virtuele netwerk in de Azure-portal of het [netwerkconfiguratiebestand.](https://msdn.microsoft.com/library/azure/jj157100) Voor cloudservices u DNS-servers opgeven via het [serviceconfiguratiebestand](https://msdn.microsoft.com/library/azure/ee758710) of met PowerShell, met [New-AzureVM.](/powershell/module/servicemanagement/azure/new-azurevm)

> [!NOTE]
> Als u de DNS-instellingen voor een virtueel netwerk of virtuele machine die al is geïmplementeerd, wijzigt om de nieuwe DNS-instellingen van kracht te laten worden, moet u een DHCP-leaseverlenging uitvoeren op alle getroffen VM's in het virtuele netwerk. Voor VM's met het Windows-besturingssysteem `ipconfig /renew` u dit doen door rechtstreeks in de VM te typen. De stappen variëren afhankelijk van het besturingssysteem. Bekijk de relevante documentatie voor uw besturingssysteemtype.

## <a name="next-steps"></a>Volgende stappen

Azure Resource Manager-implementatiemodel:

* [Een virtueel netwerk beheren](manage-virtual-network.md)
* [Een netwerkinterface beheren](virtual-network-network-interface.md)

Klassiek implementatiemodel:

* [Azure-serviceconfiguratieschema](https://msdn.microsoft.com/library/azure/ee758710)
* [Configuratieschema voor virtuele netwerken](https://msdn.microsoft.com/library/azure/jj157100)
* [Een virtueel netwerk configureren met behulp van een netwerkconfiguratiebestand](virtual-networks-using-network-configuration-file.md)
