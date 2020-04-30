---
title: 'Snelstartgids: de service inschakelen'
description: Meer informatie over het voorbereiden en inschakelen van de Azure Security Center voor IoT-beveiligings service in uw Azure IoT Hub.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 0d3e4ad63baf6883aa8fadaca5b1f2d28fa14881
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81310759"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Snelstartgids: onboard Azure Security Center voor IoT-service in IoT Hub

Dit artikel bevat een uitleg over het inschakelen van de Azure Security Center voor IoT-service op uw bestaande IoT Hub. Als u momenteel geen IoT Hub hebt, raadpleegt u [een IOT hub maken met behulp van de Azure Portal](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) om aan de slag te gaan.

> [!NOTE]
> Azure Security Center voor IoT biedt momenteel alleen ondersteuning voor IoT-hubs met Standard-laag.

## <a name="prerequisites-for-enabling-the-service"></a>Vereisten voor het inschakelen van de service

- Log Analytics-werkruimte
  - Twee soorten informatie worden standaard opgeslagen in uw Log Analytics-werk ruimte door Azure Security Center voor IoT. **beveiligings waarschuwingen** en **aanbevelingen**.
  - U kunt ervoor kiezen om opslag ruimte toe te voegen voor een extra gegevens type, **onbewerkte gebeurtenissen**. Houd er rekening mee dat bij het opslaan van **onbewerkte gebeurtenissen** in log Analytics extra opslag kosten worden uitgevoerd.
- IoT Hub (Standard-laag)
- Aan alle [service vereisten](service-prerequisites.md) voldoen

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Azure Security Center voor IoT op uw IoT Hub inschakelen

Beveiliging op uw IoT Hub inschakelen:

1. Open uw **IOT hub** in azure Portal.
1. Klik in het menu **beveiliging** op **Beveilig uw IOT-oplossing**.

Gefeliciteerd! Het inschakelen van Azure Security Center voor IoT op uw IoT Hub is voltooid.

### <a name="geolocation-and-ip-address-handling"></a>Verwerking van geolocatie en IP-adres

Als u uw IoT-oplossing wilt beveiligen, worden de IP-adressen van binnenkomende en uitgaande verbindingen van en naar uw IoT-apparaten, IoT Edge en IoT Hub (s) standaard verzameld en opgeslagen. Deze informatie is essentieel voor het detecteren van abnormale verbindingen van verdachte IP-bronnen. Bijvoorbeeld wanneer er wordt geprobeerd verbindingen tot stand te brengen met een IP-bron van een bekend botnet of van een IP-bron buiten uw geolocatie. Azure Security Center voor IoT-service biedt de flexibiliteit om het verzamelen van IP-adres gegevens op elk gewenst moment in en uit te scha kelen.

Het verzamelen van IP-adres gegevens in-of uitschakelen:

1. Open uw IoT Hub en selecteer vervolgens **overzicht** in het menu **beveiliging** .
1. Kies het scherm **instellingen** en wijzig de instellingen voor geolocatie en/of IP-verwerking naar wens.

### <a name="log-analytics-creation"></a>Log Analytics maken

Als Azure Security Center voor IoT is ingeschakeld, wordt er een standaard Azure Log Analytics-werk ruimte gemaakt om onbewerkte beveiligings gebeurtenissen, waarschuwingen en aanbevelingen voor uw IoT-apparaten, IoT Edge en IoT Hub op te slaan. Elke maand is de eerste vijf (5) GB aan gegevens die per klant zijn opgenomen in de Azure Log Analytics-service gratis. Elke GB aan gegevens die zijn opgenomen in uw Azure Log Analytics-werk ruimte, wordt gedurende de eerste 31 dagen gratis bewaard. Meer informatie over [log Analytics](https://azure.microsoft.com/pricing/details/monitor/) prijzen.

De werkruimte configuratie van Log Analytics wijzigen:

1. Open uw IoT Hub en selecteer vervolgens **overzicht** in het menu **beveiliging** .
1. Kies het scherm **instellingen** en wijzig de werkruimte configuratie van log Analytics instellingen naar wens.

### <a name="customize-your-iot-security-solution"></a>Uw IoT-beveiligings oplossing aanpassen

Als u de Azure Security Center voor IoT-oplossing inschakelt, worden standaard alle IoT-hubs in uw Azure-abonnement automatisch beveiligd.

Azure Security Center voor IoT-service inschakelen op een specifieke IoT Hub in-of uitschakelen:

1. Open uw IoT Hub en selecteer vervolgens **overzicht** in het menu **beveiliging** .
1. Kies het scherm **instellingen** en wijzig de beveiligings instellingen van een IOT-hub in uw Azure-abonnement naar wens.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel om uw oplossing te configureren...

> [!div class="nextstepaction"]
> [De oplossing configureren](quickstart-configure-your-solution.md)
