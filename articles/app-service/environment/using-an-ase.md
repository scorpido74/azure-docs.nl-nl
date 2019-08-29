---
title: App Service omgeving gebruiken-Azure
description: Apps in een Azure App Service omgeving maken, publiceren en schalen
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: cabefcc53106a53459975fc26513dc59ae7d3372
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073217"
---
# <a name="use-an-app-service-environment"></a>Een App Service omgeving gebruiken #

Azure App Service Environment is een implementatie van Azure App Service in een subnet in het virtuele Azure-netwerk van een klant. Het bestaat uit:

- **Front-ends**: De front-ends zijn waar HTTP/HTTPS eindigt in een App Service omgeving (ASE).
- **Werk nemers**: De werk nemers zijn de resources die uw apps hosten.
- **Data Base**: De Data Base bevat informatie die de omgeving definieert.
- **Opslag**: De opslag wordt gebruikt voor het hosten van de door de klant gepubliceerde apps.

> [!NOTE]
> Er zijn twee versies van App Service Environment: ASEv1 en ASEv2. In ASEv1 moet u de resources beheren voordat u ze kunt gebruiken. Zie [Configure an app service Environment v1][ConfigureASEv1](Engelstalig) voor meer informatie over het configureren en beheren van ASEv1. De rest van dit artikel richt zich op ASEv2.
>
>

U kunt een ASE (ASEv1 en ASEv2) implementeren met een externe of interne VIP voor toegang tot apps. De implementatie met een extern VIP wordt meestal een externe ASE genoemd. De interne versie wordt de ILB-ASE genoemd, omdat deze gebruikmaakt van een interne load balancer (ILB). Zie [een ILB ASE maken en gebruiken][MakeILBASE]voor meer informatie over de ILB-ASE.

## <a name="create-an-app-in-an-ase"></a>Een app maken in een ASE ##

Als u een app in een ASE wilt maken, gebruikt u hetzelfde proces als wanneer u het normaal maakt, maar met enkele kleine verschillen. Wanneer u een nieuw App Service plan maakt:

- In plaats van een geografische locatie te kiezen voor het implementeren van uw app, kiest u een ASE als uw locatie.
- Alle App Service plannen die in een ASE zijn gemaakt, moeten zich in een geïsoleerde prijs categorie benemen.

Als u geen ASE hebt, kunt u er een maken door de instructies in [een app service omgeving maken][MakeExternalASE]te volgen.

Een app maken in een ASE:

1. Selecteer **een resource** > maken**Web en mobiel** > **Web-app**.

2. Voer een naam in voor de app. Als u al een App Service plan in een ASE hebt geselecteerd, weerspiegelt de domein naam voor de app de domein naam van de ASE.

    ![App-naam selecteren][1]

1. Selecteer een abonnement.

1. Voer een naam in voor een nieuwe resource groep of selecteer **bestaande gebruiken** en selecteer er een in de vervolg keuzelijst.

1. Selecteer uw besturingssysteem. 

1. Selecteer een bestaand App Service plan in uw ASE of maak een nieuw abonnement door de volgende stappen uit te voeren:

    a. Selecteer **nieuwe maken**.

    b. Voer de naam in voor het App Service plan.

    c. Selecteer uw ASE in de vervolg keuzelijst **locatie** . 
    
    d. Selecteer een **geïsoleerde** prijs categorie. Kies **Selecteren**.

    e. Selecteer **OK**.
    
    ![Geïsoleerde prijs Categorieën][2]

    > [!NOTE]
    > Linux-apps en Windows-apps kunnen zich niet in hetzelfde App Service plan bevindt, maar kunnen zich in dezelfde App Service Environment. 
    >

2. Selecteer **Maken**.

## <a name="how-scale-works"></a>Hoe schalen werkt ##

Elke App Service-app wordt uitgevoerd in een App Service plan. Het container model is een omgeving waarin App Service plannen worden bewaard en App Service apps worden bewaard. Wanneer u een app schaalt, schaalt u het App Service plan en schaalt u alle apps in hetzelfde abonnement.

Wanneer u in ASEv2 een App Service plan schaalt, wordt de benodigde infra structuur automatisch toegevoegd. Er is een vertraging om bewerkingen te schalen terwijl de infra structuur is toegevoegd. In ASEv1 moet de benodigde infra structuur worden toegevoegd voordat u uw App Service-abonnement kunt maken of schalen. 

