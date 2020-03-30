---
title: Een ILB ASE maken met ARM
description: Meer informatie over het maken van een App Service-omgeving met een interne load balancer (ILB ASE) met Azure Resource Manager-sjablonen. Isoleer uw apps volledig van het internet.
author: ccompy
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 98345e8585a3f6653659e0d41eb5c3308a0a6634
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80057421"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Een interne serviceomgeving voor load balancer maken en gebruiken 

De Azure App Service-omgeving is een implementatie van Azure App Service in een subnet in een Extern Azure-netwerk (VNet). Er zijn twee manieren om een Azure App Service-omgeving (ASE) te implementeren: 

- Met een VIP-adres op een extern IP-adres, vaak aangeduid als Externe AS-omgeving.
- Met een VIP-adres op een intern IP-adres, vaak aangeduid als een ILB AS-omgeving omdat het interne eindpunt een ILB (Internal Load Balancer) is. 

In dit artikel wordt uitgelegd hoe u een ILB AS-omgeving maakt. Zie [Inleiding tot app-serviceomgevingen][Intro]voor een overzicht over de ASE. Zie [Create an External ASE][MakeExternalASE] (Een Externe AS-omgeving maken) voor informatie over het maken van een Externe AS-omgeving.

## <a name="overview"></a>Overzicht 

U kunt een AS-omgeving implementeren met een eindpunt dat toegankelijk is via internet of met een IP-adres in uw VNet. De AS-omgeving moet zijn geïmplementeerd met een ILB om het IP-adres in te stellen op een VNet-adres. Wanneer u uw ASE implementeert met een ILB, moet u de naam van uw ASE opgeven. De naam van uw ASE wordt gebruikt in het domeinachtervoegsel voor de apps in uw ASE.  Het domeinachtervoegsel voor uw &lt;ILB&gt;ASE is ASE-naam .appserviceenvironment.net. Apps die zijn gemaakt in een ILB ASE worden niet in de openbare DNS geplaatst. 

In eerdere versies van de ILB ASE moest u een domeinachtervoegsel en een standaardcertificaat voor HTTPS-verbindingen verstrekken. Het domeinachtervoegsel wordt niet meer verzameld bij ILB ASE-creatie en er wordt ook geen standaardcertificaat meer verzameld. Wanneer u nu een ILB ASE maakt, wordt het standaardcertificaat geleverd door Microsoft en wordt het vertrouwd door de browser. U nog steeds aangepaste domeinnamen instellen op apps in uw ASE en certificaten instellen op die aangepaste domeinnamen. 

Met een ILB ASE u dingen doen zoals:

-   Host intranetapplicaties veilig in de cloud, die u via een site-to-site of ExpressRoute openen.
-   Apps beveiligen met een WAF-apparaat
-   Apps die niet worden vermeld op openbare DNS-servers, hosten in de cloud.
-   Back-end-apps met internetisolatie maken, waarmee front-end-apps veilig kunnen worden geïntegreerd.

### <a name="disabled-functionality"></a>Uitgeschakelde functionaliteit ###

Er is een aantal dingen dat u niet kunt doen wanneer u een ILB AS-omgeving gebruikt:

-   Op IP-gebaseerd SSL gebruiken.
-   IP-adressen toewijzen aan specifieke apps.
-   Een certificaat kopen en gebruiken met een app via Azure Portal. U kunt certificaten rechtstreeks bij een certificeringsinstantie verkrijgen en ze gebruiken met uw apps. U kunt ze niet verkrijgen via Azure Portal.

## <a name="create-an-ilb-ase"></a>Een ILB AS-omgeving maken ##

Ga als volgt te werk om een ILB AS-omgeving te maken:

1. Selecteer in de Azure-portal de optie Een**resourceWeb** >  **Create a resource** > **App-serviceomgeving maken**.

2. Selecteer uw abonnement.

3. Selecteer of maak een resourcegroep.

4. Voer de naam van uw app-serviceomgeving in.

