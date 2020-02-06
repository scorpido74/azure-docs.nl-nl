---
title: Een Event Hubs toegewezen cluster maken met behulp van de Azure Portal
description: In deze Quick Start leert u hoe u een Azure Event Hubs-cluster maakt met behulp van Azure Portal.
services: event-hubs
documentationcenter: ''
author: femila
manager: ''
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 12/20/2019
ms.author: femila
ms.openlocfilehash: 7003cc90bdb456eba9cfaebc1e24332d2e4a6251
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029925"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Snelstartgids: een toegewezen Event Hubs-cluster maken met behulp van Azure Portal 
Event Hubs-clusters bieden implementaties met één Tenant voor klanten met de meest veeleisende streaming behoeften. Deze aanbieding heeft een gegarandeerde 99,99%-SLA en is alleen beschikbaar in onze speciale prijs categorie. Een [Event hubs cluster](event-hubs-dedicated-overview.md) kan miljoenen gebeurtenissen per seconde met gegarandeerde capaciteit en een subseconde van de seconden binnenkomen. Naam ruimten en Event hubs die zijn gemaakt in een cluster, bevatten alle functies van de Standard-aanbieding en meer, maar zonder ingangs limieten. De speciale aanbieding bevat ook de populaire [Event hubs Capture](event-hubs-capture-overview.md) -functie zonder extra kosten, zodat u automatisch gegevens stromen naar [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) of [Azure data Lake Storage gen 1](../data-lake-store/data-lake-store-overview.md)kunt vastleggen in batches.

Toegewezen clusters worden ingericht en gefactureerd op basis van **capaciteits eenheden (CUs)** , een vooraf toegewezen hoeveelheid CPU-en geheugen bronnen. U kunt 1, 2, 4, 8, 12, 16 of 20 voor elk cluster aanschaffen. In deze Quick Start wordt u stapsgewijs begeleid bij het maken van een 1-CU Event Hubs cluster via de Azure Portal.

