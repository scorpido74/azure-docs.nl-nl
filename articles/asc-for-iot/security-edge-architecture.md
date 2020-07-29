---
title: Beveiligings module voor IoT Edge
description: Meer informatie over de architectuur en mogelijkheden van Azure Security Center voor IoT Security module voor IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0eb68f517c4c5b04dd4f92dd1a804dcf5ad24362
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81310631"
---
# <a name="azure-iot-edge-security-module"></a>Beveiligings module Azure IoT Edge

[Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/) biedt krachtige mogelijkheden voor het beheren en uitvoeren van zakelijke werk stromen aan de rand.
Het belangrijkste deel dat IoT Edge speelt in IoT-omgevingen, maakt het bijzonder aantrekkelijk voor kwaad aardige actors.

Azure Security Center voor IoT Security module biedt een uitgebreide beveiligings oplossing voor uw IoT Edge-apparaten.
Azure Security Center voor IoT-module verzamelt, aggregeert en analyseert onbewerkte beveiligings gegevens van uw besturings systeem en container systeem in aanbevelingen voor beveiliging en waarschuwingen.

Net als Azure Security Center voor IOT-beveiligings agenten voor IoT-apparaten is de Azure Security Center voor IoT Edge module zeer aanpasbaar via de module twee.
Zie [uw agent configureren](how-to-agent-configuration.md) voor meer informatie.

Azure Security Center voor de IoT-beveiligings module voor IoT Edge biedt de volgende functies:

- Hiermee worden onbewerkte beveiligings gebeurtenissen van het onderliggende besturings systeem (Linux) en de IoT Edge container systemen verzameld.

  Zie [Azure Security Center voor IOT-agent configuratie](how-to-agent-configuration.md) voor meer informatie over beschik bare beveiligings gegevens verzamelaars.

- Analyse van IoT Edge implementatie manifesten.

- Voegt onbewerkte beveiligings gebeurtenissen samen in berichten die via [IOT Edge hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)worden verzonden.

- Verwijder de configuratie door middel van het gebruik van de beveiligings module dubbele.

  Zie [Configure an Azure Security Center voor IOT-agent](how-to-agent-configuration.md) voor meer informatie.

Azure Security Center voor de IoT-beveiligings module voor IoT Edge wordt uitgevoerd in een beschermde modus onder IoT Edge.
De modus Privileged is vereist om de module toe te staan het besturings systeem en andere IoT Edge modules te bewaken.

## <a name="module-supported-platforms"></a>Door module ondersteunde platforms

Azure Security Center voor de IoT-beveiligings module voor IoT Edge is momenteel alleen beschikbaar voor Linux.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de architectuur en mogelijkheden van Azure Security Center voor IoT Security module voor IoT Edge.

Als u aan de slag wilt gaan met Azure Security Center voor IoT-implementatie, gebruikt u de volgende artikelen:

- [Beveiligings module voor IOT Edge](how-to-deploy-edge.md) implementeren
- Meer informatie over [het configureren van uw beveiligings module](how-to-agent-configuration.md)
- Bekijk de Azure Security Center voor vereisten voor IoT- [Services](service-prerequisites.md)
- Meer informatie over het [inschakelen van Azure Security Center voor IOT-service in uw IOT hub](quickstart-onboard-iot-hub.md)
- Meer informatie over de service vindt u in de [Veelgestelde vragen over Azure Security Center voor IOT](resources-frequently-asked-questions.md)
