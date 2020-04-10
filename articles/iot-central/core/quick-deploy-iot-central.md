---
title: Een Azure IoT Central-toepassing maken | Microsoft Docs
description: Maak een nieuwe Azure IoT Central-toepassing. Maak de toepassing met behulp van het gratis prijsplan of een van de standaardprijsplannen.
author: viv-liu
ms.author: viviali
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 0ed32a4c1272c23c9500b35e05c383eac6dea185
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998948"
---
# <a name="create-an-azure-iot-central-application"></a>Een Azure IoT Central-toepassing maken

In deze snelstart ziet u hoe u een Azure IoT Central-toepassing maakt.

## <a name="create-an-application"></a>Een app maken

Navigeer naar de [Azure IoT Central Build-site.](https://aka.ms/iotcentral) Meld u vervolgens aan met een persoonlijk, werk- of schoolaccount van Microsoft.

U maakt een nieuwe toepassing vanuit de lijst met brancherelevante IoT Central-sjablonen om u te helpen snel aan de slag te gaan, of om helemaal opnieuw te beginnen met een sjabloon **voor aangepaste apps.** In deze snelstart gebruikt u de sjabloon **Aangepaste toepassing.**

Ga als volgende voor een nieuwe Azure IoT Central-toepassing op basis van de **sjabloon Aangepaste toepassing:**

1. Navigeer naar de pagina **Bouwen:**

    ![Uw IoT-toepassingspagina bouwen](media/quick-deploy-iot-central/iotcentralcreate-new-application.png)

1. Kies **Aangepaste apps** en controleer of de aangepaste **toepassingssjabloon** is geselecteerd.

1. Azure IoT Central stelt automatisch een **toepassingsnaam** voor op basis van de toepassingssjabloon die u hebt geselecteerd. U deze naam gebruiken of uw eigen vriendelijke toepassingsnaam invoeren.

1. Azure IoT Central genereert ook een uniek **voorvoegsel voor toepassing-URL's** voor u op basis van de naam van de toepassing. U gebruikt deze URL om toegang te krijgen tot uw toepassing. Wijzig dit URL-voorvoegsel naar iets meer memorabele als je wilt.

    ![Azure IoT Central Een toepassingspagina maken](media/quick-deploy-iot-central/iotcentralcreate-custom.png)

    ![Factureringsgegevens van Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png)

    > [!NOTE]
    > Als u **aangepaste app** op de vorige pagina hebt gekozen, ziet u een vervolgkeuzelijst voor **toepassingen.** Vanaf hier u schakelen tussen aangepaste en legacy templates. Mogelijk ziet u ook andere sjablonen die beschikbaar zijn gesteld voor uw organisatie.

1. Kies ervoor om deze toepassing te maken met behulp van het 7-daagse gratis proeftariefplan of een van de standaardprijsplannen:

    - Toepassingen die u maakt met behulp van het *gratis* abonnement zijn zeven dagen gratis en ondersteunen maximaal vijf apparaten. U ze converteren naar een standaard prijsplan op elk gewenst moment voordat ze verlopen.
    - Toepassingen die u maakt met behulp van een *standaardplan* worden gefactureerd op basis van een apparaat, u kiezen voor **een Standaard 1-** of **Standard 2-prijsplan** waarbij de eerste twee apparaten gratis zijn. Meer informatie over de gratis en standaard prijsplannen vindt u op de [prijspagina van Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). Als u een toepassing maakt met een standaardprijsplan, moet u uw *directory,* *Azure-abonnement*en *locatie*selecteren:
        - *Directory* is de Azure Active Directory waarin u uw toepassing maakt. Een Azure Active Directory bevat gebruikersidentiteiten, referenties en andere organisatiegegevens. Als u geen Azure Active Directory hebt, wordt er een voor u gemaakt wanneer u een Azure-abonnement maakt.
        - Als u een *Azure-abonnement* hebt, kunt u instanties van Azure-services maken. IoT Central bevoorraden resources in uw abonnement. Als u geen Azure-abonnement hebt, u er gratis een maken op de [azure-aanmeldingspagina.](https://aka.ms/createazuresubscription) Nadat u het Azure-abonnement hebt gemaakt, navigeert u terug naar de pagina **Nieuwe toepassing.** Uw nieuwe abonnement wordt nu weergegeven in de vervolgkeuzelijst **Azure-abonnement.**
        - *Locatie* is de [geografie](https://azure.microsoft.com/global-infrastructure/geographies/) waar u uw toepassing wilt maken. Meestal moet u de locatie kiezen die fysiek het dichtst bij uw apparaten ligt om optimale prestaties te krijgen. Zodra u een locatie kiest, u uw toepassing niet later naar een andere locatie verplaatsen.

1. Bekijk de algemene voorwaarden en selecteer **Maken** onder aan de pagina. Na een paar minuten is uw IoT Central-toepassing klaar voor gebruik:

    ![Azure IoT Central-toepassing](media/quick-deploy-iot-central/iotcentral-application.png)

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een IoT Central-toepassing gemaakt. Dit is de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Een gesimuleerd apparaat toevoegen aan uw IoT Central-toepassing](./quick-create-simulated-device.md)