> [!NOTE]
> Dit is momenteel beschikbaar in de preview-versie van [Azure Portal](https://aka.ms/eventhubsclusterquickstart). Als u vragen hebt over de speciale aanbieding, neem dan contact op met het [Event hubs team](mailto:askeventhubs@microsoft.com).


## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat u over het volgende beschikt om deze snelstart te voltooien:

- Een Azure-account. Als u nog geen account hebt, kunt u [het](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) pas doen voordat u begint. Deze functie wordt niet ondersteund met een gratis Azure-account. 
- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 update 3 (versie 15,3, 26730,01) of hoger.
- [.NET Standard SDK](https://dotnet.microsoft.com/download), versie 2.0 of later.
- [Een resource groep gemaakt](../event-hubs/event-hubs-create.md#create-a-resource-group).

## <a name="create-an-event-hubs-dedicated-cluster"></a>Een Event Hubs Dedicated-cluster maken
Een Event Hubs cluster bevat een unieke bereik container waarin u een of meer naam ruimten kunt maken. In deze preview-fase van de portal zelf-ondergeschikte ervaring kunt u 1 CU-clusters maken in regio's selecteren. Als u een cluster hebt dat groter is dan 1 CU, kunt u een Azure-ondersteunings aanvraag indienen om uw cluster te schalen nadat het is gemaakt.

Voer de volgende stappen uit om een cluster in uw resource groep te maken met behulp van de Azure Portal:

1. Volg [deze koppeling](https://aka.ms/eventhubsclusterquickstart) om een cluster op Azure portal te maken. Selecteer daarentegen **alle services** in het navigatie deel venster links en typ vervolgens ' event hubs clusters ' in de zoek balk en selecteer Event hubs clusters in de lijst met resultaten.
2. Configureer op de pagina **cluster maken** het volgende:
    1. Voer een **naam in voor het cluster**. Er wordt onmiddellijk gecontroleerd of de naam beschikbaar is.
    2. Selecteer het **abonnement** waarin u het cluster wilt maken.
    3. Selecteer de **resource groep** waarin u het cluster wilt maken.
    4. Selecteer een **locatie** voor het cluster. Als uw voorkeurs regio grijs wordt weer gegeven, is er tijdelijk onvoldoende capaciteit en kunt u een [ondersteunings aanvraag](#submit-a-support-request) indienen bij het event hubs-team.
    5. Selecteer de knop **volgende: Labels** onder aan de pagina. U moet een paar minuten wachten voordat het systeem de resources volledig heeft ingericht.

        ![Event Hubs-cluster maken-basis pagina](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. Configureer de volgende instellingen op de pagina **Tags** :
    1. Voer een **naam** en een **waarde** in voor het label dat u wilt toevoegen. Deze stap is **optioneel**.  
    2. Selecteer de knop **beoordeling + maken** .

        ![Event Hubs cluster pagina maken-Tags pagina](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. Controleer de details op de pagina **controleren en maken** en selecteer **maken**. 

    ![Event Hubs cluster pagina maken-pagina controleren en maken](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Een naam ruimte en Event Hub maken binnen een cluster

1. Als u een naam ruimte binnen een cluster wilt maken, selecteert u op de pagina **cluster Event hubs** voor uw cluster de optie **+ naam ruimte** in het bovenste menu.

    ![Pagina Cluster beheer-knop naam ruimte toevoegen](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. Voer op de pagina een naam ruimte maken de volgende stappen uit:
    1. Voer een **naam in voor de naamruimte**.  Het systeem controleert of de naam beschikbaar is.
    2. De naam ruimte neemt de volgende eigenschappen over:
        1. Abonnements-id
        2. Resourcegroep
        3. Locatie
        4. Clusternaam
    3. Selecteer **maken** om de naam ruimte te maken. U kunt uw cluster nu beheren.  

        ![Naam ruimte maken op de cluster pagina](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. Zodra de naam ruimte is gemaakt, kunt u [een event hub maken](event-hubs-create.md#create-an-event-hub) zoals u deze normaal gesp roken maakt in een naam ruimte. 


## <a name="submit-a-support-request"></a>Een ondersteuningsaanvraag indienen

Als u de grootte van het cluster na het maken wilt wijzigen of als uw voorkeurs regio niet beschikbaar is, kunt u een ondersteunings aanvraag indienen door de volgende stappen uit te voeren:

1. Selecteer in [Azure Portal](https://portal.azure.com) **Help + ondersteuning** in het menu links.
2. Selecteer **+ nieuwe ondersteunings aanvraag** in het menu ondersteuning.
3. Voer de volgende stappen uit op de pagina ondersteuning:
    1. Selecteer in de vervolg keuzelijst voor **type probleem**de optie **technisch** .
    2. Bij **Abonnement** selecteert u uw abonnement.
    3. Selecteer voor **service** **de optie mijn Services**en selecteer vervolgens **Event hubs**.
    4. Voor **resource**selecteert u uw cluster als dit al bestaat, anders selecteert u **algemene vraag/resource niet beschikbaar**.
    5. Selecteer voor **probleem type** **quotum**.
    6. Selecteer in de vervolg keuzelijst een van de volgende waarden voor het **subtype**van het probleem:
        1. Selecteer **aanvraag voor toegewezen SKU** om aan te vragen dat de functie wordt ondersteund in uw regio.
        2. Selecteer **aanvraag om het toegewezen cluster omhoog of omlaag** te schalen als u uw toegewezen cluster omhoog of omlaag wilt schalen. 
    7. Beschrijf het probleem voor het **onderwerp**.

        ![Pagina ondersteunings ticket](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Een toegewezen cluster verwijderen
 
1. Als u het cluster wilt verwijderen, selecteert u **verwijderen** in het bovenste menu. Houd er rekening mee dat uw cluster na het maken wordt gefactureerd met een minimum van 4 uur gebruik. 
2. Er wordt een bericht weer gegeven waarin wordt bevestigd dat u het cluster wilt verwijderen.
3. Typ de **naam van het cluster** en selecteer **verwijderen** om het cluster te verwijderen.

    ![Cluster pagina verwijderen](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een Event Hubs-cluster gemaakt. Zie de volgende zelf studies voor stapsgewijze instructies voor het verzenden en ontvangen van gebeurtenissen van een Event Hub en het vastleggen van gebeurtenissen in een Azure-opslag of Azure Data Lake Store.

- [Gebeurtenissen verzenden en ontvangen op .NET core](event-hubs-dotnet-standard-getstarted-send.md)
- [Azure Portal gebruiken om Event Hubs vastleggen in te scha kelen](event-hubs-capture-enable-through-portal.md)
- [Azure Event Hubs gebruiken voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
