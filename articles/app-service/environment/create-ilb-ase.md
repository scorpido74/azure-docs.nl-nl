---
title: Een ILB-ASE met ARM maken
description: Meer informatie over het maken van een App Service omgeving met een interne load balancer (ILB ASE) met behulp van Azure Resource Manager-sjablonen. Uw apps volledig isoleren via internet.
author: ccompy
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3bbc2dcc86d50e0a88e2f3e96c426cb317f41dea
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75375029"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Een interne Load Balancer maken en gebruiken App Service Environment 

De Azure App Service Environment is een implementatie van Azure App Service in een subnet in een virtueel netwerk van Azure (VNet). Er zijn twee manieren om een Azure App Service-omgeving (ASE) te implementeren: 

- Met een VIP-adres op een extern IP-adres, vaak aangeduid als Externe AS-omgeving.
- Met een VIP-adres op een intern IP-adres, vaak aangeduid als een ILB AS-omgeving omdat het interne eindpunt een ILB (Internal Load Balancer) is. 

In dit artikel wordt uitgelegd hoe u een ILB AS-omgeving maakt. Zie [Inleiding tot app service omgevingen][Intro]voor een overzicht van de ASE. Zie [een externe ASE maken][MakeExternalASE]voor meer informatie over het maken van een externe ASE.

## <a name="overview"></a>Overzicht 

U kunt een AS-omgeving implementeren met een eindpunt dat toegankelijk is via internet of met een IP-adres in uw VNet. De AS-omgeving moet zijn geïmplementeerd met een ILB om het IP-adres in te stellen op een VNet-adres. Wanneer u uw ASE implementeert met een ILB, moet u de naam van uw ASE opgeven. De naam van uw ASE wordt gebruikt in het domein achtervoegsel voor de apps in uw ASE.  Het domein achtervoegsel voor uw ILB ASE is &lt;ASE name&gt;. appserviceenvironment.net. Apps die zijn gemaakt in een ILB-ASE, worden niet in de open bare DNS geplaatst. 

Eerdere versies van de ILB-ASE vereisen dat u een domein achtervoegsel en een standaard certificaat voor HTTPS-verbindingen opgeeft. Het domein achtervoegsel wordt niet meer verzameld bij het maken van ILB ASE en er wordt ook geen standaard certificaat meer verzameld. Wanneer u nu een ILB-ASE maakt, wordt het standaard certificaat door micro soft verzorgd en wordt dit vertrouwd door de browser. U kunt nog steeds aangepaste domein namen instellen voor apps in uw ASE en certificaten instellen voor die aangepaste domein namen. 

Met een ILB-ASE kunt u dingen doen, zoals:

-   Host intranet toepassingen veilig in de Cloud, die u kunt gebruiken via een site-naar-site-of ExpressRoute.
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

1. Selecteer in de Azure-portal achtereenvolgens **Een resource maken** > **Web** > **App Service Environment**.

2. Selecteer uw abonnement.

3. Selecteer of maak een resourcegroep.

4. Voer de naam van uw App Service Environment in.

