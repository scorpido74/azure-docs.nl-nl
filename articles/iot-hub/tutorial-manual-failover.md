---
title: Handmatige failover van een Azure IoT Hub | Microsoft Docs
description: Meer informatie over het uitvoeren van een hand matige failover van uw IoT-hub naar een andere regio en om te controleren of deze werkt. Ga vervolgens terug naar de oorspronkelijke regio en controleer deze opnieuw.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81769915"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub"></a>Zelf studie: hand matige failover uitvoeren voor een IoT-hub

Handmatige failover is een functie van de IoT Hub-service waarmee klanten [failover](https://en.wikipedia.org/wiki/Failover) voor de bewerkingen van hun hub kunnen uitvoeren van een primaire regio naar een overeenkomstige geografisch gekoppelde Azure-regio. Handmatige failover kan worden uitgevoerd in geval van een regionale noodgeval of een langdurige servicestoring. U kunt ook een geplande failover uitvoeren om de prestaties van uw noodherstel te testen. U wordt echter aangeraden een test-IoT-hub te gebruiken in plaats van een IoT-hub die in productie wordt uitgevoerd. De functie voor handmatige failover wordt klanten zonder aanvullende kosten aangeboden.

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Een IoT-hub maken met behulp van Azure Portal. 
> * Een failover uitvoeren. 
> * De hub uitgevoerd zien worden in de secundaire locatie.
> * Een failback uitvoeren om de bewerkingen van de IoT-hub in de primaire locatie terug te plaatsen. 
> * Bevestigen dat de hub op de juiste locatie wordt uitgevoerd.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het voor beeld van het apparaat in deze zelf studie maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs-en educatieve netwerk omgevingen. Zie [verbinding maken met IOT hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com). 

2. Klik op **+ Een resource maken**, selecteer **Internet of Things** en vervolgens **IoT Hub**.

   ![Schermopname van het maken van een IoT-hub](./media/tutorial-manual-failover/create-hub-01.png)

3. Selecteer het tabblad **basis beginselen** . Vul de volgende velden in.

    **Abonnement**: selecteer het gewenste Azure abonnement.

    **Resourcegroep**: klik op **Nieuwe maken** en geef **ManlFailRG** op als naam voor de resourcegroep.

    **Regio**: Selecteer een regio dicht bij u in de buurt. In deze zelfstudie wordt `West US 2` gebruikt. Er kan alleen een failover tussen Azure-regio's worden uitgevoerd die geografische gekoppeld zijn. De regio die is gekoppeld aan het VS-West 2 is WestCentralUS.
    
   **Naam IoT-hub**: geef een naam op voor de IoT-hub. De naam van de hub moet wereldwijd uniek zijn. 

   ![Schermopname met het deelvenster Basis voor het maken van een IoT-hub](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Klik op **Controleren + maken**. (De standaardwaarden voor grootte en schaal worden gebruikt.) 

4. Controleer de informatie en klik vervolgens op **Maken** om de IoT-hub te maken. 

   ![Schermopname met de laatste stap voor het maken van een IoT-hub](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Handmatige failover uitvoeren

Er kunnen maximaal twee failovers en twee failbacks per dag voor een IoT-hub worden uitgevoerd.

1. Klik op **Resourcegroepen** en selecteer resourcegroep **ManlFailRG**. Klik in de lijst met resources op uw hub. 

1. Klik onder **instellingen** in het deel venster IOT hub op **failover**.

   ![Schermafbeelding met het venster IoT Hub-eigenschappen](./media/tutorial-manual-failover/trigger-failover-01.png)

1. In het deel venster hand matige failover ziet u de **huidige locatie** en de locatie van de **failover**. De huidige locatie geeft altijd de locatie aan waar de hub momenteel actief is. De failover-locatie is de standaard [Azure geo-gekoppelde regio](../best-practices-availability-paired-regions.md) die is gekoppeld aan de huidige locatie. U kunt de locatiewaarden niet wijzigen. Voor deze zelf studie is `West US 2` de huidige locatie en de locatie van de `West Central US`failover.

   ![Schermafbeelding met het deelvenster Handmatige failover](./media/tutorial-manual-failover/trigger-failover-02.png)

1. Klik boven aan het deel venster hand matige failover op **failover starten**. 

1. In het bevestigings venster vult u de naam van uw IoT-hub in om te bevestigen dat u de failover wilt door voeren. Klik vervolgens op **failover**om de failover te initiëren.

   De tijd die nodig is om de handmatige failover uit te voeren is evenredig met het aantal apparaten die voor uw hub zijn geregistreerd. Als u bijvoorbeeld 100.000 apparaten hebt, dan kan het vijftien minuten duren, maar als u vijf miljoen apparaten hebt, dan kan het minstens een uur duren.

   ![Schermafbeelding met het deelvenster Handmatige failover](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Terwijl het proces hand matige failover wordt uitgevoerd, wordt er een banner weer gegeven om aan te geven dat er een hand matige failover wordt uitgevoerd. 

   ![Schermafbeelding waarin wordt aangegeven dat een handmatige failover wordt uitgevoerd](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Als u het deel venster IoT Hub sluit en opnieuw opent door erop te klikken in het deel venster Resource groep, ziet u een banner dat aangeeft dat de hub zich in het midden van een hand matige failover bevindt. 

   ![Scherm opname van IoT Hub failover wordt uitgevoerd](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   Nadat het is voltooid, worden de huidige en failover-regio's op de pagina hand matige failover gespiegeld en is de hub weer actief. In dit voor beeld is de huidige locatie nu `WestCentralUS` en de failover-locatie is `West US 2`nu. 

   ![Schermafbeelding waarin wordt aangegeven dat de failover is voltooid](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

   Op de pagina overzicht wordt ook een banner weer gegeven dat aangeeft dat de failover is voltooid en `West Central US`de IOT hub wordt uitgevoerd in.

   ![Scherm opname met de failover is voltooid op de pagina overzicht](./media/tutorial-manual-failover/trigger-failover-06-finished-overview.png)


## <a name="perform-a-failback"></a>Failback uitvoeren 

Nadat u een handmatige failover hebt uitgevoerd, kunt u de bewerkingen van de hub terugschakelen naar de oorspronkelijke primaire regio. Dit wordt een failback genoemd. Als u net een failover hebt uitgevoerd, moet u ongeveer een uur wachten voordat u een failback kunt aanvragen. Als u de failback eerder wilt uitvoeren, wordt er een foutbericht weergegeven.

Een failback wordt op dezelfde manier uitgevoerd als een handmatige failover. Dit zijn de stappen: 

1. Als u een failback wilt uitvoeren, gaat u terug naar het deelvenster IoT Hub voor uw IoT-hub.

2. Klik onder **instellingen** in het deel venster IOT hub op **failover**. 

3. Klik boven aan het deel venster hand matige failover op **failover starten**. 

4. In het bevestigings venster vult u de naam van uw IoT-hub in om te bevestigen dat deze het certificaat is dat u wilt failback. Klik op OK om de failback te starten. 

   ![Schermafbeelding met de aanvraag voor handmatige failback](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   De banners worden weergegeven zoals uitgelegd in de sectie Failover uitvoeren. Nadat de failback is voltooid, wordt deze weer `West US 2` gegeven als de huidige locatie `West Central US` en als de failover-locatie, zoals oorspronkelijk is ingesteld.

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
