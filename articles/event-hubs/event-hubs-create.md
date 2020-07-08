---
title: 'Azure-quickstart: Een event hub maken met behulp van de Azure-portal'
description: In deze snelstart leert u hoe u een Azure event hub maakt met behulp van Azure Portal en vervolgens gebeurtenissen verzendt en ontvangt met behulp van .NET Standard SDK.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 9ca71dbb1a82e3fd9fe241e197b0bcbbfec2dcb8
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85323134"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Quickstart: Een event hub maken met behulp van Azure Portal
Azure Event Hubs is een big data-platform voor het streamen van gegevens en een gebeurtenisopneemservice die miljoenen gebeurtenissen per seconde kan opnemen en verwerken. Event Hubs kan gebeurtenissen, gegevens of telemetrie die wordt geproduceerd door gedistribueerde software en apparaten verwerken en opslaan. Gegevens die naar een Event Hub worden verzonden, kunnen worden omgezet en opgeslagen via een provider voor realtime analytische gegevens of batchverwerking/opslagadapters. Zie [Overzicht van Event Hubs](event-hubs-about.md) en [Functies van Event Hubs](event-hubs-features.md) voor een gedetailleerd overzicht van Event Hubs.

In deze snelstart maakt u een Event Hub met behulp van de [Azure-portal](https://portal.azure.com).

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u over het volgende beschikt om deze snelstart te voltooien:

- Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
- [Visual Studio 2019](https://www.visualstudio.com/vs) of later.
- [.NET Standard SDK](https://www.microsoft.com/net/download/windows), versie 2.0 of hoger.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische verzameling Azure-resources. Alle resources worden geïmplementeerd en beheerd in een resourcegroep. Een resourcegroep maken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Kik in het linkernavigatievenster op **Resourcegroepen**. Klik vervolgens op **Toevoegen**.

   ![Resourcegroepen - Knop Toevoegen](./media/event-hubs-quickstart-portal/resource-groups1.png)

2. Klik bij **Abonnement** op de naam van het Azure-abonnement waarin u de resourcegroep wilt maken.
3. Typ een unieke **naam voor de resourcegroep**. Het systeem controleert onmiddellijk of de naam beschikbaar is in het momenteel geselecteerde Azure-abonnement.
4. Selecteer een **regio** voor de resourcegroep.
5. Selecteer **Controleren + maken**.

   ![Resourcegroep - Maken](./media/event-hubs-quickstart-portal/resource-groups2.png)
6. Selecteer op de pagina **Controleren + maken** de optie **Maken**. 

## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken

Een Event Hubs-naamruimte biedt een unieke scopingcontainer, waarnaar wordt verwezen met de volledig gekwalificeerde domeinnaam (FQDN), waarin u een of meer Event Hubs maakt. Ga als volgt te werk om een ​​naamruimte in uw resourcegroep te maken met behulp van de portal:

1. Open de Azure-portal en klik op **Een resource maken** linksboven in het scherm.
2. Selecteer **Alle services** in het menu aan de linkerkant en selecteer de **ster (`*`)** naast **Event Hubs** in de categorie **Analyse**. Controleer of **Event Hubs** is toegevoegd aan **FAVORIETEN** in het navigatiemenu aan de linkerkant. 
    
   ![Zoeken naar Event Hubs](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
3. Selecteer **Event Hubs** onder **FAVORIETEN** in het navigatiemenu links en selecteer **Toevoegen** op de werkbalk.

   ![Knop Toevoegen](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
4. Voer op de pagina **Naamruimte maken** de volgende stappen uit:
    1. Selecteer het **abonnement** waarin u de naamruimte wilt maken.
    2. Selecteer de **resourcegroep** die u in de vorige stap hebt gemaakt. 
    3. Voer een **naam** in voor de naamruimte. In het systeem wordt onmiddellijk gecontroleerd of de naam beschikbaar is.
    4. Selecteer een **locatie** voor de naamruimte.    
    5. Kies de **prijscategorie** (Basic of Standard).  
    6. Laat de instellingen voor **doorvoereenheden** ongewijzigd. Zie [Schaalbaarheid van Event Hubs](event-hubs-scalability.md#throughput-units) voor meer informatie over doorvoereenheden  
    5. Selecteer **Controleren en maken** onderaan de pagina.

       ![Een Event Hub-naamruimte maken](./media/event-hubs-quickstart-portal/create-event-hub1.png)
   6. Controleer de instellingen op de pagina **Controleren en maken**, en selecteer **Maken**. Wacht totdat de installatie is voltooid. 

       ![Pagina Controleren en maken](./media/event-hubs-quickstart-portal/review-create.png)
   7. Selecteer op de pagina **Implementatie** de optie **Ga naar resource** om naar de pagina voor uw naamruimte te gaan. 

      ![Implementatie voltooid - ga naar resource](./media/event-hubs-quickstart-portal/deployment-complete.png)
   8. Bevestig dat de pagina **Naamruimte van Event Hubs** lijkt op het volgende voorbeeld: 

       ![Startpagina voor de naamruimte](./media/event-hubs-quickstart-portal/namespace-home-page.png)       

       > [!NOTE]
       > Azure Event Hubs biedt u een Kafka-eindpunt. Dit eindpunt biedt uw Event Hubs-naamruimte systeemeigen inzicht in [Apache Kafka](https://kafka.apache.org/intro)-berichtprotocol en API's. Met deze mogelijkheid kunt u met uw event hubs communiceren zoals u zou doen met Kafka-onderwerpen, zonder uw protocolclients te wijzigen of uw eigen clusters uit te voeren. Event Hubs ondersteunt [Apache Kafka versie 1.0.](https://kafka.apache.org/10/documentation.html) en later. Zie [Event Hubs gebruiken uit Apache Kafka-toepassingen](event-hubs-for-kafka-ecosystem-overview.md) voor meer informatie.
    
## <a name="create-an-event-hub"></a>Een Event Hub maken

Ga als volgt te werk om een Event Hub in de naamruimte te maken:

1. Op de pagina Event Hubs-naamruimte selecteert u **Event Hubs** in het menu links.
1. Klik op **+ Event Hub** bovenaan in het venster.
   
    ![Event Hub toevoegen - Knop](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Typ een naam voor uw Event Hub en klik vervolgens op **Maken**.
   
    ![Event hub maken](./media/event-hubs-quickstart-portal/create-event-hub5.png)
4. U kunt de status van het Event Hub-creatieproces bekijken in de waarschuwingen. Wanneer de Event Hub is gemaakt, wordt deze weergegeven in de lijst Event Hubs, zoals in de volgende afbeelding is te zien:

    ![Event Hub gemaakt](./media/event-hubs-quickstart-portal/event-hub-created.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een resourcegroep, een Event Hubs-naamruimte en een Event Hub gemaakt. Zie de zelfstudies **Gebeurtenissen verzenden en ontvangen** voor stapsgewijze instructies voor het verzenden van gebeurtenissen naar of ontvangen van gebeurtenissen vanuit een Event Hub: 

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-node-send-v2.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (alleen verzenden)](event-hubs-c-getstarted-send.md)
- [Apache Storm (alleen ontvangen)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
