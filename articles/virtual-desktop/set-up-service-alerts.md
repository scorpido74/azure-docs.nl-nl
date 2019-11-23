---
title: Service waarschuwingen instellen voor het virtuele bureau blad van Windows-Azure
description: Azure Service Health instellen om service meldingen te ontvangen voor Windows virtueel bureau blad.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
ms.openlocfilehash: b5fa5fc13c8ce0c98c04d2451f3a50e89b986c16
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676449"
---
# <a name="tutorial-set-up-service-alerts"></a>Zelf studie: Service waarschuwingen instellen

U kunt Azure Service Health gebruiken om service problemen en status adviezen te bewaken voor virtuele Windows-Bureau bladen. Azure Service Health kunt u op de hoogte stellen van verschillende soorten waarschuwingen (bijvoorbeeld e-mail of SMS), inzicht krijgen in het effect van een probleem en u blijft bijwerken terwijl het probleem wordt opgelost. Azure Service Health kunt u ook helpen bij het oplossen van uitval tijd en het plannen van gepland onderhoud en wijzigingen die van invloed kunnen zijn op de beschik baarheid van uw resources.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Service waarschuwingen maken en configureren.

Zie de [Azure Health-documentatie](https://docs.microsoft.com/azure/service-health/)voor meer informatie over Azure service health.

## <a name="prerequisites"></a>Vereisten

- [Zelf studie: een Tenant maken in Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory)
- [Zelf studie: Service-principals en roltoewijzingen maken met Power shell](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell)
- [Zelf studie: een hostgroep maken met Azure Marketplace](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace)

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

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
2. Selecteer **service health.**
3. Volg de instructies in [waarschuwingen voor het maken van activiteiten logboeken voor service meldingen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) om uw waarschuwingen en meldingen in te stellen.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u Azure Service Health kunt instellen en gebruiken om service problemen en status adviezen voor virtuele Windows-Bureau bladen te bewaken. Voor meer informatie over hoe u zich aanmeldt bij Windows Virtual Desktop, gaat u naar de How-procedures voor verbinding maken met Windows Virtual Desktop.

> [!div class="nextstepaction"]
> [Verbinding maken met de Extern bureaublad-client op Windows 7 en Windows 10](./connect-windows-7-and-10.md)
