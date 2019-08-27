---
title: include-bestand (preview apparaatstreams)
description: include-bestand (preview apparaatstreams)
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 46f8e59713896cd94b96de62d982072119c32513
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050171"
---
In deze sectie wordt beschreven hoe u een IoT-hub maakt met behulp van de [Azure-portal](https://portal.azure.com).

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Kies **een resource maken**en voer *IOT hub* in het veld **Marketplace doorzoeken** in.

1. Selecteer **IOT hub** in de zoek resultaten en selecteer vervolgens **maken**.

1. Vul op het tabblad **basis beginselen** de volgende velden in:

   - **Abonnement**: Selecteer het abonnement dat u wilt gebruiken voor uw hub.

   - **Resourcegroep**: Selecteer een resource groep of maak een nieuwe. Als u een nieuw item wilt maken, selecteert u **nieuwe maken** en vult u de naam in die u wilt gebruiken. Als u een bestaande resource groep wilt gebruiken, selecteert u die resource groep. Zie [Azure Resource Manager-resourcegroepen beheren](../articles/azure-resource-manager/manage-resource-groups-portal.md) voor meer informatie.

   - **Regio**: Kies de regio waarin u de hub wilt plaatsen. Selecteer een regio die ondersteuning biedt voor de preview-versie van de IoT Hub apparaten, zowel in de **VS** als in de **VS-EUAP**.

   - **Naam van de IoT Hub**: Voer een naam in voor uw hub. Deze naam moet wereldwijd uniek zijn. Als de door u opgegeven naam beschikbaar is, verschijnt er een groen vinkje.

   ![Een IoT-hub maken in de Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-device-streams.png)

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. Selecteer **Volgende: Grootte en schaal** om door te gaan met het maken van uw hub.

   ![Grootte en schaal instellen voor een nieuwe IoT-hub met behulp van de Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   In **grootte en schaal**kunt u de standaard instellingen accepteren en onder aan de onderkant de optie **controleren + maken** selecteren. Houd rekening met de volgende opties:

   - **Prijs- en schaalniveau**: De geselecteerde laag. Selecteer een van de standaard lagen (**S1**, **S2**of **S3**) of **F1: Gratis laag**. Deze keuze kan ook worden geleid door de grootte van uw vloot en de niet-streaming werk belastingen die u op uw hub verwacht, bijvoorbeeld telemetrie-berichten. Zo is de gratis laag bedoeld voor testen en evalueren. Hiermee kunnen 500 apparaten met de IoT-hub worden verbonden en maximaal 8000 berichten per dag verzonden. Elk Azure-abonnement kan één IoT-hub maken in de gratis laag. 

   - **Aantal IOT hub eenheden**: het aantal toegestane berichten per eenheid is afhankelijk van de prijscategorie van uw hub. Deze keuze is afhankelijk van de niet-streaming werk belasting die u verwacht in uw hub. U kunt 1 nu selecteren.

   - **Geavanceerde instellingen** > **voor apparaat-naar-Cloud-partities**: met deze eigenschap worden de apparaat-naar-cloud-berichten gerelateerd met het aantal gelijktijdige lezers van de berichten. De meeste hubs hebben slechts vier partities nodig.

   Zie [de juiste IOT hub-laag kiezen](../articles/iot-hub/iot-hub-scaling.md)voor meer informatie over laag opties.

1. Als u uw keuzes wilt bekijken, kiest u **controleren + maken**. De resultaten zijn vergelijkbaar met het volgende:

   ![Informatie voor het maken van de nieuwe IoT hub](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Selecteer **maken**om uw nieuwe IOT-hub te maken. Het proces duurt enkele minuten.
