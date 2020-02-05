---
title: Een Azure IoT Central-toepassing maken | Microsoft Docs
description: Maak een nieuwe Azure IoT Central-toepassing. Maak de toepassing met behulp van het gratis prijs plan of een van de standaard prijs plannen.
author: viv-liu
ms.author: viviali
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 5268f5438c005033f9c6ecf74657dc1a01d3b673
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989387"
---
# <a name="create-an-azure-iot-central-application"></a>Een Azure IoT Central-toepassing maken

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Als _maker_ gebruikt u de gebruikersinterface van Azure IoT Central om uw Microsoft Azure IoT Central-toepassing te definiëren. In deze Quick start ziet u hoe u een Azure IoT Central-toepassing kunt maken die een voorbeeld _sjabloon_bevat. De toepassing die u maakt, gebruikt geen preview-functies.

## <a name="create-an-application"></a>Een app maken

Ga naar de [Azure IOT Central build](https://aka.ms/iotcentral) -website. Meld u vervolgens aan met een persoonlijk, werk-of school account van micro soft.

Selecteer **Build**om te beginnen met het maken van een Azure IOT Central-toepassing zonder preview-functies ingeschakeld. Met deze koppeling gaat u naar de pagina **uw IOT-toepassing bouwen** .

![Pagina Azure IoT Central-build](media/quick-deploy-iot-central/iotcentralcreate.png)

Selecteer vervolgens **aangepaste app**.

Een nieuwe Azure IoT Central-toepassing maken:

1. In azure IoT Central wordt automatisch een toepassings naam voorgesteld op basis van de toepassings sjabloon die u hebt geselecteerd. U kunt deze naam accepteren of uw eigen beschrijvende toepassings naam invoeren, zoals **Contoso IOT**. Azure IoT Central genereert ook een unieke URL voor u, op basis van de naam van de toepassing. Als u wilt, kunt u dit URL-voor voegsel wijzigen in iets eenvoudiger te onthouden.

1. Selecteer de sjabloon **legacy-toepassing** die geen gebruik maakt van preview-functies.

    | Toepassingsjabloon | Beschrijving |
    | -------------------- | ----------- |
    | Oudere toepassing   | Hiermee maakt u een lege toepassing die u kunt vullen met uw eigen apparaatsjablonen en apparaten. |

    ![Azure IoT Central nieuwe toepassing](media/quick-deploy-iot-central/newapplication.png)

    ![Facturerings gegevens voor Azure IoT Central](media/quick-deploy-iot-central/billinginfo.png)

1. Kies voor het maken van deze toepassing met het prijs plan gratis proef versie van 7 dagen of een van de standaard prijs plannen:
   - Toepassingen die u maakt met behulp van het *gratis* abonnement, zijn zeven dagen gratis en ondersteunen Maxi maal vijf apparaten. U kunt ze op elk gewenst moment converteren om een standaard-prijs plan te gebruiken voordat ze verlopen.
   - Toepassingen die u maakt met behulp van het *Standard* -abonnement, worden per apparaat gefactureerd. u kunt het prijs plan **Standard 1** of **Standard 2** kiezen waarbij de eerste twee apparaten gratis zijn. Meer informatie over de gratis en standaard prijzen abonnementen op de [pagina met prijzen voor Azure IOT Central](https://azure.microsoft.com/pricing/details/iot-central/). Als u een standaard toepassing maakt, moet u uw *Directory*, het Azure- *abonnement*en de *locatie*selecteren:
        - *Directory* is de Azure Active Directory (AD) voor het maken van uw toepassing. Deze bevat gebruikers-id's, referenties en andere organisatiegegevens. Als u geen Azure AD hebt, wordt er een voor u gemaakt wanneer u een Azure-abonnement maakt.
        - Als u een *Azure-abonnement* hebt, kunt u instanties van Azure-services maken. IoT Central resources in uw abonnement. Als u geen Azure-abonnement hebt, kunt u er een maken via de [Azure-aanmeldingspagina](https://aka.ms/createazuresubscription). Nadat u het Azure-abonnement hebt gemaakt, gaat u terug naar de pagina **een toepassing maken** . Uw nieuwe abonnement wordt weergegeven in de vervolgkeuzelijst.**Azure-abonnement**.
        - *Locatie* is de [geografie](https://azure.microsoft.com/global-infrastructure/geographies/) waar u de toepassing wilt maken. Normaal gesp roken kiest u de locatie die zich het dichtst in de buurt van uw apparaten bevindt om optimaal te pres teren. Azure IoT Central is momenteel beschikbaar in de **Verenigde Staten**, **Australië**, **Azië en Stille Oceaan**of in **Europa**.  Wanneer u een locatie hebt gekozen, kunt u de toepassing later niet meer naar een andere locatie verplaatsen.
        Lees meer over prijzen op de [prijzenpagina van IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

1. Vul in stap 1 de aanvullende informatie in die vereist is voor het betalings schema dat u eerder hebt geselecteerd.

1. Selecteer **Maken** onderaan de pagina.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een IoT Central-toepassing gemaakt. Dit is de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Een nieuw apparaattype definiëren in uw Azure IoT Central-toepassing](./tutorial-define-device-type.md)
