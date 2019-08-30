---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e01bb13b904aaf8671512daa3ef3cbad182f01e3
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70161936"
---
In deze sectie wordt beschreven hoe u een IoT-hub maakt met behulp van de [Azure-portal](https://portal.azure.com).

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Kies **een resource maken**en voer *IOT hub* in het veld **Marketplace doorzoeken** in.

1. Selecteer **IOT hub** in de zoek resultaten en selecteer vervolgens **maken**.

1. Vul op het tabblad **basis beginselen** de volgende velden in:

   - **Abonnement**: Selecteer het abonnement dat u wilt gebruiken voor uw hub.

   - **Resourcegroep**: Selecteer een resource groep of maak een nieuwe. Als u een nieuw item wilt maken, selecteert u **nieuwe maken** en vult u de naam in die u wilt gebruiken. Als u een bestaande resource groep wilt gebruiken, selecteert u die resource groep. Zie [Azure Resource Manager-resourcegroepen beheren](../articles/azure-resource-manager/manage-resource-groups-portal.md) voor meer informatie.

   - **Regio**: Selecteer de regio waarin u de hub wilt plaatsen. Selecteer de locatie die het dichtst bij u ligt.

   - **Naam van de IoT Hub**: Voer een naam in voor uw hub. Deze naam moet wereldwijd uniek zijn. Als de door u opgegeven naam beschikbaar is, verschijnt er een groen vinkje.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Een hub maken in de Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics-vs2019.png)

1. Selecteer **Volgende: Grootte en schaal** om door te gaan met het maken van uw hub.

   ![De grootte en schaal instellen voor een nieuwe hub met behulp van de Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

    In dit scherm kunt u de volgende waarden instellen:

    - **Prijs- en schaalniveau**: De geselecteerde laag. U kunt kiezen uit verschillende lagen, afhankelijk van het aantal gewenste onderdelen en hoeveel berichten u per dag via uw oplossing verzendt. De gratis optie is bedoeld voor testen en evalueren. Hiermee kunnen 500-apparaten worden verbonden met de hub en Maxi maal 8.000 berichten per dag. Met de gratis optie kunt u voor elk Azure-abonnement één IoT Hub maken.

    - **IoT Hub-eenheden**: het aantal toegestane berichten per eenheid is afhankelijk van de prijscategorie van uw hub. Als u bijvoorbeeld wilt dat de hub ingangs-en 700.000-berichten ondersteunt, kiest u twee laag eenheden van S1.
    Zie [De juiste laag kiezen voor uw IoT-hub](../articles/iot-hub/iot-hub-scaling.md) voor informatie over andere opties.

    - **Geavanceerde instellingen** > **voor apparaat-naar-Cloud-partities**: met deze eigenschap worden de apparaat-naar-cloud-berichten gerelateerd met het aantal gelijktijdige lezers van de berichten. De meeste hubs hebben slechts vier partities nodig.

1. Voor dit artikel accepteert u de standaard keuzes en selecteert u vervolgens **controleren + maken** om uw keuzes te controleren. U krijgt iets te zien wat lijkt op dit scherm.

   ![Informatie over het maken van de nieuwe hub bekijken](./media/iot-hub-include-create-hub/iot-hub-create-review-vs2019.png)

1. Selecteer **maken** om uw nieuwe hub te maken. De hub wordt binnen enkele minuten gemaakt.