5. Selecteer virtueel IP-type intern.

    ![ASE maken](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

6. Netwerken selecteren

7. Selecteer of maak een virtueel netwerk. Als u hier een nieuwe VNet maakt, wordt deze gedefinieerd met een adresbereik van 192.168.250.0/23. Als u een VNet wilt maken met een ander adresbereik of in een andere brongroep dan de ASE, gebruikt u de portal voor het maken van Azure Virtual Network. 

8. Selecteer of maak een leeg subnet. Als u een subnet wilt selecteren, moet het leeg zijn en niet worden gedelegeerd. De subnetgrootte kan niet worden gewijzigd nadat de ASE is gemaakt. We raden een grootte aan van `/24`. Dit formaat bevat 256 adressen en kan de grootst mogelijke AS-omgeving verwerken en voldoen aan alle schaalbehoeften. 

    ![ASE-netwerken][1]

7. Selecteer **Controleren en maken** en selecteer Vervolgens **Maken**.

## <a name="create-an-app-in-an-ilb-ase"></a>Een app maken in een ILB AS-omgeving ##

Het maken van een app in een ILB AS-omgeving werkt hetzelfde als het maken van een app in een AS-omgeving.

1. Selecteer in de Azure-portal de optie **Een resource** > **Web** > **Web App maken**.

1. Voer de naam van de app in.

1. Selecteer het abonnement.

1. Selecteer of maak een resourcegroep.

1. Selecteer uw publicatie-, runtimestack- en besturingssysteem.

1. Selecteer een locatie waar de locatie een bestaande ILB ASE is.  U ook een nieuwe ASE maken tijdens het maken van apps door een isolated App Service-abonnement te selecteren. Als u een nieuwe ASE wilt maken, selecteert u de regio waarin u de ASE wilt maken.

1. Selecteer of maak een App Service-plan. 

1. Selecteer **Controleren en maken** en selecteer **Maken** wanneer u er klaar voor bent.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>WebJobs, Functions en de ILB AS-omgeving 

Een ILB AS-omgeving biedt ondersteuning voor zowel Functions als WebJobs. Als u echter met deze wilt werken via de portal, hebt u netwerktoegang tot de SCM-site nodig.  Dit betekent dat de host van de browser zich in het virtuele netwerk moet bevinden of ermee moet zijn verbonden. Als uw ILB ASE een domeinnaam heeft die niet eindigt in *appserviceenvironment.net,* moet u ervoor zorgen dat uw browser erop vertrouwt dat het HTTPS-certificaat wordt gebruikt door uw scm-site.

## <a name="dns-configuration"></a>DNS-configuratie 

Wanneer u een extern VIP-adres gebruikt, wordt de DNS-server beheerd met Azure. Elke app die is gemaakt in de AS-omgeving wordt automatisch toegevoegd aan Azure DNS, wat een openbaar DNS is. In een ILB AS-omgeving moet u uw eigen DNS beheren. Het domeinachtervoegsel dat wordt gebruikt bij een ILB ASE is afhankelijk van de naam van de ASE. Het domeinachtervoegsel is * &lt;&gt;ASE-naam .appserviceenvironment.net*. Het IP-adres voor uw ILB bevindt zich in de portal onder **IP-adressen.** 

Ga als een te meer met de andere DNS:

- een zone maken voor * &lt;&gt;ASE-naam .appserviceenvironment.net*
- een A-record maken in die zone die * naar het IP-adres van ILB verwijst
- een A-record in die zone maken die @ naar het IP-adres van ILB verwijst
- een zone maken in * &lt;&gt;ASE-naam .appserviceenvironment.net* met de naam scm
- een A-record maken in de scm-zone die * naar het IP-adres van ILB verwijst

## <a name="publish-with-an-ilb-ase"></a>Publiceren met een ILB AS-omgeving

Elke app die wordt gemaakt, heeft twee eindpunten. In een ILB ASE heb je * &lt;de naam&gt;van de app.&lt; ILB&gt; ASE-domein* en * &lt;app-naam&gt;.scm.&lt; ILB ASE-domein&gt;*. 

De SCM-sitenaam leidt naar de Kudu-console, genaamd de **Geavanceerde portal**, binnen Azure Portal. Met behulp van de Kudu-console kunt u omgevingsvariabelen bekijken, de schijf verkennen, een console gebruiken, en nog veel meer. Zie [Kudu-console voor Azure App Service][Kudu] voor meer informatie. 

Op internet gebaseerde CI-systemen, zoals GitHub en Azure DevOps, werken nog steeds met een ILB AS-omgeving, als de buildagent toegankelijk is via internet en zich op hetzelfde netwerk bevindt als de ILB AS-omgeving. Als de buildagent dus, in het geval van Azure DevOps, is gemaakt in hetzelfde VNET als de ILB AS-omgeving (verschillende subnetten vormen geen probleem), kan met deze agent code worden opgehaald uit Azure DevOps-git en worden geïmplementeerd in de ILB AS-omgeving. Als u niet zelf een buildagent wilt maken, moet u een CI-systeem met een pull-model gebruiken, zoals Dropbox.

De publicatie-eindpunten voor apps in een ILB AS-omgeving maken gebruik van het domein waarmee de ILB AS-omgeving is gemaakt. Dit domein wordt weergegeven in het publicatieprofiel van de app en in het portalblad van de app **(Overzicht** > **Essentials** en ook **Eigenschappen).** Als u een ILB ASE met het domein achtervoegsel * &lt;ASE naam&gt;.appserviceenvironment.net*, en een app met de naam *mytest*, gebruik *&lt; mytest. ASE-naam&gt;.appserviceenvironment.net* voor FTP en *mytest.scm.contoso.net* voor webimplementatie.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Een ILB ASE configureren met een WAF-apparaat ##

U een WAF-apparaat (Web Application Firewall) combineren met uw ILB ASE om alleen de apps die u wilt blootstellen aan het internet en de rest alleen toegankelijk te houden in het VNet. Zo u onder andere veilige multi-tier applicaties bouwen.

Zie Een firewall voor [webtoepassingen configureren met uw App Service-omgeving][ASEWAF]voor meer informatie over het configureren van uw ILB ASE met een WAF-apparaat. In dit artikel leest u hoe u een virtueel Barracuda-apparaat gebruikt met de AS-omgeving. Een andere optie is het gebruik van Azure Application Gateway. Application Gateway maakt gebruik van de OWASP-kernregels om alle toepassingen te beveiligen die erachter zijn geplaatst. Zie [Introduction to the Azure web application firewall][AppGW] (Inleiding tot de WAF (Web Application Firewall) in Azure) voor meer informatie over Application Gateway.

## <a name="ilb-ases-made-before-may-2019"></a>ILB ASEs gemaakt vóór mei 2019

ILB ASEs die vóór mei 2019 zijn gemaakt, verplichtten u het domeinachtervoegsel in te stellen tijdens ase-creatie. Ze verplichtten je ook om een standaardcertificaat te uploaden dat was gebaseerd op dat domeinachtervoegsel. Ook u met een oudere ILB ASE geen enkele aanmelding uitvoeren op de Kudu-console met apps in die ILB ASE. Wanneer u DNS configureert voor een oudere ILB ASE, moet u de wildcard A-record instellen in een zone die overeenkomt met uw domeinachtervoegsel. 

## <a name="get-started"></a>Aan de slag ##

* Zie [Introduction to App Service environments][Intro] (Inleiding tot App Service-omgevingen) om aan de slag te gaan met AS-omgevingen. 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../containers/app-service-linux-intro.md
