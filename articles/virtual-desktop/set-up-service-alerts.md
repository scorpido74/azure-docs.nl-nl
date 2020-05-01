---
title: Service waarschuwingen instellen voor het virtuele bureau blad van Windows-Azure
description: Azure Service Health instellen om service meldingen te ontvangen voor Windows virtueel bureau blad.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: ad25ab219cdb83227d39f86109d18b2c8402c38f
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612347"
---
# <a name="tutorial-set-up-service-alerts"></a>Zelf studie: Service waarschuwingen instellen

>[!IMPORTANT]
>Deze inhoud is van toepassing op de lente 2020-update met Azure Resource Manager virtueel-bureaublad objecten van Windows. Raadpleeg [dit artikel](./virtual-desktop-fall-2019/set-up-service-alerts-2019.md)als u de versie van het Windows-bureau blad van Virtual Desktop 2019 zonder Azure Resource Manager objecten gebruikt.
>
> De Windows Virtual Desktop lente 2020-update is momenteel beschikbaar als open bare preview. Deze preview-versie is beschikbaar zonder service level agreement. het wordt niet aangeraden deze te gebruiken voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. 
> Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

U kunt Azure Service Health gebruiken om service problemen en status adviezen te bewaken voor virtuele Windows-Bureau bladen. Azure Service Health kunt u op de hoogte stellen van verschillende soorten waarschuwingen (bijvoorbeeld e-mail of SMS), inzicht krijgen in het effect van een probleem en u blijft bijwerken terwijl het probleem wordt opgelost. Azure Service Health kunt u ook helpen uitval tijd te verhelpen en voor bereidingen te treffen voor gepland onderhoud en wijzigingen die van invloed kunnen zijn op de beschik baarheid van uw resources.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Service waarschuwingen maken en configureren.

Zie de [Azure Health-documentatie](https://docs.microsoft.com/azure/service-health/)voor meer informatie over Azure service health.

## <a name="create-service-alerts"></a>Service waarschuwingen maken

In deze sectie wordt beschreven hoe u Azure Service Health configureert en hoe u meldingen instelt, die u op de Azure Portal kunt openen. U kunt verschillende typen waarschuwingen instellen en deze plannen om u tijdig op de hoogte te stellen.

### <a name="recommended-service-alerts"></a>Aanbevolen Service waarschuwingen

U wordt aangeraden Service waarschuwingen te maken voor de volgende status gebeurtenis typen:

- **Service probleem:** Ontvang meldingen over belang rijke problemen die van invloed zijn op de connectiviteit van uw gebruikers met de service of met de mogelijkheid om uw Windows Virtual Desktop-Tenant te beheren.
- **Status advies:** Ontvang meldingen die uw aandacht vereisen. Hier volgen enkele voor beelden van dit type melding:
    - Virtual Machines (Vm's) niet veilig geconfigureerd als open poort 3389
    - Afschaffing van functionaliteit

### <a name="configure-service-alerts"></a>Service waarschuwingen configureren

Service waarschuwingen configureren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **service health.**
3. Volg de instructies in [waarschuwingen voor het maken van activiteiten logboeken voor service meldingen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) om uw waarschuwingen en meldingen in te stellen.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u Azure Service Health kunt instellen en gebruiken om service problemen en status adviezen voor virtuele Windows-Bureau bladen te bewaken. Voor meer informatie over hoe u zich aanmeldt bij Windows Virtual Desktop, gaat u naar de How-procedures voor verbinding maken met Windows Virtual Desktop.

> [!div class="nextstepaction"]
> [Verbinding maken met de Extern bureaublad-client op Windows 7 en Windows 10](./connect-windows-7-and-10.md)
