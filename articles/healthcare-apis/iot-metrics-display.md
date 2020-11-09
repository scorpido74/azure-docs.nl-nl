---
title: De metrische gegevens van Azure IoT connector voor FHIR (preview) weer geven en configureren
description: In dit artikel wordt uitgelegd hoe u de metrische gegevens van Azure IoT connector voor FHIR (preview) kunt weer geven en configureren.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/29/2020
ms.author: jasteppe
ms.openlocfilehash: 9a4e2c4dfe8a9de28688afe0dd036cecb7ce2b39
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381215"
---
# <a name="display-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>De metrische gegevens van Azure IoT connector voor FHIR (preview) weer geven en configureren 

In dit artikel leert u hoe u Azure IoT connector kunt weer geven en configureren voor snelle interoperabiliteits bronnen voor gezondheids zorg (FHIR&#174;) * metrische gegevens.

> [!TIP]
> Voor meer informatie over het instellen van het exporteren van metrische gegevens, volgt u de instructies in een [Azure IOT-connector exporteren voor FHIR (preview) met behulp van diagnostische instellingen](./iot-metrics-diagnostics-export.md).

## <a name="display-metrics-for-azure-iot-connector-for-fhir-preview"></a>Metrische gegevens weer geven voor Azure IoT connector voor FHIR (preview-versie)

1. Meld u aan bij de Azure Portal en selecteer vervolgens uw Azure API for FHIR-service. 

2. Selecteer **metrische gegevens** in het linkerdeel venster. 

3. Selecteer het tabblad **IOT-connector** .

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="Scherm opname van het deel venster van de IoT-connector, met lijn grafieken waarmee het aantal inkomende en genormaliseerde berichten wordt weer gegeven." lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. Selecteer een IoT-connector om de metrische gegevens weer te geven. Er zijn bijvoorbeeld vier IoT-connectors ( *connector 1* , *Connector 2* , enzovoort) die zijn gekoppeld aan deze Azure API voor de FHIR-service.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="Scherm opname van het deel venster IoT-connector, de tabbladen 1, 2, 3 en 4 van IoT-connector weer geven." lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

5. Selecteer de tijds periode (bijvoorbeeld **1 uur** , **24 uur** , **7 dagen** of **aangepast** ) van de metrische gegevens van de IOT-connector die u wilt weer geven. Als u het **aangepaste** tabblad selecteert, kunt u specifieke tijd/datum combinaties maken voor het weer geven van metrische gegevens van IOT-connector.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="Scherm afbeelding van het deel venster van de IoT-connector, met een ' 1 uur ' tijd periode lijn diagram voor ' connector 1 '." lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metric-types-for-azure-iot-connector-for-fhir-preview"></a>Metrische typen voor Azure IoT connector voor FHIR (preview-versie) 

De metrische gegevens voor de IoT-connector die u kunt weer geven, worden weer gegeven in de volgende tabel:

|Metrisch type|Metrische doel| 
|-----------|--------------|
|Aantal inkomende berichten|Hier wordt het aantal ontvangen onbewerkte inkomende berichten weer gegeven (bijvoorbeeld de gebeurtenissen van het apparaat).|
|Aantal genormaliseerde berichten|Hiermee wordt het aantal genormaliseerde berichten weer gegeven.|
|Aantal bericht groepen|Geeft het aantal groepen weer waarvoor berichten zijn samengevoegd in het opgegeven tijd venster.|
|Gemiddelde genormaliseerde fase latentie|Hiermee wordt de gemiddelde latentie van de genormaliseerde fase weer gegeven. De genormaliseerde fase voert normalisatie uit op onbewerkte inkomende berichten.|
|Gemiddelde latentie van groeps fase|Hiermee wordt de gemiddelde latentie van de groeps fase weer gegeven. De groeps fase voert buffering, aggregatie en groepering uit op genormaliseerde berichten.| 
|Totaal aantal fouten|Hiermee wordt het totale aantal fouten weer gegeven.| 

## <a name="focus-on-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>De metrische gegevens van de Azure IoT-connector voor FHIR (preview) richten en configureren

In dit voor beeld is het belang rijk om te kijken naar het **aantal metrische binnenkomende berichten** .

1. Selecteer een tijdgebonden punt waarop u wilt focussen.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="Scherm opname van het deel venster ' aantal inkomende berichten ', dat één punt in de tijd in de lijn diagram markeert." lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. In het deel venster **aantal inkomende berichten** kunt u de metriek verder aanpassen door **metrische gegevens toevoegen** , **filter toevoegen** of splitsen toe te **passen**. 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="Scherm opname van het deel venster ' aantal inkomende berichten ', met de knoppen ' metrische gegevens toevoegen, filters toevoegen en splitsing Toep assen ' gemarkeerd." lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>Conclusie 
Het hebben van toegang tot metrische gegevens vlak is essentieel voor het controleren en oplossen van problemen. Azure IoT connector voor FHIR helpt u bij deze acties met metrische gegevens. 

## <a name="next-steps"></a>Volgende stappen

Krijg antwoorden op veelgestelde vragen over Azure IoT connector voor FHIR.

>[!div class="nextstepaction"]
>[Veelgestelde vragen over Azure IoT connector voor FHIR](fhir-faq.md)

* In de Azure Portal wordt Azure IoT connector voor FHIR aangeduid als IoT-connector (preview). FHIR is een geregistreerd handels merk van HL7 en wordt gebruikt met de machtiging van HL7. 
