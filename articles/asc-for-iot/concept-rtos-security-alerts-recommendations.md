---
title: Beveiligings module voor ingebouwde Azure RTO'S-& aanpas bare waarschuwingen en aanbevelingen
description: Meer informatie over beveiligings waarschuwingen en aanbevolen herstel met behulp van de Azure IoT Security module-RTO'S.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: ebb4edf10433cce981b4718835676d0a5d76ba40
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514883"
---
# <a name="security-module-for-azure-rtos-security-alerts-and-recommendations-preview"></a>Beveiligings module voor beveiligings waarschuwingen en aanbevelingen van Azure RTO'S (preview)

De beveiligings module voor Azure RTO'S analyseert voortdurend uw IoT-oplossing met behulp van geavanceerde analyses en bedreigings informatie om u te waarschuwen voor mogelijke schadelijke activiteiten en verdachte systeem aanpassingen. U kunt ook aangepaste waarschuwingen maken op basis van uw kennis van het verwachte apparaat gedrag en basis lijnen.

Een beveiligings module voor Azure RTO'S-waarschuwing fungeert als een indicatie van mogelijke inbreuk en moet worden onderzocht en opgelost. Een beveiligings module voor Azure RTO'S-aanbeveling duidt op zwakke beveiligings postuur die moeten worden hersteld en bijgewerkt. 

In dit artikel vindt u een lijst met ingebouwde waarschuwingen en aanbevelingen die worden geactiveerd op basis van de standaardbereiken en die u kunt aanpassen met uw eigen waarden, op basis van het verwachte of basislijn gedrag. 

Zie [aanpas bare waarschuwingen](concept-customizable-security-alerts.md)voor meer informatie over de werking van het aanpassen van waarschuwingen in de Azure Security Center voor IOT-service. De specifieke waarschuwingen en aanbevelingen die beschikbaar zijn voor aanpassing wanneer u de beveiligings module voor Azure RTO'S gebruikt, worden in de volgende tabellen beschreven. 

## <a name="security-module-for-azure-rtos-supported-security-alerts"></a>Beveiligings module voor door Azure RTO'S ondersteunde beveiligings waarschuwingen

### <a name="device-related-security-alerts"></a>Beveiligings waarschuwingen met betrekking tot apparaten

|Activiteit van beveiligings waarschuwingen met betrekking tot apparaten  |Naam waarschuwing  |
|---------|---------|
|IP-adres| Communicatie met een verdacht IP-adres gedetecteerd|
|Vinger afdruk van het 509-apparaat|X. 509 apparaat certificaat vingerafdruk komt niet overeen|
|X. 509-certificaat| X. 509-certificaat is verlopen|
|SAS-token| Verlopen SAS-token|
|SAS-token| Ongeldige SAS-token handtekening|

### <a name="iot-hub-related-security-alerts"></a>Beveiligings waarschuwingen met betrekking tot IoT Hub

|Activiteit beveiligings waarschuwing IoT Hub  |Naam waarschuwing  |
|---------|---------|
|Een certificaat toevoegen    |  Er is een niet-geslaagde poging tot het toevoegen van een certificaat aan een IoT Hub gedetecteerd       |
|Een diagnostische instelling toevoegen of bewerken    | Er is een poging tot het toevoegen of bewerken van een diagnostische instelling van een IoT Hub gedetecteerd      |
|Een certificaat verwijderen    |  Er is een niet-geslaagde poging tot het verwijderen van een certificaat uit een IoT Hub gedetecteerd       |
|Een diagnostische instelling verwijderen    |  Gedetecteerde poging om een diagnostische instelling te verwijderen uit een IoT Hub      |
|Verwijderd certificaat    | Gedetecteerde verwijdering van een certificaat van een IoT Hub        |
|Nieuw certificaat     |  Het toevoegen van een nieuw certificaat aan een IoT Hub is gedetecteerd       |