In de multi tenant-App Service is schalen doorgaans onmiddellijk, omdat een groep resources zo snel mogelijk beschikbaar is om deze te ondersteunen. In een ASE is er geen buffer, en worden de bronnen na behoefte toegewezen.

In een ASE kunt u Maxi maal 100 exemplaren schalen. Deze 100-instanties kunnen allemaal in één App Service plan zijn of gedistribueerd zijn over meerdere App Service-abonnementen.

## <a name="ip-addresses"></a>IP-adressen ##

App Service beschikt over de mogelijkheid om een toegewezen IP-adres toe te wijzen aan een app. Deze mogelijkheid is beschikbaar nadat u een SSL op basis van IP hebt geconfigureerd, zoals beschreven in [een bestaand aangepast SSL-certificaat binden aan Azure app service][ConfigureSSL]. In een ASE is er echter een uitzonde ring. U kunt geen extra IP-adressen toevoegen die moeten worden gebruikt voor een SSL op basis van IP-adres in een ILB-ASE.

In ASEv1 moet u de IP-adressen als bronnen toewijzen voordat u ze kunt gebruiken. In ASEv2 gebruikt u de apps op dezelfde manier als in de multi tenant-App Service. Er bevindt zich altijd één reserve adres in ASEv2 met Maxi maal 30 IP-adressen. Telkens wanneer u er een gebruikt, wordt er een andere toegevoegd, zodat een adres altijd beschikbaar is voor gebruik. Er is een vertraging vereist om een ander IP-adres toe te wijzen, waardoor het snel achter elkaar toevoegen van IP-adressen wordt voor komen.

## <a name="front-end-scaling"></a>Front-end schalen ##

Wanneer u in ASEv2 uw App Service-abonnementen uitschaalt, worden werk nemers automatisch toegevoegd om ze te ondersteunen. Elke ASE wordt gemaakt met twee front-ends. Daarnaast wordt de front-ends automatisch uitgeschaald tegen een snelheid van één front-end voor elke 15 exemplaren in uw App Service-abonnementen. Als u bijvoorbeeld 15 exemplaren hebt, hebt u drie front-ends. Als u schaalt naar 30 instanties, hebt u vier front-ends, enzovoort.

Dit aantal front-ends moet meer dan voldoende zijn voor de meeste scenario's. U kunt echter met een snellere snelheid uitschalen. U kunt de verhouding instellen op zo weinig als een front-end voor elke vijf exemplaren. Er worden kosten in rekening gebracht voor het wijzigen van de verhouding. Zie [Azure app service prijzen][Pricing]voor meer informatie.

Front-end-resources zijn het HTTP/HTTPS-eind punt voor de ASE. Met de standaard front-end-configuratie is het geheugen gebruik per front-end consistent rond 60 procent. Werk belastingen van klanten worden niet uitgevoerd op een front-end. De belangrijkste factor voor een front-end ten opzichte van schaal is de CPU, die voornamelijk wordt aangedreven door HTTPS-verkeer.

## <a name="app-access"></a>App-toegang ##

In een externe ASE wijkt het domein dat wordt gebruikt bij het maken van apps af van de multi tenant-App Service. Het bevat de naam van de ASE. Zie [een app service omgeving maken][MakeExternalASE]voor meer informatie over het maken van een externe ASE. De domein naam in een externe ASE ziet er als volgt uit *.&lt; asename&gt;. p.azurewebsites.net*. Als uw ASE bijvoorbeeld de naam _External-ASE_ heeft en u een app met de naam _CONTOSO_ in die ASE host, bereikt u deze op de volgende url's:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

De URL contoso.scm.external-ase.p.azurewebsites.net wordt gebruikt voor toegang tot de kudu-console of voor het publiceren van uw app met behulp van Web Deploy. Zie [kudu-console voor Azure app service][Kudu]voor meer informatie over de kudu-console. De kudu-console biedt u een webinterface voor het opsporen van fouten, het uploaden van bestanden, het bewerken van bestanden en nog veel meer.

In een ILB-ASE bepaalt u het domein tijdens de implementatie. Zie [een ILB ASE maken en gebruiken][MakeILBASE]voor meer informatie over het maken van een ILB-ASE. Als u de domein naam _ILB-ASE.info_opgeeft, gebruiken de apps in die ASE dat domein tijdens het maken van de app. De Url's voor de app met de naam _Contoso_:

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Publiceren ##

