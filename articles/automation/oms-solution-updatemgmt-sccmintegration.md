---
title: Azure Update Beheer gebruiken met clients Configuration Manager
description: Dit artikel is bedoeld om u te helpen bij het configureren van Microsoft Endpoint Configuration Manager met deze oplossing om software-updates te implementeren voor ConfigMgr-clients.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: f0ca836e3b53c3cce755d45b50fe168073f0bbaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513130"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>Updates implementeren voor Microsoft Endpoint Configuration Manager-clients met Updatebeheer

Klanten die hebben geïnvesteerd in Microsoft Endpoint Configuration Manager om pc's, servers en mobiele apparaten te beheren, vertrouwen ook op de kracht en volwassenheid ervan bij het beheren van software-updates als onderdeel van hun software-updatemanagementcyclus (SOM).

U beheerde Windows-servers rapporteren en bijwerken door implementaties van software-updates te maken en vooraf te fasen in Configuration Manager en gedetailleerde status te krijgen van voltooide update-implementaties met behulp van de [oplossing Updatebeheer](automation-update-management.md). Als u Configuration Manager gebruikt voor rapportage over de naleving van updates, maar niet voor het beheren van update-implementaties met uw Windows-servers, u doorgaan met rapporteren aan Configuration Manager terwijl beveiligingsupdates worden beheerd met de updatebeheeroplossing.

## <a name="prerequisites"></a>Vereisten

* U moet de [updatebeheeroplossing](automation-update-management.md) hebben toegevoegd aan uw Automatiseringsaccount.
* Windows-servers die momenteel worden beheerd door uw Configuration Manager-omgeving, moeten ook rapporteren aan de werkruimte Log Analytics waarop ook de oplossing Updatebeheer is ingeschakeld.
* Deze functie is ingeschakeld in Configuration Manager current branch versie 1606 en hoger. Als u uw centrale beheersite voor configuratiebeheerbeheer of een zelfstandige primaire site wilt integreren met Azure Monitor-logboeken en importverzamelingen, controleert u [Configuratiebeheer verbinden met Azure Monitor-logboeken.](../azure-monitor/platform/collect-sccm.md)  
* Windows-agents moeten worden geconfigureerd om te communiceren met een WSUS-server (Windows Server Update Services of toegang hebben tot Microsoft Update als ze geen beveiligingsupdates krijgen van Configuration Manager.   

Hoe u clients die worden gehost in Azure IaaS met uw bestaande Configuration Manager-omgeving beheert, hangt hoofdzakelijk af van de verbinding die u hebt tussen Azure-datacenters en uw infrastructuur. Deze verbinding is van invloed op de ontwerpwijzigingen die u mogelijk moet aanbrengen in uw Configuration Manager-infrastructuur en de daarmee samenhangende kosten voor de ondersteuning van de benodigde wijzigingen. Om te begrijpen welke overwegingen u wilt evalueren voordat u doorgaat, leest u [Configuration Manager on Azure - Frequently Asked Questions](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking) (veelgestelde vragen).

## <a name="configuration"></a>Configuratie

### <a name="manage-software-updates-from-configuration-manager"></a>Software-updates beheren vanuit Configuration Manager 

Voer de volgende stappen uit als u update-implementaties wilt blijven beheren vanuit Configuration Manager. Azure Automation maakt verbinding met Configuration Manager om updates toe te passen op de clientcomputers die zijn verbonden met uw Log Analytics-werkruimte. Update-inhoud is beschikbaar via de cache van de clientcomputer alsof de implementatie werd beheerd door Configuration Manager.

1. Maak een implementatie van software-updates vanaf de site op het hoogste niveau in de hiërarchie Configuratiebeheer met behulp van het proces dat wordt beschreven in [Software-updates implementeren.](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates) De enige instelling die anders moet worden geconfigureerd dan bij een standaardimplementatie, is de optie **Software-updates niet installeren** om het downloadgedrag van het implementatiepakket te bepalen. Dit gedrag wordt beheerd door de oplossing Updatebeheer door in de volgende stap een geplande update-implementatie te maken.

1. Selecteer In Azure Automation de optie **Updatebeheer**. Maak een nieuwe implementatie volgens de stappen die zijn beschreven in [Het maken van een update-implementatie](automation-tutorial-update-management.md#schedule-an-update-deployment) en selecteer **Geïmporteerde groepen** in de vervolgkeuzelijst **Type** om de juiste configuratiebeheerverzameling te selecteren. Houd rekening met de volgende belangrijke punten: a. Als een onderhoudsvenster is gedefinieerd in de geselecteerde configuratiebeheer-apparaatverzameling, eren leden van de verzameling deze in plaats van de **instelling Duur** die is gedefinieerd in de geplande implementatie.
    b. Leden van de doelverzameling moeten een verbinding met internet hebben (rechtstreeks, via een proxyserver of via de Log Analytics-gateway).

Nadat de update-implementatie via Azure Automation is voltooid, installeren de doelcomputers die lid zijn van de geselecteerde computergroep updates op het geplande tijdstip vanuit hun lokale clientcache. U kunt de [status van de update-implementatie bekijken](automation-tutorial-update-management.md#view-results-of-an-update-deployment) om de resultaten van uw implementatie te bewaken.

### <a name="manage-software-updates-from-azure-automation"></a>Software-updates beheren vanuit Azure Automation

Als u updates wilt beheren voor Windows Server-VM's die Configuration Manager-clients zijn, moet u clientbeleid configureren om de Software Update Management-functie uit te schakelen voor alle clients die worden beheerd door deze oplossing. Standaard gelden clientinstellingen voor alle apparaten in de hiërarchie. Voor meer informatie over deze beleidsinstelling en hoe u deze configureren, leest u [Hoe u clientinstellingen configureert in Configuratiebeheer](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings).

Nadat u deze configuratiewijziging hebt uitgevoerd, maakt u een nieuwe implementatie volgens de stappen die zijn beschreven in [Het maken van een update-implementatie](automation-tutorial-update-management.md#schedule-an-update-deployment) en selecteert u **Geïmporteerde groepen** in de vervolgkeuzelijst **Type** om de juiste configuratiebeheerverzameling te selecteren.

## <a name="next-steps"></a>Volgende stappen

