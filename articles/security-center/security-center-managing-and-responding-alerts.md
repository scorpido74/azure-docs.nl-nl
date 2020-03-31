---
title: Beveiligingswaarschuwingen beheren in Azure Security Center | Microsoft Docs
description: Dit document bevat informatie over het gebruik van de mogelijkheden van Azure Security Center om beveiligingswaarschuwingen te beheren en hierop te reageren.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 6ea951b542d893b8fef3cdf19a964ce2ef8a034d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415713"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center

In dit onderwerp ziet u hoe u de waarschuwingen weergeven en verwerken die u hebt ontvangen om uw resources te beschermen. 

* Zie [Beveiligingswaarschuwingstypen](alerts-reference.md)voor meer informatie over de verschillende typen waarschuwingen.
* Zie [Hoe Azure Security Center](security-center-alerts-overview.md)bedreigingen detecteert en reageert voor een overzicht van hoe Beveiligingscentrum waarschuwingen genereert.

> [!NOTE]
> Als u geavanceerde detectie wilt inschakelen, voert u een upgrade uit naar Azure Security Center Standard. Er is een gratis proefversie beschikbaar. Als u een upgrade wilt uitvoeren, selecteert u de prijscategorie in het [beveiligingsbeleid](tutorial-security-policy.md). Zie [Prijsinformatie over Azure Security Center](security-center-pricing.md) voor meer informatie.

## <a name="what-are-security-alerts"></a>Wat zijn beveiligingswaarschuwingen?
Security Center verzamelt, analyseert en integreert automatisch logboekgegevens van uw Azure-resources, het netwerk en verbonden partneroplossingen, zoals firewall- en eindpuntbeveiligingsoplossingen om werkelijke dreigingen te detecteren en fout-positieven te reduceren. In Security Center wordt een lijst met beveiligingswaarschuwingen met prioriteiten weergegeven samen met de informatie die u nodig hebt om snel onderzoek te doen naar het probleem en aanbevelingen voor het herstellen van een aanval.

> [!NOTE]
> Zie [Hoe Azure Security Center bedreigingen detecteert en reageert](security-center-alerts-overview.md#detect-threats)voor meer informatie over hoe detectiemogelijkheden voor beveiligingspersoneel werken.

## <a name="manage-your-security-alerts"></a>Uw beveiligingswaarschuwingen beheren

1. Zie in het dashboard Beveiligingscentrum voor bedreigingen de tegel **Bedreigingsbeveiliging** om de waarschuwingen weer te geven en te bekijken.

    ![De tegel Beveiligingswaarschuwingen in Security Center](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Als u meer informatie over de waarschuwingen wilt zien, klikt u op de tegel.

   ![Beveiligingswaarschuwingen in Security Center](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Als u de weergegeven waarschuwingen wilt filteren, klikt u op **Filter**en selecteert u in het **filterblad** dat wordt geopend de filteropties die u wilt toepassen. De lijst wordt bijgewerkt volgens het geselecteerde filter. Filteren kan zeer nuttig zijn. U kunt u bijvoorbeeld concentreren op de beveiligingswaarschuwingen van de afgelopen 24 uur, omdat u een mogelijke inbreuk in het systeem onderzoekt.

    ![Waarschuwingen filteren in Security Center](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Reageren op beveiligingswaarschuwingen

1. Klik in de lijst **Beveiligingswaarschuwingen** op een beveiligingswaarschuwing. De middelen die betrokken zijn en de stappen die u moet nemen om een aanval te verwerkeren wordt weergegeven.

    ![Reageren op beveiligingswaarschuwingen](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. Nadat u de informatie hebt bekeken, klikt u op een bron die is aangevallen.

    ![Suggesties voor wat te doen met beveiligingswaarschuwingen](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    De sectie **Algemene informatie** kan inzicht geven in wat de beveiligingswaarschuwing heeft geactiveerd. Het toont informatie zoals de doelbron, bron-IP-adres (indien van toepassing), als de waarschuwing nog actief is en aanbevelingen over hoe te herstellen.  

    > [!NOTE]
    >In sommige gevallen is het ip-adres van de bron niet beschikbaar, sommige logboeken van Windows-beveiligingsgebeurtenissen bevatten geen IP-adres.

1. De herstelstappen die door Security Center worden voorgesteld, verschillen per beveiligingswaarschuwing. Volg ze voor elke waarschuwing. 

    In sommige gevallen moet u mogelijk andere Azure-besturingselementen of services gebruiken om de aanbevolen herstelprocedure te implementeren om een beveiligingswaarschuwing te beperken. 

## <a name="see-also"></a>Zie ook

In dit document hebt u kunnen lezen hoe u het beveiligingsbeleid in Security Center configureert. Zie de volgende onderwerpen voor meer informatie over Security Center:

* [Beveiligingswaarschuwingen in Azure Security Center](security-center-alerts-overview.md).
* [Omgaan met beveiligingsincidenten](security-center-incident.md)