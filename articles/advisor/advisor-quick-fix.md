---
title: Quick Fix-herstel voor aanbevelingen van adviseurs
description: Bulkherstel uitvoeren met Quick Fix in Advisor
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: 3c3ac27f04aa516fbef1bfff30e3392659b58919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502488"
---
# <a name="quick-fix-remediation-for-advisor"></a>Quick Fix-herstel voor adviseur
**Quick Fix** maakt een snellere en eenvoudigere manier van herstel mogelijk voor aanbeveling over meerdere resources. Het biedt mogelijkheden voor bulkherstelmogelijkheden voor resources en helpt u uw abonnementen sneller te optimaliseren met herstel op schaal voor uw resources.
De functie is alleen beschikbaar voor bepaalde aanbevelingen, via Azure portal.


## <a name="steps-to-use-quick-fix"></a>Stappen om 'Quick Fix' te gebruiken

1. Klik in de lijst met aanbevelingen met het label **Quick Fix** op de aanbeveling.

   ![Adviseur quick fix](./media/quick-fix-1.png)
   
   *Prijzen in de afbeelding zijn bijvoorbeeld alleen doeleinden*

2. Op de pagina Aanbevelingsdetails ziet u een lijst met bronnen waarvoor u deze aanbeveling hebt. Selecteer alle resources die u wilt herstellen voor de aanbeveling.

   ![Adviseur quick fix](./media/quick-fix-2.png)
   
   *Prijzen in de afbeelding zijn bijvoorbeeld alleen doeleinden*

3. Zodra u de resources hebt geselecteerd, klikt u op de knop **Snelle oplossing** om bulk te herstellen.

   > [!NOTE]
   > Sommige van de vermelde bronnen kunnen zijn uitgeschakeld, omdat u niet over de juiste machtigingen beschikt om ze te wijzigen.
   
   > [!NOTE]
   > Als er andere implicaties zijn, wordt u, naast de in Advisor genoemde voordelen, in de ervaring gecommuniceerd om u te helpen weloverwogen herstelbeslissingen te nemen.
   
4. U ontvangt een melding voor de voltooiing van de sanering. Er wordt een fout weergegeven als er resources zijn die niet zijn gesaneerd en resources in de geselecteerde modus in de weergave resourcelijst.  


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over aanbevelingen van Advisor:
* [Inleiding tot Azure Advisor](advisor-overview.md)
* [Aan de slag met Advisor](advisor-get-started.md)
* [Aanbevelingen voor kosten van adviseur](advisor-cost-recommendations.md)
* [Aanbevelingen voor prestaties van adviseur](advisor-performance-recommendations.md)
* [Aanbevelingen voor beveiliging van adviseur](advisor-security-recommendations.md)
* [Aanbevelingen adviseur Operational Excellence](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](https://docs.microsoft.com/rest/api/advisor/)
