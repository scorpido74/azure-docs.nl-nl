---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 2d2a82552a846cedfa5da3bb6ec6df8a40b67732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671506"
---
Hoewel de functie eenvoudig is in te stellen, betekent dat niet dat uw ervaring probleemloos zal zijn. Mocht u problemen ondervinden bij het bereiken van het gewenste eindpunt, dan zijn er enkele hulpprogramma's die u gebruiken om de connectiviteit vanaf de app-console te testen. Er zijn twee consoles die u gebruiken. Een daarvan is de Kudu-console en de andere is de console in de Azure-portal. Ga naar Extra-> Kudu om de Kudu-console te bereiken. U de Kudo-console ook bereiken op [sitename].scm.azurewebsites.net. Zodra de website is geladen, gaat u naar het tabblad Foutopsporingsconsole. Ga naar > Console om vanuit uw app naar de gehoste Azure-portal te gaan. 

#### <a name="tools"></a>Hulpprogramma's
De tools **ping,** **nslookup**en **tracert** werken niet via de console vanwege beveiligingsproblemen. Om de leegte te vullen, twee afzonderlijke tools toegevoegd. Om DNS-functionaliteit te testen, hebben we een tool met de naamnameresolver.exe toegevoegd. De syntaxis is:

    nameresolver.exe hostname [optional: DNS Server]

U **nameresolver** gebruiken om de hostnamen te controleren waarvan uw app afhankelijk is. Op deze manier u testen of u iets verkeerd hebt geconfigureerd met uw DNS of misschien geen toegang hebt tot uw DNS-server. U de DNS-server zien die uw app in de console gebruikt door te kijken naar de omgevingsvariabelen WEBSITE_DNS_SERVER en WEBSITE_DNS_ALT_SERVER.

Met de volgende tool u testen op TCP-connectiviteit met een host- en poortcombinatie. Dit gereedschap wordt **tcpping** genoemd en de syntaxis is:

    tcpping.exe hostname [optional: port]

De **tcpping** utility vertelt u of u een specifieke host en poort bereiken. Het kan alleen succes laten zien als: er is een toepassing luisteren op de host en poort combinatie, en er is netwerktoegang van uw app naar de opgegeven host en poort.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Foutopsporing van toegang tot vNet-gehoste bronnen
Er zijn een aantal dingen die kunnen voorkomen dat uw app een specifieke host en poort bereikt. De meeste van de tijd is het een van de drie dingen:

* **Een firewall staat in de weg.** Als u een firewall in de weg hebt, raakt u de TCP-time-out. De TCP-time-out bedraagt in dit geval 21 seconden. Gebruik het **tcpping-gereedschap** om de connectiviteit te testen. TCP time-outs kunnen te wijten zijn aan veel dingen buiten firewalls, maar daar beginnen. 
* **DNS is niet toegankelijk.** De DNS-time-out bedraagt drie seconden per DNS-server. Als u twee DNS-servers hebt, is de time-out 6 seconden. Gebruik nameresolver om te zien of DNS werkt. Vergeet niet dat u nslookup niet gebruiken, omdat er geen gebruik wordt gemaakt van de DNS waarmee uw VNet is geconfigureerd. Als u niet toegankelijk bent, u een firewall of NSG hebben die de toegang tot DNS blokkeert of deze kan worden uitgeschakeld.

Als deze items uw problemen niet beantwoorden, zoekt u eerst naar zaken als: 

**regionale VNet-integratie**
* is uw bestemming een niet-RFC1918-adres en hoeft u niet WEBSITE_VNET_ROUTE_ALL ingesteld op 1?
* is er een NSG blokkeren uitgang van uw integratie subnet?
* Als u via ExpressRoute of een VPN gaat, is uw on-premises gateway geconfigureerd om verkeer terug te leiden naar Azure? Als u eindpunten in uw VNet bereiken, maar niet on-premises, controleert u uw routes.
* heeft u voldoende machtigingen om delegatie op het integratiesubnet in te stellen? Tijdens de regionale VNet-integratieconfiguratie wordt uw integratiesubnet gedelegeerd aan Microsoft.Web. De VNet-integratie-gebruikersinterface delegert het subnet automatisch aan Microsoft.Web. Als uw account niet over voldoende netwerkmachtigingen beschikt om delegatie in te stellen, hebt u iemand nodig die kenmerken op uw integratiesubnet kan instellen om het subnet te delegeren. Als u het subnet integratie handmatig wilt delegeren, gaat u naar de subnet Gebruikersinterface van Azure Virtual Network en stelt u de delegatie in voor Microsoft.Web. 

