---
title: 'Azure IoT connector voor FHIR (preview): probleemoplossings gids en instructies'
description: Veelvoorkomende problemen met de fout berichten en voor waarden van de algemene Azure IoT connector voor FHIR (preview) oplossen en bestanden voor toewijzing kopiëren
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: jasteppe
ms.openlocfilehash: 8c372a865e34b2cbd1b5b3e6d8619c3ef0f438e0
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460419"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Gids voor probleem oplossing van Azure IoT connector voor FHIR (preview)

Dit artikel bevat stappen voor het oplossen van problemen met algemene Azure IoT-connector voor FHIR-fout berichten en-voor waarden.  

U leert ook hoe u kopieën van de Azure IoT-connector voor FHIR kunt maken voor het bewerken en archiveren van buiten de Azure Portal, of voor het leveren van technische ondersteuning van Azure bij het openen van een ondersteunings ticket. 

## <a name="error-messages-and-fixes"></a>Fout berichten en oplossingen

|Bericht   |Voorwaarde  |Herstellen         |
|----------|-----------|------------|
|Ongeldige toewijzings naam. de naam van de toewijzing moet apparaat of FHIR zijn|Het opgegeven toewijzings type is niet van het apparaat of de FHIR|Gebruik een van de twee ondersteunde toewijzings typen (bijvoorbeeld: apparaat of FHIR)|
|Opnieuw genereren van de sleutel parameters is niet gedefinieerd|Sleutel aanvraag opnieuw genereren|De para meters in de aanvraag voor het opnieuw genereren van sleutels toevoegen|
|Het maximum aantal IoT-connector *-instanties dat in dit abonnement kan worden ingericht, is bereikt|Azure IoT connector voor FHIR-abonnements quotum bereikt (standaard is (2) per abonnement)|Verwijder een van de bestaande exemplaren van Azure IoT connector voor FHIR, gebruik een ander abonnement dat het abonnements quotum niet heeft bereikt of vraag een verhoging van het abonnements quotum aan|
|Resource verplaatsen wordt niet ondersteund voor de Azure-API voor IoT-connector voor de FHIR-resource|Er wordt geprobeerd een verplaatsings bewerking uit te voeren op een Azure-API voor een FHIR-resource met een of meer exemplaren van de Azure IoT-connector voor FHIR|Bestaande exemplaren van Azure IoT connector verwijderen voor FHIR om de verplaatsings bewerking uit te voeren en te volt ooien|
|IoT-connector is niet ingericht|Er wordt geprobeerd om onderliggende services (verbindingen & toewijzingen) te gebruiken wanneer Parent (Azure IoT connector voor FHIR) niet is ingericht|Een Azure IoT-connector inrichten voor FHIR|
|De aanvraag wordt niet ondersteund|Specifieke API-aanvraag wordt niet ondersteund|De juiste API-aanvraag gebruiken|
|Het account bestaat niet|Er wordt geprobeerd een Azure IoT-connector toe te voegen voor FHIR en de Azure API voor de FHIR-resource bestaat niet|Maak de Azure API voor de FHIR-resource en voer de bewerking opnieuw uit|
|De Azure-API voor de FHIR-resource FHIR-versie wordt niet ondersteund voor IoT-connector|Er wordt geprobeerd een Azure IoT-connector te gebruiken voor FHIR met een incompatibele versie van de Azure API voor FHIR-resource|Een nieuwe Azure-API maken voor de FHIR-resource (versie R4) of een bestaande Azure API voor FHIR-resource (versie R4) gebruiken

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-mapping-files"></a>Kopieën maken van de Azure IoT connector voor FHIR-toewijzings bestanden (preview)
Het kopiëren van Azure IoT connector voor FHIR toewijzings bestanden kan handig zijn voor het bewerken en archiveren van buiten de Azure Portal website en voor het bieden van technische ondersteuning van Azure bij het openen van een ondersteunings ticket.

> [!NOTE]
> JSON is op dit moment de enige ondersteunde indeling voor de toewijzings bestanden voor apparaten en FHIR.

> [!TIP]
> Meer informatie over de Azure IoT-connector voor FHIR- [apparaat-en FHIR-toewijzing json-bestanden](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. Selecteer **' IOT-connector (preview) '** in de linkerbenedenhoek van de Azure API for FHIR resource dash board in de sectie **' invoeg toepassingen '** .

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT-connector" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Selecteer de **' connector '** waarnaar u de toewijzings bestanden wilt kopiëren.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT-connector" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Dit proces kan ook worden gebruikt voor het kopiëren en opslaan van de inhoud van de JSON **FHIR-toewijzing configureren** .

3. Selecteer **apparaattoewijzing configureren**.

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT-connector" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Selecteer de inhoud van de JSON en voer een Kopieer bewerking uit (bijvoorbeeld: Selecteer CTRL + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT-connector" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Maak een plak bewerking (bijvoorbeeld: Selecteer CTRL + v) in een nieuw bestand binnen een editor (bijvoorbeeld Visual Studio code, Klad blok) en sla het bestand op met de extensie *. json.

> [!TIP]
> Als u een [Azure-ondersteunings](https://azure.microsoft.com/support/create-ticket/) ticket voor Azure IOT connector voor FHIR gaat openen, moet u ervoor zorgen dat u kopieën van uw toewijzings bestanden opneemt om u te helpen bij het oplossen van het probleem.

## <a name="next-steps"></a>Volgende stappen

Bekijk veelgestelde vragen over de Azure IoT-connector voor FHIR.

>[!div class="nextstepaction"]
>[Veelgestelde vragen over Azure IoT connector voor FHIR (preview)](fhir-faq.md#iot-connector-preview)

* In de Azure Portal wordt de Azure IoT-connector voor FHIR aangeduid als IoT-connector (preview).

FHIR is het gedeponeerde handelsmerk van HL7 en wordt gebruikt met de toestemming van HL7.