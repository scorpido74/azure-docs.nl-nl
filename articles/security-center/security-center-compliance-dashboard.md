---
title: 'Zelfstudie: Nalevingscontroles van de regelgeving - Azure Security Center'
description: 'Zelfstudie: Meer informatie over het verbeteren van uw naleving van de regelgeving met Azure Security Center.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2019
ms.author: memildin
ms.openlocfilehash: 1a6999c05c0b3dbaf572b376412f666c50c23df7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77604455"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Zelfstudie: Uw naleving van de regelgeving verbeteren
---

Azure Security Center helpt bij het stroomlijnen van het proces om te voldoen aan wettelijke vereisten voor naleving, met behulp van het dashboard voor naleving van de **regelgeving.** In het dashboard biedt Azure Security Center inzicht in uw nalevingspostuur op basis van continue evaluatie van uw Azure-omgeving. Security Center analyseert risicofactoren in uw hybride cloudomgeving op basis van best practices voor beveiliging. Deze evaluaties worden vanuit een ondersteunde set standaarden toegewezen aan nalevingscontroles. In het dashboard naleving van regelgeving u de status van alle beoordelingen binnen uw omgeving zien in de context van een bepaalde standaard of regelgeving. Als u handelt naar de aanbevelingen en risicofactoren in uw omgeving vermindert, verbetert uw nalevingshouding.

In deze zelfstudie leert u het volgende:

-   De naleving van uw regelgeving evalueren met het dashboard Naleving van regelgeving

