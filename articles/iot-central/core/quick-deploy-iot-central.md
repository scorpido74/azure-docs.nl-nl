---
title: Een Azure IoT Central-toepassing maken | Microsoft Docs
description: Maak een nieuwe Azure IoT Central-toepassing. Maak de toepassing met behulp van het gratis prijs plan of een van de standaard prijs plannen.
author: viv-liu
ms.author: viviali
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: b670776804c9758774bf216052254148f063e7da
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81758141"
---
# <a name="create-an-azure-iot-central-application"></a>Een Azure IoT Central-toepassing maken

In deze Quick start ziet u hoe u een Azure IoT Central-toepassing maakt.

## <a name="create-an-application"></a>Een app maken

Ga naar de [Azure IOT Central build](https://aka.ms/iotcentral) -site. Meld u vervolgens aan met een persoonlijk, werk-of school account van micro soft.

U maakt een nieuwe toepassing in de lijst met branchespecifieke IoT Central sjablonen waarmee u snel aan de slag kunt gaan, of helemaal met een aangepaste sjabloon voor **apps** . In deze Quick Start gebruikt u de sjabloon **aangepaste toepassing** .

Een nieuwe Azure IoT Central-toepassing maken vanuit de sjabloon voor **aangepaste toepassingen** :

1. Ga naar de pagina **bouwen** :

    ![Uw IoT-toepassings pagina bouwen](media/quick-deploy-iot-central/iotcentralcreate-new-application.png)

1. Kies **aangepaste apps** en zorg ervoor dat de sjabloon voor de **aangepaste toepassing** is geselecteerd.

1. In azure IoT Central wordt automatisch een **toepassings naam** voorgesteld op basis van de toepassings sjabloon die u hebt geselecteerd. U kunt deze naam gebruiken of uw eigen beschrijvende toepassings naam invoeren.

1. Azure IoT Central genereert ook een uniek voor voegsel voor de **toepassings-URL** , op basis van de naam van de toepassing. U gebruikt deze URL om toegang te krijgen tot uw toepassing. Wijzig dit URL-voor voegsel in iets wat u graag wilt onthouden.

    ![Een toepassings pagina maken in azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate-custom.png)

    ![Facturerings gegevens voor Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png)

    > [!NOTE]
    > Als u op de vorige pagina **aangepaste app** hebt gekozen, ziet u een vervolg keuzelijst voor de **toepassings sjabloon** . Hier kunt u scha kelen tussen aangepaste en verouderde Sjablonen. Mogelijk ziet u ook andere sjablonen die beschikbaar zijn gesteld voor uw organisatie.

1. Kies voor het maken van deze toepassing met het prijs plan gratis proef versie van 7 dagen of een van de standaard prijs plannen:

    - Toepassingen die u maakt met behulp van het *gratis* abonnement, zijn zeven dagen gratis en ondersteunen Maxi maal vijf apparaten. U kunt ze op elk gewenst moment converteren om een standaard-prijs plan te gebruiken voordat ze verlopen.
    - Toepassingen die u maakt met behulp van een *standaard* abonnement, worden per apparaat gefactureerd, u kunt het prijs plan **Standard 1** of **Standard 2** kiezen waarbij de eerste twee apparaten gratis zijn. Meer informatie over de gratis en standaard prijzen abonnementen op de [pagina met prijzen voor Azure IOT Central](https://azure.microsoft.com/pricing/details/iot-central/). Als u een toepassing maakt met behulp van een Standard-prijs plan, moet u uw *Directory*, *Azure-abonnement*en *locatie*selecteren:
        - *Directory* is de Azure Active Directory waarin u uw toepassing maakt. Een Azure Active Directory bevat gebruikers identiteiten, referenties en andere informatie over de organisatie. Als u geen Azure Active Directory hebt, wordt er een voor u gemaakt wanneer u een Azure-abonnement maakt.
        - Als u een *Azure-abonnement* hebt, kunt u instanties van Azure-services maken. IoT Central resources in uw abonnement. Als u geen Azure-abonnement hebt, kunt u er gratis een maken op de [Azure-aanmeldings pagina](https://aka.ms/createazuresubscription). Nadat u het Azure-abonnement hebt gemaakt, gaat u terug naar de pagina **nieuwe toepassing** . Uw nieuwe abonnement wordt nu weer gegeven in de vervolg keuzelijst van het **Azure-abonnement** .
        - *Locatie* is de [geografie](https://azure.microsoft.com/global-infrastructure/geographies/) waar u de toepassing wilt maken. Normaal gesp roken kiest u de locatie die zich het dichtst in de buurt van uw apparaten bevindt om optimaal te pres teren. Wanneer u een locatie hebt gekozen, kunt u de toepassing later niet verplaatsen naar een andere locatie.

1. Bekijk de voor waarden en selecteer **maken** onder aan de pagina. Na een paar minuten kunt u IoT Central toepassing gereed is voor gebruik:

    ![Azure IoT Central-toepassing](media/quick-deploy-iot-central/iotcentral-application.png)

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een IoT Central-toepassing gemaakt. Hier volgt de voorgestelde volgende stap om te blijven leren over IoT Central:

> [!div class="nextstepaction"]
> [Een gesimuleerd apparaat toevoegen aan uw IoT Central-toepassing](./quick-create-simulated-device.md)

Als u een ontwikkelaar van het apparaat bent en een aantal code wilt laten opdoen, is de voorgestelde volgende stap:
> [!div class="nextstepaction"]
> [Een client toepassing maken en verbinden met uw Azure IoT Central-toepassing](./tutorial-connect-device-nodejs.md)
