---
title: Informatie over het IP-adres van uw IoT-hub | Microsoft Docs
description: Meer informatie over het opvragen van uw IoT hub IP-adres en de bijbehorende eigenschappen. Het IP-adres van uw IoT-hub kan worden gewijzigd tijdens bepaalde scenario's, zoals herstel na nood gevallen of een regionale failover.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 5c77fb30ef60c1ad82d0a87442bc8af186c54321
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383902"
---
# <a name="iot-hub-ip-addresses"></a>IoT Hub IP-adressen

De IP-adres voorvoegsels van de IoT-hub worden periodiek gepubliceerd onder het label *AzureIoTHub* - [service](../virtual-network/service-tags-overview.md). Om ervoor te zorgen dat de juiste werking is, moeten uw IoT-apparaten uitgaande verbindingen hebben met adres voorvoegsels die worden vermeld onder *AzureIoTHub* service tag. Uw IoT-toepassings services moeten ook uitgaande verbindingen hebben met adres voorvoegsels die worden vermeld onder *EventHub* service tag.


## <a name="best-practices"></a>Aanbevolen procedures

* De IP-adres voorvoegsels van IoT hub zijn onderhevig aan wijzigingen. Deze wijzigingen worden regel matig gepubliceerd via service Tags voordat ze van kracht worden. Het is daarom belang rijk dat u processen ontwikkelt om regel matig de meest recente service tags op te halen en te gebruiken. Dit proces kan worden geautomatiseerd via de [service Tags detectie-API](../virtual-network/service-tags-overview.md#service-tags-in-on-premises).
* Gebruik de *AzureIoTHub. [ regio naam]* label voor het identificeren van IP-voor voegsels die worden gebruikt door IOT hub-eind punten in een specifieke regio. Als u wilt account voor herstel na nood gevallen van Data Center of [regionale failover](iot-hub-ha-dr.md) , zorgt u ervoor dat de verbinding met IP-voor voegsels van de geografische paar regio van uw IOT hub ook is ingeschakeld.


## <a name="support-for-ipv6"></a>Ondersteuning voor IPv6 

IPv6 wordt momenteel niet ondersteund in IoT Hub.