---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: c731e2a7d4da1a66aabb50b335d526fbb6a0a302
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671531"
---
Door gebruik te maken van regionale VNet-integratie kan uw app toegang tot:

* resources in het VNet in dezelfde regio die u integreert met 
* resources in VNets die zijn gekoppeld aan uw VNet die zich in dezelfde regio bevinden
* Services voor beveiligde service-eind punten
* bronnen over ExpressRoute-verbindingen
* resources in het VNet waarmee u verbinding hebt
* bronnen in peered verbindingen, waaronder ExpressRoute-verbindingen
* Privé-eind punten 

Wanneer u VNet-integratie met VNets in dezelfde regio gebruikt, kunt u de volgende Azure-netwerk functies gebruiken:

* Netwerk beveiligings groepen (Nsg's): u kunt uitgaand verkeer blok keren met een netwerk beveiligings groep die is opgenomen in het subnet met integratie. De regels voor binnenkomende verbindingen zijn niet van toepassing omdat u VNet-integratie niet kunt gebruiken om inkomende toegang tot uw app te bieden.
* Route tabellen (Udr's): u kunt een route tabel plaatsen op het subnet met integratie om uitgaand verkeer te verzenden naar de gewenste locatie. 

Standaard stuurt uw app alleen RFC1918-verkeer naar uw VNet. Als u al het uitgaande verkeer wilt door sturen naar uw VNet, past u de app-instelling WEBSITE_VNET_ROUTE_ALL toe op uw app. De app-instelling configureren:

1. Ga naar de configuratie GEBRUIKERSINTERFACE in de app-Portal. **Nieuwe toepassings instelling** selecteren
1. Typ **WEBSITE_VNET_ROUTE_ALL** in het veld naam en **1** in het veld waarde

   ![Toepassings instelling opgeven][4]

1. Selecteer **OK**
1. Selecteer **Opslaan**

Als u al het uitgaande verkeer naar uw VNet routert, is het onderhevig aan de Nsg's en Udr's die worden toegepast op uw integratie subnet. Wanneer u al het uitgaande verkeer naar uw VNet routert, zijn uw uitgaande adressen nog steeds de uitgaande adressen die worden vermeld in de app-eigenschappen, tenzij u routes opgeeft om het verkeer elders te verzenden. 

Er zijn enkele beperkingen bij het gebruik van VNet-integratie met VNets in dezelfde regio:

* U kunt geen resources bereiken in globale peering-verbindingen
* De functie is alleen beschikbaar vanaf nieuwere App Service schaal eenheden die ondersteuning bieden voor PremiumV2 App Service-plannen.
* Het integratie subnet kan slechts door één App Service plan worden gebruikt
* De functie kan niet worden gebruikt door toepassingen van het geïsoleerde abonnement in een App Service Environment
* Voor de functie is een ongebruikt subnet van a/27 met 32 adressen of groter vereist in een resource manager-VNet
* De app en het VNet moeten zich in dezelfde regio bevinden
* U kunt een VNet met een geïntegreerde app niet verwijderen. Verwijder de integratie voordat u het VNet verwijdert. 
* U kunt alleen integreren met VNets in hetzelfde abonnement als de app
* U kunt slechts één regionale VNet-integratie per App Service plan hebben. Meerdere apps in hetzelfde App Service-abonnement kunnen hetzelfde VNet gebruiken. 
* U kunt het abonnement van een app of een abonnement niet wijzigen terwijl er een app is die gebruikmaakt van regionale VNet-integratie

Er wordt één adres gebruikt voor elk exemplaar van het abonnement. Als u uw app schaalt naar vijf instanties, worden er vijf adressen gebruikt. Aangezien de grootte van het subnet niet kan worden gewijzigd na toewijzing, moet u een subnet gebruiken dat groot genoeg is om te kunnen voldoen aan de schaal van uw app. Een/26 met 64-adressen is de aanbevolen grootte. Een/26 met 64-adressen is geschikt voor een Premium-abonnement met 30 exemplaren. Wanneer u een plan omhoog of omlaag schaalt, hebt u twee keer zoveel adressen nodig voor een korte periode. 

Als u wilt dat uw apps in een ander abonnement een VNet bereiken dat al is verbonden met apps in een ander abonnement, moet u een ander subnet selecteren dan het dat wordt gebruikt door de al bestaande VNet-integratie.  

De functie is beschikbaar als preview-versie voor Linux. Het Linux-formulier van de functie ondersteunt alleen het maken van aanroepen naar RFC 1918-adressen (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web--function-app-for-containers"></a>Web/functie-app voor containers

Als u uw app host op Linux met de ingebouwde installatie kopieën, werkt de regionale VNet-integratie zonder extra wijzigingen. Als u web/functie-app voor containers gebruikt, moet u uw docker-installatie kopie aanpassen om VNet-integratie te kunnen gebruiken. Gebruik in uw docker-installatie kopie de variabele poort omgeving als de gateway poort van de hoofd webserver, in plaats van een hardcoded poort nummer te gebruiken. De variabele poort omgeving wordt automatisch ingesteld door het platform op de opstart tijd van de container. Als u SSH gebruikt, moet de SSH-daemon worden geconfigureerd om te Luis teren naar het poort nummer dat is opgegeven door de SSH_PORT omgevings variabele bij gebruik van regionale VNet-integratie.  Er is geen ondersteuning voor de gateway vereiste VNet-integratie op Linux. 

### <a name="service-endpoints"></a>Service-eind punten

Met regionale VNet-integratie kunt u service-eind punten gebruiken.  Als u service-eind punten wilt gebruiken in combi natie met uw app, gebruikt u de regionale VNet-integratie om verbinding te maken met een geselecteerd VNet en configureert u vervolgens service-eind punten in het subnet dat u voor de integratie hebt gebruikt. 

### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen

Met netwerk beveiligings groepen kunt u binnenkomend en uitgaand verkeer naar resources in een VNet blok keren. Een app die regionale VNet-integratie gebruikt, kan de [netwerk beveiligings groep][VNETnsg] gebruiken om uitgaand verkeer naar resources in uw VNet of Internet te blok keren. Als u verkeer naar open bare adressen wilt blok keren, moet u de toepassings instelling WEBSITE_VNET_ROUTE_ALL instellen op 1. De binnenkomende regels in een NSG zijn niet van toepassing op uw app omdat VNet-integratie alleen invloed heeft op uitgaand verkeer van uw app. Als u inkomend verkeer naar uw app wilt beheren, gebruikt u de functie toegangs beperkingen. Een NSG die wordt toegepast op het integratie subnet, wordt van kracht ongeacht eventuele routes die worden toegepast op het integratie subnet. Als WEBSITE_VNET_ROUTE_ALL is ingesteld op 1 en u geen routes hebt die invloed hebben op het verkeer van open bare adressen op het subnet met integratie, is al uw uitgaand verkeer nog steeds onderhevig aan Nsg's toegewezen aan uw integratie subnet. Als WEBSITE_VNET_ROUTE_ALL niet is ingesteld, wordt Nsg's alleen toegepast op RFC1918-verkeer.

### <a name="routes"></a>Routes

Met route tabellen kunt u uitgaand verkeer vanuit uw app naar de gewenste locatie routeren. Routerings tabellen zijn standaard alleen van invloed op uw RFC1918-doel verkeer.  Als u WEBSITE_VNET_ROUTE_ALL instelt op 1, worden al uw uitgaande oproepen beïnvloed. Routes die zijn ingesteld op het integratie subnet, zijn niet van invloed op antwoorden op aanvragen voor inkomende apps. Algemene doelen kunnen Firewall apparaten of gateways zijn. Als u al het uitgaande verkeer on-premises wilt routeren, kunt u een route tabel gebruiken om al het uitgaande verkeer naar uw ExpressRoute-gateway te verzenden. Als u verkeer naar een gateway stuurt, moet u ervoor zorgen dat routes worden ingesteld in het externe netwerk om antwoorden terug te sturen.

Border Gateway Protocol (BGP)-routes hebben ook invloed op uw app-verkeer. Als u BGP-routes van iets zoals een ExpressRoute-gateway hebt, heeft dit gevolgen voor het uitgaande verkeer van uw app. Standaard heeft BGP-routes alleen invloed op uw RFC1918-doel verkeer. Als WEBSITE_VNET_ROUTE_ALL is ingesteld op 1, kan al het uitgaande verkeer worden beïnvloed door de BGP-routes. 


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/