**gateway vereist VNet-integratie**
* is het adresbereik van punt tot locatie in de RFC 1918-reeksen (10.0.0.0-10.255.255.255 / 172.16.0-172.31.255.255 / 192.168.0-192.168.255.255)?
* Is de Gateway weergegeven als zijnde in het portaal? Als uw gateway is uitgeschakeld, breng het dan terug naar boven.
* Worden certificaten weergegeven als synchroon of vermoedt u dat de netwerkconfiguratie is gewijzigd?  Als uw certificaten niet synchroon lopen of als u vermoedt dat er een wijziging is aangebracht in uw VNet-configuratie die niet is gesynchroniseerd met uw ASP's, drukt u op 'Synchronisatienetwerk'.
* Als u over een VPN gaat, is de on-premises gateway geconfigureerd om verkeer terug te leiden naar Azure? Als u eindpunten in uw VNet bereiken, maar niet on-premises, controleert u uw routes.
* Probeert u een coëxistentiegateway te gebruiken die zowel point-to-site als ExpressRoute ondersteunt? Coexistence gateways worden niet ondersteund met VNet Integration.

Het opsporen van netwerkproblemen is een uitdaging omdat u daar niet zien wat de toegang tot een specifieke host:port-combinatie blokkeert. Enkele van de oorzaken zijn:

* U hebt een firewall op uw host die de toegang tot de toepassingspoort verhindert van uw point-to-site-IP-bereik. Het kruisen van subnetten vereist vaak Openbare toegang.
* Je doelhost is uitgeschakeld.
* Je aanvraag is uitgevallen.
* Je had de verkeerde IP of hostname.
* Uw applicatie luistert op een andere poort dan u had verwacht. U uw proces-ID koppelen aan de luisterpoort met behulp van netstat -aon op de endpointhost. 
* Uw netwerkbeveiligingsgroepen zijn zo geconfigureerd dat ze de toegang tot uw toepassingshost en -poort van uw point-to-site-IP-bereik verhinderen.

Vergeet niet dat u niet weet welk adres uw app daadwerkelijk zal gebruiken. Het kan elk adres in het integratiesubnet of point-to-site-adresbereik zijn, dus u moet toegang toestaan vanuit het gehele adresbereik. 

Aanvullende foutopsporingsstappen zijn:

* Maak verbinding met een VM in uw VNet en probeer vanaf daar uw resourcehost:port te bereiken. Als u wilt testen op TCP-toegang, gebruikt u de **PowerShell-opdrachttestnetverbinding**. De syntaxis is:

      test-netconnection hostname [optional: -Port]

* Een toepassing op een vm weergeven en de toegang tot die host en poort vanuit de console testen vanuit uw app met behulp van **tcpping**

#### <a name="on-premises-resources"></a>On-premises resources ####

Als uw app geen on-premises bron kan bereiken, controleert u of u de bron van uw VNet bereiken. Gebruik de **powershell-opdracht testnetverbinding** om te controleren op TCP-toegang. Als uw vm uw on-premises bron niet kan bereiken, is uw VPN- of ExpressRoute-verbinding mogelijk niet goed geconfigureerd.

Als uw vnet gehoste VM uw on-premises systeem kan bereiken, maar uw app niet, dan is de oorzaak waarschijnlijk een van de volgende redenen:

* Uw routes zijn niet geconfigureerd met uw subnet of wijzen naar siteadresbereiken in uw on-premises gateway.
* Uw netwerkbeveiligingsgroepen blokkeren de toegang voor uw Point-to-Site IP-bereik.
* Uw on-premises firewalls blokkeren verkeer vanuit uw Point-to-Site IP-bereik.
* U probeert een niet-RFC 1918-adres te bereiken met behulp van de regionale VNet-integratiefunctie.