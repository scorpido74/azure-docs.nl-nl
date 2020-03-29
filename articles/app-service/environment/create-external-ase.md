---
title: Een externe ASE maken
description: Meer informatie over het maken van een App Service-omgeving met een app erin of een zelfstandige (lege) ASE maken.
author: ccompy
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 6c4838e3226b91cbb5d6f86b83266a986418c120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430518"
---
# <a name="create-an-external-app-service-environment"></a>Een externe app-serviceomgeving maken

Azure App Service Environment is een implementatie van Azure App Service in een subnet in een virtueel Azure-netwerk (VNet).

> [!NOTE]
> Elke App Service Omgeving heeft een Virtual IP (VIP), die kan worden gebruikt om contact op te nemen met de App Service Environment.

Er zijn twee manieren om een Azure App Service-omgeving (ASE) te implementeren:

- Met een VIP-adres op een extern IP-adres, vaak aangeduid als Externe AS-omgeving.
- Met de VIP op een intern IP-adres, vaak een ILB ASE genoemd omdat het interne eindpunt een Internal Load Balancer (ILB) is.

In dit artikel ziet u hoe u een externe ASE maakt. Zie [Een inleiding tot de App Service Environment][Intro]voor een overzicht van de ASE. Zie [Een ILB ASE maken en gebruiken][MakeILBASE]voor informatie over het maken van een ILB ASE.

## <a name="before-you-create-your-ase"></a>Voordat u uw ASE maakt

Nadat u uw ASE hebt gemaakt, u het volgende niet wijzigen:

- Locatie
- Abonnement
- Resourcegroep
- VNet gebruikt
- Subnet gebruikt
- Subnetgrootte

> [!NOTE]
> Wanneer u een VNet kiest en een subnet opgeeft, moet u ervoor zorgen dat deze groot genoeg is om tegemoet te komen aan toekomstige groei- en schaalbehoeften. Wij raden een `/24` grootte van met 256 adressen.
>

## <a name="three-ways-to-create-an-ase"></a>Drie manieren om een ASE te maken

Er zijn drie manieren om een ASE te maken:

- **Tijdens het maken van een App Service-abonnement**. Met deze methode wordt het ASE- en App-serviceplan in één stap gemaakt.
- **Als een standalone actie**. Deze methode creëert een standalone ASE, dat is een ASE met niets in. Deze methode is een geavanceerder proces om een ASE te maken. U gebruikt het om een ASE te maken met een ILB.
- **Vanuit een Azure Resource Manager-sjabloon**. Deze methode is voor gevorderde gebruikers. Zie [Een ASE maken op basis van een sjabloon][MakeASEfromTemplate]voor meer informatie.

Een externe ASE heeft een openbare VIP, wat betekent dat al het HTTP/HTTPS-verkeer naar de apps in de ASE een IP-adres bereikt dat via internet toegankelijk is. Een ASE met een ILB heeft een IP-adres van het subnet dat door de ASE wordt gebruikt. De apps die worden gehost in een ILB ASE worden niet rechtstreeks blootgesteld aan het internet.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Samen een ASE- en een App-serviceplan maken

Het App Service-abonnement is een container met apps. Wanneer u een app maakt in App-service, kiest u een App Service-abonnement of maakt u deze. App-serviceomgevingen bevatten App-serviceplannen en App Service-abonnementen bevatten apps.

Ga als u een ASE maakt terwijl u een App Service-abonnement maakt:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)de optie **Een bron** > **web + mobiele** > **web-app**maken .

    ![Het maken van webapps][1]

2. Selecteer uw abonnement. De app en de ASE worden gemaakt in dezelfde abonnementen.

3. Selecteer of maak een resourcegroep. Met resourcegroepen u gerelateerde Azure-resources beheren als een eenheid. Brongroepen zijn ook handig wanneer u regels voor toegangsbeheer op basis van rollen voor uw apps vaststelt. Zie [Overzicht van Azure Resource Manager][ARMOverview] voor meer informatie.

4. Selecteer uw besturingssysteem (Windows, Linux of Docker). 

5. Selecteer het app-serviceplan en selecteer **Nieuw maken**. Linux-webapps en Windows-webapps kunnen niet in hetzelfde App-serviceplan staan, maar bevinden zich in dezelfde App-serviceomgeving. 

    ![Nieuw App-serviceplan][2]

