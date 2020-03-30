---
title: Een speciaal cluster met gebeurtenishubs maken met de Azure-portal
description: In deze quickstart leert u hoe u een Azure Event Hubs-cluster maakt met Azure-portal.
services: event-hubs
documentationcenter: ''
author: femila
manager: ''
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 12/20/2019
ms.author: femila
ms.openlocfilehash: 5b1574eaac8771043e09500225b65e4835c8e627
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77157479"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Snelstart: een speciaal cluster van gebeurtenishubs maken met Azure-portal 
Event Hubs-clusters bieden implementaties met één tenant voor klanten met de meest veeleisende streamingbehoeften. Dit aanbod heeft een gegarandeerde SLA van 99,99% en is alleen beschikbaar op onze dedicated prijscategorie. Een [cluster van Gebeurtenishubs](event-hubs-dedicated-overview.md) kan miljoenen gebeurtenissen per seconde binnendringen met gegarandeerde capaciteit en subsecondelatentie. Naamruimten en gebeurtenishubs die binnen een cluster zijn gemaakt, bevatten alle functies van het standaardaanbod en meer, maar zonder invallen. Het speciale aanbod bevat ook de populaire functie voor [het vastleggen van gebeurtenishubs](event-hubs-capture-overview.md) zonder extra kosten, zodat u automatisch gegevensstromen batchen en logboeken naar [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) of Azure Data Lake Storage [Gen 1](../data-lake-store/data-lake-store-overview.md).