5. Selecteer het virtuele IP-type van intern.

    ![ASE maken](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

6. Netwerken selecteren

7. Selecteer of maak een Virtual Network. Als u hier een nieuw VNet maakt, wordt dit gedefinieerd met een adres bereik van 192.168.250.0/23. Als u een VNet wilt maken met een ander adres bereik of in een andere resource groep dan het ASE, gebruikt u de Azure Virtual Network-Portal maken. 

8. Selecteer of maak een leeg subnet. Als u een subnet wilt selecteren, moet dit leeg zijn en niet worden overgedragen. De grootte van het subnet kan niet worden gewijzigd nadat de ASE is gemaakt. We raden een grootte aan van `/24`. Dit formaat bevat 256 adressen en kan de grootst mogelijke AS-omgeving verwerken en voldoen aan alle schaalbehoeften. 

    ![ASE-netwerken][1]

7. Selecteer **controleren en maken en** Selecteer vervolgens **maken**.

## <a name="create-an-app-in-an-ilb-ase"></a>Een app maken in een ILB AS-omgeving ##

Het maken van een app in een ILB AS-omgeving werkt hetzelfde als het maken van een app in een AS-omgeving.

1. Selecteer in de Azure Portal **een resource maken** > **web** - **app** > .

1. Voer de naam van de app in.

1. Selecteer het abonnement.

1. Selecteer of maak een resourcegroep.

1. Selecteer uw publicatie, runtime stack en besturings systeem.

1. Selecteer een locatie waar de locatie een bestaande ILB-ASE is.  U kunt ook een nieuwe ASE maken tijdens het maken van een app door een geïsoleerd App Service-abonnement te selecteren. Als u een nieuwe ASE wilt maken, selecteert u de regio waarin u wilt dat de ASE wordt gemaakt.

1. Selecteer of maak een App Service-plan. 

1. Selecteer **controleren en maken en** Selecteer vervolgens **maken** wanneer u klaar bent.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>WebJobs, Functions en de ILB AS-omgeving 

Een ILB AS-omgeving biedt ondersteuning voor zowel Functions als WebJobs. Als u echter met deze wilt werken via de portal, hebt u netwerktoegang tot de SCM-site nodig.  Dit betekent dat de host van de browser zich in het virtuele netwerk moet bevinden of ermee moet zijn verbonden. Als uw ILB-ASE een domein naam heeft die niet eindigt op *appserviceenvironment.net*, moet u uw browser zo instellen dat het HTTPS-certificaat dat wordt gebruikt door uw SCM-site wordt vertrouwd.

## <a name="dns-configuration"></a>DNS-configuratie 

Wanneer u een extern VIP-adres gebruikt, wordt de DNS-server beheerd met Azure. Elke app die is gemaakt in de AS-omgeving wordt automatisch toegevoegd aan Azure DNS, wat een openbaar DNS is. In een ILB AS-omgeving moet u uw eigen DNS beheren. Het domein achtervoegsel dat wordt gebruikt met een ILB-ASE is afhankelijk van de naam van de ASE. Het domein achtervoegsel is *&lt;ASE name&gt;. appserviceenvironment.net*. Het IP-adres voor uw ILB bevindt zich in de portal onder **IP-adressen**. 

Uw DNS configureren:

- Maak een zone voor *&lt;ASE name&gt;. appserviceenvironment.net*
- een A-record in die zone maken die verwijst naar * naar het IP-adres van de ILB
- een A-record in die zone maken die verwijst naar @ naar het IP-adres van de ILB
- Maak een zone in *&lt;ASE name&gt;. appserviceenvironment.net* met de naam SCM
- een A-record maken in de SCM-zone die * verwijst naar het IP-adres van de ILB

## <a name="publish-with-an-ilb-ase"></a>Publiceren met een ILB AS-omgeving

Elke app die wordt gemaakt, heeft twee eindpunten. In een ILB-ASE hebt u *&lt;app-naam&gt;.&lt;ILB ASE domain&gt;* en *&lt;app name&gt;. scm.&lt;ILB ASE Domain&gt;* . 

De SCM-sitenaam leidt naar de Kudu-console, genaamd de **Geavanceerde portal**, binnen Azure Portal. Met behulp van de Kudu-console kunt u omgevingsvariabelen bekijken, de schijf verkennen, een console gebruiken, en nog veel meer. Zie [kudu-console voor Azure app service][Kudu]voor meer informatie. 

Op internet gebaseerde CI-systemen, zoals GitHub en Azure DevOps, werken nog steeds met een ILB AS-omgeving, als de buildagent toegankelijk is via internet en zich op hetzelfde netwerk bevindt als de ILB AS-omgeving. Als de buildagent dus, in het geval van Azure DevOps, is gemaakt in hetzelfde VNET als de ILB AS-omgeving (verschillende subnetten vormen geen probleem), kan met deze agent code worden opgehaald uit Azure DevOps-git en worden geïmplementeerd in de ILB AS-omgeving. Als u niet zelf een buildagent wilt maken, moet u een CI-systeem met een pull-model gebruiken, zoals Dropbox.

De publicatie-eindpunten voor apps in een ILB AS-omgeving maken gebruik van het domein waarmee de ILB AS-omgeving is gemaakt. Dit domein wordt weergegeven in het publicatieprofiel van de app en in de portalblade van de app (**Overzicht** > **Essentials** en ook **Eigenschappen**). Als u een ILB-ASE hebt met het domein achtervoegsel *&lt;naam&gt;. appserviceenvironment.net*en een app met de naam *mytest*, gebruikt u *mytest.&lt;ASE name&gt;. appserviceenvironment.net* voor FTP en *mytest.scm.contoso.net* voor webimplementatie.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Een ILB-ASE met een WAF-apparaat configureren ##

U kunt een Web Application Firewall-apparaat (WAF) combi neren met uw ILB-ASE om alleen de apps weer te geven die u nodig hebt via internet en de rest alleen toegankelijk vanuit het VNet beschikbaar te houden. Zo kunt u beveiligde toepassingen met meerdere lagen bouwen onder andere.

Zie [Configure a Web Application Firewall with your app service Environment][ASEWAF](Engelstalig) voor meer informatie over het configureren van uw ILB-ASE met een WAF-apparaat. In dit artikel leest u hoe u een virtueel Barracuda-apparaat gebruikt met de AS-omgeving. Een andere optie is het gebruik van Azure Application Gateway. Application Gateway maakt gebruik van de OWASP-kernregels om alle toepassingen te beveiligen die erachter zijn geplaatst. Zie [Introduction to the Azure Web Application firewall][AppGW]voor meer informatie over Application Gateway.

## <a name="ilb-ases-made-before-may-2019"></a>ILB as gemaakt vóór 2019 mei

ILB as die zijn gemaakt vóór 2019, moest u het domein achtervoegsel instellen tijdens het maken van ASE. U moet ook een standaard certificaat uploaden dat is gebaseerd op het achtervoegsel van dat domein. Met een oudere ILB-ASE kunt u ook eenmalige aanmelding niet uitvoeren op de kudu-console met apps in die ILB ASE. Bij het configureren van DNS voor een oudere ILB-ASE moet u het Joker teken A-record instellen in een zone die overeenkomt met uw domein achtervoegsel. 

## <a name="get-started"></a>Aan de slag ##

* Zie [Introduction to app service environments][Intro](Engelstalig) om aan de slag te gaan met as. 

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
