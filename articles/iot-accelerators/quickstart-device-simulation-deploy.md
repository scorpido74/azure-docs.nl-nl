---
title: Een oplossing voor apparaatsimulatie proberen en uitvoeren - Azure | Microsoft Docs
description: In deze snelstart implementeert u Azure IoT Apparaatsimulatie en voert u een simulatie uit
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/08/2019
ms.author: troyhop
ms.openlocfilehash: 68e0c3c89698bafbab65ca786231e0364900213a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "65467836"
---
# <a name="quickstart-deploy-and-run-an-iot-device-simulation-in-azure"></a>Snelstart: Een IoT-apparaatsimulatie in Azure implementeren en uitvoeren

Deze snelstart laat zien hoe u Azure IoT Apparaatsimulatie implementeert om uw IoT-oplossing te testen. Nadat u de oplossingsversneller hebt geïmplementeerd, voert u een voorbeeldsimulatie uit om aan de slag te gaan.

U hebt een actief Azure-abonnement nodig om deze snelstart te voltooien.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="deploy-device-simulation"></a>Apparaatsimulatie implementeren

Wanneer u Apparaatsimulatie implementeert in uw Azure-abonnement, moet u bepaalde configuratieopties instellen.

Meld u aan bij [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) met behulp van de referenties van uw Azure-account.

Klik op de tegel **Apparaatsimulatie**:

![Kies Apparaatsimulatie](./media/quickstart-device-simulation-deploy/devicesimulation.png)

Klik op **Nu proberen** op de pagina waar Apparaatsimulatie wordt beschreven:

![Klik op Nu proberen](./media/quickstart-device-simulation-deploy/devicesimulationpdp1.png)

Voer een unieke **Oplossingsnaam** in op de pagina **Apparaatsimulatieoplossing maken**.

Selecteer het **Abonnement** en de **Regio** die u wilt gebruiken om de oplossingsverbetering te implementeren. U kiest meestal de regio die zich het meest in uw buurt bevindt. U moet een [globale beheerder of gebruiker](iot-accelerators-permissions.md) zijn in het abonnement.

Schakel het selectievakje in om een IoT-hub te implementeren voor gebruik met uw apparaatsimulatieoplossing. U kunt de IoT-hub die de simulatie gebruikt later altijd wijzigen.

Klik op **Maken** om uw oplossing in te richten. Het duurt minstens vijf minuten om dit proces uit te voeren:

![Informatie over apparaatsimulatieoplossing](./media/quickstart-device-simulation-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Aanmelden bij de oplossing

Wanneer het inrichtingsproces is voltooid, kunt u zich aanmelden bij uw instantie van Apparaatsimulatie door op de knop **Starten** te klikken:

![Apparaatsimulatie openen](./media/quickstart-device-simulation-deploy/choosenew.png)

Klik op **Accepteren** om de machtigingsaanvraag te accepteren. Het oplossingsdashboard voor apparaatsimulatie wordt weergegeven in de browser.

Bij eerste opening ziet u het Apparaatsimulatie-dashboard met een **Aan de slag**-handleiding. Klik op de eerste tegel om een voorbeeldsimulatie te openen. Als u de **Aan de slag**-handleiding sluit, kunt u de **Eenvoudige voorbeeldsimulatie** openen vanuit het dashboard door op de tegel ervan te klikken:

![Dashboard van de oplossing](./media/quickstart-device-simulation-deploy/gettingstarted1.png)

## <a name="sample-simulation"></a>Voorbeeldsimulatie

Omdat het een voorbeeldsimulatie is, kan deze niet worden bewerkt. De simulatie wordt geconfigureerd met de volgende instellingen:

| Instelling             | Waarde                       |
| ------------------- | --------------------------- |
| Doel-IoT-hub      | Vooraf ingerichte IoT-hub gebruiken |
| Apparaatmodel        | Truck                       |
| Aantal apparaten   | 10                          |
| Telemetriefrequentie | 10 seconden                  |
| Simulatieduur | Voor onbepaalde tijd uitvoeren            |

![Simulatieconfiguratie](./media/quickstart-device-simulation-deploy/samplesimulation1.png)

## <a name="run-the-simulation"></a>De simulatie uitvoeren

Klik op **Simulatie starten**. Zoals geconfigureerd, wordt de simulatie voor onbepaalde tijd uitgevoerd. U kunt de simulatie op elk gewenst moment stoppen door te klikken op **Simulatie stoppen**. De simulatie toont de statistieken voor de huidige sessie.

![Simulatie-uitvoering](./media/quickstart-device-simulation-deploy/runningsimulation.png)

U kunt slechts één simulatie tegelijk uitvoeren vanuit een Apparaatsimulatie-instantie.

## <a name="clean-up-resources"></a>Resources opschonen

Laat Apparaatsimulatie geïmplementeerd als u nog meer wilt verkennen.

Als u Apparaatsimulatie niet langer nodig hebt, verwijdert u deze van de pagina [Ingerichte oplossingen](https://www.azureiotsolutions.com/Accelerators#dashboard) door op de tegel te klikken en vervolgens op ** Oplossing verwijderen ** te klikken:

![Oplossing verwijderen](media/quickstart-device-simulation-deploy/deletesolution.png)

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u Apparaatsimulatie geïmplementeerd en een voorbeeld-IoT-simulatie uitgevoerd.

> [!div class="nextstepaction"]
> [Een simulatie maken met een of meer apparaattypen](iot-accelerators-device-simulation-create-simulation.md)