6. Selecteer **in** de vervolgkeuzelijst Locatie het gebied waar u de ASE wilt maken. Als u een bestaande ASE selecteert, wordt er geen nieuwe ASE gemaakt. Het App-serviceplan wordt gemaakt in de ASE die u hebt geselecteerd. 

7. Selecteer **de prijslaag**en kies een van de **Geïsoleerde** prijs-SKU's. Als u een **geïsoleerde SKU-kaart** en een locatie kiest die geen ASE is, wordt op die locatie een nieuwe ASE gemaakt. Als u het proces wilt starten om een ASE te maken, selecteert u **Selecteren**. De **Geïsoleerde** SKU is alleen beschikbaar in combinatie met een ASE. U ook geen andere prijsssku's gebruiken in een andere ASE dan **Geïsoleerd.** 

    ![Selectie van prijsniveaus][3]

8. Voer de naam voor uw ASE in. Deze naam wordt gebruikt in de adresseerbare naam voor uw apps. Als de naam van de ASE _appsvcenvdemo_is, is de domeinnaam *.appsvcenvdemo.p.azurewebsites.net*. Als u een app met de naam *mytestapp*maakt, is deze adresseerbaar bij mytestapp.appsvcenvdemo.p.azurewebsites.net. Je geen witruimte gebruiken in de naam. Als u hoofdletters gebruikt, is de domeinnaam de totale kleine lettersversie van die naam.

    ![Naam van het nieuwe App-abonnement][4]

9. Geef uw azure-netwerkgegevens op. Selecteer **Nieuw maken** of **Bestaand selecteren**. De optie om een bestaande VNet te selecteren is alleen beschikbaar als u een VNet in de geselecteerde regio hebt. Als u **Nieuw maken**selecteert, voert u een naam in voor het VNet. Er wordt een nieuwe Resource Manager VNet met die naam gemaakt. Het gebruikt de `192.168.250.0/23` adresruimte in het geselecteerde gebied. Als u **Bestaand selecteren**selecteert, moet u:

    a. Selecteer het VNet-adresblok als u er meer dan één hebt.

    b. Voer een nieuwe subnetnaam in.

    c. Selecteer de grootte van het subnet. *Vergeet niet om een maat te selecteren die groot genoeg is om toekomstige groei van uw ASE aan te passen.* Wij `/24`raden aan, die 128 adressen heeft en kan omgaan met een maximale grootte ASE. We raden het `/28`bijvoorbeeld niet aan, omdat er slechts 16 adressen beschikbaar zijn. Infrastructuur gebruikt ten minste zeven adressen en Azure Networking gebruikt nog eens 5. In `/28` een subnet blijft u achter met een maximale schaling van 4 app-service-abonnementsexemplaren voor een extern ASE- en slechts 3 App Service-abonnementsexemplaren voor een ILB ASE.

    d. Selecteer het subnet-IP-bereik.

10. Selecteer **Maken** om de ASE te maken. Dit proces maakt ook het App Service-abonnement en de app. Het ASE-, App-service-abonnement en de app bevinden zich allemaal onder hetzelfde abonnement en ook in dezelfde brongroep. Als uw ASE een aparte resourcegroep nodig heeft of als u een ILB ASE nodig hebt, volgt u de stappen om zelf een ASE te maken.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Een ASE- en Een Linux-web-app maken met een aangepaste Docker-afbeelding samen

