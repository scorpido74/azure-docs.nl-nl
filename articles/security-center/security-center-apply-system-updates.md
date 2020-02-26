---
title: Systeem updates Toep assen in Azure Security Center | Microsoft Docs
description: In dit document wordt beschreven hoe u de Azure Security Center aanbevelingen implementeert **systeem updates Toep assen** en **opnieuw opstarten na systeem updates**.
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
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604548"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Systeem updates Toep assen in Azure Security Center
Azure Security Center bewaakt dagelijks Windows-en Linux-virtuele machines (Vm's) en computers voor ontbrekende updates van het besturings systeem. Security Center haalt een lijst met beschik bare beveiligings-en essentiële updates op van Windows Update of Windows Server Update Services (WSUS), afhankelijk van welke service op een Windows-computer is geconfigureerd. Security Center controleert ook op de nieuwste updates in Linux-systemen. Als op uw virtuele machine of computer een systeem update ontbreekt, wordt Security Center aanbevolen om systeem updates toe te passen.

## <a name="implement-the-recommendation"></a>De aanbeveling implementeren
Systeem updates Toep assen wordt weer gegeven als aanbeveling in Security Center. Als op uw virtuele machine of computer een systeem update ontbreekt, wordt deze aanbeveling weer gegeven onder **aanbevelingen** en onder **Compute**.  Als u de aanbeveling selecteert, wordt het dash board **systeem updates Toep assen** geopend.

In dit voor beeld gebruiken we **reken kracht**.

1. Selecteer **berekenen** onder het hoofd menu van Security Center.

   ![Reken proces selecteren][1]

2. Selecteer onder **Compute**de optie **ontbrekende systeem updates**. Het dash board **systeem updates Toep assen** wordt geopend.

   ![Het dash board systeem updates Toep assen][2]

   De bovenkant van het dash board biedt:

    - Het totale aantal virtuele Windows-en Linux-machines en computers waarop systeem updates ontbreken.
    - Het totale aantal essentiële updates dat op uw Vm's en computers ontbreekt.
    - Het totale aantal beveiligings updates dat op uw Vm's en computers ontbreekt.

   De onderkant van het dash board bevat alle ontbrekende updates op uw Vm's en computers en de ernst van de ontbrekende update.  De lijst bevat:

    - NAAM: naam van de ontbrekende update.
    - Geen. VAN Vm's & COMPUTERS: totaal aantal Vm's en computers waarop deze update ontbreekt.
    - STATUS: de huidige status van de aanbeveling:

      - Open: de aanbeveling is nog niet verholpen.
      - Wordt uitgevoerd: de aanbeveling wordt momenteel toegepast op deze resources en er hoeft geen actie te worden ondernomen.
      - Opgelost: de aanbeveling is al voltooid. (Als het probleem is opgelost, wordt de vermelding grijs).

    - Ernst: Hiermee wordt de ernst van de betreffende aanbeveling beschreven:

      - Hoog: er bestaat een beveiligings probleem met een zinvolle resource (toepassing, virtuele machine of netwerk beveiligings groep) en vereist aandacht.
      - Gemiddeld: er zijn niet-kritieke of extra stappen nodig om een proces te volt ooien of een beveiligings probleem op te lossen.
      - Laag: er moet een beveiligings probleem worden opgelost, maar dit vereist geen onmiddellijke aandacht. (Aanbevelingen met de ernstaanduiding Laag worden niet standaard weergegeven, maar u kunt hierop filteren als u deze aanbevelingen wilt bekijken.)

3. Selecteer een ontbrekende update in de lijst om de details weer te geven.

   ![Ontbrekende beveiligings update][3]

4. Selecteer het **Zoek** pictogram in het bovenste lint.  De zoek query van een Azure Monitor Logboeken wordt geopend op de computers waarop de update ontbreekt.

   ![Azure Monitor logboeken zoeken][4]

5. Selecteer een computer in de lijst voor meer informatie. Er wordt een ander Zoek resultaat geopend met informatie die alleen voor die computer wordt gefilterd.

    ![Azure Monitor logboeken zoeken][5]

## <a name="next-steps"></a>Volgende stappen
Zie de volgende onderwerpen voor meer informatie over het Beveiligingscentrum:

* [Setting security policies in Azure Security Center](tutorial-security-policy.md) (Beveiligingsbeleid instellen in Azure Security Center): leer hoe u beveiligingsbeleid voor uw Azure-abonnementen en -resourcegroepen configureert.
* [Aanbevelingen voor beveiliging in azure Security Center](security-center-recommendations.md) : Ontdek hoe aanbevelingen u helpen uw Azure-resources te beveiligen.
* [Beveiligings status controleren in azure Security Center](security-center-monitoring.md) --meer informatie over het controleren van de status van uw Azure-resources.
* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md) (Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center): ontdek hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren.
* [Partneroplossingen bewaken met Azure Security Center](security-center-partner-solutions.md): leer hoe u de integriteitsstatus van uw partneroplossingen kunt bewaken.
* [Azure-beveiligings blog](https://blogs.msdn.com/b/azuresecurity/) : vind blog berichten over beveiliging en naleving van Azure.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
