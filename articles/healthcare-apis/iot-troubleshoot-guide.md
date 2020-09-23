---
title: 'Azure IoT connector voor FHIR (preview): probleemoplossings gids en instructies'
description: Veelvoorkomende problemen met de fout berichten en voor waarden van de algemene Azure IoT connector voor FHIR (preview) oplossen en toewijzings bestanden kopiëren
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 09/16/2020
ms.author: jasteppe
ms.openlocfilehash: 64056ef2f63331686553c52040af9e10ee0ac468
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982988"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Gids voor probleem oplossing van Azure IoT connector voor FHIR (preview)

Dit artikel bevat stappen voor het oplossen van problemen met algemene Azure IoT-connector voor FHIR *-fout berichten en-voor waarden.  

U leert ook hoe u een kopie maakt van de Azure IoT connector voor FHIR-conversie toewijzingen JSON (bijvoorbeeld: apparaat en FHIR).  

U kunt de JSON-kopieën van de conversie toewijzing gebruiken voor het bewerken en archiveren buiten het Azure Portal.  

> [!TIP]
> Als u een [Azure-ondersteunings](https://azure.microsoft.com/support/create-ticket/) ticket voor Azure IOT connector voor FHIR opent, moet u ervoor zorgen dat u kopieën van de JSON-conversie toewijzing opneemt om u te helpen bij het oplossen van het probleem.

## <a name="device-and-fhir-conversion-mapping-json-template-validations-for-azure-iot-connector-for-fhir-preview"></a>Validatie toewijzing van JSON-sjabloon voor apparaat en FHIR voor Azure IoT-connector voor FHIR (preview-versie)
In deze sectie vindt u meer informatie over het validatie proces dat door Azure IoT connector voor FHIR wordt uitgevoerd om de JSON-sjablonen van het apparaat en de FHIR-conversie te valideren voordat ze voor gebruik kunnen worden opgeslagen.  Deze elementen zijn vereist in de JSON van het apparaat en de FHIR-conversie toewijzing.

**Apparaattoewijzing**

|Element|Vereist|
|:-------|:------|
|TypeName|Waar|
|TypeMatchExpression|Waar|
|DeviceIdExpression|Waar|
|TimestampExpression|Waar|
|Values []. ValueName|Waar|
|Values []. ValueExpression|Waar|

> [!NOTE]
> Values []. Waardenaam en waarden []. ValueExpression
>
> Deze elementen zijn alleen vereist als u een waarde hebt ingevoerd in de matrix. het kan zijn dat er geen waarden zijn toegewezen. Dit wordt gebruikt wanneer de telemetrie die wordt verzonden een gebeurtenis is. Bijvoorbeeld: wanneer een wearable IoMT-apparaat wordt geplaatst of verwijderd. De elementen hebben geen waarden behalve een naam die Azure IoT connector voor FHIR overeenkomt met en levert. Op de FHIR-conversie wijst Azure IoT connector voor FHIR het toe aan een code concept op basis van het semantische type: er worden geen werkelijke waarden ingevuld.

**FHIR toewijzing**

|Element|Vereist|
|:------|:-------|
|TypeName|Waar|

> [!NOTE]
> Dit is het enige vereiste FHIR-toewijzings element dat op dit moment is gevalideerd.

## <a name="error-messages-and-fixes-for-azure-iot-connector-for-fhir-preview"></a>Fout berichten en oplossingen voor Azure IoT connector voor FHIR (preview)

|Bericht|Weer|Conditie|Herstellen| 
|-------|---------|---------|---|
|Ongeldige toewijzings naam. de naam van de toewijzing moet apparaat of FHIR zijn.|API|Het opgegeven toewijzings type is geen apparaat-of FHIR.|Gebruik een van de twee ondersteunde toewijzings typen (bijvoorbeeld: apparaat of FHIR).|
|Validatie is mislukt. De vereiste gegevens ontbreken of zijn ongeldig.|API en Azure Portal|Bij het opslaan van een conversie toewijzing ontbreekt de vereiste informatie of element.|Voeg ontbrekende gegevens van de conversie toewijzing of het element toe en probeer de conversie toewijzing opnieuw op te slaan.|
|Het opnieuw genereren van de sleutel parameters is niet gedefinieerd.|API|Genereer de sleutel aanvraag opnieuw.|Neem de para meters op in de aanvraag voor het opnieuw genereren van sleutels.|
|Het maximum aantal IoT-connector instanties dat kan worden ingericht in dit abonnement is bereikt.|API en Azure Portal|De Azure IoT-connector voor het quotum voor het FHIR-abonnement is bereikt (de standaard waarde is (2) per abonnement.|Verwijder een van de bestaande exemplaren van Azure IoT connector voor FHIR.  Gebruik een ander abonnement dat het abonnements quotum nog niet heeft bereikt.  Vraag een verhoging van het abonnements quotum aan.|
|Resource verplaatsen wordt niet ondersteund voor de Azure-API voor IoT-connector voor de FHIR-resource.|API en Azure Portal|Er wordt geprobeerd een Verplaats bewerking uit te voeren op een Azure-API voor een FHIR-resource met een of meer exemplaren van de Azure IoT-connector voor FHIR.|Verwijder bestaande exemplaren van Azure IoT connector voor FHIR om de verplaatsings bewerking uit te voeren.|
|IoT-connector is niet ingericht.|API|Er wordt geprobeerd om onderliggende services (verbindingen & toewijzingen) te gebruiken wanneer Parent (Azure IoT connector voor FHIR) niet is ingericht.|Richt een Azure IoT-connector in voor FHIR.|
|De aanvraag wordt niet ondersteund.|API|Een specifieke API-aanvraag wordt niet ondersteund.|Gebruik de juiste API-aanvraag.|
|Het account bestaat niet.|API|Poging een Azure IoT-connector voor FHIR toe te voegen en de Azure API voor FHIR-resource bestaat niet.|Maak de Azure API voor de FHIR-resource en probeer het opnieuw.|
|De Azure-API voor de FHIR-resource FHIR-versie wordt niet ondersteund voor IoT-connector.|API|Er wordt geprobeerd een Azure IoT-connector te gebruiken voor FHIR met een incompatibele versie van de Azure API voor FHIR-resource.|Maak een nieuwe Azure-API voor de FHIR-resource (versie R4) of gebruik een bestaande Azure API voor FHIR-resource (versie R4).

##  <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>Waarom is mijn Azure IoT connector voor FHIR (preview)-gegevens die niet worden weer gegeven in de Azure-API voor FHIR?

|Potentiële problemen|Oplossingen|
|----------------|-----|
|De gegevens worden nog verwerkt.|Gegevens worden egressed naar de Azure-API voor FHIR in batches (elke ~ 15 minuten).  Het is mogelijk dat de gegevens nog worden verwerkt en dat er extra tijd nodig is om te voor komen dat de gegevens behouden blijven in de Azure API voor FHIR.|
|De JSON voor de toewijzing van de apparaat conversie is niet geconfigureerd.|De conformiteit van de JSON van de apparaat conversie configureren en opslaan.|
|De JSON van de FHIR-conversie toewijzing is niet geconfigureerd.|De FHIR-conversie toewijzing JSON configureren en opslaan.|
|Het bericht van het apparaat bevat geen verwachte expressie die is gedefinieerd in de apparaattoewijzing.|Controleer of de JsonPath-expressies die in de apparaattoewijzing zijn gedefinieerd, overeenkomen met de tokens die zijn gedefinieerd in het bericht apparaat.|
|Er is geen apparaatnaam gemaakt in de Azure API voor FHIR (oplossings type: alleen zoek opdracht) *.|Maak een geldige apparaat-resource in de Azure API voor FHIR. Zorg ervoor dat de resource van het apparaat een id bevat die overeenkomt met de apparaat-id in het binnenkomende bericht.|
|Een patiënt-resource is niet gemaakt in de Azure API voor FHIR (oplossings type: alleen zoek opdracht) *.|Maak een geldige patiënt resource in de Azure API voor FHIR.|
|De verwijzing naar het apparaat. patiënt is niet ingesteld of de verwijzing is ongeldig (oplossings type: alleen zoeken) *.|Zorg ervoor dat de resource van het apparaat een geldige [verwijzing](https://www.hl7.org/fhir/device-definitions.html#Device.patient) naar een patiënt bron bevat.| 

* Naslag informatie voor [Quick Start: implementeer Azure IOT connector (preview) met behulp van Azure Portal](iot-fhir-portal-quickstart.md#create-new-azure-iot-connector-for-fhir-preview) voor een functionele beschrijving van de Azure IOT-connector voor FHIR-oplossings typen (bijvoorbeeld: zoeken of maken).

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>Maken van kopieën van de JSON-conversie toewijzing van Azure IoT connector voor FHIR (preview)
Het kopiëren van Azure IoT connector voor FHIR toewijzings bestanden kan handig zijn voor het bewerken en archiveren van buiten de Azure Portal-website.

Het toewijzings bestand moet worden voorzien van de technische ondersteuning van Azure bij het openen van een ondersteunings ticket om problemen op te lossen.

> [!NOTE]
> JSON is op dit moment de enige ondersteunde indeling voor de toewijzings bestanden voor apparaten en FHIR.

> [!TIP]
> Meer informatie over de Azure IoT connector voor FHIR- [apparaat en FHIR-conversie toewijzing JSON](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. Selecteer **' IOT-connector (preview) '** in de linkerbenedenhoek van de Azure API for FHIR resource dash board in de sectie **' invoeg toepassingen '** .

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT-Connector1" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Selecteer de **connector** waarmee u de JSON van de conversie toewijzing wilt kopiëren.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT-Connector2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Dit proces kan ook worden gebruikt voor het kopiëren en opslaan van de inhoud van de JSON **FHIR-toewijzing configureren** .

3. Selecteer **apparaattoewijzing configureren**.

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT-Connector3" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Selecteer de inhoud van de JSON en voer een Kopieer bewerking uit (bijvoorbeeld: Selecteer CTRL + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT-Connector4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Maak een plak bewerking (bijvoorbeeld: Selecteer CTRL + v) in een nieuw bestand binnen een editor (bijvoorbeeld Visual Studio code, Klad blok) en sla het bestand op met de extensie *. json.

> [!TIP]
> Als u een [Azure-ondersteunings](https://azure.microsoft.com/support/create-ticket/) ticket voor Azure IOT connector voor FHIR opent, moet u ervoor zorgen dat u kopieën van de JSON-conversie toewijzing opneemt om u te helpen bij het oplossen van het probleem.

## <a name="next-steps"></a>Volgende stappen

Bekijk veelgestelde vragen over de Azure IoT-connector voor FHIR.

>[!div class="nextstepaction"]
>[Veelgestelde vragen over Azure IoT connector voor FHIR](fhir-faq.md#azure-iot-connector-for-fhir-preview)

*In Azure Portal wordt Azure IoT Connector for FHIR aangeduid als IoT Connector (preview).

FHIR is het gedeponeerde handelsmerk van HL7 en wordt gebruikt met de toestemming van HL7.
