---
title: 'Zelfstudie: Controles op naleving van regelgeving - Azure Security Center'
description: 'Zelfstudie: Meer informatie over het verbeteren van de naleving van uw regelgeving met behulp van Azure Security Center.'
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
ms.openlocfilehash: a636acab42d14925f507568e8a9fff4693c8f71c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86519640"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Zelfstudie: Uw regelnaleving verbeteren
---

Met Azure Security Center kunt u ervoor zorgen dat het proces voldoet aan de wettelijke vereisten met behulp van het dashboard **Naleving van regelgeving**. In het dashboard biedt Azure Security Center inzicht in uw nalevingspostuur op basis van continue evaluatie van uw Azure-omgeving. Security Center analyseert risicofactoren in uw hybride cloudomgeving overeenkomstig best practices voor beveiliging. Deze evaluaties worden vanuit een ondersteunde set standaarden toegewezen aan nalevingscontroles. In het dashboard Naleving van regelgeving ziet u in de context van een bepaalde regelgevingsstandaard een overzicht van de status van deze evaluaties binnen uw omgeving. Terwijl u reageert op de aanbevelingen en u de risicofactoren in uw omgeving verkleint, wordt uw nalevingspostuur verbeterd.

In deze zelfstudie leert u het volgende:

-   De naleving van uw regelgeving evalueren met het dashboard Naleving van regelgeving

-   Uw nalevingspostuur verbeteren door op aanbevelingen te reageren

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om de functies in deze zelfstudie te doorlopen, moet u beschikken over de Standard-prijscategorie van Security Center. U kunt Security Center Standard kosteloos proberen.
Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie. In de snelstartgids [Onboard your Azure subscription to Security Center Standard](https://docs.microsoft.com/azure/security-center/security-center-get-started) (Uw Azure-abonnement registreren voor Security Center Standard) wordt u begeleid bij het upgraden naar Standard.

##  <a name="assess-your-regulatory-compliance"></a>Naleving van uw regelgeving beoordelen

In Azure Security Center wordt de configuratie van uw resources voortdurend beoordeeld om beveiligingsproblemen en kwetsbaarheden te identificeren. Deze beoordelingen worden gepresenteerd als aanbevelingen, die zich richten op het verbeteren van uw beveiligingscontrole. In het dashboard Naleving van regelgeving kunt u een set nalevingsstandaarden met alle bijbehorende vereisten bekijken, waar ondersteunde vereisten worden toegewezen aan van toepassing zijnde beveiligingsbeoordelingen. Hierdoor kunt u uw nalevingspostuur bekijken met betrekking tot de standaard, op basis van de status van deze beoordelingen.

Dankzij de weergave in het dashboard Naleving van regelgeving kunt u uw aandacht richten op de hiaten in de naleving aan de hand van een standaard of regelgeving die belangrijk voor u is. Met deze gerichte weergave kunt u ook voortdurend de score van uw naleving bewaken in dynamische cloud- en hybride omgevingen.

>[!NOTE]
> Standaard ondersteunt Security Center de volgende regelgevingsstandaarden: Azure CIS, PCI DSS 3.2, ISO 27001 en SOC TSP. 
>
> Met de functie [dynamische nalevingspakketten (preview)](update-regulatory-compliance-packages.md) kunt u de standaarden in uw nalevingsdashboard upgraden naar de nieuwe *dynamische* pakketten. U kunt dezelfde preview-functie ook gebruiken om nieuwe nalevingspakketten toe te voegen en uw naleving met aanvullende standaarden te bewaken. 

1.  Ga naar het hoofdmenu van Azure Security Center en selecteer onder **BELEID EN NALEVING** de optie **Naleving van regelgeving**. <br>
Boven aan het scherm ziet u een dashboard met een overzicht van de nalevingsstatus in combinatie met de set ondersteunde richtlijnen voor naleving. U ziet de totale nalevingsscore en het aantal positieve en negatieve beoordelingen die op elke standaard van toepassing zijn.

    ![computerbeschrijving met hoge betrouwbaarheid](./media/security-center-compliance-dashboard/compliance-dashboard.png)

2.  Selecteer een tabblad voor een nalevingsstandaard die voor u relevant is. U krijgt een lijst met alle besturingselementen voor die standaard te zien. Voor de van toepassing zijnde besturingselementen kunt u de details bekijken van positieve en negatieve beoordelingen met betrekking tot dat besturingselement. Sommige besturingselementen zijn grijs weergegeven. Op deze besturingselementen zijn geen beoordelingen van het Azure Security Center van toepassing. Controleer de vereisten hiervoor en ze zelf in uw omgeving te beoordelen. Sommige hiervan kunnen procesgerelateerd zijn in plaats van technisch.

    ![Tabblad Naleving](./media/security-center-compliance-dashboard/compliance-pci.png)

1. Als u een samenvattend PDF-rapport van uw nalevingsstatus voor een bepaalde norm wilt genereren en downloaden, klikt u op **Rapport downloaden**.

    Het rapport biedt een samenvatting op hoog niveau van uw nalevingsstatus voor de geselecteerde standaard op basis van uw Security Center-beoordelingsgegevens en is geordend overeenkomstig de controles van die bepaalde standaard. Het rapport kan worden gedeeld met relevante belanghebbenden en dient mogelijk als bewijs voor interne en externe auditeurs.

    ![downloadenen](./media/security-center-compliance-dashboard/download-report.png)

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

Raadpleeg voor meer informatie:

-   [Bijwerken naar dynamische nalevingspakketten in uw nalevingsdashboard (preview)](update-regulatory-compliance-packages.md): meer informatie over deze preview-functie waarmee u de standaarden die worden weergegeven in het nalevingsdashboard kunt bijwerken naar de nieuwe *dynamische* pakketten. U kunt dezelfde preview-functie ook gebruiken om nieuwe nalevingspakketten toe te voegen en uw naleving met aanvullende standaarden te bewaken. 

-   [Beveiligingsstatus controleren in Azure Security Center](security-center-monitoring.md): meer informatie over het controleren van de status van uw Azure-resources.

-   [Aanbevelingen voor beveiliging in Azure Security Center beheren](security-center-recommendations.md): leer hoe u aanbevelingen in Azure Security Center kunt gebruiken om uw Azure-resources te beveiligen.

-   [Uw beveiligingsscore in Azure Security Center verbeteren](secure-score-security-controls.md): leer hoe u prioriteit kunt geven aan kwetsbaarheden en aanbevelingen over beveiliging om uw beveiligingspostuur zo goed mogelijk te verbeteren.