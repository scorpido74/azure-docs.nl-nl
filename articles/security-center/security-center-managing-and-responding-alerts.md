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
ms.openlocfilehash: 8e44ce594375deeac47f037515d96c57d15c8359
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80398407"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center

In dit onderwerp wordt beschreven hoe u de waarschuwingen die u hebt ontvangen, kunt weer geven en verwerken om uw resources te beveiligen. 

* Zie [beveiligings waarschuwings typen](alerts-reference.md)voor meer informatie over de verschillende typen waarschuwingen.
* Zie [how Azure Security Center detecteert en reageert op bedreigingen](security-center-alerts-overview.md)voor een overzicht van de manier waarop Security Center waarschuwingen genereert.

> [!NOTE]
> Als u geavanceerde detectie wilt inschakelen, voert u een upgrade uit naar Azure Security Center Standard. Er is een gratis proefversie beschikbaar. Als u een upgrade wilt uitvoeren, selecteert u de prijscategorie in het [beveiligingsbeleid](tutorial-security-policy.md). Zie [Prijsinformatie over Azure Security Center](security-center-pricing.md) voor meer informatie.

## <a name="what-are-security-alerts"></a>Wat zijn beveiligingswaarschuwingen?
Security Center verzamelt, analyseert en integreert automatisch logboekgegevens van uw Azure-resources, het netwerk en verbonden partneroplossingen, zoals firewall- en eindpuntbeveiligingsoplossingen om werkelijke dreigingen te detecteren en fout-positieven te reduceren. In Security Center wordt een lijst met beveiligingswaarschuwingen met prioriteiten weergegeven samen met de informatie die u nodig hebt om snel onderzoek te doen naar het probleem en aanbevelingen voor het herstellen van een aanval.

> [!NOTE]
> Zie [how Azure Security Center detecteert en reageert op bedreigingen](security-center-alerts-overview.md#detect-threats)voor meer informatie over de werking van Security Center detectie mogelijkheden.

## <a name="manage-your-security-alerts"></a>Beveiligings waarschuwingen beheren

1. Ga in het Security Center-dash board naar de tegel **bedreigings beveiliging** om de waarschuwingen weer te geven en te bekijken.

    ![De tegel Beveiligingswaarschuwingen in Security Center](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Klik op de tegel om meer informatie over de waarschuwingen weer te geven.

   ![Beveiligingswaarschuwingen in Security Center](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Als u de weer gegeven waarschuwingen wilt filteren, klikt u op **filter**en selecteert u op de Blade **filter** die wordt geopend de filter opties die u wilt Toep assen. De lijst wordt bijgewerkt op basis van het geselecteerde filter. Filteren kan zeer nuttig zijn. U kunt u bijvoorbeeld concentreren op de beveiligingswaarschuwingen van de afgelopen 24 uur, omdat u een mogelijke inbreuk in het systeem onderzoekt.

    ![Waarschuwingen filteren in Security Center](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Reageren op beveiligingswaarschuwingen

1. Klik in de lijst **beveiligings waarschuwingen** op een beveiligings waarschuwing. De betrokken resources en de stappen die u moet nemen om een aanval te herstellen, worden weer gegeven.

    ![Reageren op beveiligingswaarschuwingen](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. Nadat u de informatie hebt bekeken, klikt u op een bron die is aangevallen.

    ![Suggesties voor wat u moet doen over beveiligings waarschuwingen](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    De sectie **algemene informatie** kan inzicht bieden in wat de beveiligings waarschuwing heeft geactiveerd. Hierin worden gegevens weer gegeven, zoals de doel resource, het bron-IP-adres (indien van toepassing), als de waarschuwing nog steeds actief is en aanbevelingen voor het oplossen van problemen.  

    > [!NOTE]
    >In sommige gevallen is het bron-IP-adres niet beschikbaar. sommige logboeken voor Windows-beveiligings gebeurtenissen bevatten niet het IP-adres.

1. De herstelstappen die door Security Center worden voorgesteld, verschillen per beveiligingswaarschuwing. Volg deze voor elke waarschuwing. 

    In sommige gevallen moet u, om een beveiligings waarschuwing te verminderen, mogelijk andere Azure-besturings elementen of-services gebruiken om het aanbevolen herstel te implementeren. 

## <a name="see-also"></a>Zie ook

In dit document hebt u kunnen lezen hoe u het beveiligingsbeleid in Security Center configureert. Zie de volgende onderwerpen voor meer informatie over Security Center:

- [De Microsoft Learn-module voor het gebruik van de waarschuwings mogelijkheden van Azure Security Center om bedreigingen te bekijken en te reageren](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
* [Beveiligings waarschuwingen in azure Security Center](security-center-alerts-overview.md).
* [Beveiligings incidenten afhandelen](security-center-incident.md)