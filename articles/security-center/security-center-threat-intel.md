---
title: Toewijzing & Security Alert-Azure Security Center voor bedreigings informatie
description: Meer informatie over het gebruik van de functie voor het toewijzen van beveiligings waarschuwingen en bedreigings informatie in Azure Security Center om mogelijke dreigingen in uw Vm's en computers te identificeren.
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
ms.openlocfilehash: 97975d72214426907a2ab91f0d3cd98d0ce6734b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693486"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Overzicht beveiligingswaarschuwingen en informatie over bedreigingen
Dit artikel helpt u bij het gebruik van de Azure Security Center Security Alerts en beveiliging op basis van bedreigings informatie die betrekking heeft op beveiligings problemen.

> [!NOTE]
> De knop voor de toewijzing van beveiligings *gebeurtenissen* is op 31 juli 2019 ingetrokken. Zie voor meer informatie en alternatieve Services de [buiten gebruiks telling van Security Center-functies (2019 juli)](security-center-features-retirement-july2019.md#menu_securityeventsmap).


## <a name="how-the-security-alerts-map-works"></a>Hoe de toewijzing van beveiligings waarschuwingen werkt
Security Center biedt u een kaart waarmee u beveiligings Risico's voor de omgeving kunt identificeren. U kunt bijvoorbeeld bepalen of een bepaalde computer deel uitmaakt van een botnet en waar de dreiging van afkomstig is. Computers kunnen knoop punten worden in een botnet wanneer aanvallers schadelijke software installeren die op een geheime wijze communiceert met opdracht en beheer waarmee het botnet wordt beheerd. 

Security Center maakt gebruik van gegevens die afkomstig zijn uit meerdere bronnen in micro soft om deze kaart samen te stellen. Security Center gebruikt deze gegevens om mogelijke dreigingen toe te wijzen aan uw omgeving. 

Een van de stappen van de [procedure om te reageren op een beveiligingsincident](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) is het identificeren van de ernst van het aangevallen systeem. In deze fase moet u de volgende taken uitvoeren:

- Bepaal de aard van de aanval.
- Bepaal het punt van oorsprong van de aanval.
- Bepaal de bedoeling van de aanval. Was de aanval gericht op uw organisatie om specifieke gegevens te verkrijgen, of was het een willekeurige aanval?
- Stel vast welke systemen zijn aangevallen.
- Stel vast welke bestanden zijn geopend en bepaal de gevoeligheid van die bestanden.

U kunt de toewijzing van beveiligings waarschuwingen in Security Center gebruiken om te helpen bij deze taken.

## <a name="access-the-security-alerts-map"></a>De toewijzing van de beveiligings waarschuwingen openen
Als u de huidige bedreigingen voor uw omgeving wilt visualiseren, opent u de map met beveiligings waarschuwingen:

1. Open het dashboard van **Security Center**.
2. Selecteer in het linkerdeel venster onder **bedreigingen beveiliging** de optie **beveiligings waarschuwingen toewijzen**. De kaart wordt geopend.
3. Als u meer informatie wilt over de waarschuwing en de stappen voor herbemiddeling wilt ontvangen, klikt u op de waarschuwings stip op de kaart en volgt u de instructies. 
 
De toewijzing van beveiligings waarschuwingen is gebaseerd op waarschuwingen. Deze waarschuwingen zijn gebaseerd op activiteiten waarvoor netwerk communicatie is gekoppeld aan een IP-adres dat is omgezet, ongeacht of het IP-adres een bekend riskant IP-adres is (bijvoorbeeld een bekend cryptominer) of een IP-adres dat niet wordt herkend voorheen als riskant. De kaart biedt waarschuwingen over alle abonnementen die u eerder hebt geselecteerd in Azure. 

De waarschuwingen op de kaart worden weer gegeven op basis van de geografische locatie waar ze worden gedetecteerd als afkomstig van, en ze zijn in kleur gecodeerd op Ernst. 
    ![informatie over Threat Intelligence](./media/security-center-threat-intel/security-center-alert-map.png)



## <a name="see-also"></a>Zie ook
In dit artikel hebt u geleerd hoe u bedreigingsinformatie in Security Center gebruikt om u te helpen bij het identificeren van verdachte activiteiten. Zie de volgende artikelen voor meer informatie over Security Center:

* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informatie over het beheren van waarschuwingen en het reageren op beveiligingsincidenten in Security Center.
* [Beveiligingsstatus bewaken in Azure Security Center](security-center-monitoring.md). Meer informatie over het controleren van de status van uw Azure-resources.
* [Beveiligingswaarschuwingen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Meer informatie over de verschillende typen beveiligingswaarschuwingen.
* [Handleiding voor het oplossen van problemen met Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informatie over het oplossen van veelvoorkomende problemen met Security Center.
* [Veelgestelde vragen over Azure Security Center](security-center-faq.md). Zoek antwoorden op veelgestelde vragen over het gebruik van de service.
* [Azure-beveiligingsblog](https://blogs.msdn.com/b/azuresecurity/). Raadpleeg de blogberichten over beveiliging en naleving in Azure.
