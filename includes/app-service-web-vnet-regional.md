---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: c731e2a7d4da1a66aabb50b335d526fbb6a0a302
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671531"
---
Met behulp van regionale VNet-integratie heeft uw app toegang tot:

* bronnen in het VNet in dezelfde regio waarmee u integreert 
* bronnen in VNets die naar uw VNet zijn gekeken en zich in dezelfde regio bevinden
* serviceeindpuntbeveiligde services
* bronnen op ExpressRoute-verbindingen
* bronnen in het VNet waaru mee verbonden bent
* bronnen op peered-verbindingen, waaronder ExpressRoute-verbindingen
* privéeindpunten 

Wanneer u VNet-integratie met VNets in dezelfde regio gebruikt, u de volgende Azure Networking-functies gebruiken:

* Network Security Groups (NSGs) - U uitgaand verkeer blokkeren met een netwerkbeveiligingsgroep die op uw integratiesubnet wordt geplaatst. De inkomende regels zijn niet van toepassing omdat u VNet-integratie niet gebruiken om inkomende toegang tot uw app te bieden.
* Routetabellen (UDR's) - U een routetabel op het integratiesubnet plaatsen om uitgaand verkeer te verzenden waar u dat wilt. 

Standaard wordt rfc1918-verkeer door uw app alleen naar uw VNet gerouteerd. Als u al uw uitgaande verkeer naar uw VNet wilt leiden, past u de app-instelling WEBSITE_VNET_ROUTE_ALL toe op uw app. Ga als u de app-instelling configureren:

1. Ga naar de configuratie-gebruikersinterface in uw app-portal. **Nieuwe toepassingsinstelling selecteren**
1. Typ **WEBSITE_VNET_ROUTE_ALL** in het veld Naam en **1** in het veld Waarde

   ![Toepassingsinstelling verstrekken][4]

1. Selecteer **OK**
1. Selecteer **Opslaan**

Als u al uw uitgaande verkeer route in uw VNet, dan zal het onderworpen zijn aan de NSGs en UDR's die worden toegepast op uw integratie subnet. Wanneer u al uw uitgaande verkeer naar uw VNet leidt, zijn uw uitgaande adressen nog steeds de uitgaande adressen die in uw app-eigenschappen worden vermeld, tenzij u routes opgeeft om het verkeer elders te verzenden. 

Er zijn enkele beperkingen met het gebruik van VNet-integratie met VNets in dezelfde regio:

* U geen bronnen bereiken in wereldwijde peering-verbindingen
* De functie is alleen beschikbaar via nieuwere appserviceschaaleenheden die PremiumV2 App Service-abonnementen ondersteunen.
* Het integratiesubnet kan slechts door één App Service-abonnement worden gebruikt
* De functie kan niet worden gebruikt door geïsoleerde-apps die zich in een app-serviceomgeving bevinden
* De functie vereist een ongebruikt subnet dat een /27 is met 32 adressen of groter in een Resource Manager VNet
* De app en het VNet moeten zich in dezelfde regio bevinden
* U een VNet niet verwijderen met een geïntegreerde app. Verwijder de integratie voordat u de VNet verwijdert. 
* U alleen integreren met VNets in hetzelfde abonnement als de app
* U slechts één regionaal VNet-integratie-abonnement per App-serviceabonnement hebben. Meerdere apps in hetzelfde App Service-abonnement kunnen hetzelfde VNet gebruiken. 
* U het abonnement van een app of abonnement niet wijzigen terwijl er een app is die regionale VNet-integratie gebruikt

Voor elk exemplaar van het abonnement wordt één adres gebruikt. Als u uw app schaalt naar vijf exemplaren, worden vijf adressen gebruikt. Aangezien de subnetgrootte na toewijzing niet kan worden gewijzigd, moet u een subnet gebruiken dat groot genoeg is om aan te passen aan de schaal die uw app kan bereiken. Een /26 met 64 adressen is de aanbevolen grootte. Een /26 met 64 adressen is geschikt voor een Premium-abonnement met 30 exemplaren. Wanneer u een abonnement omhoog of omlaag schaalt, hebt u twee keer zoveel adressen nodig voor een korte periode. 

Als u wilt dat uw apps in een ander plan een VNet bereiken dat al is verbonden met apps in een ander abonnement, moet u een ander subnet selecteren dan het subnet dat wordt gebruikt door de reeds bestaande VNet-integratie.  

De functie is in preview voor Linux. De Linux-vorm van de functie ondersteunt alleen bellen naar RFC 1918-adressen (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web--function-app-for-containers"></a>Web / Functie App voor containers

Als u uw app op Linux host met de ingebouwde afbeeldingen, werkt regionale VNet-integratie zonder extra wijzigingen. Als u web/ functie-app voor containers gebruikt, moet u uw dockerafbeelding wijzigen om VNet-integratie te kunnen gebruiken. Gebruik in uw dockerafbeelding de variabele POORT-omgevings als de luisterpoort van de hoofdwebserver in plaats van een hardcoded poortnummer te gebruiken. De poortomgevingsvariabele wordt automatisch ingesteld door het platform op de opstarttijd van de container. Als u SSH gebruikt, moet de SSH-daemon zijn geconfigureerd om te luisteren op het poortnummer dat is opgegeven door de SSH_PORT omgevingsvariabele bij het gebruik van regionale VNet-integratie.  Er is geen ondersteuning voor gateway vereist VNet-integratie op Linux. 

### <a name="service-endpoints"></a>Service-eindpunten

Met regionale VNet-integratie u serviceeindpunten gebruiken.  Als u serviceeindpunten met uw app wilt gebruiken, gebruikt u regionale VNet-integratie om verbinding te maken met een geselecteerde VNet en configureert u serviceeindpunten op het subnet dat u voor de integratie hebt gebruikt. 

### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen

Met netwerkbeveiligingsgroepen u binnenkomend en uitgaand verkeer naar resources in een VNet blokkeren. Een app met regionale VNet-integratie kan [Network Security Group][VNETnsg] gebruiken om uitgaand verkeer naar bronnen in uw VNet of internet te blokkeren. Als u verkeer naar openbare adressen wilt blokkeren, moet u de instelling van de toepassing hebben WEBSITE_VNET_ROUTE_ALL ingesteld op 1. De binnenkomende regels in een NSG zijn niet van toepassing op uw app, omdat VNet-integratie alleen van invloed is op uitgaand verkeer vanuit uw app. Als u binnenkomend verkeer naar uw app wilt beheren, gebruikt u de functie Toegangsbeperkingen. Een NSG die wordt toegepast op uw integratie subnet zal van kracht zijn, ongeacht alle routes toegepast op uw integratie subnet. Als WEBSITE_VNET_ROUTE_ALL was ingesteld op 1 en u geen routes had die van invloed waren op het openbare adresverkeer op uw integratiesubnet, zou al uw uitgaande verkeer nog steeds onderworpen zijn aan NSG's die zijn toegewezen aan uw integratiesubnet. Als WEBSITE_VNET_ROUTE_ALL niet was ingesteld, zouden DEG's alleen worden toegepast op RFC1918-verkeer.

### <a name="routes"></a>Routes

Met routetabellen u uitgaand verkeer van uw app naar de gewenste route leiden. Routetabellen hebben standaard alleen invloed op het bestemmingsverkeer Van RFC1918.  Als u WEBSITE_VNET_ROUTE_ALL op 1 instelt, worden al uw uitgaande gesprekken beïnvloed. Routes die zijn ingesteld op uw integratiesubnet hebben geen invloed op antwoorden op binnenkomende app-verzoeken. Veelvoorkomende bestemmingen kunnen firewallapparaten of gateways zijn. Als u al het uitgaande verkeer on-premises wilt routeren, u een routetabel gebruiken om al het uitgaande verkeer naar uw ExpressRoute-gateway te verzenden. Als u het verkeer naar een gateway route, moet u routes in het externe netwerk instellen om eventuele antwoorden terug te sturen.

De Routes van het Border Gateway Protocol (BGP) hebben ook invloed op uw app-verkeer. Als u BGP-routes hebt vanaf iets als een ExpressRoute-gateway, wordt uw uitgaand verkeer van uw app beïnvloed. Standaard hebben BGP-routes alleen invloed op uw RFC1918-bestemmingsverkeer. Als WEBSITE_VNET_ROUTE_ALL is ingesteld op 1, kan al het uitgaande verkeer worden beïnvloed door uw BGP-routes. 


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/