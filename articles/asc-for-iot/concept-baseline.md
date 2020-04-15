---
title: Basislijn- en aangepaste controles
description: Meer informatie over het concept van Azure Security Center for IoT-basislijn.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2019
ms.author: mlottner
ms.openlocfilehash: c52a3e55e3801eaaac885b9a3c364283f74906ba
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311647"
---
# <a name="azure-security-center-for-iot-baseline-and-custom-checks"></a>Azure Security Center voor IoT-basislijn- en aangepaste controles

In dit artikel wordt uitgelegd in azure security center voor IoT-basislijn en worden alle bijbehorende eigenschappen van aangepaste basislijncontroles samengevat.

## <a name="baseline"></a>Basislijn

Een basislijn stelt standaardgedrag vast voor elk apparaat en maakt het gemakkelijker om ongewoon gedrag of afwijking van verwachte normen vast te stellen.

## <a name="baseline-custom-checks"></a>Aangepaste controles basislijn

Aangepaste controles basislijn stellen een aangepaste lijst met controles samen voor elke basislijn met behulp van de **moduleidentiteitstweeling** van het apparaat.

## <a name="setting-baseline-properties"></a>Basislijneigenschappen instellen

1. Zoek en selecteer in uw IoT-hub het apparaat dat u wilt wijzigen.
1. Klik op het apparaat en klik vervolgens op de **azureiotsecuritymodule.**
1. Klik **op Module Identiteit Twin**.
1. Upload het **aangepaste controlebestand basislijn** naar het apparaat.
1. Voeg basislijneigenschappen toe aan de beveiligingsmodule en klik op **Opslaan**.

### <a name="baseline-custom-check-file-example"></a>Voorbeeld van aangepaste controle-bestands basislijn

Aangepaste controles basislijn configureren:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>Aangepaste controleeigenschappen basislijn

| Name| Status | Geldige waarden| Standaardwaarden| Beschrijving |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|basislijnCustomChecksIngeschakeld|Vereist: true |Geldige waarden: **Boolean** |Standaardwaarde: **false** |Maximaal tijdsinterval voordat berichten met hoge prioriteit worden verzonden.|
|basislijnCustomChecksFilePath |Vereist: true|Geldige waarden: **Tekenreeks**, **null** |Standaardwaarde: **null** |Volledig pad van de xml-configuratie basislijn|
|basislijnCustomChecksFileHash |Vereist: true|Geldige waarden: **Tekenreeks**, **null** |Standaardwaarde: **null** |`sha256sum`van het xml-configuratiebestand. Gebruik de [sha256sum-referentie](https://linux.die.net/man/1/sha256sum) voor aanvullende informatie. |

Zie voorbeeld van aangepaste [basislijn -1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) en [aangepast basislijnvoorbeeld -2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml)als u aanvullende basislijnvoorbeelden wilt bekijken.

## <a name="next-steps"></a>Volgende stappen

- Toegang tot uw [ruwe beveiligingsgegevens](how-to-security-data-access.md)
- [Een apparaat onderzoeken](how-to-investigate-device.md)
- [Beveiligingsaanbevelingen](concept-recommendations.md) begrijpen en verkennen
- [Beveiligingswaarschuwingen](concept-security-alerts.md) begrijpen en verkennen
