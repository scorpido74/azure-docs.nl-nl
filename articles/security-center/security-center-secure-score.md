---
title: Beveiligde score in Azure Security Center | Microsoft Documenten
description: " Geef prioriteit aan uw beveiligingsaanbevelingen met de Beveiligde score in Azure Security Center. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/15/2019
ms.author: memildin
ms.openlocfilehash: 30405ce5cc875144fcd1cf83d4a3f883a0304989
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415772"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Uw veilige score verbeteren in Azure Security Center

> [!NOTE]
> Dit artikel gaat over de vorige versie van de beveiligde score. Deze veilige score ervaring is nog steeds beschikbaar van de UI, maar zal worden uitgefaseerd na verloop van tijd. De twee veilige score-ervaringen worden naast elkaar uitgevoerd om een soepelere overgang mogelijk te maken.
>
> Voor meer informatie over de nieuwere veilige score, zie [hier](secure-score-security-controls.md).
>

Met zoveel services die beveiligingsvoordelen bieden, is het vaak moeilijk om te weten welke stappen u eerst moet nemen om uw werklast te beveiligen en te verharden. De Secure Score beoordeelt uw beveiligingsaanbevelingen en geeft prioriteit aan deze aanbevelingen, zodat u weet welke aanbevelingen u het eerst moet uitvoeren. Dit helpt u bij het vinden van de ernstigste beveiligingsproblemen, zodat u prioriteit geven aan onderzoek. Secure Score is een tool die u helpt bij het beoordelen van uw werkbelasting beveiligingshouding.

## <a name="secure-score-calculation"></a>Berekening van de beveiligde score

Security Center bootst het werk van een beveiligingsanalist na, bekijkt uw beveiligingsaanbevelingen en past geavanceerde algoritmen toe om te bepalen hoe cruciaal elke aanbeveling is.
Azure Security Center controleert voortdurend uw actieve aanbevelingen en berekent uw beveiligde score op basis daarvan, de score van een aanbeveling is afgeleid van de ernst en beveiligingsbestpractices die uw workloadbeveiliging het meest beïnvloeden.

Security Center biedt u ook een **algemene beveiligde score.** 

**Overall Secure Score** is een opeenstapeling van al uw aanbevelingsscores. U uw algemene beveiligde score bekijken voor uw abonnementen of beheergroepen, afhankelijk van wat u selecteert. De score is afhankelijk van het geselecteerde abonnement en de actieve aanbevelingen voor deze abonnementen.

Als u wilt controleren welke aanbevelingen het meest van invloed zijn op uw beveiligde score, u de drie meest impactvolle aanbevelingen in het dashboard van het beveiligingscentrum bekijken of u de aanbevelingen in het lijstblad van de aanbevelingen sorteren met de impactkolom **Secure Score.**

Ga als resultaat naar uw algemene beveiligde score:

1. Klik in het Azure-dashboard op **Beveiligingscentrum** en klik vervolgens op **Beveiligde score**.

2. Bovenaan zie je hoogtepunten van de beveiligde score:
   - De **algemene beveiligde score** vertegenwoordigt de score per beleid, per geselecteerd abonnement
   - **Secure Score per categorie** laat zien welke resources de meeste aandacht nodig hebben
   - **De belangrijkste aanbevelingen van Secure Score impact** bieden u een lijst met aanbevelingen die uw Secure Score het meest verbeteren als u ze implementeert.
 
   ![Veilige score](./media/security-center-secure-score/secure-score-dashboard.png)

3. In de onderstaande tabel u elk van uw abonnementen en de algehele secure score voor elk.

   > [!NOTE]
   > De som van de Secure Score van elk abonnement is niet gelijk aan de totale Secure Score. De Secure Score is een berekening op basis van de verhouding tussen uw gezonde resources en uw totale resources per aanbeveling, niet een som van secure scores voor uw abonnementen. 
   >
4. Klik **op Aanbevelingen weergeven** om de aanbevelingen voor dat abonnement te bekijken die u herstellen om uw beveiligde score te verbeteren.
4. In de lijst met aanbevelingen u zien dat er voor elke aanbeveling een kolom is die de impact van de **beveiligde score**vertegenwoordigt. Dit getal geeft aan hoeveel uw algehele beveiligde score zal verbeteren als u de aanbevelingen opvolgt. Bijvoorbeeld, in het scherm hieronder, als u **kwetsbaarheden in container beveiligingsconfiguraties te verhelpen,** uw Secure Score zal toenemen met 35 punten.

   ![Veilige score](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Individuele beveiligde score

Om individuele beveiligde scores te bekijken, u deze bovendien vinden in het individuele aanbevelingsblad.  

De **AanbevelingS beveiligde score** is een berekening op basis van de verhouding tussen uw gezonde resources en uw totale resources. Als het aantal gezonde resources gelijk is aan het totale aantal resources, krijgt u de maximale beveiligde score van de aanbeveling van 50. Als u wilt proberen uw beveiligde score dichter bij de maximale score te krijgen, lost u de ongezonde resources op door de aanbevelingen op te volgen.

Met **de invloed op de aanbeveling** u weten hoeveel uw beveiligde score verbetert als u de aanbevelingsstappen toepast. Als uw beveiligde score bijvoorbeeld 42 is en de **impact op aanbeveling** +3 is, verbetert het uitvoeren van de stappen die in de aanbeveling worden beschreven uw score tot 45.

De aanbeveling laat zien aan welke bedreigingen uw werkbelasting wordt blootgesteld als de herstelstappen niet worden uitgevoerd.

![individuele aanbeveling veilige score](./media/security-center-secure-score/indiv-recommendation-secure-score.png)


## <a name="next-steps"></a>Volgende stappen
In dit artikel ziet u hoe u uw beveiligingshouding verbeteren met **Secure Score** in Azure Security Center. Zie de volgende onderwerpen voor meer informatie over Azure Security Center:

* [Azure Security Center FAQ](security-center-faq.md): raadpleeg veelgestelde vragen over het gebruik van de service.
* [Beveiligingsstatus bewaken in Azure Security Center](security-center-monitoring.md): meer informatie over het bewaken van de status van uw Azure-resources.
