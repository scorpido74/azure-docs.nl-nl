---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 652d42d6e2d9e909c3a03bd82a3a36f91bc73807
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419565"
---
De functie is eenvoudig in te stellen, maar dat betekent niet dat uw ervaring probleemloos zal zijn. Als u problemen ondervindt bij de toegang tot het gewenste eindpunt, zijn er enkele hulpprogramma's die u gebruiken om de connectiviteit vanaf de app-console te testen. Er zijn twee consoles die u gebruiken. Een daarvan is de Kudu-console, en de andere is de console in de Azure-portal. Ga naar **Extra** > **Kudu**om de Kudu-console te bereiken. U de Kudo-console ook bereiken op [sitename].scm.azurewebsites.net. Nadat de website is geladen, gaat u naar het tabblad **Foutopsporingsconsole.** Als u vanuit uw app naar de azure-portalconsole wilt gaan, gaat u naar > **Extraconsole**. **Tools**

#### <a name="tools"></a>Hulpprogramma's
De tools **ping,** **nslookup**en **tracert** werken niet via de console vanwege beveiligingsproblemen. Om de leegte op te vullen, worden twee afzonderlijke gereedschappen toegevoegd. Om DNS-functionaliteit te testen, hebben we een tool met de **naamnameresolver.exe**toegevoegd. De syntaxis is:

    nameresolver.exe hostname [optional: DNS Server]

U nameresolver gebruiken om de hostnamen te controleren waarvan uw app afhankelijk is. Op deze manier u testen of u iets verkeerd hebt geconfigureerd met uw DNS of misschien geen toegang hebt tot uw DNS-server. U de DNS-server zien die uw app in de console gebruikt door te kijken naar de omgevingsvariabelen WEBSITE_DNS_SERVER en WEBSITE_DNS_ALT_SERVER.

U het volgende hulpprogramma gebruiken om te testen op TCP-connectiviteit met een host- en poortcombinatie. Dit gereedschap wordt **tcpping** genoemd en de syntaxis is:

    tcpping.exe hostname [optional: port]

De **tcpping** utility vertelt u of u een specifieke host en poort bereiken. Het kan alleen succes laten zien als er een toepassing luistert naar de host- en poortcombinatie en er netwerktoegang is vanuit uw app naar de opgegeven host en poort.

#### <a name="debug-access-to-virtual-network-hosted-resources"></a>Foutopsporingstoegang tot bronnen die door virtuele netwerken worden gehost
Een aantal dingen kan voorkomen dat uw app een specifieke host en poort bereikt. Meestal is het een van deze dingen:

* **Een firewall staat in de weg.** Als u een firewall in de weg hebt, raakt u de TCP-time-out. De TCP-time-out bedraagt in dit geval 21 seconden. Gebruik het **tcpping-gereedschap** om de connectiviteit te testen. TCP-time-outs kunnen worden veroorzaakt door veel dingen buiten firewalls, maar daar beginnen.
* **DNS is niet toegankelijk.** De DNS-time-out is 3 seconden per DNS-server. Als u twee DNS-servers hebt, is de time-out 6 seconden. Gebruik nameresolver om te zien of DNS werkt. U nslookup niet gebruiken, omdat daar geen gebruik van wordt gemaakt met de DNS waarmee uw virtuele netwerk is geconfigureerd. Als u niet toegankelijk bent, u een firewall of NSG hebben die de toegang tot DNS blokkeert of deze kan worden uitgeschakeld.

Als deze items uw problemen niet beantwoorden, zoekt u eerst naar zaken als:

**Regionale VNet-integratie**
* Is uw bestemming een niet-RFC1918-adres en hoeft u niet WEBSITE_VNET_ROUTE_ALL ingesteld op 1?
* Is er een NSG blokkeren uitgang van uw integratie subnet?
* Als u Azure ExpressRoute of een VPN overgaat, is uw on-premises gateway geconfigureerd om verkeer terug te leiden naar Azure? Als u eindpunten in uw virtuele netwerk bereiken, maar niet on-premises, controleert u uw routes.
* Heeft u voldoende machtigingen om delegatie op het integratiesubnet in te stellen? Tijdens de regionale VNet-integratieconfiguratie wordt uw integratiesubnet gedelegeerd aan Microsoft.Web. De VNet-integratie-gebruikersinterface delegeert het subnet automatisch naar Microsoft.Web. Als uw account niet over voldoende netwerkmachtigingen beschikt om delegatie in te stellen, hebt u iemand nodig die kenmerken op uw integratiesubnet kan instellen om het subnet te delegeren. Als u het subnet integratie handmatig wilt delegeren, gaat u naar de gebruikersinterface van het Azure Virtual Network-subnet en stelt u de delegatie in voor Microsoft.Web.