1. Maak in de [Azure-portal](https://portal.azure.com/) **een Resource** > **Web + Mobile** > **Web App voor containers.** 

    ![Het maken van webapps][7]

1. Selecteer uw abonnement. De app en de ASE worden gemaakt in dezelfde abonnementen.

1. Selecteer of maak een resourcegroep. Met resourcegroepen u gerelateerde Azure-resources beheren als een eenheid. Brongroepen zijn ook handig wanneer u regels voor toegangsbeheer op basis van rollen voor uw apps vaststelt. Zie [Overzicht van Azure Resource Manager][ARMOverview] voor meer informatie.

1. Selecteer het app-serviceplan en selecteer **Nieuw maken**. Linux-webapps en Windows-webapps kunnen niet in hetzelfde App-serviceplan staan, maar bevinden zich in dezelfde App-serviceomgeving. 

    ![Nieuw App-serviceplan][8]

1. Selecteer **in** de vervolgkeuzelijst Locatie het gebied waar u de ASE wilt maken. Als u een bestaande ASE selecteert, wordt er geen nieuwe ASE gemaakt. Het App-serviceplan wordt gemaakt in de ASE die u hebt geselecteerd. 

1. Selecteer **de prijslaag**en kies een van de **Geïsoleerde** prijs-SKU's. Als u een **geïsoleerde SKU-kaart** en een locatie kiest die geen ASE is, wordt op die locatie een nieuwe ASE gemaakt. Als u het proces wilt starten om een ASE te maken, selecteert u **Selecteren**. De **Geïsoleerde** SKU is alleen beschikbaar in combinatie met een ASE. U ook geen andere prijsssku's gebruiken in een andere ASE dan **Geïsoleerd.** 

    ![Selectie van prijsniveaus][3]

1. Voer de naam voor uw ASE in. Deze naam wordt gebruikt in de adresseerbare naam voor uw apps. Als de naam van de ASE _appsvcenvdemo_is, is de domeinnaam *.appsvcenvdemo.p.azurewebsites.net*. Als u een app met de naam *mytestapp*maakt, is deze adresseerbaar bij mytestapp.appsvcenvdemo.p.azurewebsites.net. Je geen witruimte gebruiken in de naam. Als u hoofdletters gebruikt, is de domeinnaam de totale kleine lettersversie van die naam.

    ![Naam van het nieuwe App-abonnement][4]

1. Geef uw azure-netwerkgegevens op. Selecteer **Nieuw maken** of **Bestaand selecteren**. De optie om een bestaande VNet te selecteren is alleen beschikbaar als u een VNet in de geselecteerde regio hebt. Als u **Nieuw maken**selecteert, voert u een naam in voor het VNet. Er wordt een nieuwe Resource Manager VNet met die naam gemaakt. Het gebruikt de `192.168.250.0/23` adresruimte in het geselecteerde gebied. Als u **Bestaand selecteren**selecteert, moet u:

    a. Selecteer het VNet-adresblok als u er meer dan één hebt.

    b. Voer een nieuwe subnetnaam in.

    c. Selecteer de grootte van het subnet. *Vergeet niet om een maat te selecteren die groot genoeg is om toekomstige groei van uw ASE aan te passen.* Wij `/24`raden aan, die 128 adressen heeft en kan omgaan met een maximale grootte ASE. We raden het `/28`bijvoorbeeld niet aan, omdat er slechts 16 adressen beschikbaar zijn. Infrastructuur gebruikt ten minste zeven adressen en Azure Networking gebruikt nog eens 5. In `/28` een subnet blijft u achter met een maximale schaling van 4 app-service-abonnementsexemplaren voor een extern ASE- en slechts 3 App Service-abonnementsexemplaren voor een ILB ASE.

    d. Selecteer het subnet-IP-bereik.

1.  Selecteer 'Container configureren'.
    * Voer uw aangepaste afbeeldingsnaam in (u Azure Container Registry, Docker Hub en uw eigen privéregister gebruiken). Als u uw eigen aangepaste container niet wilt gebruiken, u gewoon uw code meenemen en een ingebouwde afbeelding gebruiken met App Service op Linux, met behulp van de bovenstaande instructies. 

    ![Container configureren][9]

1. Selecteer **Maken** om de ASE te maken. Dit proces maakt ook het App Service-abonnement en de app. Het ASE-, App-service-abonnement en de app bevinden zich allemaal onder hetzelfde abonnement en ook in dezelfde brongroep. Als uw ASE een aparte resourcegroep nodig heeft of als u een ILB ASE nodig hebt, volgt u de stappen om zelf een ASE te maken.


## <a name="create-an-ase-by-itself"></a>Een ASE zelf maken

Als u een ASE standalone maakt, heeft er niets in. Een lege ASE brengt nog steeds een maandelijkse vergoeding voor de infrastructuur. Volg deze stappen om een ASE met een ILB te maken of om een ASE te maken in de eigen resourcegroep. Nadat u uw ASE hebt gemaakt, u er apps in maken met behulp van het normale proces. Selecteer uw nieuwe ASE als locatie.

1. Zoek in de Azure Marketplace for **App Service-omgeving**of selecteer **Een resource** > **Web Mobile** > **App Service-omgeving maken**. 

1. Voer de naam van uw ASE in. Deze naam wordt gebruikt voor de apps die in de ASE zijn gemaakt. Als de naam *mynewdemoase*is, is de subdomeinnaam *.mynewdemoase.p.azurewebsites.net*. Als u een app met de naam *mytestapp*maakt, is deze mytestapp.mynewdemoase.p.azurewebsites.net. Je geen witruimte gebruiken in de naam. Als u hoofdletters gebruikt, is de domeinnaam de totale kleine lettersversie van de naam. Als u een ILB gebruikt, wordt uw ASE-naam niet gebruikt in uw subdomein, maar wordt deze expliciet vermeld tijdens het maken van ASE.

    ![ASE-naamgeving][5]

1. Selecteer uw abonnement. Dit abonnement is ook degene die alle apps in de ASE gebruiken. U uw ASE niet in een VNet plaatsen dat in een ander abonnement zit.

1. Een nieuwe resourcegroep selecteren of opgeven. De resourcegroep die voor uw ASE wordt gebruikt, moet dezelfde groep zijn die wordt gebruikt voor uw VNet. Als u een bestaande VNet selecteert, wordt de selectie van de brongroep voor uw ASE bijgewerkt om die van uw VNet weer te geven. *U een ASE maken met een resourcegroep die verschilt van de VNet-brongroep als u een resourcemanagersjabloon gebruikt.* Zie [Een App Service-omgeving maken op basis van een sjabloon][MakeASEfromTemplate]als u een ASE wilt maken op basis van een sjabloon.

    ![Resourcegroep selecteren][6]

1. Selecteer uw VNet en locatie. U een nieuw VNet maken of een bestaand VNet selecteren: 

    * Als u een nieuw VNet selecteert, kunt u een naam en locatie opgeven. 
    
    * De nieuwe VNet heeft het adresbereik 192.168.250.0/23 en een subnet met de naam standaard. Het subnet wordt gedefinieerd als 192.168.250,0/24. U alleen een VNet voor Resource manager selecteren. De **VIP-typeselectie** bepaalt of uw ASE rechtstreeks toegankelijk is vanaf het internet (Extern) of dat deze een ILB gebruikt. Zie Een interne load [balancer maken en gebruiken met een App Service-omgeving][MakeILBASE]voor meer informatie over deze opties. 

      * Als u **Extern** voor het **VIP-type selecteert,** u selecteren met hoeveel externe IP-adressen het systeem is gemaakt voor SSL-doeleinden op BASIS van IP. 
    
      * Als u **Intern** voor het **VIP-type selecteert,** moet u het domein opgeven dat uw ASE gebruikt. U een ASE implementeren in een VNet dat gebruikmaakt van openbare of private adresbereiken. Als u een VNet met een openbaar adresbereik wilt gebruiken, moet u het VNet van tevoren maken. 
    
    * Als u een bestaand VNet selecteert, wordt een nieuw subnet gemaakt wanneer de ASE wordt gemaakt. *U geen vooraf gemaakt subnet in de portal gebruiken. U een ASE maken met een bestaand subnet als u een resourcemanagersjabloon gebruikt.* Zie [Een app-serviceomgeving maken op basis van een sjabloon][MakeASEfromTemplate]als u een ASE wilt maken op basis van een sjabloon.

## <a name="app-service-environment-v1"></a>App Service-omgeving v1

U nog steeds exemplaren maken van de eerste versie van App Service Environment (ASEv1). Als u dat proces wilt starten, zoekt u in de Marketplace for **App Service Environment v1**. U maakt de ASE op dezelfde manier als u de zelfstandige ASE maakt. Als het klaar is, heeft je ASEv1 twee front-ends en twee werknemers. Met ASEv1 moet je de front-ends en werknemers beheren. Ze worden niet automatisch toegevoegd wanneer u uw appserviceplannen maakt. De frontends fungeren als de HTTP/HTTPS-eindpunten en verzenden verkeer naar de werknemers. De werknemers zijn de rollen die uw apps hosten. U de hoeveelheid front-ends en werknemers aanpassen nadat u uw ASE hebt gemaakt. 

Zie [Inleiding tot de app-serviceomgeving v1][ASEv1Intro]voor meer informatie over ASEv1. Zie [Een App Service-omgeving configureren][ConfigureASEv1]voor meer informatie over het schalen, beheren en bewaken van ASEv1.

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[9]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



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
