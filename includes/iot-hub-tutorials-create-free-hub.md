---
title: bestand opnemen
description: bestand opnemen
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 367a0b1d17f8d5ebe4f46835ace963b00e75354e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68229315"
---
Een IoT-hub maken met behulp van Azure Portal:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **een resource** > **maken internet of Things** > **IOT hub**.

    ![Selecteren om IoT Hub te installeren](media/iot-hub-tutorials-create-free-hub/selectiothub.png)

1. Gebruik de waarden in de volgende tabellen om uw IoT-hub in de gratis laag te maken:

    | Instelling | Waarde |
    | ------- | ----- |
    | Abonnement | Selecteer uw Azure-abonnement in de vervolgkeuzelijst. |
    | Resourcegroep | Nieuwe maken. In deze zelfstudie wordt de naam **tutorials-iot-hub-rg** gebruikt. |
    | Regio | In deze zelfstudie wordt gebruikgemaakt van **VS - west**. U kunt de regio kiezen die zich het meest in uw buurt bevindt. |
    | Naam | In de volgende schermafbeelding wordt de naam **tutorials-iot-hub** gebruikt. U moet uw eigen unieke naam kiezen wanneer u uw hub maakt. |

    ![Hub-instellingen 1](media/iot-hub-tutorials-create-free-hub/hubdefinition-1.png)

    | Instelling | Waarde |
    | ------- | ----- |
    | Prijs- en schaalniveau | F1 Gratis. U kunt maar één hub in de gratis laag in een abonnement hebben. |
    | IoT Hub-eenheden | 1 |

    ![Hub-instellingen 2](media/iot-hub-tutorials-create-free-hub/hubdefinition-2.png)

1. Klik op **maken**. Het kan enkele minuten duren voordat de hub is gemaakt.

    ![Hub-instellingen 3](media/iot-hub-tutorials-create-free-hub/hubdefinition-3.png)

1. Noteer de naam van de IoT-hub die u hebt gekozen. U gebruikt deze waarde verderop in de zelfstudie.
