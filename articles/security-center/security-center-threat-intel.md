---
title: Waarschuwingskaart voor bedreigingsinformatie & beveiliging - Azure Security Center
description: Meer informatie over het gebruik van de beveiligingskaart kaart en threat intelligence mogelijkheden in Azure Security Center om potentiële bedreigingen in uw VM's en computers te identificeren.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 39835d9d73adcbe474d3b70dfced313e18d1a3b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603432"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Overzicht beveiligingswaarschuwingen en informatie over bedreigingen
Met dit artikel u de map beveiligingswaarschuwingen van Azure Security Center en de kaart voor beveiligingsgebeurtenissen op basis van bedreigingsinformatie gebruiken om beveiligingsgerelateerde problemen op te lossen.

> [!NOTE]
> De kaartknop *Beveiligingsevenementen* is op 31 juli 2019 met pensioen gegaan. Zie Functies van het [Pension of Security Center (juli 2019) voor](security-center-features-retirement-july2019.md#menu_securityeventsmap)meer informatie en alternatieve diensten.


## <a name="how-the-security-alerts-map-works"></a>Hoe de kaart met beveiligingswaarschuwingen werkt
Security Center biedt u een kaart waarmee u beveiligingsbedreigingen voor de omgeving identificeren. U bijvoorbeeld bepalen of een bepaalde computer deel uitmaakt van een botnet en waar de dreiging vandaan komt. Computers kunnen knooppunten in een botnet worden wanneer aanvallers illegaal malware installeren die in het geheim communiceert met commando en controle die het botnet beheren. 

Om deze kaart te bouwen, gebruikt Security Center gegevens die afkomstig zijn van meerdere bronnen binnen Microsoft. Security Center gebruikt deze gegevens om potentiële bedreigingen voor uw omgeving in kaart te brengen. 

Een van de stappen van de [procedure om te reageren op een beveiligingsincident](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) is het identificeren van de ernst van het aangevallen systeem. In deze fase moet u de volgende taken uitvoeren:

- Bepaal de aard van de aanval.
- Bepaal het punt van oorsprong van de aanval.
- Bepaal de bedoeling van de aanval. Was de aanval gericht op uw organisatie om specifieke gegevens te verkrijgen, of was het een willekeurige aanval?
- Stel vast welke systemen zijn aangevallen.
- Stel vast welke bestanden zijn geopend en bepaal de gevoeligheid van die bestanden.

U de kaart Beveiligingswaarschuwingen in Beveiligingscentrum gebruiken om u te helpen bij deze taken.

## <a name="access-the-security-alerts-map"></a>Toegang tot de kaart met beveiligingswaarschuwingen
Als u de huidige bedreigingen in uw omgeving wilt visualiseren, opent u de kaart Beveiligingswaarschuwingen:

1. Open het dashboard van **Security Center**.
2. Selecteer in het linkerdeelvenster onder **Bedreigingsbeveiliging** de **optie Beveiligingswaarschuwingenkaart**. De kaart gaat open.
3. Klik op de waarschuwingspunt op de kaart en volg de instructies om meer informatie over de waarschuwing te krijgen en herstelstappen te ontvangen. 
 
De kaart met beveiligingswaarschuwingen is gebaseerd op waarschuwingen. Deze waarschuwingen zijn gebaseerd op activiteiten waarvoor netwerkcommunicatie is gekoppeld aan een IP-adres dat met succes is opgelost, ongeacht of het IP-adres een bekend riskant IP-adres is (bijvoorbeeld een bekende cryptominer) of een IP-adres dat niet wordt herkend voorheen als riskant. De kaart bevat waarschuwingen voor alle abonnementen die u eerder in Azure hebt geselecteerd. 

De waarschuwingen op de kaart worden weergegeven op basis van de geografische locatie waar ze worden gedetecteerd als afkomstig van, en ze zijn kleur gecodeerd door de ernst. 
    ![Bedreigingsinformatie](./media/security-center-threat-intel/security-center-alert-map.png)



## <a name="see-also"></a>Zie ook
In dit artikel hebt u geleerd hoe u bedreigingsinformatie in Security Center gebruikt om u te helpen bij het identificeren van verdachte activiteiten. Zie de volgende artikelen voor meer informatie over Security Center:

* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informatie over het beheren van waarschuwingen en het reageren op beveiligingsincidenten in Security Center.
* [Bewaking van de beveiligingsstatus in Azure Security Center](security-center-monitoring.md). Meer informatie over het controleren van de status van uw Azure-resources.
* [Beveiligingswaarschuwingen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Meer informatie over de verschillende typen beveiligingswaarschuwingen.
* [Handleiding voor het oplossen van problemen met Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informatie over het oplossen van veelvoorkomende problemen met Security Center.