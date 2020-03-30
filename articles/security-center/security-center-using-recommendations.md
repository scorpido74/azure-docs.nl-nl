---
title: Aanbevelingen voor Azure Security Center gebruiken om de beveiliging te verbeteren | Microsoft Documenten
description: " Meer informatie over het gebruik van beveiligingsbeleid en -aanbevelingen in Azure Security Center om een beveiligingsaanval te beperken. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: memildin
ms.openlocfilehash: a1034eb47010da2b0e795ee8c79646f06151cac1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603285"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Azure Security Center-aanbevelingen gebruiken om de beveiliging te verbeteren
U de kans op een belangrijke beveiligingsgebeurtenis verkleinen door een beveiligingsbeleid te configureren en vervolgens de aanbevelingen van Azure Security Center te implementeren. In dit artikel ziet u hoe u beveiligingsbeleid en aanbevelingen gebruiken in Security Center om een beveiligingsaanval te beperken. 

Security Center voert automatisch continue scans uit om de beveiligingsstatus van uw Azure-bronnen te analyseren. Wanneer Security Center potentiële beveiligingsproblemen identificeert, worden er aanbevelingen gemaakt die u begeleiden bij het configureren van de benodigde beveiligingsbesturingselementen. Security Center werkt zijn aanbevelingen binnen 24 uur bij, met de volgende uitzonderingen:

- Aanbevelingen voor beveiligingsconfiguratie van het besturingssysteem worden binnen 48 uur bijgewerkt
- Aanbevelingen voor problemen met problemen met endpointbeveiliging worden binnen 8 uur bijgewerkt

## <a name="scenario"></a>Scenario
In dit scenario ziet u hoe u Security Center gebruiken om de kans op een beveiligingsincident te verkleinen door aanbevelingen van het Beveiligingscentrum te controleren en actie te ondernemen. Het scenario maakt gebruik van de fictieve bedrijf, Contoso, en rollen gepresenteerd in het Security Center [planning en operations guide](security-center-planning-and-operations-guide.md#security-roles-and-access-controls). In dit scenario richten we ons op de rollen van de volgende persona's:

![Scenariorollen](./media/security-center-using-recommendations/scenario-roles.png)

Contoso heeft onlangs een aantal van hun on-premises resources gemigreerd naar Azure. Contoso wil hun resources beschermen en de kwetsbaarheid van hun bronnen in de cloud verminderen.

## <a name="use-azure-security-center"></a>Azure Security Center gebruiken
David, van de IT-beveiliging van Contoso, heeft er al voor gekozen om het Security Center in te gaan op de abonnementen van Contoso op Azure Security Center om beveiligingsproblemen te voorkomen en op te sporen. 

Security Center analyseert automatisch de beveiligingsstatus van de Azure-bronnen van Contoso en past standaardbeveiligingsbeleid toe. Wanneer Security Center potentiële beveiligingsproblemen identificeert, worden **aanbevelingen** uitgevoerd op basis van de besturingselementen die zijn ingesteld in het beveiligingsbeleid. 

David voert de standaardlaag azure security uit voor al hun abonnementen om de volledige suite met aanbevelingen en beveiligingsfuncties beschikbaar te krijgen. Jeff ontdoet zich ook aan al hun bestaande on-premises servers die nog niet zijn gemigreerd naar de cloud, zodat ze kunnen profiteren van de hybride ondersteuning van Security Center op hun [Windows-](quick-onboard-windows-computer.md) en [Linux-servers.](quick-onboard-linux-computer.md)

Jeff is eigenaar van een cloudworkload. Jeff is verantwoordelijk voor het toepassen van beveiligingscontroles in overeenstemming met het beveiligingsbeleid van Contoso. 

Jeff voert de volgende taken uit:

- Beveiligingsaanbevelingen van Security Center bewaken
- Evalueer beveiligingsaanbevelingen en beslis of ze de aanbevelingen moeten toepassen of afwijzen.
- Beveiligingsaanbevelingen toepassen

### <a name="remediate-threats-using-recommendations"></a>Bedreigingen herstellen met aanbevelingen
Als onderdeel van hun dagelijkse bewakingsactiviteiten meldt Jeff zich aan bij Azure en opent het Beveiligingscentrum. 

1. Jeff selecteert de abonnementen van de workload.

2. Jeff controleert de **Secure Score** om een algemeen beeld te krijgen van hoe veilig de abonnementen zijn en ziet dat de score 548 is.

3. Jeff moet beslissen welke aanbevelingen eerst moeten worden gedaan. Dus Jeff klikt op Secure Score en begint aanbevelingen te verwerken op basis van hoeveel het zijn [secure score impact](security-center-secure-score.md)verbetert.

4. Omdat Jeff veel verbonden VM's en servers heeft, besluit Jeff zich te richten op **Compute en apps.**

5. Wanneer Jeff op **Compute en apps**klikt, zien ze een lijst met aanbevelingen en verwerken ze deze op basis van de impact van de secure score.

6. Jeff heeft tal van internet geconfronteerd met VM's, en omdat hun poorten worden blootgesteld, zijn ze bang dat een aanvaller controle over de servers zou kunnen krijgen. Dus Jeff kiest ervoor om [**just-in-time VM-toegang**](security-center-just-in-time.md)te gebruiken.

Jeff blijft bewegen door de hoge prioriteit en medium prioriteit aanbevelingen, en neemt beslissingen over de uitvoering. Voor elke aanbeveling bekijkt Jeff de gedetailleerde informatie die door Security Center wordt verstrekt om te begrijpen welke bronnen worden beïnvloed, wat de impact van de secure score is, wat elke aanbeveling betekent en herstelstappen voor het beperken van elk probleem.

## <a name="conclusion"></a>Conclusie
Met de aanbevelingen voor het bewaken in Security Center u beveiligingsproblemen verwijderen voordat er een aanval plaatsvindt. Wanneer u aanbevelingen herstelt, verbeteren uw secure score en de beveiligingshouding van uw workloads. Security Center detecteert automatisch nieuwe resources die u implementeert, beoordeelt ze in strijd met uw beveiligingsbeleid en geeft nieuwe aanbevelingen voor het beveiligen ervan.


## <a name="next-steps"></a>Volgende stappen
Zorg ervoor dat u een bewakingsproces hebt, waarbij u regelmatig de aanbevelingen in Security Center controleert, zodat u ervoor zorgen dat uw resources na verloop van tijd veilig zijn.

In dit scenario ziet u hoe u beveiligingsbeleid en aanbevelingen gebruiken in Security Center om een beveiligingsaanval te beperken.

Meer informatie over hoe u reageren op bedreigingen met [Het beheren en reageren op beveiligingswaarschuwingen.](security-center-managing-and-responding-alerts.md)