**VNet-integratie met gatewayvereist**
* Is het adresbereik van punt tot locatie in de RFC 1918-reeksen (10.0.0.0-10.255.255.255 / 172.16.0-172.31.255.255 / 192.168.0-192.168.255.255)?
* Staat de gateway in het portaal? Als uw gateway is uitgeschakeld, breng het dan terug naar boven.
* Worden certificaten weergegeven als synchroon of vermoedt u dat de netwerkconfiguratie is gewijzigd?  Als uw certificaten niet synchroon lopen of als u vermoedt dat er een wijziging is aangebracht in uw virtuele netwerkconfiguratie die niet is gesynchroniseerd met uw ASP's, selecteert u **Netwerk synchroniseren**.
* Als u over een VPN gaat, is de on-premises gateway geconfigureerd om verkeer terug te leiden naar Azure? Als u eindpunten in uw virtuele netwerk bereiken, maar niet on-premises, controleert u uw routes.
* Probeert u een coëxistentiegateway te gebruiken die zowel point-to-site als ExpressRoute ondersteunt? Coexistence gateways worden niet ondersteund met VNet Integration.

Het opsporen van netwerkproblemen is een uitdaging omdat u niet zien wat de toegang tot een specifieke host:port-combinatie blokkeert. Enkele oorzaken zijn:

* U hebt een firewall op uw host die toegang tot de toepassingspoort voorkomt vanuit uw point-to-site IP-bereik. Het kruisen van subnetten vereist vaak toegang van het publiek.
* Je doelhost is uitgeschakeld.
* Je aanvraag is uitgevallen.
* Je had de verkeerde IP of hostname.
* Uw applicatie luistert op een andere poort dan u had verwacht. U uw proces-ID koppelen aan de luisterpoort met behulp van netstat -aon op de endpointhost.
* Uw netwerkbeveiligingsgroepen zijn zo geconfigureerd dat ze de toegang tot uw toepassingshost en -poort vanuit uw point-to-site IP-bereik verhinderen.

U weet niet welk adres uw app daadwerkelijk gebruikt. Het kan elk adres in het integratiesubnet of point-to-site-adresbereik zijn, dus u moet toegang toestaan vanuit het gehele adresbereik.

Aanvullende foutopsporingsstappen zijn:

* Connect to a VM in your virtual network and attempt to reach your resource host:port from there. Als u wilt testen op TCP-toegang, gebruikt u de **PowerShell-opdrachttestnetverbinding**. De syntaxis is:

      test-netconnection hostname [optional: -Port]

* Breng een toepassing op een VM naar boven en test de toegang tot die host en poort vanuit de console vanuit uw app met behulp van **tcpping.**

#### <a name="on-premises-resources"></a>On-premises resources ####

Als uw app geen on-premises bron kan bereiken, controleert u of u de bron vanuit uw virtuele netwerk bereiken. Gebruik de **powershell-opdracht testnetverbinding** om te controleren op TCP-toegang. Als uw vm uw on-premises bron niet kan bereiken, is uw VPN- of ExpressRoute-verbinding mogelijk niet goed geconfigureerd.

Als uw virtuele netwerkgehoste VM uw on-premises systeem kan bereiken, maar uw app niet, is de oorzaak waarschijnlijk een van de volgende redenen:

* Uw routes zijn niet geconfigureerd met uw subnet of point-to-site-adresbereiken in uw on-premises gateway.
* Uw netwerkbeveiligingsgroepen blokkeren de toegang voor uw point-to-site IP-bereik.
* Uw on-premises firewalls blokkeren verkeer van uw point-to-site IP-bereik.
* U probeert een niet-RFC 1918-adres te bereiken met behulp van de regionale VNet-integratiefunctie.