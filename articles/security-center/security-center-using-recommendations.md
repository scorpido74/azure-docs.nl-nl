---
title: Gebruik Azure Security Center aanbevelingen voor het verbeteren van de beveiliging | Microsoft Docs
description: " Meer informatie over het gebruik van beveiligings beleid en aanbevelingen in Azure Security Center voor het oplossen van een beveiligings aanval. "
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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 4c6b8b0bfa78dca5ca32c8c72edcc463ebb9057a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322135"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Azure Security Center-aanbevelingen gebruiken om de beveiliging te verbeteren

U kunt de kans op een belang rijke beveiligings gebeurtenis verminderen door een beveiligings beleid te configureren en vervolgens de aanbevelingen te implementeren die door Azure Security Center worden gegeven. In dit artikel leest u hoe u beveiligings beleid en aanbevelingen in Security Center kunt gebruiken om een beveiligings aanval te verhelpen. 

Security Center doorlopende scans wordt automatisch uitgevoerd om de beveiligings status van uw Azure-resources te analyseren. Wanneer Security Center mogelijke beveiligings problemen identificeert, worden er aanbevelingen gemaakt die u door het proces van het configureren van de benodigde beveiligings controles leiden. Security Center worden aanbevelingen binnen 24 uur bijgewerkt, met de volgende uitzonde ringen:

- Aanbevelingen voor de beveiligings configuratie van het besturings systeem worden binnen 48 uur bijgewerkt
- Aanbevelingen voor Endpoint Protection-problemen worden binnen 8 uur bijgewerkt

## <a name="scenario"></a>Scenario
In dit scenario ziet u hoe u Security Center kunt gebruiken om de kans op een beveiligings incident te verminderen door te controleren of er aanbevelingen zijn voor Security Center en actie te ondernemen. In het scenario wordt gebruikgemaakt van het fictieve bedrijf, Contoso en de functies die worden weer gegeven in de Security Center [plannings-en bedienings handleiding](security-center-planning-and-operations-guide.md#security-roles-and-access-controls). In dit scenario richten we ons op de rollen van de volgende personen:

![Scenario rollen](./media/security-center-using-recommendations/scenario-roles.png)

Contoso heeft een aantal van hun on-premises resources naar Azure gemigreerd. Contoso wil hun resources beveiligen en het beveiligings probleem van hun resources in de Cloud verminderen.

## <a name="use-azure-security-center"></a>Azure Security Center gebruiken
David, van de IT-beveiliging van contoso, heeft al gekozen om Security Center te doen op de abonnementen van Contoso op Azure Security Center om beveiligings problemen te voor komen en te detecteren. 

Security Center analyseert automatisch de beveiligings status van de Azure-resources van Contoso en past het standaard beveiligings beleid toe. Wanneer Security Center mogelijke beveiligings problemen identificeert, worden **aanbevelingen** gemaakt op basis van de besturings elementen die zijn ingesteld in het beveiligings beleid. 

David voert Azure-beveiliging uit met Azure Defender ingeschakeld, voor al hun abonnementen voor het verkrijgen van de volledige suite met aanbevelingen en beveiligings functies die beschikbaar zijn. Jeroen voert ook alle bestaande on-premises servers uit die nog niet zijn gemigreerd naar de Cloud, zodat ze kunnen profiteren van de hybride ondersteuning van Security Center op hun [Windows-en Linux-servers](quickstart-onboard-machines.md).

Jeff is een eigenaar van de Cloud-workload. Jeff is verantwoordelijk voor het Toep assen van beveiligings controles in overeenstemming met het beveiligings beleid van contoso. 

Jeff voert de volgende taken uit:

- Beveiligings aanbevelingen bewaken die worden verschaft door Security Center
- Evalueer beveiligings aanbevelingen en beslis of de aanbevelingen moeten worden toegepast of genegeerd.
- Aanbevelingen voor beveiliging Toep assen

### <a name="remediate-threats-using-recommendations"></a>Bedreigingen herstellen met behulp van aanbevelingen
Als onderdeel van hun dagelijkse bewakings activiteiten houdt Jeff zich aan bij Azure en opent Security Center. 

1. Jeff selecteert de abonnementen van de werk belasting.

2. Jeff controleert de **beveiligde Score** om een beeld te krijgen van hoe veilig de abonnementen zijn en ziet dat de Score 548 is.

3. Jeff moet bepalen welke aanbevelingen het eerst moeten worden verwerkt. Met Jeff klikt u op beveiligde Score en begint u met het afhandelen van aanbevelingen op basis van de mate waarin de [beveiligde Score](secure-score-security-controls.md)wordt verbeterd.

4. Omdat Jeff veel verbonden Vm's heeft, besluit Jeff te focussen op zijn machines in [Asset Inventory](asset-inventory.md).

5. Wanneer Jeff de asset-inventaris opent, wordt een lijst met aanbevelingen weer gegeven. Jeff verwerkt deze op basis van de impact op de beveiligde Score.

6. Jeroen heeft talloze Internet gerichte Vm's en omdat hun poorten worden weer gegeven, is het een goed moment dat een aanvaller controle kan krijgen over de servers. Jeff kiest voor het gebruik [**van just-in-time-VM-toegang**](security-center-just-in-time.md).

Jeff gaat door met de aanbevelingen met hoge prioriteit en normale prioriteit en maakt beslissingen bij de implementatie. Voor elke aanbeveling bekijkt Jeff de gedetailleerde informatie die wordt verstrekt door Security Center om te begrijpen welke resources worden beïnvloed, wat de invloed heeft op de gevolgen van de veilige Score, wat elke aanbeveling betekent en stappen voor herstel voor het oplossen van elk probleem.

### <a name="enforce-recommendations-to-prevent-security-misconfigurations"></a>Aanbevelingen afdwingen om onjuiste configuraties van beveiliging te voor komen

Om ervoor te zorgen dat gebruikers geen resources maken die een negatieve invloed hebben op de Score van Jeff, configureren ze de opties afdwingen en weigeren voor de aanbevelingen die het belangrijkst voor hen zijn. Meer informatie over het [voor komen van onjuiste configuraties met het afdwingen/weigeren van aanbevelingen](prevent-misconfigurations.md).


## <a name="conclusion"></a>Conclusie
Als u aanbevelingen in Security Center bewaken, kunt u beveiligings problemen elimineren voordat een aanval plaatsvindt. Wanneer u aanbevelingen herstelt, worden uw beveiligde Score en de beveiligings postuur van uw workloads verbeterd. Security Center detecteert automatisch nieuwe resources die u implementeert, beoordeelt deze tegen uw beveiligings beleid en biedt nieuwe aanbevelingen voor het beveiligen ervan.


## <a name="next-steps"></a>Volgende stappen
Zorg ervoor dat u een controle proces hebt, waarin u regel matig de aanbevelingen in Security Center controleert, zodat u ervoor kunt zorgen dat uw resources in de loop van de tijd veilig blijven.

In dit scenario wordt uitgelegd hoe u beveiligings beleid en aanbevelingen in Security Center kunt gebruiken om een beveiligings aanval te verhelpen.

Meer informatie over hoe u kunt reageren op bedreigingen met [beveiligings waarschuwingen beheren en erop reageren](security-center-managing-and-responding-alerts.md).
