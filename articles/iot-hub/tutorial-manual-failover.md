---
title: Handmatige failover van een Azure IoT Hub | Microsoft Docs
description: Lees hoe u een handmatige failover van uw IoT-hub naar een andere regio uitvoeren en bevestigt dat deze werkt en retourneerdeze naar de oorspronkelijke regio en controleer deze opnieuw.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/24/2019
ms.author: robinsh
ms.custom:
- mvc
- mqtt
ms.openlocfilehash: 86b39beb2958194f7c86409c5d78992616234b05
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769915"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub"></a>Zelfstudie: Handmatige failover uitvoeren voor een IoT-hub

Handmatige failover is een functie van de IoT Hub-service waarmee klanten [failover](https://en.wikipedia.org/wiki/Failover) voor de bewerkingen van hun hub kunnen uitvoeren van een primaire regio naar een overeenkomstige geografisch gekoppelde Azure-regio. Handmatige failover kan worden uitgevoerd in geval van een regionale noodgeval of een langdurige servicestoring. U kunt ook een geplande failover uitvoeren om de prestaties van uw noodherstel te testen. U wordt echter aangeraden een test-IoT-hub te gebruiken in plaats van een IoT-hub die in productie wordt uitgevoerd. De functie voor handmatige failover wordt klanten zonder aanvullende kosten aangeboden.

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Een IoT-hub maken met behulp van Azure Portal. 
> * Een failover uitvoeren. 
> * De hub uitgevoerd zien worden in de secundaire locatie.
> * Een failback uitvoeren om de bewerkingen van de IoT-hub in de primaire locatie terug te plaatsen. 
> * Bevestigen dat de hub op de juiste locatie wordt uitgevoerd.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in deze zelfstudie maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 

2. Klik op **+ Een resource maken**, selecteer **Internet of Things** en vervolgens **IoT Hub**.

   ![Schermopname van het maken van een IoT-hub](./media/tutorial-manual-failover/create-hub-01.png)

3. Selecteer het tabblad **Basisbeginselen.** Vul de volgende velden in.

    **Abonnement**: selecteer het gewenste Azure abonnement.

    **Resourcegroep**: klik op **Nieuwe maken** en geef **ManlFailRG** op als naam voor de resourcegroep.

    **Regio**: selecteer een gebied bij u in de buurt. In deze zelfstudie wordt `West US 2` gebruikt. Er kan alleen een failover tussen Azure-regio's worden uitgevoerd die geografische gekoppeld zijn. De regio geo-paired met West US 2 is WestCentralUS.
    
   **Naam IoT-hub**: geef een naam op voor de IoT-hub. De naam van de hub moet wereldwijd uniek zijn. 

   ![Schermopname met het deelvenster Basis voor het maken van een IoT-hub](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Klik op **Controleren + maken**. (De standaardwaarden voor grootte en schaal worden gebruikt.) 

4. Controleer de informatie en klik vervolgens op **Maken** om de IoT-hub te maken. 

   ![Schermopname met de laatste stap voor het maken van een IoT-hub](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Handmatige failover uitvoeren

Er kunnen maximaal twee failovers en twee failbacks per dag voor een IoT-hub worden uitgevoerd.

1. Klik op **Resourcegroepen** en selecteer resourcegroep **ManlFailRG**. Klik in de lijst met resources op uw hub. 

1. Klik **onder Instellingen** in het deelvenster IoT-hub op **Failover**.

   ![Schermafbeelding met het venster IoT Hub-eigenschappen](./media/tutorial-manual-failover/trigger-failover-01.png)

1. In het deelvenster Handmatige failover ziet u de **locatie Huidige** en de **failoverlocatie**. De huidige locatie geeft altijd aan op welke locatie de hub momenteel actief is. De failoverlocatie is het standaard [geo-gekoppelde azure-gebied](../best-practices-availability-paired-regions.md) dat is gekoppeld aan de huidige locatie. U kunt de locatiewaarden niet wijzigen. Voor deze zelfstudie is `West US 2` de huidige locatie `West Central US`en is de failoverlocatie .

   ![Schermafbeelding met het deelvenster Handmatige failover](./media/tutorial-manual-failover/trigger-failover-02.png)

1. Klik boven aan het deelvenster Handmatige failover op **Failover starten**. 

1. Vul in het bevestigingsvenster de naam van uw IoT-hub in om te bevestigen dat het de naam is die u wilt mislukken. Klik vervolgens op **Failover**om de failover te starten.

   De tijd die nodig is om de handmatige failover uit te voeren is evenredig met het aantal apparaten die voor uw hub zijn geregistreerd. Als u bijvoorbeeld 100.000 apparaten hebt, dan kan het vijftien minuten duren, maar als u vijf miljoen apparaten hebt, dan kan het minstens een uur duren.

   ![Schermafbeelding met het deelvenster Handmatige failover](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Terwijl het handmatige failoverproces wordt uitgevoerd, verschijnt er een banner die u vertelt dat er een handmatige failover aan de gang is. 

   ![Schermafbeelding waarin wordt aangegeven dat een handmatige failover wordt uitgevoerd](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Als u het deelvenster IoT-hub sluit en opnieuw opent door erop te klikken in het deelvenster Resourcegroep, ziet u een banner die aangeeft dat de hub zich in het midden van een handmatige failover bevindt. 

   ![Schermafbeelding van IoT Hub-failover in uitvoering](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   Nadat deze is voltooid, worden de huidige en failovergebieden op de pagina Handmatigmislukken omgedraaid en is de hub weer actief. In dit voorbeeld is de `WestCentralUS` huidige locatie nu `West US 2`en is de failoverlocatie nu . 

   ![Schermafbeelding waarin wordt aangegeven dat de failover is voltooid](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

   De overzichtspagina toont ook een banner die aangeeft dat de `West Central US`failover is voltooid en dat de IoT-hub wordt uitgevoerd in .

   ![Schermafbeelding van failover is voltooid in overzichtspagina](./media/tutorial-manual-failover/trigger-failover-06-finished-overview.png)


## <a name="perform-a-failback"></a>Failback uitvoeren 

Nadat u een handmatige failover hebt uitgevoerd, kunt u de bewerkingen van de hub terugschakelen naar de oorspronkelijke primaire regio. Dit wordt een failback genoemd. Als u net een failover hebt uitgevoerd, moet u ongeveer een uur wachten voordat u een failback kunt aanvragen. Als u de failback eerder wilt uitvoeren, wordt er een foutbericht weergegeven.

Een failback wordt op dezelfde manier uitgevoerd als een handmatige failover. Dit zijn de stappen: 

1. Als u een failback wilt uitvoeren, gaat u terug naar het deelvenster IoT Hub voor uw IoT-hub.

2. Klik **onder Instellingen** in het deelvenster IoT-hub op **Failover**. 

3. Klik boven aan het deelvenster Handmatige failover op **Failover starten**. 

4. Vul in het bevestigingsvenster de naam van uw IoT-hub in om te bevestigen dat het de naam is die u wilt mislukken. Klik op OK om de failback te starten. 

   ![Schermafbeelding met de aanvraag voor handmatige failback](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   De banners worden weergegeven zoals uitgelegd in de sectie Failover uitvoeren. Nadat de failback is voltooid, wordt deze opnieuw weergegeven `West US 2` als de huidige locatie en `West Central US` als de failoverlocatie, zoals oorspronkelijk ingesteld.

## <a name="clean-up-resources"></a>Resources opschonen 

Als u de resources die u in deze zelfstudie hebt gemaakt, wilt verwijderen, verwijdert u de resourcegroep. Hiermee verwijdert u ook alle resources in de groep. In dit geval worden de IoT-hub en de resourcegroep zelf verwijderd. 

1. Klik op **Resourcegroepen**. 

2. Selecteer de resourcegroep **ManlFailRG**. Klik erop om het te openen. 

3. Klik op **Resourcegroep verwijderen**. Als u de naam van de resourcegroep wordt gevraagd, voert u deze in en klikt u op **Verwijderen** ter bevestiging. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een handmatige failover moet configureren en uitvoeren en hoe u een failback kunt aanvragen door de volgende taken uit te voeren:

> [!div class="checklist"]
> * Een IoT-hub maken met behulp van Azure Portal. 
> * Een failover uitvoeren. 
> * De hub uitgevoerd zien worden in de secundaire locatie.
> * Een failback uitvoeren om de bewerkingen van de IoT-hub in de primaire locatie terug te plaatsen. 
> * Bevestigen dat de hub op de juiste locatie wordt uitgevoerd.

Ga door naar de volgende zelfstudie voor informatie over het beheren van de toestand van een IoT-apparaat. 

> [!div class="nextstepaction"]
> [De status van een IoT-apparaat beheren](tutorial-device-twins.md)
