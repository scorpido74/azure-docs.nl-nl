---
title: Bescherm uw machines en toepassingen
description: Dit document behandelt aanbevelingen in Security Center waarmee u uw virtuele machines en computers en uw web-apps en App Service-omgevingen beschermen.
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
ms.openlocfilehash: 93dde2e873a00303dcb8563caed4d56dbf11cc12
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435631"
---
# <a name="protect-your-machines-and-applications"></a>Bescherm uw machines en toepassingen
Wanneer Azure Security Center potentiële beveiligingsproblemen identificeert, worden er aanbevelingen gemaakt die u begeleiden bij het configureren van de benodigde besturingselementen om uw resources te verharden en te beschermen.

In dit artikel wordt de pagina **Compute en Apps** van de sectie resourcebeveiliging van het Beveiligingscentrum uitgelegd.

Zie [Aanbevelingen voor compute en apps](recommendations-reference.md#recs-computeapp)voor een volledige lijst met aanbevelingen die u op deze pagina zien.


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>De beveiliging van uw reken- en appsbronnen weergeven

[![Dashboard van Security Center](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

Als u de status van uw reken- en appsbronnen wilt weergeven, selecteert u in het linkerdeelvenster in beveiligingscentrum de optie **&-apps berekenen.** De volgende tabbladen zijn beschikbaar:

* **Overzicht**: bevat de aanbevelingen voor alle compute- en apps-bronnen en hun huidige beveiligingsstatus 

* [**VM's en servers:**](#vms-and-computers)bevat de aanbevelingen voor uw VM's, computers en huidige beveiligingsstatus van elk

* [**VM-schaalsets:**](#vmscale-sets)bevat de aanbevelingen voor uw schaalsets, 

* [**Cloudservices:**](#cloud-services)bevat de aanbevelingen voor uw web- en werknemersrollen die worden gecontroleerd door Security Center

* [**App-services:**](#app-services)bevat de aanbevelingen voor uw App-serviceomgevingen en de huidige beveiligingsstatus van elk

* [**Containers**](#containers): bevat de aanbevelingen voor uw containers en beveiligingsbeoordeling van hun configuraties

* **Compute resources:** bevat de aanbevelingen voor uw compute resources, zoals Service Fabric-clusters en gebeurtenishubs

### <a name="whats-in-each-tab"></a>Wat staat er op elk tabblad?

Elk tabblad heeft meerdere secties en in elke sectie u inzoomen om meer details over het weergegeven item weer te geven.

In elk tabblad ziet u ook aanbevelingen voor de relevante bronnen in uw bewaakte omgeving. In de eerste kolom wordt de aanbeveling weergegeven, de tweede toont het totale aantal betrokken resources en de derde geeft de ernst van het probleem weer.

Elke aanbeveling heeft een reeks acties die u uitvoeren nadat u deze hebt geselecteerd. Als u bijvoorbeeld **Ontbrekende systeemupdates**selecteert, wordt het aantal VM's en computers dat patches mist en wordt de ernst van de ontbrekende update weergegeven.

> [!NOTE]
> De beveiligingsaanbevelingen zijn dezelfde als die op de pagina **Aanbevelingen,** maar hier worden ze gefilterd op het specifieke resourcetype dat u hebt geselecteerd. Zie [Beveiligingsaanbevelingen implementeren in Azure Security Center](security-center-recommendations.md)voor meer informatie over het oplossen van aanbevelingen.
>




### <a name="vms-and-servers"></a><a name="vms-and-computers"></a>VM's en servers
De sectie VM's en computers geeft u een overzicht van alle beveiligingsaanbevelingen voor uw VM's en computers. Vier soorten machines zijn inbegrepen:

![Niet-Azure-computer](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Computer zonder Azure.

![Azure Resource Manager VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager VM.

![Azure Classic VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Azure Classic VM.

![VM's geïdentificeerd vanuit de werkruimte](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Virtuele machines die alleen zijn geïdentificeerd vanuit de werkruimte die deel uitmaakt van het weergegeven abonnement. Dit geldt ook voor VM's van andere abonnementen die rapporteren aan de werkruimte in dit abonnement en VM's die zijn geïnstalleerd met de directe agent Operations Manager en geen resource-id hebben.

Het pictogram dat onder elke aanbeveling wordt weergegeven, helpt u om snel de VM en computer te identificeren die aandacht nodig hebben, en het type aanbeveling. U de filters ook gebruiken om de lijst te doorzoeken op **resourcetype** en **op ernst.**

Als u wilt inzoomen op de beveiligingsaanbevelingen voor elke virtuele machine, klikt u op de vm.
Hier ziet u de beveiligingsgegevens voor de VM of computer. Onderaan ziet u de aanbevolen actie en de ernst van elk probleem.

[![Cloudservices](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="virtual-machine-scale-sets"></a><a name="vmscale-sets"></a>Virtuele-machineschaalsets
Security Center detecteert automatisch of u schaalsets hebt en raadt u aan de Log Analytics-agent erop te installeren.

Ga als eerste over de installatie van de loganalytics-agent: 

1. Selecteer de aanbeveling **Installeer de bewakingsagent op de virtuele machineschaalset**. U krijgt een lijst met niet-bewaakte schaalsets.

1. Selecteer een ongezonde schaalset. Volg de instructies om de bewakingsagent te installeren met behulp van een bestaande werkruimte of maak een nieuwe. Zorg ervoor dat u de prijslaag voor [werkruimtes](security-center-pricing.md) instelt als deze niet is ingesteld.

   ![MMS installeren](./media/security-center-virtual-machine-recommendations/install-mms.png)

Ga als volgende voor het automatisch installeren van de Log Analytics-agent:
1. Ga naar Azure Policy en klik op **Definities**.

1. Zoek naar de **beleidsmedewerker Logboekanalyse-agent implementeren voor Windows- virtuele machineschaalsets** en klik erop.

1. Klik op **Toewijzen**.

1. Stel de **werkruimte** **Scope** en Log Analytics in en klik op **Toewijzen**.

Als u alle bestaande schaalsets wilt instellen om de loganalyse-agent te installeren, gaat u in Azure Policy naar **Herstel** en past u het bestaande beleid toe op bestaande schaalsets.





### <a name="cloud-services"></a><a name="cloud-services"></a>Cloudservices
Voor cloudservices wordt een aanbeveling gemaakt wanneer de versie van het besturingssysteem verouderd is.

![Cloud services](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

Volg in een scenario waarin u een aanbeveling hebt, de stappen in de aanbeveling om het besturingssysteem bij te werken. Wanneer er een update beschikbaar is, krijgt u een waarschuwing (rood of oranje - afhankelijk van de ernst van het probleem). Klik op **Os-versie bijwerken** onder de kolom **BESCHRIJVING voor** een volledige uitleg van deze aanbeveling.






### <a name="app-services"></a><a name="app-services"></a>App-services
Als u de app-servicegegevens wilt weergeven, moet u de standaardprijslaag van securitycenter gebruiken en App Service inschakelen in uw abonnement. Zie [App-service beveiligen met Azure Security Center](security-center-app-services.md)voor instructies voor het inschakelen van deze functie.

Onder **App-services**vindt u een lijst met uw App-serviceomgevingen en het statusoverzicht op basis van het uitgevoerde beveiligingscentrum voor beoordeling.

![App-services](./media/security-center-virtual-machine-recommendations/app-services.png)

Er zijn drie soorten toepassingsservices weergegeven:

![App-services-omgeving](./media/security-center-virtual-machine-recommendations/ase.png) App-services-omgeving

![Webtoepassing](./media/security-center-virtual-machine-recommendations/web-app.png) Webtoepassing

![Functietoepassing](./media/security-center-virtual-machine-recommendations/function-app.png) Functietoepassing

Als u een webtoepassing selecteert, wordt een overzichtsweergave geopend met drie tabbladen:

   - **Aanbevelingen:** op basis van beoordelingen uitgevoerd door Security Center die zijn mislukt.
   - **Geslaagde beoordelingen**: lijst van beoordelingen uitgevoerd door Security Center die is geslaagd.
   - **Niet-beschikbare beoordelingen**: lijst met beoordelingen die niet zijn uitgevoerd als gevolg van een fout of de aanbeveling is niet relevant voor de specifieke app-service

   Onder **Aanbevelingen** is een lijst met de aanbevelingen voor de geselecteerde webtoepassing en de ernst van elke aanbeveling.

   ![Aanbevelingen voor App-services](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Selecteer een aanbeveling om een beschrijving van de aanbeveling en een lijst met ongezonde bronnen, gezonde bronnen en niet-gescande bronnen te bekijken.

   - De kolom **Geslaagde beoordelingen** toont een lijst met geslaagde beoordelingen. De ernst van deze beoordelingen is altijd groen.

   - Selecteer een geslaagde beoordeling in de lijst voor een beschrijving van de beoordeling, een lijst met ongezonde en gezonde resources en een lijst met niet-gescande bronnen. Er is een tabblad voor ongezonde resources, maar die lijst is altijd leeg sinds de beoordeling is geslaagd.





### <a name="containers"></a><a name="containers"></a>Containers

Wanneer u het tabblad **Containers** opent, afhankelijk van uw omgeving, ziet u mogelijk een van de drie typen resources:

![Containerhost](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) Containerhosts - VM's met docker 

![Kubernetes-service](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) AKS-clusters (Azure Kubernetes Service)

![Containerregister](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) ACR-registers (Azure Container Registry) - Alleen weergegeven wanneer u zich op de standaardprijslaag bevindt en wanneer u de Azure Container Registry-bundel hebt ingeschakeld.

Zie De beveiliging van uw containers [controleren](monitor-container-security.md)voor instructies over het gebruik van de beveiligingsfuncties van de container.

De voordelen van de Azure Container Registry-bundel worden [hier](azure-container-registry-integration.md) uitgelegd

De voordelen van de Kubernetes Services-bundel worden [hier](azure-kubernetes-service-integration.md) uitgelegd

[![Tabblad Containers](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

Als u de aanbevelingen voor een specifieke resource in de lijst wilt bekijken, klikt u op die bron.

#### <a name="visibility-into-container-registries"></a>Zichtbaarheid in containerregisters

Als u bijvoorbeeld op het acr-register van asc-demo klikt in de lijst in de bovenstaande afbeelding, leidt u naar deze pagina met details:

[![Aanbevelingen voor een specifiek ACR-register](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>Inzicht in containers gehost op IaaS Linux machines

Wanneer u op een van de VM's met docker klikt, ziet u de pagina met details met informatie over de containers op de machine, zoals de Docker-versie en het aantal afbeeldingen dat op de host wordt uitgevoerd.

![Aanbevelingen voor een vm-docker](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>Beveiligingsaanbevelingen op basis van CIS-benchmark voor Docker

Security Center scant uw Docker-configuraties en geeft u inzicht in onjuiste configuraties door een lijst op te stellen met alle mislukte regels die zijn beoordeeld. Security Center biedt richtlijnen om u te helpen deze problemen snel op te lossen en tijd te besparen. Docker-configuraties worden doorlopend beoordeeld door Security Center en u ziet steeds wat de actuele status is.

![tabblad container](./media/security-center-virtual-machine-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over aanbevelingen die van toepassing zijn op andere Azure-brontypen:

* [Volledige referentielijst met beveiligingsaanbevelingen van Azure Security Center](recommendations-reference.md)
* [Identiteit en toegang bewaken in Azure Security Center](security-center-identity-access.md)
* [Protecting your network in Azure Security Center](security-center-network-recommendations.md) (Uw netwerk beveiligen in Azure Security Center)
* [Uw Azure SQL-service beveiligen in Azure Security Center](security-center-sql-service-recommendations.md)