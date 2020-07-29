---
title: 'Azure IoT connector voor FHIR (IoT-connector): probleemoplossings gids en instructies'
description: Veelvoorkomende problemen met fout berichten van IoT connector en voor waarden en kopiëren van toewijzings bestanden
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jasteppe
ms.openlocfilehash: eff1272318413da7855134b0a8a44dd0a0711a6c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285574"
---
# <a name="iot-connector-preview-troubleshooting-guide"></a>Probleemoplossings gids voor IoT-connector (preview)

Dit artikel bevat stappen voor het oplossen van problemen met veelvoorkomende fout berichten van IoT connector (preview) en voor waarden.  

U leert ook hoe u kopieën van de JSON-toewijzings bestanden voor de IoT-connector (preview) maakt voor het bewerken en archiveren buiten de Azure Portal.

## <a name="error-messages-and-fixes"></a>Fout berichten en oplossingen

|Bericht   |Voorwaarde  |Herstellen         |
|----------|-----------|------------|
|Ongeldige toewijzings naam. de naam van de toewijzing moet apparaat of FHIR zijn|Het opgegeven toewijzings type is niet van het apparaat of de FHIR|Gebruik een van de twee ondersteunde toewijzings typen (bijvoorbeeld: apparaat of FHIR)|
|Opnieuw genereren van de sleutel parameters is niet gedefinieerd|Sleutel aanvraag opnieuw genereren|De para meters in de aanvraag voor het opnieuw genereren van sleutels toevoegen|
|Het maximum aantal IoT-connector instanties dat in dit abonnement kan worden ingericht, is bereikt|Het quotum voor het abonnement voor IoT connector is bereikt (de standaard instelling is 2 per abonnement)|Verwijder een van de bestaande connectors, gebruik een ander abonnement dat niet het quotum van (2) connectors per abonnement heeft bereikt of een verhoging van het abonnements quotum vraagt|
|Resource verplaatsen wordt niet ondersteund voor de Azure-API voor IoT-connector voor de FHIR-resource|Er wordt geprobeerd een verplaatsings bewerking uit te voeren op een Azure-API voor een FHIR-resource met een of meer IoT-connectors|Bestaande connectors verwijderen om de verplaatsings bewerking (en) uit te voeren/volt ooien|
|Voor de Azure-API voor de FHIR-resource is een persoonlijke koppeling ingeschakeld.  Privé koppeling wordt niet ondersteund met IoT-connector|Er wordt geprobeerd een IoT-connector toe te voegen aan een Azure-API voor de FHIR-resource waarvoor een persoonlijke koppeling is ingeschakeld|Selecteer of maak een Azure-API voor FHIR resource (versie R4) waarvoor geen persoonlijke koppeling is ingeschakeld|
|IoT-connector is niet ingericht|Er wordt geprobeerd om onderliggende services (verbindingen & toewijzingen) te gebruiken wanneer bovenliggende (IoT-connector) niet is ingericht|Een IoT-connector inrichten|
|De aanvraag wordt niet ondersteund|Specifieke API-aanvraag wordt niet ondersteund|De juiste API-aanvraag gebruiken|
|Het account bestaat niet|Poging tot het toevoegen van een IoT-connector en de Azure API voor de FHIR-resource bestaat niet|Maak de Azure API voor de FHIR-resource en voer de bewerking opnieuw uit|
|De Azure-API voor de FHIR-resource FHIR-versie wordt niet ondersteund voor IoT-connector|Poging tot gebruik van een IoT-connector met een incompatibele versie van de Azure API voor de FHIR-resource|Een nieuwe Azure-API maken voor de FHIR-resource (versie R4) of een bestaande Azure API voor FHIR-resource (versie R4) gebruiken

## <a name="creating-copies-of-the-iot-connector-preview-mapping-files"></a>Kopieën maken van de toewijzings bestanden van de IoT-connector (preview)
> [!NOTE]
> JSON is op dit moment de enige ondersteunde indeling voor de toewijzings bestanden voor apparaten en FHIR.

> [!TIP]
> Het kopiëren van IoT connector-toewijzings bestanden kan handig zijn voor het bewerken en archiveren van buiten de Azure Portal-website en voor het bieden van technische ondersteuning van Azure bij het openen van een ondersteunings ticket.
> 
> Meer informatie over het apparaat voor IoT-connector [en json-bestanden met FHIR-toewijzing](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. Selecteer **' IOT-connector (preview) '** in de linkerbenedenhoek van de Azure API for FHIR resource dash board in de sectie **' invoeg toepassingen '** .

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT-connector" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Selecteer de **' connector '** waarnaar u de toewijzings bestanden wilt kopiëren.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT-connector" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

3. Selecteer **apparaattoewijzing configureren**.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT-connector" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

> [!NOTE]
> Dit proces kan ook worden gebruikt voor het kopiëren/opslaan van de inhoud van de JSON **FHIR-toewijzing configureren** .

4. Selecteer de inhoud van de JSON en voer een Kopieer bewerking uit (bijvoorbeeld: Selecteer CTRL + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT-connector" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Maak een plak bewerking (bijvoorbeeld: Selecteer CTRL + v) in een nieuw bestand binnen een editor (bijvoorbeeld Visual Studio code, Klad blok) en sla het bestand op met de extensie *. json.

> [!TIP]
> Als u een [Azure technische ondersteunings](https://azure.microsoft.com/support/create-ticket/) ticket voor de IOT-connector opent, moet u ervoor zorgen dat u kopieën van uw toewijzings bestanden opneemt om u te helpen bij het oplossen van het probleem.

## <a name="next-steps"></a>Volgende stappen

Bekijk veelgestelde vragen over IoT connector

>[!div class="nextstepaction"]
>[Veelgestelde vragen over IoT-connector](fhir-faq.md#iot-connector-preview)


FHIR is het gedeponeerde handelsmerk van HL7 en wordt gebruikt met de toestemming van HL7.