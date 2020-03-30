---
title: 'Snelstart: Azure Security Center inschakelen voor IoT-service in IoT-hub'
description: Meer informatie over het gebruik en inschakelen van de Azure Security Center for IoT-beveiligingsservice in uw Azure IoT Hub.
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
ms.openlocfilehash: 0b1f09cfaf107802e1ce6586b3f96b14269aaced
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74664855"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Snelstart: Onboard Azure Security Center voor IoT-service in IoT Hub

In dit artikel wordt uitgelegd hoe u de Azure Security Center for IoT-service inschakelt op uw bestaande IoT Hub. Zie [Een IoT-hub maken met de Azure-portal](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) om aan de slag te gaan als u momenteel geen IoT-hub hebt. 

> [!NOTE]
> Azure Security Center for IoT ondersteunt momenteel alleen IoT-hubs met standaardlagen.


## <a name="prerequisites-for-enabling-the-service"></a>Voorwaarden voor het inschakelen van de service

- Log Analytics-werkruimte
  - Er worden standaard twee soorten gegevens opgeslagen in uw Log Analytics-werkruimte door Azure Security Center for IoT. **beveiligingswaarschuwingen** en **-aanbevelingen**. 
  - U ervoor kiezen om opslag toe te voegen van een extra informatietype, **onbewerkte gebeurtenissen.** Houd er rekening mee dat het opslaan van **onbewerkte gebeurtenissen** in Log Analytics extra opslagkosten met zich meebrengt. 
- IoT-hub (standaardlaag)
- Voldoen aan alle [servicevereisten](service-prerequisites.md) 

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Azure Security Center voor IoT inschakelen op uw IoT-hub 

Beveiliging inschakelen op uw IoT-hub: 

1. Open uw **IoT-hub** in Azure-portal. 
1. Klik **onder het** menu Beveiliging op **Uw IoT-oplossing beveiligen.**    


Gefeliciteerd! U hebt Azure Security Center voor IoT ingeschakeld op uw IoT-hub. 

### <a name="geolocation-and-ip-address-handling"></a>Geolocatie en behandeling van IP-adres

Om uw IoT-oplossing te beveiligen, worden IP-adressen van inkomende en uitgaande verbindingen van en naar uw IoT-apparaten, IoT Edge en IoT Hub(s) standaard verzameld en opgeslagen. Deze informatie is essentieel om abnormale connectiviteit van verdachte IP-bronnen te detecteren. Bijvoorbeeld wanneer pogingen worden gedaan om verbindingen te leggen van een IP-bron van een bekend botnet of van een IP-bron buiten uw geolocatie. Azure Security Center for IoT-service biedt de flexibiliteit om het verzamelen van IP-adresgegevens op elk gewenst moment in en uit te schakelen. 

Ga als het gaat om het verzamelen van IP-adresgegevens in of uit te schakelen: 

1. Open uw IoT-hub en selecteer **Overzicht** in het menu **Beveiliging.** 
2. Kies het scherm **Instellingen** en wijzig de geolocatie- en/of IP-verwerkingsinstellingen naar wens.

### <a name="log-analytics-creation"></a>Logboekanalyse maken

Wanneer Azure Security Center for IoT is ingeschakeld, wordt een standaard Azure Log Analytics-werkruimte gemaakt om onbewerkte beveiligingsgebeurtenissen, waarschuwingen en aanbevelingen op te slaan voor uw IoT-apparaten, IoT Edge en IoT Hub. Elke maand is de eerste vijf (5) GB aan gegevens die per klant worden ingenomen voor de Azure Log Analytics-service gratis. Elke GB aan gegevens die in uw Azure Log Analytics-werkruimte worden opgenomen, wordt de eerste 31 dagen kosteloos bewaard. Meer informatie over de prijzen [van Log Analytics.](https://azure.microsoft.com/pricing/details/monitor/)

Ga als een andere versie van de werkruimteconfiguratie van Log Analytics:

1. Open uw IoT-hub en selecteer **Overzicht** in het menu **Beveiliging.** 
2. Kies het scherm **Instellingen** en wijzig de werkruimteconfiguratie van loganalytics-instellingen naar wens.

### <a name="customize-your-iot-security-solution"></a>Uw IoT-beveiligingsoplossing aanpassen
Standaard schakelt u de Azure Security Center for IoT-oplossing in, waarbij alle IoT-hubs onder uw Azure-abonnement worden beveiligd. 

Ga als lid van het Azure Security Center voor IoT-service in op een specifieke IoT-hub: 

1. Open uw IoT-hub en selecteer **Overzicht** in het menu **Beveiliging.** 
2. Kies het scherm **Instellingen** en wijzig de beveiligingsinstellingen van een IoT-hub in uw Azure-abonnement zoals u dat wilt.


## <a name="next-steps"></a>Volgende stappen

Ga door naar het volgende artikel om uw oplossing te configureren...

> [!div class="nextstepaction"]
> [De oplossing configureren](quickstart-configure-your-solution.md)
