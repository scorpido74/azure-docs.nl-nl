---
title: Azure Updatebeheer gebruiken met Configuration Manager-clients
description: Dit artikel is bedoeld om u te helpen bij het configureren van micro soft endpoint Configuration Manager met deze oplossing voor het implementeren van software-updates op ConfigMgr-clients.
services: automation
ms.subservice: update-management
ms.date: 03/19/2018
ms.topic: conceptual
ms.openlocfilehash: 9df401ec9c6d11bfef5d1d60833c855029f8ca01
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769945"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>Updates implementeren voor micro soft endpoint Configuration Manager-clients met Updatebeheer

Klanten die hebben geïnvesteerd in micro soft endpoint Configuration Manager om Pc's, servers en mobiele apparaten te beheren, zijn ook afhankelijk van de kracht en de verval datum van het beheer van software-updates als onderdeel van hun software-update beheer cyclus.

U kunt rapporteren en beheerde Windows-servers bijwerken door te maken en vooraf staging-software-update-implementaties in Configuration Manager en gedetailleerde status van de voltooide update-implementaties met behulp van de [updatebeheer-oplossing](automation-update-management.md). Als u Configuration Manager gebruikt voor het rapporteren van de naleving van updates, maar niet voor het beheren van update-implementaties met uw Windows-servers, kunt u door gaan met de rapportage aan Configuration Manager terwijl beveiligings updates worden beheerd met de Updatebeheer oplossing.

## <a name="prerequisites"></a>Vereisten

* Hebt u de [oplossing Update Management](automation-update-management.md) toegevoegd aan uw Automation-account.
* Windows-servers die momenteel worden beheerd door uw Configuration Manager omgeving moeten ook rapporteren aan de Log Analytics-werk ruimte waarop ook de Updatebeheer oplossing is ingeschakeld.
* Deze functie is ingeschakeld in Configuration Manager huidige branch versie 1606 en hoger. Als u uw Configuration Manager centrale beheer site of een zelfstandige primaire site wilt integreren met Azure Monitor logboeken en verzamelingen importeren, controleert [u Configuration Manager verbinding maken met Azure monitor logboeken](../azure-monitor/platform/collect-sccm.md).  
* Windows-agents moeten worden geconfigureerd om te communiceren met een WSUS-server (Windows Server Update Services of toegang hebben tot Microsoft Update als ze geen beveiligingsupdates krijgen van Configuration Manager.   

Hoe u clients die worden gehost in Azure IaaS met uw bestaande Configuration Manager-omgeving beheert, hangt hoofdzakelijk af van de verbinding die u hebt tussen Azure-datacenters en uw infrastructuur. Deze verbinding is van invloed op de ontwerpwijzigingen die u mogelijk moet aanbrengen in uw Configuration Manager-infrastructuur en de daarmee samenhangende kosten voor de ondersteuning van de benodigde wijzigingen. Om te begrijpen welke overwegingen u wilt evalueren voordat u doorgaat, leest u [Configuration Manager on Azure - Frequently Asked Questions](/sccm/core/understand/configuration-manager-on-azure#networking) (veelgestelde vragen).

## <a name="configuration"></a>Configuratie

### <a name="manage-software-updates-from-configuration-manager"></a>Software-updates beheren vanuit Configuration Manager 

Voer de volgende stappen uit als u update-implementaties wilt blijven beheren vanuit Configuration Manager. Azure Automation koppelt aan Configuration Manager updates toepassen op de clientcomputers die zijn verbonden met uw Log Analytics-werkruimte. Update-inhoud is beschikbaar via de cache van de clientcomputer alsof de implementatie werd beheerd door Configuration Manager.

1. Maak een software-update-implementatie van de site op het hoogste niveau in uw Configuration Manager-hiërarchie met behulp van het proces dat wordt beschreven in het proces voor het [implementeren van software-updates](/sccm/sum/deploy-use/deploy-software-updates) (Engelstalig artikel). De enige instelling die anders moet worden geconfigureerd dan bij een standaardimplementatie, is de optie **Software-updates niet installeren** om het downloadgedrag van het implementatiepakket te bepalen. Dit gedrag wordt beheerd door de oplossing Update Management door het maken van een geplande update-implementatie in de volgende stap.

1. Selecteer in Azure Automation, **updatebeheer**. Maak een nieuwe implementatie volgen de stappen in [het maken van een Update-implementatie](automation-tutorial-update-management.md#schedule-an-update-deployment) en selecteer **groepen geïmporteerd** op de **Type** vervolgkeuzelijst voor de juiste Configuration Manager-verzameling. Houd rekening met de volgende belangrijke punten: een. Als er een onderhoudsvenster is gedefinieerd op de geselecteerde apparaatverzameling van Configuration Manager, leden van de verzameling in acht neemt deze in plaats van de **duur** instelling die is gedefinieerd in de geplande implementatie.
    b. Leden van de doelverzameling moeten verbinding hebben met Internet (rechtstreeks, via een proxyserver of via de gateway van Log Analytics).

Na het voltooien van de update-implementatie via Azure Automation, wordt de doelcomputers die lid van de geselecteerde computergroep zijn updates op het geplande tijdstip installeren vanuit hun lokale clientcache. U kunt de [status van de update-implementatie bekijken](automation-tutorial-update-management.md#view-results-of-an-update-deployment) om de resultaten van uw implementatie te bewaken.

### <a name="manage-software-updates-from-azure-automation"></a>Software-updates beheren in Azure Automation

Als u updates wilt beheren voor Windows Server-VM's die Configuration Manager-clients zijn, moet u clientbeleid configureren om de Software Update Management-functie uit te schakelen voor alle clients die worden beheerd door deze oplossing. Standaard gelden clientinstellingen voor alle apparaten in de hiërarchie. Raadpleeg voor meer informatie over deze beleidsinstelling en hoe u deze kunt configureren [Het configureren van clientinstellingen in System Center Configuration Manager](/sccm/core/clients/deploy/configure-client-settings).

Na het uitvoeren van deze configuratiewijziging, maakt u een nieuwe implementatie volgen de stappen in [het maken van een Update-implementatie](automation-tutorial-update-management.md#schedule-an-update-deployment) en selecteer **groepen geïmporteerd** op de **Type** vervolgkeuzelijst om de juiste Configuration Manager-verzameling te selecteren.

## <a name="next-steps"></a>Volgende stappen

