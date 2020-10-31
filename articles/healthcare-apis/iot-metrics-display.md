---
title: De metrische gegevens van Azure IoT connector voor FHIR (preview) weer geven en configureren
description: In dit artikel wordt uitgelegd hoe u de metrische gegevens van Azure IoT connector voor FHIR (preview) weergeeft en configureert.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/29/2020
ms.author: jasteppe
ms.openlocfilehash: 76166fc5c525d36474a585179472e93b22dad647
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133543"
---
# <a name="view-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>De metrische gegevens van Azure IoT connector voor FHIR (preview) weer geven en configureren 

In dit artikel leert u hoe u Azure IoT connector kunt weer geven en configureren voor FHIR *-metrische gegevens. 

> [!TIP]
> Volg de richt lijnen in [Azure IOT connector voor FHIR (preview) exporteren via Diagnostische instellingen](https://docs.microsoft.com/azure/healthcare-apis/iot-metrics-diagnostics-export) voor meer informatie over het instellen van het exporteren van metrische gegevens.

## <a name="view-metrics-for-azure-iot-connector-for-fhir-preview"></a>Metrische gegevens weer geven voor Azure IoT connector voor FHIR (preview)
1. Als u metrische gegevens voor IoT-connectors wilt weer geven, selecteert u uw Azure API for FHIR-service in de Azure Portal. 

2. Ga naar **metrische gegevens** 

3. Selecteer het tabblad **IOT-connector** .

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="IoT-Connector1" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. Selecteer een IoT-connector om de metrische gegevens weer te geven (bijvoorbeeld: er zijn (4) IoT-connectors gekoppeld aan deze Azure API for FHIR-service).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="IoT-Connector1" lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

> [!NOTE]
> Op het tabblad **aangepast** kunnen specifieke tijd/datum combinaties worden gemaakt voor het weer geven van metrische gegevens voor IOT-connector.

5. Selecteer de tijds periode van de gegevens van de IoT-connector die moeten worden weer gegeven (bijvoorbeeld: 1 uur, 24 uur, 7 dagen of aangepast).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="IoT-Connector1" lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metrics-types-for-azure-iot-connector-for-fhir-preview"></a>Metrische typen voor Azure IoT connector voor FHIR (preview-versie) 
De gegevens van de weer gegeven IoT-connector zijn als volgt:

|Type metrische gegevens|Doel van metrische gegevens| 
|-----------|--------------|
|Aantal inkomende berichten|Het aantal ontvangen onbewerkte inkomende berichten (bijvoorbeeld: de gebeurtenissen van het apparaat).|
|Aantal genormaliseerde berichten|Het aantal genormaliseerde berichten.|
|Aantal bericht groepen|Het aantal groepen waarvoor berichten zijn samengevoegd in een opgegeven tijd venster.|
|Gemiddelde genormaliseerde fase latentie|Gemiddelde latentie van de normale-fase. Normaliseren fase is het uitvoeren van normalisatie op onbewerkte inkomende berichten.|
|Gemiddelde latentie van groeps fase|Gemiddelde latentie van de groeps fase. Groeps fase is het uitvoeren van buffers, het samen voegen en groeperen van genormaliseerde berichten.| 
|Totaal aantal fouten|Totaal aantal fouten.| 

## <a name="focusing-and-configuring-azure-iot-connector-for-fhir-preview-metrics"></a>Het concentreren en configureren van Azure IoT connector voor FHIR-metrische gegevens (preview)
In dit voor beeld wordt gefocust op het **aantal metrische gegevens van binnenkomende berichten** .

1. Selecteer een tijdgebonden punt waarop u wilt focussen.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="IoT-Connector1" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. In dit scherm kunt u **metrische gegevens toevoegen** , **filters toevoegen** en **splitsing Toep assen** voor verdere aanpassingen. 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="IoT-Connector1" lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>Conclusie 
Het hebben van toegang tot metrische gegevens vlak is essentieel voor het controleren en oplossen van problemen.  Azure IoT connector voor FHIR helpt u bij het uitvoeren van deze acties via metrische gegevens. 

## <a name="next-steps"></a>Volgende stappen

Bekijk veelgestelde vragen over de Azure IoT-connector voor FHIR.

>[!div class="nextstepaction"]
>[Veelgestelde vragen over Azure IoT connector voor FHIR](fhir-faq.md)

*In Azure Portal wordt Azure IoT Connector for FHIR aangeduid als IoT Connector (preview).

FHIR is het gedeponeerde handelsmerk van HL7 en wordt gebruikt met de toestemming van HL7.