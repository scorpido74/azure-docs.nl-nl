---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 2d2a82552a846cedfa5da3bb6ec6df8a40b67732
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671506"
---
Hoewel de functie eenvoudig kan worden ingesteld, betekent dat niet dat uw ervaring geen probleem is. Als u problemen ondervindt met het verkrijgen van toegang tot uw gewenste eind punt, kunt u een aantal hulpprogram ma's gebruiken om de connectiviteit vanuit de app-console te testen. Er zijn twee consoles die u kunt gebruiken. Een van de kudu-console en de andere is de console in de Azure Portal. Als u de kudu-console wilt bereiken vanuit uw app, gaat u naar extra-> kudu. U kunt de Kudo-console ook bereiken op [site naam]. scm. azurewebsites. net. Zodra de website is geladen, gaat u naar het tabblad debug console. Als u naar de Azure Portal gehoste console wilt gaan vanuit uw app, gaat u naar Hulpprogram Ma's->-console. 

#### <a name="tools"></a>Hulpprogramma's
De hulpprogram ma's **ping**, **nslookup**en **tracert** werken niet via de console vanwege beveiligings beperkingen. Als u de void wilt vullen, worden er twee afzonderlijke extra hulp middelen toegevoegd. We hebben een hulp programma met de naam nameresolver. exe toegevoegd om de DNS-functionaliteit te testen. De syntaxis is:

    nameresolver.exe hostname [optional: DNS Server]

U kunt **nameresolver** gebruiken om de hostnamen te controleren waarvan uw app afhankelijk is. Op deze manier kunt u testen of er iets mis is met uw DNS of dat u mogelijk geen toegang hebt tot uw DNS-server. U kunt de DNS-server zien die door uw app wordt gebruikt in de-console door te kijken naar de omgevings variabelen WEBSITE_DNS_SERVER en WEBSITE_DNS_ALT_SERVER.

Met het volgende hulp programma kunt u testen op TCP-connectiviteit met een combi natie van host en poort. Dit hulp programma heet **tcpping** en de syntaxis is:

    tcpping.exe hostname [optional: port]

Het **tcpping** -hulp programma vertelt u of u een specifieke host en poort kunt bereiken. Het kan alleen succes weer geven als: er luistert een toepassing naar de combi natie van host en poort en er is netwerk toegang vanuit uw app naar de opgegeven host en poort.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Fout opsporing voor gehoste resources in VNet
Er zijn een aantal dingen die kunnen voor komen dat uw app een specifieke host en poort bereikt. De meeste tijd is het een van de volgende drie dingen:

* **Een firewall is in de weg.** Als u een firewall op de gewenste manier hebt, bereikt u de TCP-time-out. In dit geval is de TCP-time-out 21 seconden. Gebruik het hulp programma **tcpping** om de connectiviteit te testen. TCP-time-outs kunnen te wijten zijn aan veel dingen buiten firewalls, maar u kunt hier beginnen. 
* **DNS is niet toegankelijk.** De DNS-time-out is drie seconden per DNS-server. Als u twee DNS-servers hebt, is de time-out 6 seconden. Gebruik nameresolver om te controleren of DNS werkt. Houd er rekening mee dat u Nslookup niet kunt gebruiken als dat geen gebruik maakt van de DNS waarvoor uw VNet is geconfigureerd. Als dat niet mogelijk is, kunt u een firewall hebben of NSG de toegang tot DNS blok keren of kan deze worden uitgeschakeld.

Als dat niet het geval is, zoekt u eerst naar zaken als: 

**regionale VNet-integratie**
* is uw doel een niet-RFC1918-adres en hebt u geen WEBSITE_VNET_ROUTE_ALL ingesteld op 1?
* is er een NSG die uitkomend verkeer van uw integratie subnet blokkeert?
* Als u een ExpressRoute of een VPN-verbinding wilt maken, is uw on-premises gateway geconfigureerd voor het routeren van verkeer naar Azure? Als u eind punten in uw VNet maar niet on-premises kunt bereiken, controleert u uw routes.
* hebt u voldoende machtigingen voor het instellen van delegering op het subnet met integratie? Tijdens de configuratie van de regionale VNet-integratie wordt uw integratie-subnet gedelegeerd naar micro soft. Web. Het subnet wordt automatisch door de gebruikers interface van VNet-integratie naar micro soft. Web gedelegeerd. Als uw account niet voldoende netwerk machtigingen heeft voor het instellen van delegering, hebt u iemand nodig die kenmerken kan instellen op het subnet met integratie om het subnet te delegeren. Als u het subnet met integratie hand matig wilt overdragen, gaat u naar de gebruikers interface van het Azure Virtual Network-subnet en stelt u de overdracht in voor micro soft. Web 

