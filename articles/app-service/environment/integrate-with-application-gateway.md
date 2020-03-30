---
title: Met Application Gateway integreren
description: Meer informatie over het integreren van een app in uw ILB-appserviceomgeving met een Application Gateway in deze end-to-end-walk-through.
author: ccompy
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: dfb6d72b3f8f61e1350101173ecec6134a614edf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687141"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Uw ILB App Service-omgeving integreren met Azure Application Gateway #

De [App Service-omgeving](./intro.md) is een implementatie van Azure App Service in het subnet van het Azure-netwerk van een klant. Het kan worden geïmplementeerd met een openbaar of privé eindpunt voor app-toegang. De implementatie van de App Service-omgeving met een privéeindpunt (dat wil zeggen een interne load balancer) wordt een ILB App Service Environment genoemd.  

Firewalls voor webapplicaties helpen uw webapplicaties te beveiligen door binnenkomend webverkeer te inspecteren om SQL-injecties, Cross-Site Scripting, malware-uploads & toepassing DDoS en andere aanvallen te blokkeren. Het inspecteert ook de reacties van de back-end webservers voor Data Loss Prevention (DLP). U een WAF-apparaat ophalen van de Azure-marktplaats of u de [Azure Application Gateway gebruiken.][appgw]

De Azure Application Gateway is een virtueel toestel dat layer 7 load balancing, SSL offloading en Web Application Firewall (WAF) bescherming biedt. Het kan luisteren op een openbaar IP-adres en routeverkeer naar het eindpunt van uw toepassing. De volgende informatie beschrijft hoe u een WAF-geconfigureerde toepassingsgateway integreren met een app in een ILB-appserviceomgeving.  

De integratie van de applicatiegateway met de ILB App Service Environment is op app-niveau. Wanneer u de toepassingsgateway configureert met uw ILB-appserviceomgeving, doet u dit voor specifieke apps in uw ILB-appserviceomgeving. Deze techniek maakt het hosten van veilige multitenant-applicaties mogelijk in één ILB-appserviceomgeving.  

![Toepassingsgateway die naar de app wijst op een ILB-appserviceomgeving][1]

In deze procedure gaat u het volgende doen:

* Maak een Azure Application Gateway.
* Configureer de toepassingsgateway om een app in uw ILB-appserviceomgeving aan te wijzen.
* Configureer uw app om de aangepaste domeinnaam te eren.
* Bewerk de openbare DNS-hostnaam die naar uw toepassingsgateway verwijst.

## <a name="prerequisites"></a>Vereisten

Als u uw Application Gateway wilt integreren met uw ILB-appserviceomgeving, moet u het:

* Een ILB App-serviceomgeving.
* Een app die wordt uitgevoerd in de ILB-appserviceomgeving.
* Een internetrouteerbare domeinnaam die met uw app kan worden gebruikt in de ILB App Service-omgeving.
* Het ILB-adres dat uw ILB-appserviceomgeving gebruikt. Deze informatie bevindt zich in de portal App Service Environment onder **Instellingen** > **IP-adressen:**

    ![Voorbeeldlijst met IP-adressen die worden gebruikt door de ILB-appserviceomgeving][9]
    
* Een openbare DNS-naam die later wordt gebruikt om naar uw Application Gateway te wijzen. 

Zie [Een ILB-appserviceomgeving maken en gebruiken][ilbase]voor meer informatie over het maken van een ILB-appserviceomgeving.

In dit artikel wordt ervan uitgegaan dat u een toepassingsgateway wilt in hetzelfde virtuele Azure-netwerk waar de App Service-omgeving is geïmplementeerd. Voordat u de toepassingsgateway gaat maken, kiest of maakt u een subnet dat u gebruikt om de gateway te hosten. 

U moet een subnet gebruiken dat niet het subnet is met de naam GatewaySubnet. Als u de Application Gateway in GatewaySubnet plaatst, u later geen virtuele netwerkgateway meer maken. 

U de gateway ook niet in het subnet plaatsen dat uw ILB App Service-omgeving gebruikt. De App Service Omgeving is het enige dat kan worden in dit subnet.

## <a name="configuration-steps"></a>Configuratiestappen ##

