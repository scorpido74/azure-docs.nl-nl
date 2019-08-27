---
title: include-bestand (preview apparaatstreams)
description: include-bestand (preview apparaatstreams)
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/14/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a02c16a75bbdf4827ce6a6c5f50751caf9c5aab8
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "67445987"
---
In deze sectie wordt beschreven hoe u een IoT-hub maakt met behulp van de [Azure Portal](https://portal.azure.com).

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **een resource maken**en selecteer vervolgens **Internet of Things**.

1. Selecteer **IOT hub**in de lijst aan de rechter kant. De eerste pagina voor het maken van een IoT hub wordt geopend.

   ![Een IoT-hub maken in de Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-01.png)

   Vul de velden in:

   a. Selecteer in de vervolg keuzelijst **abonnement** het abonnement dat u wilt gebruiken voor uw IOT-hub.

   b. Voer een van de volgende handelingen uit voor de **resource groep**: 
      * Als u een nieuwe resource groep wilt maken, selecteert u **nieuwe maken** en voert u de naam in die u wilt gebruiken. 
      * Als u een bestaande resource groep wilt gebruiken, selecteert u **bestaande gebruiken** en selecteert u vervolgens de resource groep in de vervolg keuzelijst. 
      
        Zie [Azure Resource Manager-resourcegroepen beheren](../articles/azure-resource-manager/manage-resource-groups-portal.md) voor meer informatie.

   c. Selecteer in de vervolg keuzelijst **regio** de regio waarin u wilt dat uw hub zich bevindt. Selecteer een regio die ondersteuning biedt voor de preview-versie van de IoT Hub apparaten, zowel in de **VS** als in de **VS-EUAP**.

   d. Voer in het vak **IOT hub naam** de naam in voor uw IOT-hub. De naam moet wereldwijd uniek zijn. Als de door u opgegeven naam beschikbaar is, verschijnt er een groen vinkje.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. Selecteer **volgende om door te gaan met het maken van uw IOT-hub: Grootte en schaal**.

   ![Grootte en schaal instellen voor een nieuwe IoT-hub met behulp van de Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   In dit deel venster kunt u de standaard instellingen accepteren en onder aan de onderkant selecteren op **controleren + maken** . Houd rekening met de volgende opties:

   * Selecteer in de vervolg keuzelijst **prijs-en schaal niveau** een van de standaard lagen (**S1**, **S2**of **S3**) of **F1: Gratis laag**. Deze keuze kan ook worden geleid door de grootte van uw vloot en de niet-streaming werk belastingen die u op uw hub verwacht (bijvoorbeeld telemetrie-berichten). Zo is de gratis laag bedoeld voor testen en evalueren. Hiermee kunnen 500 apparaten met de IoT-hub worden verbonden en maximaal 8000 berichten per dag verzonden. Elk Azure-abonnement kan één IoT-hub maken in de gratis laag. 

   * Voor het **aantal IOT hub eenheden**: Deze keuze is afhankelijk van de niet-streaming werk belasting die u verwacht in uw hub. U kunt 1 nu selecteren.

   Zie [de juiste IOT hub-laag kiezen](../articles/iot-hub/iot-hub-scaling.md)voor meer informatie over laag opties.

1. Als u uw keuzes wilt bekijken, selecteert u het tabblad **controleren + maken** . Het deel venster dat wordt geopend, is vergelijkbaar met het volgende:

   ![Informatie voor het maken van de nieuwe IoT hub](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Selecteer **maken**om uw nieuwe IOT-hub te maken. Het proces duurt enkele minuten.
