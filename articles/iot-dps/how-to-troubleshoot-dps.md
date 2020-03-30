---
title: Diagnose en problemen met de verbindingen met Azure IoT Hub DPS
description: Meer informatie over het diagnosticeren en oplossen van veelvoorkomende fouten met apparaatconnectiviteit voor Azure IoT Hub Device Provisioning Service (DPS)
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: 3cbab09c6b50abb590cfe9f2720713a8fa547aa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646469"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Probleemoplossing met Azure IoT Hub Device Provisioning Service

Verbindingsproblemen voor IoT-apparaten kunnen moeilijk op te lossen zijn omdat er veel mogelijke fouten zijn, zoals attestfouten, registratiefouten enz. In dit artikel vindt u richtlijnen voor het detecteren en oplossen van problemen met apparaatconnectiviteit via [Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/overview)

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Azure Monitor gebruiken om statistieken weer te geven en waarschuwingen in te stellen

In de volgende procedure wordt beschreven hoe u de statistiek Van IoT Hub Device Provisioning Service weergeven en instellen. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Blader naar uw IoT Hub Device Provisioning Service.

3. Selecteer **Metrische gegevens**.

4. Selecteer de gewenste statistiek. 
   <br />Momenteel zijn er drie statistieken voor DPS:

    | Metrische naam | Beschrijving |
    |-------|------------|
    | Attestpogingen | Aantal apparaten dat heeft geprobeerd te verifiëren met de service voor apparaatinrichting|
    | Registratiepogingen | Aantal apparaten dat zich heeft geregistreerd bij IoT Hub na succesvolle verificatie|
    | Toegewezen apparaat | Aantal apparaten dat is toegewezen aan IoT Hub|

5. Selecteer de gewenste aggregatiemethode om een visuele weergave van de statistiek te maken. 

6. Als u een waarschuwing van een statistiek wilt instellen, selecteert u **Nieuwe waarschuwingsregels** rechtsboven in het metrische blad, op dezelfde manier u naar **het hoofd van waarschuwing** gaan en Nieuwe **waarschuwingsregels**selecteren.

7. Selecteer **Voorwaarde toevoegen**en selecteer vervolgens de gewenste statistiek en drempelwaarde door aanwijzingen te volgen.

Zie [Wat zijn klassieke waarschuwingen in Microsoft Azure?](../azure-monitor/platform/alerts-overview.md)

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>Log-analytische gebruiken om fouten weer te geven en op te lossen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Blader naar uw IoT-hub.

3. Selecteer **Diagnostische instellingen**.

4. Selecteer **Diagnostische gegevens inschakelen**.

5. Schakel de gewenste logboeken in om te worden verzameld.

    | Logboeknaam | Beschrijving |
    |-------|------------|
    | Apparaatbewerkingen | Logboeken met betrekking tot gebeurtenissen in apparaatverbinding |
    | ServiceBewerkingen | Gebeurtenislogboeken met betrekking tot het gebruik van service SDK (bijvoorbeeld inschrijvingsgroepen maken of bijwerken)|

6. Schakel **Verzenden naar logboekanalyse** in[(zie prijzen).](https://azure.microsoft.com/pricing/details/log-analytics/) 

7. Ga naar het tabblad **Logboeken** in de Azure-portal onder de bron Device Provisioning Service.

8. Klik **op Uitvoeren** om recente gebeurtenissen weer te geven.

9. Als er resultaten zijn, `OperationName`zoekt u naar , `ResultType`, `ResultSignature`en `ResultDescription` (foutbericht) om meer details over de fout te krijgen.


## <a name="common-error-codes"></a>Veelvoorkomende foutcodes
Gebruik deze tabel om veelvoorkomende fouten te begrijpen en op te lossen.

| Foutcode| Beschrijving | HTTP-statuscode |
|-------|------------|------------|
| 400 | De instantie van het verzoek is niet geldig; het kan bijvoorbeeld niet worden ontleed of het object kan niet worden gevalideerd.| 400 Slecht formaat |
| 401 | Het autorisatietoken kan niet worden gevalideerd; het is bijvoorbeeld verlopen of is niet van toepassing op de URI van het verzoek. Deze foutcode wordt ook geretourneerd naar apparaten als onderdeel van de TPM-atteststroom. | 401 Ongeautoriseerd|
| 404 | De instantie Apparaatinrichtingsservice of een resource (bijvoorbeeld een inschrijving) bestaat niet. |404 Niet gevonden |
| 412 | De ETag in de aanvraag komt niet overeen met de ETag van de bestaande resource, volgens RFC7232. | 412 Voorwaarde mislukt |
| 429 | Operaties worden beperkt door de service. Zie Voor specifieke servicelimieten de [limieten voor het inrichten van IoT-hubapparaten](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits). | 429 Te veel aanvragen |
| 500 | Er is een interne fout opgetreden. | 500 Interne serverfout|
