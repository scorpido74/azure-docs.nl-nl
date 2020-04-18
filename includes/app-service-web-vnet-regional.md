---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: f7208307df51ecefb76f9adaedea59b327cdc19e
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604880"
---
Met behulp van regionale VNet-integratie heeft uw app toegang tot:

* Bronnen in een VNet in dezelfde regio als uw app.
* Resources in VNets die naar de VNet zijn gekeken, waarmee uw app is geïntegreerd.
* Serviceendpoint beveiligde services.
* Bronnen voor Azure ExpressRoute-verbindingen.
* Bronnen in het VNet waarmee u bent geïntegreerd.
* Bronnen voor peered-verbindingen, waaronder Azure ExpressRoute-verbindingen.
* Privéeindpunten 

Wanneer u VNet-integratie met VNets in dezelfde regio gebruikt, u de volgende Azure-netwerkfuncties gebruiken:

* **Netwerkbeveiligingsgroepen (NSGs)**: U uitgaand verkeer blokkeren met een NSG die op uw integratiesubnet is geplaatst. De binnenkomende regels zijn niet van toepassing omdat u VNet-integratie niet gebruiken om inkomende toegang tot uw app te bieden.
* **Routetabellen (UDR's)**: U een routetabel op het integratiesubnet plaatsen om uitgaand verkeer te verzenden waar u dat wilt.

Standaard leidt uw app alleen RFC1918-verkeer naar uw VNet. Als u al uw uitgaande verkeer naar uw VNet wilt leiden, past u de app-instelling WEBSITE_VNET_ROUTE_ALL toe op uw app. Ga als u de app-instelling configureren:

1. Ga naar **Configuration** de configuratie-gebruikersinterface in uw app-portal. Selecteer **Nieuwe toepassingsinstelling**selecteren .
1. Voer **WEBSITE_VNET_ROUTE_ALL** in het vak **Naam** in en voer **1** in het vak **Waarde** in.

   ![Toepassingsinstelling verstrekken][4]

1. Selecteer **OK**.
1. Selecteer **Opslaan**.

Als u al uw uitgaande verkeer naar uw VNet route, het is onderworpen aan de NSGs en UDR's die worden toegepast op uw integratie subnet. Wanneer u al uw uitgaande verkeer naar uw VNet leidt, zijn uw uitgaande adressen nog steeds de uitgaande adressen die in uw app-eigenschappen worden vermeld, tenzij u routes opgeeft om het verkeer elders te verzenden.

Er zijn enkele beperkingen met het gebruik van VNet-integratie met VNets in dezelfde regio:

* U geen bronnen bereiken via wereldwijde peering-verbindingen.
* De functie is alleen beschikbaar via nieuwere Azure App Service-schaaleenheden die PremiumV2 App Service-abonnementen ondersteunen.
* Het integratiesubnet kan slechts door één App Service-abonnement worden gebruikt.
* De functie kan niet worden gebruikt door apps voor geïsoleerde apps die zich in een App-serviceomgeving bevinden.
* De functie vereist een ongebruikt subnet dat een /27 is met 32 adressen of groter in een Azure Resource Manager VNet.
* De app en het VNet moeten zich in dezelfde regio bevinden.
* U een VNet niet verwijderen met een geïntegreerde app. Verwijder de integratie voordat u het VNet verwijdert.
* U alleen integreren met VNets in hetzelfde abonnement als de app.
* U slechts één regionaal VNet-integratie-abonnement per App-serviceabonnement hebben. Meerdere apps in hetzelfde App Service-abonnement kunnen hetzelfde VNet gebruiken.
* U het abonnement van een app of abonnement niet wijzigen terwijl er een app is die regionale VNet-integratie gebruikt.
* Uw app kan adressen in Azure DNS Private Zones niet oplossen zonder configuratiewijzigingen

Voor elk exemplaar van het abonnement wordt één adres gebruikt. Als u uw app schaalt naar vijf exemplaren, worden vijf adressen gebruikt. Aangezien de subnetgrootte na toewijzing niet kan worden gewijzigd, moet u een subnet gebruiken dat groot genoeg is om aan te passen aan de schaal die uw app kan bereiken. Een /26 met 64 adressen is de aanbevolen grootte. Een /26 met 64 adressen biedt plaats aan een Premium-abonnement met 30 exemplaren. Wanneer u een abonnement omhoog of omlaag schaalt, hebt u twee keer zoveel adressen nodig voor een korte periode.

Als u wilt dat uw apps in een ander plan een VNet bereiken waarmee apps al zijn verbonden in een ander abonnement, selecteert u een ander subnet dan het subnet dat wordt gebruikt door de reeds bestaande VNet-integratie.

De functie is in preview voor Linux. De Linux-vorm van de functie ondersteunt alleen bellen naar RFC 1918-adressen (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web-or-function-app-for-containers"></a>Web- of functie-app voor containers

Als u uw app op Linux host met de ingebouwde afbeeldingen, werkt regionale VNet-integratie zonder extra wijzigingen. Als u web- of functie-app voor containers gebruikt, moet u de dockerafbeelding wijzigen om VNet-integratie te gebruiken. Gebruik in uw dockerafbeelding de variabele POORT-omgevings als de luisterpoort van de hoofdwebserver in plaats van een hardcoded poortnummer te gebruiken. De poortomgevingsvariabele wordt automatisch ingesteld door het platform op de opstarttijd van de container. Als u SSH gebruikt, moet de SSH-daemon zijn geconfigureerd om te luisteren op het poortnummer dat is opgegeven door de SSH_PORT omgevingsvariabele wanneer u regionale VNet-integratie gebruikt. Er is geen ondersteuning voor door gateway vereist VNet-integratie op Linux.

### <a name="service-endpoints"></a>Service-eindpunten

Met regionale VNet-integratie u serviceeindpunten gebruiken. Als u serviceeindpunten wilt gebruiken met uw app, gebruikt u regionale VNet-integratie om verbinding te maken met een geselecteerde VNet en configureert u serviceeindpunten met de doelservice op het subnet dat u voor de integratie hebt gebruikt. Als u vervolgens toegang wilt krijgen tot een service via serviceeindpunten:

1. regionale VNet-integratie configureren met uw web-app
1. ga naar de doelservice en configureer serviceeindpunten op het subnet dat wordt gebruikt voor integratie

### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen

U netwerkbeveiligingsgroepen gebruiken om binnenkomend en uitgaand verkeer naar bronnen in een VNet te blokkeren. Een app die regionale VNet-integratie gebruikt, kan een [netwerkbeveiligingsgroep][VNETnsg] gebruiken om uitgaand verkeer naar bronnen in uw VNet of internet te blokkeren. Als u verkeer naar openbare adressen wilt blokkeren, moet u de instelling van de toepassing hebben WEBSITE_VNET_ROUTE_ALL ingesteld op 1. De binnenkomende regels in een NSG zijn niet van toepassing op uw app omdat VNet-integratie alleen van invloed is op uitgaand verkeer vanuit uw app.

Als u binnenkomend verkeer naar uw app wilt beheren, gebruikt u de functie Toegangsbeperkingen. Een NSG die is toegepast op uw integratiesubnet is van kracht, ongeacht de routes die op uw integratiesubnet worden toegepast. Als WEBSITE_VNET_ROUTE_ALL is ingesteld op 1 en u geen routes hebt die van invloed zijn op het openbare adresverkeer op uw integratiesubnet, is al uw uitgaande verkeer nog steeds onderworpen aan NSG's die zijn toegewezen aan uw integratiesubnet. Als WEBSITE_VNET_ROUTE_ALL niet is ingesteld, worden NSG's alleen toegepast op RFC1918-verkeer.

### <a name="routes"></a>Routes

U routetabellen gebruiken om uitgaand verkeer van uw app naar de gewenste plaats te leiden. Routetabellen hebben standaard alleen invloed op het bestemmingsverkeer Van RFC1918. Als u WEBSITE_VNET_ROUTE_ALL op 1 instelt, worden al uw uitgaande gesprekken beïnvloed. Routes die zijn ingesteld op uw integratiesubnet hebben geen invloed op antwoorden op binnenkomende app-aanvragen. Veelvoorkomende bestemmingen kunnen firewallapparaten of gateways zijn.

Als u al het uitgaande verkeer on-premises wilt routeren, u een routetabel gebruiken om al het uitgaande verkeer naar uw ExpressRoute-gateway te verzenden. Als u het verkeer naar een gateway route, moet u routes in het externe netwerk instellen om eventuele antwoorden terug te sturen.

NGP-routes (Border Gateway Protocol) hebben ook invloed op uw app-verkeer. Als u BGP-routes hebt vanaf iets als een ExpressRoute-gateway, wordt uw uitgaand verkeer van uw app beïnvloed. Standaard zijn BGP-routes alleen van invloed op uw RFC1918-bestemmingsverkeer. Als WEBSITE_VNET_ROUTE_ALL is ingesteld op 1, kan al het uitgaande verkeer worden beïnvloed door uw BGP-routes.

### <a name="azure-dns-private-zones"></a>Azure DNS-privézones 

Nadat uw app is geïntegreerd met uw VNet, maakt deze gebruik van dezelfde DNS-server waarmee uw VNet is geconfigureerd. Standaard werkt uw app niet met Azure DNS Private Zones. Als u wilt werken met Azure DNS-privézones, moet u de volgende app-instellingen toevoegen:

1. WEBSITE_DNS_SERVER met waarde 168.63.129,16 
1. WEBSITE_VNET_ROUTE_ALL met waarde 1

Deze instellingen sturen al uw uitgaande oproepen vanuit uw app naar uw VNet, naast het inschakelen van uw app om Azure DNS-privézones te gebruiken.

### <a name="private-endpoints"></a>Privéeindpunten

Als u wilt bellen naar [privéeindpunten,][privateendpoints]moet u integreren met Azure DNS Private Zones of het privéeindpunt beheren in de DNS-server die door uw app wordt gebruikt. 

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