Net als bij de multi tenant App Service, in een ASE kunt u het volgende publiceren:

- Webimplementatie.
- FTP.
- Continue integratie.
- Sleep en zet neer in de kudu-console.
- Een IDE, zoals Visual Studio, eclips of IntelliJ.

Met een externe ASE werken deze publicatie opties allemaal hetzelfde. Zie [implementatie in azure app service][AppDeploy]voor meer informatie. 

Het belangrijkste verschil met publiceren is ten opzichte van een ILB-ASE. Met een ILB-ASE zijn de publicatie-eind punten alleen beschikbaar via de ILB. De ILB bevindt zich op een privé-IP in het subnet ASE in het virtuele netwerk. Als u geen netwerk toegang tot de ILB hebt, kunt u geen apps publiceren op die ASE. Zoals vermeld in [Create and use a ILB ASE][MakeILBASE], moet u DNS configureren voor de apps in het systeem. Die het SCM-eind punt bevat. Als ze niet juist zijn gedefinieerd, kunt u niet publiceren. Uw Ide's moet ook netwerk toegang hebben tot de ILB om er rechtstreeks naar te kunnen publiceren.

Op internet gebaseerde CI-systemen, zoals GitHub en Azure DevOps, werken niet met een ILB ASE omdat het publicatie-eind punt niet toegankelijk is via internet. Voor Azure DevOps kunt u dit probleem omzeilen door een zelf-hostende release agent te installeren in het interne netwerk waar de ILB kan worden bereikt. U kunt ook een CI-systeem gebruiken dat gebruikmaakt van een pull-model, zoals Dropbox.

De publicatie-eindpunten voor apps in een ILB AS-omgeving maken gebruik van het domein waarmee de ILB AS-omgeving is gemaakt. U kunt dit zien in het publicatie Profiel van de app en in de portal-Blade van > de app (in overzichts**kernen** en ook in **Eigenschappen**). 

## <a name="pricing"></a>Prijzen ##

De prijs-SKU met de naam **geïsoleerd** is gemaakt voor gebruik met ASEv2. Alle App Service plannen die worden gehost in ASEv2, bevinden zich in de afzonderlijke prijs-SKU. De afzonderlijke App Service plan tarieven kunnen per regio verschillen. 

Naast de prijs voor uw App Service plannen, is er een vast tarief voor ASE zelf. Het vaste tempo wordt niet gewijzigd door de grootte van uw ASE en betaalt voor de ASE-infra structuur met een standaard schaal factor van 1 extra front-end voor elke 15 App Service-abonnement.  

Als de standaard schaal van 1 front-end voor elke 15 App Service-plan exemplaren niet snel genoeg is, kunt u de verhouding aanpassen waarmee de front-ends worden toegevoegd of de grootte van de front-ends.  Wanneer u de verhouding of de grootte aanpast, betaalt u voor de front-end-kernen die niet standaard worden toegevoegd.  

Als u bijvoorbeeld de schaal verhouding wijzigt in 10, wordt een front-end toegevoegd voor elke 10 instanties in uw App Service-abonnementen. De vaste kosten omvatten een schaal tarief van een front-end voor elke 15 exemplaren. Met een schaal ratio van 10 betaalt u een vergoeding voor de derde front-end die is toegevoegd voor de tien App Service-plan exemplaren. U hoeft er niet voor te betalen wanneer u 15 exemplaren bereikt, omdat deze automatisch is toegevoegd.

Als u de grootte van de front-ends hebt aangepast aan twee kernen, maar de verhouding niet aanpast, betaalt u voor de extra kernen.  Een ASE wordt gemaakt met twee front-ends, dus zelfs onder de drempel waarde voor automatisch schalen kunt u twee extra kernen betalen als u de grootte hebt verhoogd naar 2 kern front-ends.

Zie [Azure app service prijzen][Pricing]voor meer informatie.

## <a name="delete-an-ase"></a>Een ASE verwijderen ##

Een ASE verwijderen: 

1. Gebruik **verwijderen** boven aan de blade **app service Environment** . 

1. Voer de naam van uw ASE in om te bevestigen dat u deze wilt verwijderen. Wanneer u een ASE verwijdert, verwijdert u ook alle inhoud erin. 

    ![ASE verwijderen][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


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
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
