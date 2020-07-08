---
title: Uw computers en toepassingen beveiligen
description: Dit document bevat aanbevelingen in Security Center waarmee u uw virtuele machines en computers en uw web-apps en App Service omgevingen kunt beveiligen.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: 8faff37e127e80447e0f066ef17e2885d4bcb597
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85799731"
---
# <a name="protect-your-machines-and-applications"></a>Uw computers en toepassingen beveiligen
Wanneer Azure Security Center mogelijke beveiligings problemen identificeert, worden er aanbevelingen gemaakt die u door het proces van het configureren van de benodigde besturings elementen leiden om uw resources te beschermen en te beveiligen.

In dit artikel wordt de pagina **Compute and apps** van Security Center resource Security beschreven.

Zie voor een volledige lijst met aanbevelingen die u op deze pagina kunt zien, [aanbevelingen voor Compute en apps](recommendations-reference.md#recs-computeapp).


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>De beveiliging van uw Compute-en app-resources weer geven

[![Dashboard van Security Center](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

Als u de status van uw Compute-en apps-resources wilt weer geven, selecteert u in het linkerdeel venster van Security Center **compute & apps**. De volgende tabbladen zijn beschikbaar:

* **Overzicht**: geeft een lijst van de aanbevelingen voor alle Compute-en app-resources, evenals de huidige beveiligings status 

* [**Vm's en servers**](#vms-and-computers): geeft een lijst van de aanbevelingen voor uw virtuele machines, computers en huidige beveiligings status van elke

* [**VM-schaal sets**](#vmscale-sets): Hier vindt u een lijst met aanbevelingen voor uw schaal sets, 

* [**Cloud Services**](#cloud-services): geeft een lijst van de aanbevelingen voor uw web-en werk rollen die worden bewaakt door Security Center

* [**App Services**](#app-services): geeft een lijst van de aanbevelingen voor uw app service-omgevingen en de huidige beveiligings status van elke

* [**Containers**](#containers): hier worden de aanbevelingen voor uw containers en de beveiligings beoordeling van de configuraties vermeld

* **Reken resources**: hier worden de aanbevelingen voor uw reken resources vermeld, zoals service Fabric clusters en Event hubs

### <a name="whats-in-each-tab"></a>Wat zit er op elk tabblad?

Elk tabblad heeft meerdere secties en in elke sectie kunt u inzoomen om meer informatie over het weer gegeven item weer te geven.

Op elk tabblad ziet u ook aanbevelingen voor de relevante resources in uw bewaakte omgeving. De eerste kolom bevat de aanbeveling, het tweede toont het totale aantal betrokken resources en de derde geeft de ernst van het probleem.

Elke aanbeveling heeft een set acties die u kunt uitvoeren nadat u deze hebt geselecteerd. Als u bijvoorbeeld **ontbrekende systeem updates**selecteert, wordt het aantal vm's en computers waarvoor patches ontbreken, en de ernst van de ontbrekende update weer gegeven.

> [!NOTE]
> De aanbevelingen voor beveiliging zijn hetzelfde als die op de pagina **aanbevelingen** , maar hier worden ze gefilterd op het specifieke resource type dat u hebt geselecteerd. Zie [beveiligings aanbevelingen implementeren in azure Security Center](security-center-recommendations.md)voor meer informatie over het oplossen van aanbevelingen.
>




### <a name="vms-and-servers"></a><a name="vms-and-computers"></a>Vm's en servers
In de sectie Vm's en computers vindt u een overzicht van alle beveiligings aanbevelingen voor uw virtuele machines en computers. Er zijn vier typen machines inbegrepen:

![Niet-Azure-computer](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Computer zonder Azure.

![Azure Resource Manager VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager VM.

![Klassieke Azure-VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Klassieke Azure-VM.

![Vm's die zijn geïdentificeerd in de werk ruimte](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Virtuele machines die alleen zijn geïdentificeerd vanuit de werkruimte die deel uitmaakt van het weergegeven abonnement. Dit omvat Vm's van andere abonnementen die rapporteren aan de werk ruimte in dit abonnement en Vm's die met Operations Manager direct agent zijn geïnstalleerd en geen Resource-ID hebben.

Het pictogram dat onder elke aanbeveling wordt weer gegeven, helpt u snel de virtuele machine en computer te identificeren die aandacht nodig hebben, en het type aanbeveling. U kunt ook de filters gebruiken om de lijst te doorzoeken op **resource type** en op **Ernst**.

Als u wilt inzoomen op de beveiligings aanbevelingen voor elke virtuele machine, klikt u op de virtuele machine.
Hier ziet u de beveiligings Details voor de virtuele machine of computer. Onderaan ziet u de aanbevolen actie en de ernst van elk probleem.

[![Cloud Services](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="virtual-machine-scale-sets"></a><a name="vmscale-sets"></a>Schaal sets voor virtuele machines
Security Center detecteert automatisch of u schaal sets hebt en raadt u aan de Log Analytics-agent op de juiste installatie te installeren.

De Log Analytics-agent installeren: 

1. Selecteer de aanbeveling **de bewakings agent installeren op de virtuele-machine schaalset**. U krijgt een lijst met niet-bewaakte schaal sets.

1. Selecteer een onjuiste schaalset. Volg de instructies voor het installeren van de bewakings agent met behulp van een bestaande gevulde werk ruimte of maak een nieuwe. Zorg ervoor dat u de [prijs categorie](security-center-pricing.md) voor de werk ruimte instelt als deze niet is ingesteld.

   ![MMS installeren](./media/security-center-virtual-machine-recommendations/install-mms.png)

Nieuwe schaal sets instellen om automatisch de Log Analytics-agent te installeren:
1. Ga naar Azure Policy en klik op **definities**.

1. Zoek naar het beleid **implementeren log Analytics agent voor virtuele-machine schaal sets voor Windows** en klik erop.

1. Klik op **Toewijzen**.

1. Stel het **bereik** en de **log Analytics-werk ruimte** in en klik op **toewijzen**.

Als u alle bestaande schaal sets wilt instellen om de Log Analytics-agent te installeren, gaat u in Azure Policy naar **herstel** en past u het bestaande beleid toe op bestaande schaal sets.





### <a name="cloud-services"></a><a name="cloud-services"></a>Cloud Services
Voor Cloud Services wordt een aanbeveling gemaakt wanneer de versie van het besturings systeem verouderd is.

![Cloud services](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

In een scenario waarin u een aanbeveling hebt, volgt u de stappen in de aanbeveling om het besturings systeem bij te werken. Wanneer er een update beschikbaar is, hebt u een waarschuwing (rood of oranje), afhankelijk van de ernst van het probleem. Voor een volledige uitleg van deze aanbeveling klikt u op **versie van besturings systeem bijwerken** onder de kolom **Beschrijving** .






### <a name="app-services"></a><a name="app-services"></a>App-Services
Als u de App Service informatie wilt weer geven, moet u de prijs categorie Standard van Security Center hebben en App Service in uw abonnement inschakelen. Zie [app service beveiligen met Azure Security Center](security-center-app-services.md)voor instructies over het inschakelen van deze functie.

Onder **app Services**vindt u een lijst met uw app service-omgevingen en het status overzicht op basis van de evaluatie Security Center uitgevoerd.

![App-Services](./media/security-center-virtual-machine-recommendations/app-services.png)

Er worden drie typen toepassings services weer gegeven:

![App Services-omgeving](./media/security-center-virtual-machine-recommendations/ase.png) App Services-omgeving

![Webtoepassing](./media/security-center-virtual-machine-recommendations/web-app.png) Webtoepassing

![Functie toepassing](./media/security-center-virtual-machine-recommendations/function-app.png) Functie toepassing

Als u een webtoepassing selecteert, wordt er een samen vatting weer gegeven met drie tabbladen:

   - **Aanbevelingen**: gebaseerd op evaluaties die zijn uitgevoerd door Security Center die zijn mislukt.
   - **Geslaagde evaluaties**: een lijst met evaluaties die zijn uitgevoerd door Security Center die zijn door gegeven.
   - **Niet-beschik bare evaluaties**: lijst met evaluaties die niet kunnen worden uitgevoerd vanwege een fout of omdat de aanbeveling niet relevant is voor de specifieke app service

   Onder **aanbevelingen** vindt u een lijst met aanbevelingen voor de geselecteerde webtoepassing en ernst van elke aanbeveling.

   ![Aanbevelingen voor App Services](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Selecteer een aanbeveling voor een beschrijving van de aanbeveling en een lijst met slechte resources, in orde zijnde resources en niet-gescande resources.

   - De kolom **door gegeven beoordelingen** bevat een lijst met geslaagde evaluaties. De ernst van deze evaluaties is altijd groen.

   - Selecteer een door gegeven evaluatie in de lijst voor een beschrijving van de evaluatie, een lijst met slechte en gezonde resources en een lijst met niet-gescande resources. Er is een tabblad voor beschadigde resources, maar deze lijst is altijd leeg sinds de evaluatie is geslaagd.





### <a name="containers"></a><a name="containers"></a>Containers

Wanneer u het tabblad **containers** opent, is het mogelijk dat er een van de drie typen resources wordt weer geven, afhankelijk van uw omgeving:

![Container host](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) Container-hosts-Vm's met docker 

![Kubernetes service ](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) Azure Kubernetes service (AKS)-clusters. [Meer informatie over de AKS bundel van Security Center](azure-kubernetes-service-integration.md)

![Register vermeldingen van container Registry ](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) Azure container Registry (ACR). [Meer informatie over de ACR bundel van Security Center](azure-container-registry-integration.md)

Zie [de beveiliging van uw containers bewaken](monitor-container-security.md)voor instructies over het gebruik van de beveiligings functies van de container.



[![Tabblad containers](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

Als u de aanbevelingen voor een specifieke resource in de lijst wilt weer geven, klikt u op die resource.

#### <a name="visibility-into-container-registries"></a>Zicht baarheid van container registers

Klik bijvoorbeeld op het REGI ster ASC-demo ACR in de lijst die wordt weer gegeven in de afbeelding hierboven vindt u informatie over deze pagina:

[![Aanbevelingen voor een specifiek ACR-REGI ster](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>Zicht baarheid van containers die worden gehost op IaaS Linux-machines

Wanneer u op een van de Vm's met docker klikt, wordt de pagina Details weer geven met informatie met betrekking tot de containers op de machine, zoals docker-versie en het aantal installatie kopieën dat op de host wordt uitgevoerd.

![Aanbevelingen voor een VM waarop docker wordt uitgevoerd](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>Aanbevelingen voor beveiliging op basis van CIS-benchmark test voor docker

Security Center scant uw Docker-configuraties en geeft u inzicht in onjuiste configuraties door een lijst op te stellen met alle mislukte regels die zijn beoordeeld. Security Center biedt richt lijnen die u helpen bij het snel oplossen van deze problemen en het besparen van tijd. Docker-configuraties worden doorlopend beoordeeld door Security Center en u ziet steeds wat de actuele status is.

![tabblad container](./media/security-center-virtual-machine-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over de aanbevelingen die van toepassing zijn op andere Azure-resource typen:

* [Volledige referentie lijst met aanbevelingen voor de beveiliging van Azure Security Center](recommendations-reference.md)
* [Identiteit en toegang bewaken in Azure Security Center](security-center-identity-access.md)
* [Protecting your network in Azure Security Center](security-center-network-recommendations.md) (Uw netwerk beveiligen in Azure Security Center)
* [Uw Azure SQL-service beveiligen in Azure Security Center](security-center-sql-service-recommendations.md)