## <a name="security-module-for-azure-rtos-supported-customizable-alerts"></a>Beveiligings module voor door Azure RTO'S ondersteunde aanpas bare waarschuwingen

### <a name="device-related-customizable-alerts"></a>Aan het apparaat gerelateerde aanpas bare waarschuwingen

|Aan apparaat gerelateerde activiteit |Naam waarschuwing  |
|---------|---------|
|Actieve verbindingen|Aantal actieve verbindingen bevindt zich niet in het toegestane bereik|
|Cloud-naar-apparaat-berichten in het **MQTT** -Protocol|Het aantal berichten van de Cloud naar het apparaat in het **MQTT** -protocol bevindt zich niet in het toegestane bereik|
|Uitgaande verbinding| Uitgaande verbinding met een IP-adres dat niet is toegestaan|

### <a name="hub-related-customizable-alerts"></a>Aan hub gerelateerde aanpas bare waarschuwingen 

|Aan hub gerelateerde activiteit  |Naam waarschuwing  |
|---------|---------|
|Opschoningen van opdrachten wachtrij     |  Aantal opschoningen van de opdracht wachtrij buiten het toegestane bereik       |
|Cloud-naar-apparaat-berichten in het **MQTT** -Protocol    |  Aantal Cloud-naar-apparaat-berichten in het **MQTT** -protocol buiten het toegestane bereik       |
|Apparaat-naar-Cloud berichten in het **MQTT** -Protocol    | Aantal van het apparaat naar Cloud berichten in het **MQTT** -protocol buiten het toegestane bereik        |
|Directe methode aanroepen     |  Aantal directe methoden dat buiten het toegestane bereik valt       |
|Geweigerde berichten van de Cloud naar het apparaat in het **MQTT** -Protocol     |   Het aantal geweigerde Cloud-naar-apparaat-berichten in het **MQTT** -protocol buiten het toegestane bereik      |
|Updates voor dubbele modules     |  Aantal updates voor dubbele modules buiten het toegestane bereik       |
|Niet-geautoriseerde bewerkingen    |  Aantal niet-geautoriseerde bewerkingen buiten het toegestane bereik       |

## <a name="security-module-for-azure-rtos-supported-recommendations"></a>Beveiligings module voor door Azure RTO'S ondersteunde aanbevelingen

### <a name="device-related-recommendations"></a>Aanbevelingen met betrekking tot apparaten

|Apparaat-gerelateerde activiteit  |Naam aanbeveling |
|---------|---------|
|Verificatie referenties    |  Identieke verificatie referenties die worden gebruikt door meerdere apparaten       |

### <a name="hub-related-recommendations"></a>Aan de hub gerelateerde aanbevelingen

|IoT Hub activiteit  |Naam aanbeveling |
|---------|---------|
|IP-filter beleid   |  Het standaard IP-filter beleid moet worden ingesteld op **weigeren**  |
|IP-filter regel| De IP-filter regel bevat een groot IP-bereik|
|Logboeken met diagnostische gegevens|Suggestie voor het inschakelen van Diagnostische logboeken in IoT Hub|

### <a name="all-azure-security-center-for-iot-alerts-and-recommendations"></a>Alle Azure Security Center voor IoT-waarschuwingen en-aanbevelingen

Voor een volledige lijst met alle Azure Security Center voor IoT-Service waarschuwingen en aanbevelingen raadpleegt u IoT- [beveiligings waarschuwingen](concept-security-alerts.md), IOT-beveiligings [aanbevelingen](concept-recommendations.md).

## <a name="next-steps"></a>Volgende stappen

- [Snelstartgids: Security module voor Azure RTO'S](quickstart-azure-rtos-security-module.md)
- [Beveiligings module voor Azure RTO'S configureren en aanpassen](how-to-azure-rtos-security-module.md)
- Raadpleeg de [beveiligings module voor Azure rto's-API](azure-rtos-security-module-api.md)