**vereiste VNet-integratie voor gateway**
* is het punt-naar-site-adres bereik in de RFC 1918-bereiken (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Wordt de gateway in de portal weer gegeven? Als uw gateway niet beschikbaar is, zet u deze terug.
* Worden certificaten weer gegeven als gesynchroniseerd of vermoedt u dat de netwerk configuratie is gewijzigd?  Als uw certificaten niet zijn gesynchroniseerd of als u vermoedt dat er een wijziging is aangebracht in uw VNet-configuratie die niet is gesynchroniseerd met uw ASPs, klikt u op ' netwerk synchroniseren '.
* Als u een VPN-verbinding wilt maken, is de on-premises gateway die is geconfigureerd voor het routeren van verkeer naar Azure? Als u eind punten in uw VNet maar niet on-premises kunt bereiken, controleert u uw routes.
* probeert u een gateway voor samen werking te gebruiken die beide Point-to-site-en ExpressRoute ondersteunt? Naast elkaar aanwezige gateways worden niet ondersteund met VNet-integratie.

Fout opsporing in netwerk problemen is een uitdaging omdat u niet kunt zien wat de toegang tot een specifieke host blokkeert: poort combinatie. Enkele oorzaken zijn onder andere:

* U hebt een firewall op uw host waarmee u de toegang tot de toepassings poort van uw Point-site-IP-bereik kunt voor komen. Voor kruisende subnetten is vaak open bare toegang vereist.
* De doelhost is niet beschikbaar.
* Uw toepassing is niet beschikbaar.
* U had een onjuist IP-adres of hostnaam.
* Uw toepassing luistert op een andere poort dan verwacht. U kunt uw proces-ID met de luister poort overeenkomen met ' netstat-Aon ' op de host van het eind punt. 
* Uw netwerk beveiligings groepen worden zo geconfigureerd dat ze de toegang tot uw toepassingshost en poort van uw punt tot het IP-bereik van de site verhinderen.

Houd er rekening mee dat u niet weet welk adres uw app daad werkelijk zal gebruiken. Dit kan elk adres zijn in het integratie subnet of het adres bereik van punt-naar-site. u moet dus toegang tot het hele adres bereik toestaan. 

Aanvullende stappen voor fout opsporing zijn onder andere:

* Maak verbinding met een virtuele machine in uw VNet en probeer uw bronhost: poort te bereiken. Als u wilt testen op TCP-toegang, gebruikt u de Power shell **-opdracht test-NetConnection**. De syntaxis is:

      test-netconnection hostname [optional: -Port]

* een toepassing op een virtuele machine weer geven en de toegang tot die host en poort testen vanuit de console vanuit uw app met behulp van **tcpping**

#### <a name="on-premises-resources"></a>On-premises resources ####

Als uw app een on-premises resource niet kan bereiken, controleert u of u de bron kunt bereiken vanuit uw VNet. Gebruik de Power shell **-opdracht test-NetConnection** om te controleren op TCP-toegang. Als uw virtuele machine uw on-premises resource niet kan bereiken, is uw VPN-of ExpressRoute-verbinding mogelijk niet juist geconfigureerd.

Als uw door VNet gehoste VM uw on-premises systeem kan bereiken, maar uw app niet kan, kan dit een van de volgende oorzaken hebben:

* Uw routes zijn niet geconfigureerd met uw subnet of verwijzen naar site adresbereiken in uw on-premises gateway.
* Uw netwerk beveiligings groepen blok keren de toegang tot uw punt-naar-site-IP-bereik.
* Uw on-premises firewalls blok keren verkeer van uw punt-naar-site-IP-bereik.
* U probeert een niet-RFC 1918-adres te bereiken met behulp van de functie voor regionale VNet-integratie.