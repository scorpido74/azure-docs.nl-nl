---
title: Beveiligingswaarschuwingen voor omgevingen in Azure DevTest Labs
description: In dit artikel ziet u hoe u beveiligingswaarschuwingen voor een omgeving in DevTest Labs bekijken en een passende actie ondernemen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588702"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Beveiligingswaarschuwingen voor omgevingen in Azure DevTest Labs
Als labgebruiker u nu azure security center-waarschuwingen voor uw labomgevingen bekijken. Security Center verzamelt, analyseert en integreert automatisch logboekgegevens van uw Azure-resources, het netwerk en verbonden partneroplossingen, zoals firewall- en eindpuntbeveiligingsoplossingen om werkelijke dreigingen te detecteren en fout-positieven te reduceren. In Security Center wordt een lijst met beveiligingswaarschuwingen met prioriteiten weergegeven samen met de informatie die u nodig hebt om snel onderzoek te doen naar het probleem en aanbevelingen voor het herstellen van een aanval. [Meer informatie over beveiligingswaarschuwingen in Azure Security Center](../security-center//security-center-alerts-overview.md).  


## <a name="prerequisites"></a>Vereisten
Momenteel u beveiligingswaarschuwingen alleen bekijken voor PaaS-omgevingen (Platform As A Service) die in uw lab worden geïmplementeerd. Als u deze functie wilt testen of gebruiken, [implementeert u een omgeving in uw lab.](devtest-lab-create-environment-from-arm.md) 

## <a name="view-security-alerts-for-an-environment"></a>Beveiligingswaarschuwingen voor een omgeving weergeven

1. Selecteer op de startpagina van uw lab **beveiligingswaarschuwingen** in het linkermenu. U ziet het aantal beveiligingswaarschuwingen (hoog, gemiddeld en laag). Meer informatie over [hoe waarschuwingen worden geclassificeerd](../security-center/security-center-alerts-overview.md#how-are-alerts-classified).

    ![Beveiligingswaarschuwingen - overzicht](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Klik met de rechtermuisknop op drie puntjes (...) in de laatste kolom en selecteer **Beveiligingswaarschuwingen weergeven**. 

    ![Beveiligingswaarschuwingen bekijken](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. U ziet meer informatie over de waarschuwingen en aanbevelingen van adviseurs. Meer informatie over [het beheren en reageren op beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md).

    ![Beveiligingswaarschuwingen bekijken](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over omgevingen:

- [Multivm-omgevingen en PaaS-resources maken met Azure Resource Manager-sjablonen](devtest-lab-create-environment-from-arm.md)
- [Openbare omgevingen configureren en gebruiken](devtest-lab-configure-use-public-environments.md)
