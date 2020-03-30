---
title: Targetingmonitoringoplossingen in Azure Monitor | Microsoft Documenten
description: Targeting monitoring oplossingen u monitoring oplossingen te beperken tot een specifieke set van agenten.  In dit artikel wordt beschreven hoe u een scopeconfiguratie maakt en deze toepast op een oplossing.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: dd3279db67fb45aee43cf1b0ef1bebf49433eef4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663229"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Targetingmonitoringoplossingen in Azure Monitor (Preview)
Wanneer u een bewakingsoplossing toevoegt aan uw abonnement, wordt deze standaard automatisch geïmplementeerd op alle Windows- en Linux-agents die zijn aangesloten op uw Log Analytics-werkruimte.  Misschien wilt u uw kosten beheren en de hoeveelheid gegevens die voor een oplossing wordt verzameld beperken door deze te beperken tot een bepaalde set agents.  In dit artikel wordt beschreven hoe u **Solution Targeting kunt** gebruiken, een functie waarmee u een bereik toepassen op uw oplossingen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>Hoe een oplossing te richten
Er zijn drie stappen om een oplossing te targeten zoals beschreven in de volgende secties. 


### <a name="1-create-a-computer-group"></a>1. Een computergroep maken
U geeft de computers op die u in een bereik wilt opnemen door een [computergroep](../platform/computer-groups.md) te maken in Azure Monitor.  De computergroep kan worden gebaseerd op een logboekquery of worden geïmporteerd uit andere bronnen, zoals Active Directory- of WSUS-groepen. Zoals [hieronder beschreven,](#solutions-and-agents-that-cant-be-targeted)worden alleen computers die rechtstreeks zijn aangesloten op Azure Monitor opgenomen in het bereik.

Zodra u de computergroep in uw werkruimte hebt gemaakt, neemt u deze op in een scopeconfiguratie die op een of meer oplossingen kan worden toegepast.
 
 
### <a name="2-create-a-scope-configuration"></a>2. Een scopeconfiguratie maken
 Een **scopeconfiguratie** bevat een of meer computergroepen en kan worden toegepast op een of meer oplossingen. 
 
 Maak een scopeconfiguratie met het volgende proces.  

 1. Navigeer in de Azure-portal naar **Log Analytics-werkruimten** en selecteer uw werkruimte.
 2. Selecteer **Bereikconfiguraties**in de eigenschappen voor de werkruimte onder **Werkruimtegegevensbronnen** .
 3. Klik **op Toevoegen** om een nieuwe scopeconfiguratie te maken.
 4. Typ een **naam** voor de scopeconfiguratie.
 5. Klik **op Computergroepen selecteren**.
 6. Selecteer de computergroep die u hebt gemaakt en eventueel andere groepen die u aan de configuratie wilt toevoegen.  Klik **op Selecteren**.  
 6. Klik op **OK** om de scopeconfiguratie te maken. 


### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Pas de scopeconfiguratie toe op een oplossing.
Zodra u een scopeconfiguratie hebt, u deze toepassen op een of meer oplossingen.  Houd er rekening mee dat één scopeconfiguratie met meerdere oplossingen kan worden gebruikt, maar dat elke oplossing slechts één scopeconfiguratie kan gebruiken.

Pas een scopeconfiguratie toe met het volgende proces.  

 1. Navigeer in de Azure-portal naar **Log Analytics-werkruimten** en selecteer uw werkruimte.
 2. Selecteer **Oplossingen**in de eigenschappen voor de werkruimte .
 3. Klik op de oplossing die u wilt bieden.
 4. Selecteer **Oplossingstargeting**in de eigenschappen voor de oplossing onder **Werkruimtegegevensbronnen** .  Als de optie niet beschikbaar is, [kan deze oplossing niet worden getarget](#solutions-and-agents-that-cant-be-targeted).
 5. Klik **op Scopeconfiguratie toevoegen**.  Als u al een configuratie hebt toegepast op deze oplossing, is deze optie niet beschikbaar.  U moet de bestaande configuratie verwijderen voordat u een andere configuratie toevoegt.
 6. Klik op de scopeconfiguratie die u hebt gemaakt.
 7. Bekijk de **status** van de configuratie om ervoor te zorgen dat deze **geslaagd wordt weergegeven.**  Als de status een fout aangeeft, klikt u op de ellips rechts van de configuratie en selecteert u **Scopeconfiguratie bewerken** om wijzigingen aan te brengen.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Oplossingen en agenten die niet kunnen worden gericht
Hieronder volgen de criteria voor agents en oplossingen die niet kunnen worden gebruikt met oplossingstargeting.

- Oplossingstargeting is alleen van toepassing op oplossingen die worden geïmplementeerd voor agents.
- Oplossingstargeting is alleen van toepassing op oplossingen van Microsoft.  Het is niet van toepassing op oplossingen die door uzelf of partners zijn [gemaakt.](solutions-creating.md)
- U alleen agents filteren die rechtstreeks verbinding maken met Azure Monitor.  Oplossingen worden automatisch geïmplementeerd voor agents die deel uitmaken van een verbonden Operations Manager-beheergroep, ongeacht of ze al dan niet zijn opgenomen in een scopeconfiguratie.

### <a name="exceptions"></a>Uitzonderingen
Oplossingstargeting kan niet worden gebruikt met de volgende oplossingen, ook al voldoen ze aan de gestelde criteria.

- Agent Gezondheidsbeoordeling

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over bewakingsoplossingen, waaronder de oplossingen die beschikbaar zijn om in uw omgeving te installeren bij [Bewakingsoplossingen toevoegen van Azure Log Analytics voor uw werkruimte.](solutions.md)
- Meer informatie over het maken van computergroepen bij [computergroepen in azure monitor-logboekquery's](../platform/computer-groups.md).
