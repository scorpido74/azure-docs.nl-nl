---
title: Beveiligde Score in Azure Security Center | Microsoft Docs
description: " Volg prioriteiten voor uw beveiligings aanbevelingen met behulp van de beveiligde Score in Azure Security Center. "
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
ms.openlocfilehash: 15a94a9724fac29d36f6bb88ee4810b3bc7ca607
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603478"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Verbeter uw beveiligde Score in Azure Security Center

> [!NOTE]
> Er is een verbeterde beveiligde Score beschikbaar in de preview-versie. Door de verbeterde beveiligde score wordt de bestaande beveiligde Score uiteindelijk vervangen, maar voor een tijdje worden ze naast elkaar uitgevoerd om de overgang te vereenvoudigen.
>
> Zie [hier](secure-score-security-controls.md)voor meer informatie over de voor delen van de verbeterde beveiligde Score.
>
> Open Azure Portal, start Azure Security Center en selecteer beveiligde Score om deel te nemen aan de preview-versie. Hier ziet u een banner aan de bovenkant van de pagina met de nieuwe beveiligde Score ervaring. U kunt ook [hier](https://aka.ms/ascnewscore)klikken.

Met zoveel services die beveiligings voordelen bieden, is het vaak moeilijk om te weten welke stappen het eerst moet worden uitgevoerd om uw werk belasting te beveiligen en te beschermen. Met de beveiligde Score worden uw beveiligings aanbevelingen beoordeeld en worden prioriteiten voor u gegeven, zodat u weet welke aanbevelingen het eerst moeten worden uitgevoerd. Zo kunt u de meest ernstige beveiligings problemen vinden zodat u de prioriteit van het onderzoek kan bepalen. Secure Score is een hulp programma waarmee u de beveiligings postuur van uw werk belasting kunt beoordelen.

## <a name="secure-score-calculation"></a>Berekening van beveiligde Score

Security Center imiteert het werk van een beveiligings analist, bekijkt uw beveiligings aanbevelingen en past geavanceerde algoritmen toe om te bepalen hoe belang rijk elke aanbeveling is.
Azure Security Center bekijkt voortdurend uw actieve aanbevelingen en berekent uw beveiligde Score op basis hiervan, de Score van een aanbeveling is afgeleid van de ernst en de best practices die van invloed zijn op de beveiliging van uw werk belasting.

Security Center biedt u ook een **algemene beveiligde Score**. 

De **algemene beveiligde Score** is een accumulatie van al uw aanbevelings scores. U kunt uw algemene beveiligde score voor uw abonnementen of beheer groepen bekijken, afhankelijk van wat u selecteert. De score is afhankelijk van het geselecteerde abonnement en de actieve aanbevelingen voor deze abonnementen.

Als u wilt controleren welke aanbevelingen van invloed zijn op uw beveiligde Score, kunt u de bovenste drie meest voorkomende aanbevelingen in het dash board van Security Center bekijken of u kunt de aanbevelingen in de Blade met de lijst met aanbevelingen sorteren met behulp van de kolom **beveiligde Score effect** .

Uw algemene beveiligde Score weer geven:

1. Klik in het Azure-dash board op **Security Center** en klik vervolgens op **beveiligde Score**.

2. Bovenaan ziet u de belangrijkste aandachtspunten voor de Score:
   - De **totale beveiligde Score** vertegenwoordigt de Score per beleid, per geselecteerd abonnement
   - **Beveiligde Score per categorie** toont u welke resources de meeste aandacht nodig hebben
   - De **belangrijkste aanbevelingen van een beveiligde score hebben** een lijst met de aanbevelingen die uw beveiligde Score optimaal verbeteren als u ze implementeert.
 
   ![Beveiligde Score](./media/security-center-secure-score/secure-score-dashboard.png)

3. In de onderstaande tabel ziet u elk van uw abonnementen en de algehele beveiligde score voor elk abonnement.

   > [!NOTE]
   > De som van de beveiligde Score van elk abonnement is niet gelijk aan de algehele beveiligde Score. De beveiligde Score is een berekening op basis van de verhouding tussen uw gezonde resources en het totale aantal resources per aanbeveling, en niet de som van de beveiligde scores in uw abonnementen. 
   >
4. Klik op **aanbevelingen weer geven** om de aanbevelingen voor dat abonnement te bekijken die u kunt herstellen om uw beveiligde score te verbeteren.
4. In de lijst met aanbevelingen ziet u dat er voor elke aanbeveling een kolom is die de impact van de **beveiligde Score**weergeeft. Dit aantal geeft aan hoeveel uw algemene beveiligde score zal verbeteren als u de aanbevelingen volgt. Als u bijvoorbeeld in het onderstaande scherm **beveiligings problemen in container beveiligings configuraties**herstelt, wordt uw beveiligde Score met 35 punten verhoogd.

   ![Beveiligde Score](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Afzonderlijke beveiligde Score

Als u afzonderlijke beveiligde scores wilt bekijken, kunt u deze ook vinden op de Blade met de aanbevelingen.  

De **aanbevolen beveiligings Score** is een berekening op basis van de verhouding tussen uw in orde zijnde resources en uw totale resources. Als het aantal in orde zijnde resources gelijk is aan het totale aantal resources, krijgt u de maximale beveiligde Score van de aanbeveling van 50. Als u wilt proberen om uw beveiligde Score dichter bij de maximale score te halen, kunt u de beschadigde bronnen corrigeren door de aanbevelingen te volgen.

Met de impact op de **aanbeveling** kunt u zien hoeveel uw beveiligde Score verbetert als u de aanbevolen stappen toepast. Als uw beveiligde Score bijvoorbeeld 42 is en de impact van de **aanbeveling** + 3 is, voert u de stappen uit die worden beschreven in de aanbeveling uw score te verbeteren tot 45.

De aanbeveling laat zien welke bedreigingen uw werk belasting beschikbaar maakt als de herstel stappen niet worden uitgevoerd.

![afzonderlijke aanbevelings veilige Score](./media/security-center-secure-score/indiv-recommendation-secure-score.png)




## <a name="next-steps"></a>Volgende stappen
In dit artikel wordt uitgelegd hoe u uw beveiligings postuur kunt verbeteren met behulp van **beveiligde scores** in azure Security Center. Zie de volgende onderwerpen voor meer informatie over Azure Security Center:

* [Veelgestelde](faq-general.md)vragen over de Azure Security Center: vind een veelgestelde vraag over de service en de beveiligde Score.
* [Security health monitoring in Azure Security Center](security-center-monitoring.md): ontdek hoe u de status van uw Azure-resources kunt monitoren.
* [Beveiligde Score-verbeterd](secure-score-security-controls.md)--meer informatie over de voor delen van de verbeterde beveiligde Score die momenteel als preview-versie beschikbaar is.