Speciale clusters worden ingericht en gefactureerd door **Capaciteitseenheden (CPU's),** een vooraf toegewezen hoeveelheid CPU- en geheugenbronnen. U voor elk cluster 1, 2, 4, 8, 12, 16 of 20 CPU's kopen. In deze quickstart nemen we je mee door het maken van een cluster van 1 CU-gebeurtenishubs via de Azure-portal.

> [!NOTE]
> Deze self-serve-ervaring is momenteel beschikbaar in preview op [Azure Portal.](https://aka.ms/eventhubsclusterquickstart) Als je vragen hebt over het dedicated-aanbod, neem dan contact op met het [Event Hubs-team.](mailto:askeventhubs@microsoft.com)


## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat u over het volgende beschikt om deze snelstart te voltooien:

- Een Azure-account. Als je er geen hebt, [koop je een account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) voordat je begint. Deze functie wordt niet ondersteund met een gratis Azure-account. 
- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 Update 3 (versie 15.3, 26730.01) of hoger.
- [.NET Standard SDK](https://dotnet.microsoft.com/download), versie 2.0 of hoger.
- [Een resourcegroep maken](../event-hubs/event-hubs-create.md#create-a-resource-group).

## <a name="create-an-event-hubs-dedicated-cluster"></a>Een speciaal cluster voor gebeurtenishubs maken
Een cluster van Gebeurtenishubs biedt een unieke scopingcontainer waarin u een of meer naamruimten maken. In deze previewfase van de zelfbedieningservaring van de portal u 1 CU-clusters maken in bepaalde regio's. Als u een cluster nodig hebt dat groter is dan 1 CU, u een Azure-ondersteuningsaanvraag indienen om uw cluster op te schalen na de creatie ervan.

Voer de volgende stappen uit om een cluster in uw brongroep te maken met behulp van de Azure-portal:

1. Volg [deze koppeling](https://aka.ms/eventhubsclusterquickstart) om een cluster op Azure-portal te maken. Selecteer daarentegen **Alle services** in het linkernavigatiedeelvenster, typ vervolgens 'Clusterclusters van gebeurtenishubs' in de zoekbalk en selecteer 'Clusterclusters van gebeurtenishubs' in de lijst met resultaten.
2. Configureer op de pagina **Cluster maken** het volgende:
    1. Voer een **naam in voor het cluster**. In het systeem wordt onmiddellijk gecontroleerd of de naam beschikbaar is.
    2. Selecteer het **abonnement** waarin u het cluster wilt maken.
    3. Selecteer de **resourcegroep** waarin u het cluster wilt maken.
    4. Selecteer een **locatie** voor het cluster. Als uw voorkeursregio grijs is, is deze tijdelijk buiten capaciteit en u een [ondersteuningsverzoek](#submit-a-support-request) indienen bij het Team Gebeurtenishubs.
    5. Selecteer de knop **Volgende: Tags** onder aan de pagina. U moet een paar minuten wachten voordat het systeem de resources volledig heeft ingericht.

        ![Cluster gebeurtenishubs maken - basispagina](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. Configureer op de pagina **Labels** het volgende:
    1. Voer een **naam** en een **waarde** in voor de tag die u wilt toevoegen. Deze stap is **optioneel**.  
    2. Selecteer de knop **Controleren + Maken.**

        ![Clusterpagina gebeurtenishubs maken - pagina Labels](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. Bekijk op de pagina **Controleren + Maken** de details en selecteer **Maken**. 

    ![Clusterpagina gebeurtenishubs maken - Pagina Controleren + Maken](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Een naamruimte- en gebeurtenishub maken binnen een cluster

1. Als u een naamruimte in een cluster wilt maken, selecteert u op de clusterpagina **Van Gebeurtenishubs** voor uw cluster **+Naamruimte** in het bovenste menu.

    ![Pagina Clusterbeheer - knop naamruimte toevoegen](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. Ga op de pagina Een naamruimte maken de volgende stappen uit:
    1. Voer een **naam in voor de naamruimte**.  Het systeem controleert of de naam beschikbaar is.
    2. De naamruimte neemt de volgende eigenschappen over:
        1. Abonnements-id
        2. Resourcegroep
        3. Locatie
        4. Clusternaam
    3. Selecteer **Maken** om de naamruimte te maken. Nu u uw cluster beheren.  

        ![Naamruimte maken op de clusterpagina](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. Zodra uw naamruimte is gemaakt, u [een gebeurtenishub maken](event-hubs-create.md#create-an-event-hub) zoals u er normaal gesproken een zou maken binnen een naamruimte. 


## <a name="submit-a-support-request"></a>Een ondersteuningsverzoek indienen

Als u de grootte van uw cluster na het maken wilt wijzigen of als uw voorkeursregio niet beschikbaar is, dient u een ondersteuningsverzoek in door de volgende stappen te volgen:

1. Selecteer **help + ondersteuning** in de [Azure-portal](https://portal.azure.com)in het linkermenu.
2. Selecteer **+ Nieuw ondersteuningsverzoek** in het menu Ondersteuning.
3. Volg op de ondersteuningspagina de volgende stappen:
    1. Selecteer Bij **Issue Type**De optie **Techniek** in de vervolgkeuzelijst.
    2. Bij **Abonnement** selecteert u uw abonnement.
    3. Selecteer Voor **Service** **Mijn services**en selecteer **vervolgens Gebeurtenishubs**.
    4. Selecteer **voor Resource**uw cluster als het al bestaat, anders selecteert u Algemene **vraag/resource niet beschikbaar**.
    5. Selecteer Quota voor **probleemtype** **.**
    6. Selecteer **voor subtype Probleem**een van de volgende waarden in de vervolgkeuzelijst:
        1. Selecteer **Verzoek om speciale SKU** om te vragen of de functie in uw regio moet worden ondersteund.
        2. Selecteer **Verzoek om dedicated cluster op te schalen of op te schalen** als u uw specifieke cluster wilt opschalen of schalen. 
    7. Voor **Onderwerp,** beschrijf het probleem.

        ![Pagina ondersteuningsticket](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Een speciaal cluster verwijderen
 
1. Als u het cluster wilt verwijderen, selecteert u **Verwijderen** in het bovenste menu. Houd er rekening mee dat uw cluster na het maken ten minste 4 uur wordt gefactureerd. 
2. Er verschijnt een bericht waarin wordt bevestigd dat u het cluster wilt verwijderen.
3. Typ de **naam van het cluster** en selecteer **Verwijderen** om het cluster te verwijderen.

    ![Clusterpagina verwijderen](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een cluster van gebeurtenishubs gemaakt. Zie de volgende zelfstudies voor stapsgewijze instructies voor het verzenden en ontvangen van gebeurtenissen vanuit een gebeurtenishub en het vastleggen van gebeurtenissen naar een Azure-opslag of Azure Data Lake Store:

- Gebeurtenissen verzenden en ontvangen 
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
- [Azure-portal gebruiken om gebeurtenishubs vast te leggen](event-hubs-capture-enable-through-portal.md)
- [Azure-gebeurtenishubs gebruiken voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
