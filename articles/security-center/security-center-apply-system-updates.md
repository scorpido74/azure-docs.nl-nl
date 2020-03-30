---
title: Systeemupdates toepassen in Azure Security Center | Microsoft Documenten
description: In dit document ziet u hoe u de aanbevelingen van het Azure Security Center implementeert **Systeemupdates toepassen** en **Opnieuw opstarten na systeemupdates.**
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 3f27753b0775f44cbdf9d4c478a19e423b8e1f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604548"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Systeemupdates toepassen in Azure Security Center
Azure Security Center controleert dagelijks virtuele Windows- en Linux-machines (VM's) en computers op ontbrekende updates van het besturingssysteem. Security Center haalt dagelijks een lijst met beschikbare beveiligingsupdates en essentiële updates op van Windows Update of WSUS, afhankelijk van welke service is geconfigureerd op een Windows-computer. Security Center controleert ook op de nieuwste updates voor Linux-systemen. Als op uw VM of computer een systeemupdate ontbreekt, ziet u een melding van Security Center met het verzoek om systeemupdates toe te passen.

## <a name="implement-the-recommendation"></a>De aanbeveling uitvoeren
Systeemupdates toepassen wordt gepresenteerd als een aanbeveling in Security Center. Als uw vm of computer een systeemupdate mist, wordt deze aanbeveling weergegeven onder **Aanbevelingen** en onder **Berekenen**.  Als u de aanbeveling selecteert, wordt het dashboard **Systeemupdates toepassen** geopend.

In dit voorbeeld gebruiken we **Compute**.

1. Selecteer **Berekenen** onder het hoofdmenu Van het Beveiligingscentrum.

   ![Compute selecteren][1]

2. Selecteer **onder Berekenen**de optie **Ontbrekende systeemupdates**. Het dashboard **Systeemupdates toepassen** wordt geopend.

   ![Dashboard systeemupdates toepassen][2]

   De bovenkant van het dashboard biedt:

    - Het totale aantal Windows- en Linux-VM's en computers dat systeemupdates mist.
    - Het totale aantal kritieke updates dat ontbreekt in uw VM's en computers.
    - Het totale aantal beveiligingsupdates dat ontbreekt in uw VM's en computers.

   De onderkant van het dashboard bevat alle ontbrekende updates op uw VM's en computers en de ernst van de ontbrekende update.  De lijst bevat:

    - NAAM: naam van de ontbrekende update.
    - №. VAN VM's & COMPUTERS: Totaal aantal VM's en computers die deze update missen.
    - STATUS: de huidige status van de aanbeveling:

      - Open: De aanbeveling is nog niet behandeld.
      - Wordt uitgevoerd: de aanbeveling wordt momenteel toegepast op deze resources en u hoeft geen actie te ondernemen.
      - Opgelost: de aanbeveling is al voltooid. (Als het probleem is opgelost, wordt de vermelding grijs).

    - ERNST: hiermee wordt de ernst van deze bepaalde aanbeveling beschreven:

      - Hoog: er bestaat een beveiligingsprobleem voor een belangrijke resource (toepassing, virtuele machine, netwerkbeveiligingsgroep) en dit probleem vereist uw aandacht.
      - Gemiddeld: er zijn niet-kritieke of extra stappen nodig om een proces te voltooien of een beveiligingsprobleem op te lossen.
      - Laag: een beveiligingsprobleem moet worden opgelost, maar dit vereist niet uw onmiddellijke aandacht. (Aanbevelingen met de ernstaanduiding Laag worden niet standaard weergegeven, maar u kunt hierop filteren als u deze aanbevelingen wilt bekijken.)

3. Selecteer een ontbrekende update in de lijst om details weer te geven.

   ![Ontbrekende beveiligingsupdate][3]

4. Selecteer het pictogram **Zoeken** op het bovenste lint.  Een Azure Monitor registreert zoekopdracht opent gefilterd op de computers die de update ontbreekt.

   ![Azure Monitor registreert zoeken][4]

5. Selecteer een computer in de lijst voor meer informatie. Een ander zoekresultaat wordt geopend met informatie die alleen voor die computer wordt gefilterd.

    ![Azure Monitor registreert zoeken][5]

## <a name="next-steps"></a>Volgende stappen
Zie de volgende onderwerpen voor meer informatie over Security Center:

* [Setting security policies in Azure Security Center](tutorial-security-policy.md) (Beveiligingsbeleid instellen in Azure Security Center): leer hoe u beveiligingsbeleid voor uw Azure-abonnementen en -resourcegroepen configureert.
* [Beveiligingsaanbevelingen beheren in Azure Security Center:](security-center-recommendations.md) lees hoe aanbevelingen u helpen uw Azure-bronnen te beschermen.
* [Bewaking van de beveiligingsstatus in Azure Security Center:](security-center-monitoring.md) meer informatie over het bewaken van de status van uw Azure-resources.
* [Beveiligingswaarschuwingen beheren en beantwoorden in Azure Security Center:](security-center-managing-and-responding-alerts.md) meer informatie over het beheren en reageren op beveiligingswaarschuwingen.
* [Partnersoplossingen bewaken met Azure Security Center:](security-center-partner-solutions.md) meer informatie over het bewaken van de status van uw partneroplossingen.
* [Azure Security-blog](https://blogs.msdn.com/b/azuresecurity/) : zoek blogberichten over Azure-beveiliging en -naleving.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
