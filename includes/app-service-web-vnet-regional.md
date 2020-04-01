---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: e0db3ce7d31b838ca6f7d566083a33ee215d3399
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419523"
---
Met behulp van regionale VNet-integratie heeft uw app toegang tot:

* Bronnen in het virtuele netwerk in dezelfde regio waarmee u integreert.
* Bronnen in virtuele netwerken die zijn gekeken naar uw virtuele netwerk die zich in dezelfde regio bevinden.
* Serviceendpoint beveiligde services.
* Bronnen voor Azure ExpressRoute-verbindingen.
* Bronnen in het virtuele netwerk waarop u bent verbonden.
* Bronnen voor peered-verbindingen, waaronder Azure ExpressRoute-verbindingen.
* Privé eindpunten.

Wanneer u VNet-integratie met virtuele netwerken in dezelfde regio gebruikt, u de volgende Azure-netwerkfuncties gebruiken:

* **Netwerkbeveiligingsgroepen (NSGs)**: U uitgaand verkeer blokkeren met een NSG die op uw integratiesubnet is geplaatst. De binnenkomende regels zijn niet van toepassing omdat u VNet-integratie niet gebruiken om inkomende toegang tot uw app te bieden.
* **Routetabellen (UDR's)**: U een routetabel op het integratiesubnet plaatsen om uitgaand verkeer te verzenden waar u dat wilt.

Standaard leidt uw app alleen RFC1918-verkeer naar uw virtuele netwerk. Als u al uw uitgaande verkeer naar uw virtuele netwerk wilt leiden, past u de app-instelling WEBSITE_VNET_ROUTE_ALL toe op uw app. Ga als u de app-instelling configureren:

1. Ga naar **Configuration** de configuratie-gebruikersinterface in uw app-portal. Selecteer **Nieuwe toepassingsinstelling**selecteren .
1. Voer **WEBSITE_VNET_ROUTE_ALL** in het vak **Naam** in en voer **1** in het vak **Waarde** in.

   ![Toepassingsinstelling verstrekken][4]

1. Selecteer **OK**.
1. Selecteer **Opslaan**.

Als u al uw uitgaande verkeer naar uw virtuele netwerk routeert, is dit onderworpen aan de NSG's en UDR's die worden toegepast op uw integratiesubnet. Wanneer u al uw uitgaande verkeer naar uw virtuele netwerk leidt, zijn uw uitgaande adressen nog steeds de uitgaande adressen die in uw app-eigenschappen worden vermeld, tenzij u routes opgeeft om het verkeer elders te verzenden.

Er zijn enkele beperkingen met het gebruik van VNet-integratie met virtuele netwerken in dezelfde regio:

* U geen bronnen bereiken via wereldwijde peering-verbindingen.
* De functie is alleen beschikbaar via nieuwere Azure App Service-schaaleenheden die PremiumV2 App Service-abonnementen ondersteunen.
* Het integratiesubnet kan slechts door één App Service-abonnement worden gebruikt.
* De functie kan niet worden gebruikt door apps voor geïsoleerde apps die zich in een App-serviceomgeving bevinden.
* De functie vereist een ongebruikt subnet dat een /27 is met 32 adressen of groter in een virtueel Azure Resource Manager-netwerk.
* De app en het virtuele netwerk moeten zich in dezelfde regio bevinden.
* U een virtueel netwerk niet verwijderen met een geïntegreerde app. Verwijder de integratie voordat u het virtuele netwerk verwijdert.
* U alleen integreren met virtuele netwerken in hetzelfde abonnement als de app.
* U slechts één regionaal VNet-integratie-abonnement per App-serviceabonnement hebben. Meerdere apps in hetzelfde App Service-abonnement kunnen hetzelfde virtuele netwerk gebruiken.
* U het abonnement van een app of abonnement niet wijzigen terwijl er een app is die regionale VNet-integratie gebruikt.

Voor elk exemplaar van het abonnement wordt één adres gebruikt. Als u uw app schaalt naar vijf exemplaren, worden vijf adressen gebruikt. Aangezien de subnetgrootte na toewijzing niet kan worden gewijzigd, moet u een subnet gebruiken dat groot genoeg is om aan te passen aan de schaal die uw app kan bereiken. Een /26 met 64 adressen is de aanbevolen grootte. Een /26 met 64 adressen biedt plaats aan een Premium-abonnement met 30 exemplaren. Wanneer u een abonnement omhoog of omlaag schaalt, hebt u twee keer zoveel adressen nodig voor een korte periode.

Als u wilt dat uw apps in een ander plan een virtueel netwerk bereiken waarmee apps al zijn verbonden in een ander abonnement, selecteert u een ander subnet dan het subnet dat wordt gebruikt door de reeds bestaande VNet-integratie.

De functie is in preview voor Linux. De Linux-vorm van de functie ondersteunt alleen bellen naar RFC 1918-adressen (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web-or-function-app-for-containers"></a>Web- of functie-app voor containers

Als u uw app op Linux host met de ingebouwde afbeeldingen, werkt regionale VNet-integratie zonder extra wijzigingen. Als u web- of functie-app voor containers gebruikt, moet u de dockerafbeelding wijzigen om VNet-integratie te gebruiken. Gebruik in uw dockerafbeelding de variabele POORT-omgevings als de luisterpoort van de hoofdwebserver in plaats van een hardcoded poortnummer te gebruiken. De poortomgevingsvariabele wordt automatisch ingesteld door het platform op de opstarttijd van de container. Als u SSH gebruikt, moet de SSH-daemon zijn geconfigureerd om te luisteren op het poortnummer dat is opgegeven door de SSH_PORT omgevingsvariabele wanneer u regionale VNet-integratie gebruikt. Er is geen ondersteuning voor door gateway vereist VNet-integratie op Linux.

### <a name="service-endpoints"></a>Service-eindpunten

Met regionale VNet-integratie u serviceeindpunten gebruiken. Als u serviceeindpunten wilt gebruiken met uw app, gebruikt u regionale VNet-integratie om verbinding te maken met een geselecteerd virtueel netwerk. Configureer vervolgens serviceeindpunten op het subnet dat u voor de integratie hebt gebruikt.

### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen

U netwerkbeveiligingsgroepen gebruiken om binnenkomend en uitgaand verkeer naar bronnen in een virtueel netwerk te blokkeren. Een app die regionale VNet-integratie gebruikt, kan een [netwerkbeveiligingsgroep][VNETnsg] gebruiken om uitgaand verkeer naar bronnen in uw virtuele netwerk of internet te blokkeren. Als u verkeer naar openbare adressen wilt blokkeren, moet u de instelling van de toepassing hebben WEBSITE_VNET_ROUTE_ALL ingesteld op 1. De binnenkomende regels in een NSG zijn niet van toepassing op uw app omdat VNet-integratie alleen van invloed is op uitgaand verkeer vanuit uw app.

Als u binnenkomend verkeer naar uw app wilt beheren, gebruikt u de functie Toegangsbeperkingen. Een NSG die is toegepast op uw integratiesubnet is van kracht, ongeacht de routes die op uw integratiesubnet worden toegepast. Als WEBSITE_VNET_ROUTE_ALL is ingesteld op 1 en u geen routes hebt die van invloed zijn op het openbare adresverkeer op uw integratiesubnet, is al uw uitgaande verkeer nog steeds onderworpen aan NSG's die zijn toegewezen aan uw integratiesubnet. Als WEBSITE_VNET_ROUTE_ALL niet is ingesteld, worden NSG's alleen toegepast op RFC1918-verkeer.

### <a name="routes"></a>Routes

U routetabellen gebruiken om uitgaand verkeer van uw app naar de gewenste plaats te leiden. Routetabellen hebben standaard alleen invloed op het bestemmingsverkeer Van RFC1918. Als u WEBSITE_VNET_ROUTE_ALL op 1 instelt, worden al uw uitgaande gesprekken beïnvloed. Routes die zijn ingesteld op uw integratiesubnet hebben geen invloed op antwoorden op binnenkomende app-aanvragen. Veelvoorkomende bestemmingen kunnen firewallapparaten of gateways zijn.

Als u al het uitgaande verkeer on-premises wilt routeren, u een routetabel gebruiken om al het uitgaande verkeer naar uw ExpressRoute-gateway te verzenden. Als u het verkeer naar een gateway route, moet u routes in het externe netwerk instellen om eventuele antwoorden terug te sturen.

NGP-routes (Border Gateway Protocol) hebben ook invloed op uw app-verkeer. Als u BGP-routes hebt vanaf iets als een ExpressRoute-gateway, wordt uw uitgaand verkeer van uw app beïnvloed. Standaard zijn BGP-routes alleen van invloed op uw RFC1918-bestemmingsverkeer. Als WEBSITE_VNET_ROUTE_ALL is ingesteld op 1, kan al het uitgaande verkeer worden beïnvloed door uw BGP-routes.


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/