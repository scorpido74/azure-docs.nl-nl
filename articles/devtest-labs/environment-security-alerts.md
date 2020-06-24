---
title: Beveiligings waarschuwingen voor omgevingen in Azure DevTest Labs
description: In dit artikel leest u hoe u beveiligings waarschuwingen kunt weer geven voor een omgeving in DevTest Labs en een passende maat regelen kunt nemen.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2020
ms.author: spelluru
ms.openlocfilehash: fbac5a2fab91cdac8ebf626e324f12f209cfade5
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84898400"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Beveiligings waarschuwingen voor omgevingen in Azure DevTest Labs
Als test gebruiker kunt u nu Azure Security Center waarschuwingen weer geven voor uw test omgevingen. Security Center verzamelt, analyseert en integreert automatisch logboekgegevens van uw Azure-resources, het netwerk en verbonden partneroplossingen, zoals firewall- en eindpuntbeveiligingsoplossingen om werkelijke dreigingen te detecteren en fout-positieven te reduceren. In Security Center wordt een lijst met beveiligingswaarschuwingen met prioriteiten weergegeven samen met de informatie die u nodig hebt om snel onderzoek te doen naar het probleem en aanbevelingen voor het herstellen van een aanval. Meer [informatie over beveiligings waarschuwingen in azure Security Center](../security-center//security-center-alerts-overview.md).  


## <a name="prerequisites"></a>Vereisten
Op dit moment kunt u alleen beveiligings waarschuwingen weer geven voor PaaS-omgevingen (platform as a Service) die zijn geïmplementeerd in uw Lab. Als u deze functie wilt testen of gebruiken, [implementeert u een omgeving in uw Lab](devtest-lab-create-environment-from-arm.md). 

## <a name="view-security-alerts-for-an-environment"></a>Beveiligings waarschuwingen voor een omgeving weer geven

1. Op de start pagina van uw Lab selecteert u **beveiligings waarschuwingen** in het menu links. U ziet het aantal beveiligings waarschuwingen (hoog, gemiddeld en laag). Meer informatie over [hoe waarschuwingen worden geclassificeerd](../security-center/security-center-alerts-overview.md#how-are-alerts-classified).

    ![Beveiligings waarschuwingen-overzicht](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Klik met de rechter muisknop op drie punten (...) in de laatste kolom en selecteer **beveiligings waarschuwingen weer geven**. 

    ![Beveiligingswaarschuwingen bekijken](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. U vindt meer informatie over de aanbevelingen voor waarschuwingen en Advisor. Meer informatie over [het beheren van en reageren op beveiligings waarschuwingen in azure Security Center](../security-center/security-center-managing-and-responding-alerts.md).

    ![Beveiligingswaarschuwingen bekijken](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over omgevingen:

- [Multi-VM-omgevingen en PaaS-resources maken met Azure Resource Manager sjablonen](devtest-lab-create-environment-from-arm.md)
- [Openbare omgevingen configureren en gebruiken](devtest-lab-configure-use-public-environments.md)
