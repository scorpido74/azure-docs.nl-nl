---
title: Verbeter de naleving van uw regelgeving met behulp van Azure Security Center | Microsoft Docs
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
ms.date: 4/30/2019
ms.author: memildin
ms.openlocfilehash: 20842997c5df81835024a6f458cd863b4e4d78b0
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202425"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Zelfstudie: Uw regelnaleving verbeteren
---

Met Azure Security Center kunt u ervoor zorgen dat het proces voldoet aan de wettelijke vereisten met behulp van het dashboard Naleving van regelgeving. In het dashboard biedt Azure Security Center inzicht in uw nalevingspostuur op basis van continue evaluatie van uw Azure-omgeving. Met de door Azure Security Center uitgevoerde evaluaties worden risicofactoren geanalyseerd in uw hybride cloudomgeving, in overeenstemming met de best practices van uw beveiliging. Deze evaluaties worden vanuit een ondersteunde set standaarden toegewezen aan nalevingscontroles. In het dashboard Naleving van regelgeving hebt u in de context van een bepaalde regelgevingsstandaard een duidelijk overzicht van de status van deze evaluaties binnen uw omgeving. Terwijl u reageert op de aanbevelingen en u de risicofactoren in uw omgeving verkleint, ziet u dat uw nalevingspostuur verbetert.

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
> Momenteel ondersteunde regelgevingsnormen: Azure CIS, PCI DSS 3.2, ISO 27001 en SOC TSP. Aanvullende standaarden worden tijdens de ontwikkeling in het dashboard van kracht.
1.  Selecteer in het hoofd menu van Security Center onder **beleids &AMP; naleving** de optie naleving van **regelgeving**. <br>
Boven aan het scherm ziet u een dashboard met een overzicht van de nalevingsstatus in combinatie met de set ondersteunde richtlijnen voor naleving. U ziet de totale nalevingsscore en het aantal positieve en negatieve beoordelingen die op elke standaard van toepassing zijn.

    ![computerbeschrijving met hoge betrouwbaarheid](./media/security-center-compliance-dashboard/compliance-dashboard.png)


2.  Selecteer een tabblad voor een nalevingsstandaard die voor u relevant is. U krijgt een lijst met alle besturingselementen voor die standaard te zien. Voor de van toepassing zijnde besturingselementen kunt u de details bekijken van positieve en negatieve beoordelingen met betrekking tot dat besturingselement. Sommige besturingselementen zijn grijs weergegeven. Op deze besturingselementen zijn geen beoordelingen van het Azure Security Center van toepassing. U dient de vereisten hiervoor te analyseren en ze zelf in uw omgeving te beoordelen. Sommige hiervan kunnen procesgerelateerd zijn in plaats van technisch.

    ![Tabblad Naleving](./media/security-center-compliance-dashboard/compliance-pci.png)

3. Selecteer het tabblad **Alle** voor een weergave van alle relevante aanbevelingen van het Azure Security Center en de bijbehorende standaarden. Deze weergave kan handig zijn voor het identificeren van alle verschillende standaarden waarop een bepaalde aanbeveling van invloed is. <br> U kunt deze weergave mogelijk gebruiken om prioriteit te geven aan aanbevelingen die u moet oplossen. Als u bijvoorbeeld ziet dat de aanbeveling **MFA inschakelen voor accounts met eigenaarsmachtigingen voor uw abonnement** voor meerdere resources mislukt en aan meerdere standaarden is gekoppeld, dan heeft het oplossen van deze aanbeveling een grotere impact op de totale score van uw naleving.

    ![Impact nalevingsscore](./media/security-center-compliance-dashboard/compliance-all-tabs.png)

1. Als u een PDF-rapport wilt genereren en downloaden met een overzicht van uw huidige nalevings status voor een bepaalde standaard, klikt u op **rapport downloaden**.

    Het rapport bevat een samen vatting van de nalevings status voor de geselecteerde standaard op basis van Security Center evaluatie gegevens en is ingedeeld volgens de besturings elementen van die specifieke standaard. Het rapport kan worden gedeeld met relevante belanghebbenden en kan dienen om bewijs te leveren aan interne en externe accountants.

    ![download](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>De nalevingspostuur verbeteren

Met de informatie in het dashboard Naleving van regelgeving kunt u uw nalevingspostuur verbeteren door aanbevelingen rechtstreeks vanuit het dashboard op te lossen.

1.  Klik op de negatieve beoordelingen die in het dashboard worden weergegeven om de details ervan te bekijken. Elke aanbeveling bevat een serie herstelstappen die dienen te worden gevolgd om het probleem op te lossen.

2.  U kunt een bepaalde resource selecteren om meer details te zien en de aanbeveling voor die resource op te lossen. <br>Op bijvoorbeeld het tabblad **Azure CIS standard** kunt u op de aanbeveling **Require secure transfer to storage account** klikken.

    ![Aanbeveling voor naleving](./media/security-center-compliance-dashboard/compliance-recommendation.png)

3. Als u op de informatie in de aanbevelingen klikt en een resource selecteert die niet in orde is, wordt u in de Azure-portal rechtstreeks doorgestuurd naar een plaats waar u **overdracht van beveiligde opslag** kunt inschakelen.<br>Zie [Beveiligingsaanbevelingen implementeren in Azure Security Center](security-center-recommendations.md) voor meer informatie over het toepassen van aanbevelingen.

    ![Aanbeveling voor naleving](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

4.  Nadat u actie hebt ondernomen om aanbevelingen op te lossen, ziet u de impact ervan in het dashboardrapport over de naleving omdat de nalevingsscore is verbeterd.

    > [!NOTE]
    > Beoordelingen worden ongeveer elke 12 uur uitgevoerd, dus u ziet de impact ervan op uw nalevingsgegevens pas nadat de beoordelingen worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u het dashboard Naleving van regelgeving van het Azure Security Center kunt gebruiken om:

-   Uw nalevingspostuur te bekijken en bewaken met betrekking tot de standaarden en richtlijnen die voor u van belang zijn.

-   De nalevingsstatus te verbeteren door relevante beoordelingen op te lossen en te zien hoe de nalevingsscore verbetert.

Met het dashboard Naleving van regelgeving kunt u het nalevingsproces fors vereenvoudigen en de benodigde tijd voor het verzamelen van bewijzen van naleving voor uw Azure- en hybride omgeving aanzienlijk verkorten.

Zie de volgende onderwerpen voor meer informatie over Azure Security Center:

-   [Beveiligingsstatus bewaken in Azure Security Center](security-center-monitoring.md): meer informatie over het bewaken van de status van uw Azure-resources.

-   [Aanbevelingen voor beveiliging in Azure Security Center beheren](security-center-recommendations.md): leer hoe u aanbevelingen in Azure Security Center kunt gebruiken om uw Azure-resources te beveiligen.

-   [Uw beveiligingsscore in Azure Security Center verbeteren](security-center-secure-score.md): leer hoe u prioriteit kunt geven aan kwetsbaarheden en aanbevelingen over beveiliging om uw beveiligingspostuur zo goed mogelijk te verbeteren.

-   [Azure Security Center FAQ](security-center-faq.md): raadpleeg veelgestelde vragen over het gebruik van de service.
