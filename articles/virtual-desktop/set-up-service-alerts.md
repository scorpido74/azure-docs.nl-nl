---
title: Servicewaarschuwingen instellen voor Windows Virtual Desktop - Azure
description: Azure Service Status instellen om servicemeldingen voor Windows Virtual Desktop te ontvangen.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2834ba924fa9c29d955c38fbaeb45ab23e5c4e9b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79127710"
---
# <a name="tutorial-set-up-service-alerts"></a>Zelfstudie: Servicewaarschuwingen instellen

U Azure Service Health gebruiken om serviceproblemen en statusadviezen voor Windows Virtual Desktop te controleren. Azure Service Health kan u op de hoogte stellen van verschillende soorten waarschuwingen (bijvoorbeeld e-mail of sms), u helpen het effect van een probleem te begrijpen en u up-to-date houden terwijl het probleem is opgelost. Azure Service Health kan u ook helpen downtime te beperken en u voor te bereiden op gepland onderhoud en wijzigingen die van invloed kunnen zijn op de beschikbaarheid van uw resources.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Servicewaarschuwingen maken en configureren.

Zie de [Azure Health Documentation](https://docs.microsoft.com/azure/service-health/)voor meer informatie over Azure Service Health.

## <a name="prerequisites"></a>Vereisten

- [Zelfstudie: Een tenant maken in Windows Virtueel bureaublad](tenant-setup-azure-active-directory.md)
- [Zelfstudie: Serviceprincipals en roltoewijzingen maken met PowerShell](create-service-principal-role-powershell.md)
- [Zelfstudie: Een hostgroep maken met Azure Marketplace](create-host-pools-azure-marketplace.md)

## <a name="create-service-alerts"></a>Servicewaarschuwingen maken

In deze sectie ziet u hoe u Azure Service Health configureert en hoe u meldingen instelt, die u openen op de Azure-portal. U verschillende soorten waarschuwingen instellen en deze plannen om u tijdig op de hoogte te stellen.

### <a name="recommended-service-alerts"></a>Aanbevolen servicewaarschuwingen

We raden u aan servicewaarschuwingen te maken voor de volgende typen gezondheidsgebeurtenissen:

- **Serviceprobleem:** Ontvang meldingen over belangrijke problemen die van invloed zijn op de connectiviteit van uw gebruikers met de service of met de mogelijkheid om uw Windows Virtual Desktop-tenant te beheren.
- **Gezondheidsadvies:** Ontvang meldingen die uw aandacht vereisen. Hieronder volgen enkele voorbeelden van dit type melding:
    - Virtuele machines (VM's) niet veilig geconfigureerd als open poort 3389
    - Afschaffing van functionaliteit

### <a name="configure-service-alerts"></a>Servicewaarschuwingen configureren

Servicewaarschuwingen configureren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer **Servicestatus.**
3. Gebruik de instructies in [Meldingen voor activiteitenlogboeken maken bij servicemeldingen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) om uw meldingen en meldingen in te stellen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u Azure Service Health instellen en gebruiken om serviceproblemen en statusadviezen voor Windows Virtual Desktop te controleren. Ga door met het aanmaken van de functie Verbinding maken met Windows Virtual Desktop voor meer informatie over het aanmelden bij Windows Virtual Desktop.

> [!div class="nextstepaction"]
> [Verbinding maken met de Extern bureaublad-client op Windows 7 en Windows 10](./connect-windows-7-and-10.md)