1. Ga in de Azure-portal naar **Nieuwe** > **netwerktoepassingsgateway****Network** > .

2. In de **basis:**

   a. Voer **voor Naam**de naam van de toepassingsgateway in.

   b. Selecteer **WAF**voor **Laag**.

   c. Selecteer **bij Abonnement**hetzelfde abonnement dat het virtuele netwerk van de App-serviceomgeving gebruikt.

   d. Maak of selecteer de resourcegroep voor **resourcegroep.**

   e. Selecteer **bij Locatie**de locatie van het virtuele netwerk App Service Environment.

   ![Basisprincipes voor het maken van nieuwe toepassingsgateways][2]

3. Ga als het gaat om **instellingen:**

   a. Selecteer **voor virtueel netwerk**het virtuele netwerk App Service Environment.

   b. Selecteer **bij Subnet**het subnet waarin de toepassingsgateway moet worden geïmplementeerd. Gebruik GatewaySubnet niet, omdat het het maken van VPN-gateways zal voorkomen.

   c. Selecteer Openbaar voor **IP-adrestype** **.**

   d. Selecteer **voor openbaar IP-adres**een openbaar IP-adres. Als je er geen hebt, maak er dan nu een.

   e. Selecteer **HTTP** of **HTTPS**voor **Protocol**. Als u configureert voor HTTPS, moet u een PFX-certificaat verstrekken.

   f. Voor **firewall voor webtoepassingen**u de firewall inschakelen en deze ook instellen voor **detectie** of **preventie naar** eigen inzicht.

   ![Nieuwe instellingen voor het maken van toepassingsgateways][3]
    
4. Controleer **in** de sectie Samenvatting de instellingen en selecteer **OK**. Het kan iets meer dan 30 minuten duren voordat de installatie is voltooid.  

5. Nadat de installatie van uw toepassingsgateway is voltooid, gaat u naar de Portal van de Toepassingsgateway. Selecteer **Backend-groep**. Voeg het ILB-adres toe voor uw ILB-appserviceomgeving.

   ![Backend-pool configureren][4]

6. Nadat het configureren van uw back-endpool is voltooid, selecteert u **Statussondes**. Maak een statussonde voor de domeinnaam die u voor uw app wilt gebruiken. 

   ![Statuscontroles configureren][5]
    
7. Nadat het configureren van uw statussondes is voltooid, selecteert u **HTTP-instellingen**. Bewerk de bestaande instellingen, selecteer **Aangepaste sonde gebruiken**en kies de sonde die u hebt geconfigureerd.

   ![HTTP-instellingen configureren][6]
    
8. Ga naar de sectie **Overzicht** van de toepassingsgateway en kopieer het openbare IP-adres dat uw Application Gateway gebruikt. Stel dat IP-adres in als een A-record voor uw app-domeinnaam of gebruik de DNS-naam voor dat adres in een CNAME-record. Het is eenvoudiger om het openbare IP-adres te selecteren en te kopiëren vanuit de gebruikersinterface van het openbare IP-adres in plaats van het te kopiëren via de koppeling in de sectie **Overzicht** van de toepassingsgateway. 

   ![Toepassingsgatewayportal][7]

9. Stel de aangepaste domeinnaam voor uw app in in uw ILB-appserviceomgeving. Ga naar uw app in de portal en selecteer onder **Instellingen** **aangepaste domeinen**.

   ![Aangepaste domeinnaam instellen in de app][8]

Er vindt u informatie over het instellen van aangepaste domeinnamen voor uw web-apps in het artikel [Aangepaste domeinnamen instellen voor uw web-app.][custom-domain] Maar voor een app in een ILB App Service Environment is er geen validatie op de domeinnaam. Omdat u eigenaar bent van de DNS die de eindpunten van de app beheert, u daar alles in plaatsen wat u wilt. De aangepaste domeinnaam die u in dit geval toevoegt, hoeft niet in uw DNS te zitten, maar moet wel worden geconfigureerd met uw app. 

Nadat de installatie is voltooid en u een korte tijd hebt toegestaan voor het doorgeven van uw DNS-wijzigingen, u uw app openen met behulp van de aangepaste domeinnaam die u hebt gemaakt. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
