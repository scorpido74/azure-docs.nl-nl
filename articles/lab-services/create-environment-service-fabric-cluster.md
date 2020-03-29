---
title: Een clusteromgeving voor Servicefabric maken in Azure DevTest Labs
description: Leer hoe u een omgeving maakt met een op zichzelf staand Cluster servicestructuur en het cluster start en stopt met behulp van planningen.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: EMaher
manager: femila
editor: spelluru
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: enewman
ms.openlocfilehash: 71793b81d8735c80881fc25a9b7ec31bc4fc6762
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170340"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Een omgeving maken met een op zichzelf staand ServiceFabric-cluster in Azure DevTest Labs
In dit artikel vindt u informatie over het maken van een omgeving met een op zichzelf staand Service Fabric-cluster in Azure DevTest Labs. 

## <a name="overview"></a>Overzicht
DevTest Labs kan zelfstandige testomgevingen maken zoals gedefinieerd door Azure Resource Management-sjablonen. Deze omgevingen bevatten zowel IaaS-bronnen, zoals virtuele machines, als PaaS-bronnen, zoals Service Fabric. Met DevTest Labs u virtuele machines in een omgeving beheren door opdrachten te geven om de virtuele machines te bedienen. Deze opdrachten geven u de mogelijkheid om een virtuele machine te starten of te stoppen op een schema. Op dezelfde manier kan DevTest Labs u ook helpen bij het beheren van Service Fabric-clusters in een omgeving. U een Cluster ServiceFabric handmatig of via een planning starten of stoppen in een omgeving.

## <a name="create-a-service-fabric-cluster"></a>Een Service Fabric-cluster maken
Service Fabric clusters worden gemaakt met behulp van omgevingen in DevTest Labs. Elke omgeving wordt gedefinieerd door een Azure Resource Manager-sjabloon in een Git-opslagplaats. De [openbare Git-opslagplaats](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) voor DevTest Labs bevat de sjabloon Resourcemanager om een cluster servicestructuur te maken in de map [ServiceFabric-Cluster.](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) 

1. Maak eerst een lab in Azure DevTest Labs met behulp van instructies in het volgende artikel: [Een lab maken.](devtest-lab-create-lab.md) De optie **Openbare omgevingen** is standaard **ingeschakeld.** 
2. Controleer of de Provider van Service Fabric is geregistreerd voor uw abonnement door de volgende stappen te volgen:
    1. Selecteer **Abonnementen** in het linkernavigatiemenu en selecteer uw **abonnement**
    2. Selecteer **op** de pagina Abonnement de optie **Bronproviders** in de sectie **Instellingen** in het linkermenu. 
    3. Als **Microsoft.ServiecFabric** niet is geregistreerd, selecteert u **Register**. 
3. Selecteer op de pagina **DevTest Lab** voor uw lab de optie **+ Toevoegen** op de werkbalk. 
    
    ![Knop Toevoegen op de werkbalk](./media/create-environment-service-fabric-cluster/add-button.png)