-   Uw nalevingspostuur verbeteren door op aanbevelingen te reageren

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om de functies in deze zelfstudie te doorlopen, moet u beschikken over de Standard-prijscategorie van Security Center. U kunt Security Center Standard kosteloos proberen.
Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie. In de snelstartgids [Onboard your Azure subscription to Security Center Standard](https://docs.microsoft.com/azure/security-center/security-center-get-started) (Uw Azure-abonnement registreren voor Security Center Standard) wordt u begeleid bij het upgraden naar Standard.

##  <a name="assess-your-regulatory-compliance"></a>Naleving van uw regelgeving beoordelen

In Azure Security Center wordt de configuratie van uw resources voortdurend beoordeeld om beveiligingsproblemen en kwetsbaarheden te identificeren. Deze beoordelingen worden gepresenteerd als aanbevelingen, die zich richten op het verbeteren van uw beveiligingscontrole. In het dashboard Naleving van regelgeving kunt u een set nalevingsstandaarden met alle bijbehorende vereisten bekijken, waar ondersteunde vereisten worden toegewezen aan van toepassing zijnde beveiligingsbeoordelingen. Hierdoor kunt u uw nalevingspostuur bekijken met betrekking tot de standaard, op basis van de status van deze beoordelingen.

Dankzij de weergave in het dashboard Naleving van regelgeving kunt u uw aandacht richten op de hiaten in de naleving aan de hand van een standaard of regelgeving die belangrijk voor u is. Met deze gerichte weergave kunt u ook voortdurend de score van uw naleving bewaken in dynamische cloud- en hybride omgevingen.

>[!NOTE]
> Standaard ondersteunt Security Center de volgende wettelijke standaarden: Azure CIS, PCI DSS 3.2, ISO 27001 en SOC TSP. 
>
> Met de functie [Dynamische compliancepakketten (preview)](update-regulatory-compliance-packages.md) u de standaarden in uw dashboard voor naleving van de regelgeving upgraden naar de nieuwe *dynamische* pakketten. U dezelfde voorbeeldfunctie ook gebruiken om nieuwe nalevingspakketten toe te voegen en te controleren of u voldoet aan aanvullende normen. 

1.  Selecteer in het hoofdmenu van het Beveiligingscentrum onder **BELEIDS& COMPLIANCE** de optie **Naleving van de regelgeving**. <br>
Boven aan het scherm ziet u een dashboard met een overzicht van de nalevingsstatus in combinatie met de set ondersteunde richtlijnen voor naleving. U ziet de totale nalevingsscore en het aantal positieve en negatieve beoordelingen die op elke standaard van toepassing zijn.

    ![computerbeschrijving met hoge betrouwbaarheid](./media/security-center-compliance-dashboard/compliance-dashboard.png)

2.  Selecteer een tabblad voor een nalevingsstandaard die voor u relevant is. U krijgt een lijst met alle besturingselementen voor die standaard te zien. Voor de van toepassing zijnde besturingselementen kunt u de details bekijken van positieve en negatieve beoordelingen met betrekking tot dat besturingselement. Sommige besturingselementen zijn grijs. Deze besturingselementen hebben geen Beoordelingen van het Beveiligingscentrum die eraan zijn gekoppeld. Controleer de eisen hiervoor en beoordeel ze in uw omgeving op uw eigen omgeving. Sommige hiervan kunnen procesgerelateerd zijn in plaats van technisch.

    ![Tabblad Naleving](./media/security-center-compliance-dashboard/compliance-pci.png)

1. Als u een PDF-rapport wilt genereren en downloaden waarin uw huidige nalevingsstatus voor een bepaalde standaard wordt samengevat, klikt u op **Rapport downloaden**.

    Het rapport bevat een overzicht op hoog niveau van uw nalevingsstatus voor de geselecteerde standaard op basis van gegevens over beoordelingen van het Beveiligingscentrum en is geordend op basis van de besturingselementen van die specifieke standaard. Het rapport kan worden gedeeld met relevante belanghebbenden en kan dienen om bewijsmateriaal te leveren aan interne en externe auditors.

    ![downloaden](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>De nalevingspostuur verbeteren

Met de informatie in het dashboard Naleving van regelgeving kunt u uw nalevingspostuur verbeteren door aanbevelingen rechtstreeks vanuit het dashboard op te lossen.

1.  Klik op de negatieve beoordelingen die in het dashboard worden weergegeven om de details ervan te bekijken. Elke aanbeveling bevat een serie herstelstappen die dienen te worden gevolgd om het probleem op te lossen.

1.  U kunt een bepaalde resource selecteren om meer details te zien en de aanbeveling voor die resource op te lossen. <br>Op bijvoorbeeld het tabblad **Azure CIS standard** kunt u op de aanbeveling **Require secure transfer to storage account** klikken.

    ![Aanbeveling voor naleving](./media/security-center-compliance-dashboard/compliance-recommendation.png)

1. Als u op de informatie in de aanbevelingen klikt en een resource selecteert die niet in orde is, wordt u in de Azure-portal rechtstreeks doorgestuurd naar een plaats waar u **overdracht van beveiligde opslag** kunt inschakelen.

    Zie [Beveiligingsaanbevelingen implementeren in Azure Security Center](security-center-recommendations.md) voor meer informatie over het toepassen van aanbevelingen.

    ![Aanbeveling voor naleving](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

1.  Nadat u actie hebt ondernomen om aanbevelingen op te lossen, ziet u de impact ervan in het dashboardrapport over de naleving omdat de nalevingsscore is verbeterd.

    > [!NOTE]
    > Beoordelingen worden ongeveer elke 12 uur uitgevoerd, dus u ziet de impact ervan op uw nalevingsgegevens pas nadat de beoordelingen worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u het dashboard Naleving van regelgeving van het Azure Security Center kunt gebruiken om:

-   Uw nalevingspostuur te bekijken en bewaken met betrekking tot de standaarden en richtlijnen die voor u van belang zijn.

-   De nalevingsstatus te verbeteren door relevante beoordelingen op te lossen en te zien hoe de nalevingsscore verbetert.

Met het dashboard Naleving van regelgeving kunt u het nalevingsproces fors vereenvoudigen en de benodigde tijd voor het verzamelen van bewijzen van naleving voor uw Azure- en hybride omgeving aanzienlijk verkorten.

Zie voor meer informatie:

-   [Update naar dynamische compliancepakketten in uw dashboard voor naleving van de regelgeving (Preview)](update-regulatory-compliance-packages.md) - Lees meer over deze voorbeeldfunctie waarmee u de standaarden in uw dashboard voor naleving van de regelgeving bijwerken naar de nieuwe *dynamische* pakketten. U ook dezelfde voorbeeldfunctie gebruiken om nieuwe nalevingspakketten toe te voegen en te controleren of u voldoet aan aanvullende normen. 

-   [Beveiligingsstatusbewaking in Azure Security Center](security-center-monitoring.md) - Meer informatie over het bewaken van de status van uw Azure-resources.

-   [Beveiligingsaanbevelingen beheren in Azure Security Center](security-center-recommendations.md) : lees hoe u aanbevelingen gebruiken in Azure Security Center om uw Azure-bronnen te beschermen.

-   [Verbeter uw beveiligde score in Azure Security Center](security-center-secure-score.md) - Lees hoe u prioriteit geven aan kwetsbaarheden en beveiligingsaanbevelingen om uw beveiligingshouding het meest te verbeteren.