3. Selecteer op de **basispagina Een basispagina kiezen** de optie **Service Fabric Lab Cluster** in de lijst. 

    ![Het labcluster van servicefabric in de lijst selecteren](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Ga op de pagina **Instellingen configureren** de volgende stappen uit: 
    1. Geef een **naam** op voor uw **clusteromgeving.** Dit is de naam van de brongroep in Azure waarin het cluster Servicefabric wordt gemaakt. 
    2. Selecteer het **besturingssysteem (OS)** voor de virtuele clustermachines. De standaardwaarde is: **Windows**.
    3. Geef een naam op voor de **beheerder** voor het cluster. 
    4. Geef een **wachtwoord** op voor de beheerder. 
    5. Voer voor het **certificaat**uw certificaatgegevens in als een door basis64 gecodeerde tekenreeks. Ga als volgt te werk om een certificaat te maken:
        1. Download het **bestand Create-ClusterCertificate.ps1** uit de [Git-repository.](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) U ook de repository op uw machine klonen. 
        2. PowerShell **starten**. 
        3. Voer het **ps1-bestand** uit met de opdracht `.\Create-ClusterCertificate.ps1`. U ziet een tekstbestand geopend in kladblok met de informatie die u nodig hebt om de certificaatgerelateerde velden op deze pagina in te vullen. . 
    6. Voer het **wachtwoord voor het certificaat in.**
    7. Geef de **duimafdruk** voor uw certificaat op.
    8. Selecteer **Toevoegen** op de pagina **Instellingen configureren.** 

        ![Clusterinstellingen configureren](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Nadat het cluster is gemaakt, ziet u een resourcegroep met de naam van de omgeving die u in de vorige stap hebt opgegeven. Wanneer u uitvouwt, ziet u het cluster Servicefabric erin. Als de status van de resourcegroep vastzit bij **Maken,** selecteert u **Vernieuwen** op de werkbalk. De **clusteromgeving ServiceFabric** maakt een cluster van 1 nodetype met 5 nodetype op Linux of Windows.

    In het volgende voorbeeld is **mysfabricclusterrg** de naam van de resourcegroep die speciaal is gemaakt voor het cluster ServiceFabric. Het is belangrijk op te merken dat labomgevingen op zichzelf staan binnen de resourcegroep waarin ze zijn gemaakt. Dit betekent dat de sjabloon die de omgeving definieert, die alleen toegang heeft tot bronnen binnen de nieuw gemaakte brongroep of [virtuele netwerken die zijn geconfigureerd om door het lab te worden gebruikt.](devtest-lab-configure-vnet.md) In dit voorbeeld hierboven worden alle vereiste resources in dezelfde resourcegroep gemaakt.

    ![Geclusterd gemaakt](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Het cluster starten of stoppen
U het cluster starten of stoppen vanaf de devTest Lab-pagina zelf of vanaf de pagina Service Fabric Cluster van DevTest Labs. 

### <a name="from-the-devtest-lab-page"></a>Vanaf de devTest Lab-pagina
U het cluster starten of stoppen op de devTest Lab-pagina voor uw lab. 

1. Selecteer **drie puntjes (...)** voor het cluster Servicefabric zoals weergegeven in de volgende afbeelding: 

    ![Opdrachten voor het cluster starten en stoppen](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. U ziet twee opdrachten in het contextmenu om het cluster te **starten** en **te stoppen.** Met de opdracht Start worden alle knooppunten in een cluster gestart. Met de opdracht Stoppen worden alle knooppunten in een cluster gestopt. Zodra een cluster is gestopt, blijft het cluster Servicefabric zelf in een gereedstaat, maar zijn er geen knooppunten beschikbaar totdat de startopdracht opnieuw is uitgegeven op het cluster in het lab.

    Er zijn een paar overwegingen op te merken bij het gebruik van een Service Fabric-cluster in een testomgeving. Het kan enige tijd duren voordat het cluster ServiceFabric volledig is gerehydrateerd nadat de knooppunten opnieuw zijn opgestart. Om gegevens van afsluiten tot opstarten te bewaren, moeten gegevens worden opgeslagen op een beheerde schijf die aan de virtuele machine is gekoppeld. Er zijn gevolgen voor de prestaties bij het gebruik van een gekoppelde beheerde schijf, dus het wordt alleen aanbevolen voor testomgevingen. Als er geen back-back wordt gemaakt van de schijf die wordt gebruikt voor gegevensopslag, gaan gegevens verloren wanneer de stopopdracht op het cluster wordt uitgegeven.

### <a name="from-the-service-fabric-cluster-page"></a>Vanaf de pagina Cluster van servicefabric 
Er is een andere manier om het cluster te starten of te stoppen. 

1. Selecteer uw cluster Servicefabric in de structuurweergave op de pagina DevTest Lab. 

    ![Selecteer uw cluster](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Op de clusterpagina **Servicefabric** voor het cluster ziet u opdrachten op de werkbalk om het cluster te starten of te stoppen. 

    ![Opdrachten starten of stoppen op de pagina Cluster servicestructuur](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Schema voor automatisch opstarten en automatisch afsluiten configureren
Service Fabric-clusters kunnen ook volgens een planning worden gestart of gestopt. Deze ervaring is vergelijkbaar met de ervaring voor virtuele machines in een lab. Om geld te besparen, standaard, elk cluster gemaakt in een lab wordt automatisch afgesloten op het moment dat wordt gedefinieerd door het lab [shutdown beleid](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy). U overschrijven door op te geven of het cluster moet worden afgesloten of door de tijd op te geven waarop het cluster wordt afgesloten. 

![Bestaande schema's voor het starten en automatisch afsluiten van automatisch](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Aanmelden voor het automatisch startschema
Ga als volgt te werk om u aan te melden voor het opstartschema:

1. **Automatisch starten selecteren** in het linkermenu
2. Selecteer **Aan** voor **Toestaan dat dit cluster van servicefabricen automatisch wordt gestart**. Deze pagina is alleen ingeschakeld als de eigenaar van het lab gebruikers toestemming heeft gegeven om hun virtuele machines of Service Fabric-clusters automatisch te starten.
3. Selecteer **Opslaan** op de werkbalk. 

    ![Autosterpagina](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Instellingen voor automatisch afsluiten configureren 
Ga als volgt te werk om instellingen voor afsluiten te wijzigen:

1. Selecteer **Automatisch afsluiten** in het linkermenu. 
2. Op deze pagina u zich afmelden voor automatische uitschakeling door **Uit** voor **Ingeschakeld te**selecteren. 
3. Als u **Aan** hebt geselecteerd voor **Ingeschakeld,** voert u de volgende stappen uit:
    1. Geef de **tijd** voor afsluiten op.
    2. Geef de **tijdzone** voor de tijd op. 
    3. Geef op of u wilt dat DevTest Labs **meldingen** verzendt voordat u automatisch wordt afgesloten. 
    4. Als u **Ja** hebt geselecteerd voor de meldingsoptie, geeft u de URL en/of **het e-mailadres** **webhook** op om meldingen te verzenden. 
    5. Selecteer **Opslaan** op de werkbalk.

        ![Pagina Automatisch afsluiten](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Knooppunten weergeven in het cluster Servicefabric
De clusterpagina ServiceFabric die u in de stappen eerder hebt gezien, is specifiek voor de pagina DevTest Labs. Het toont u niet de knooppunten in het cluster. Voer de volgende stappen uit om meer informatie over het cluster te bekijken:

1. Selecteer op de pagina **DevTest Lab** voor uw lab de **resourcegroep** in de structuurweergave in de sectie **Mijn virtuele machines.**

    ![Resourcegroep selecteren](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Op de pagina **Resourcegroep** ziet u alle bronnen in de resourcegroep in een lijst. Selecteer uw **cluster Servicefabric** in de lijst. 

    ![Selecteer uw cluster in de lijst](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. U ziet de clusterpagina **Servicefabric** voor uw cluster. Dit is de pagina die de Service Fabric biedt. U ziet alle informatie over de clusters, zoals knooppunten, knooppunttypen, enz.

    ![Startpagina van servicestructuurcluster](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over omgevingen: 

- [Multi-VM-omgevingen en PaaS-resources maken met Azure Resource Manager-sjablonen](devtest-lab-create-environment-from-arm.md)
- [Openbare omgevingen configureren en gebruiken in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
