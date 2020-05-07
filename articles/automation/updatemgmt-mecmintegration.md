---
title: Azure Automation Updatebeheer gebruiken met Configuration Manager-clients
description: Dit artikel is bedoeld om u te helpen bij het configureren van micro soft endpoint Configuration Manager met deze oplossing voor het implementeren van software-updates op ConfigMgr-clients.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 2dbc33aa56c7e930596ba6806ba1dd2e128e1c82
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780212"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>Updates implementeren voor micro soft endpoint Configuration Manager-clients met Updatebeheer

Klanten die hebben geïnvesteerd in micro soft endpoint Configuration Manager om Pc's, servers en mobiele apparaten te beheren, zijn ook afhankelijk van de kracht en de verval datum van het beheer van software-updates als onderdeel van hun software-update beheer cyclus.

U kunt beheerde Windows-servers rapporteren en bijwerken door software-update-implementaties te maken en voor te bereiden in Configuration Manager en gedetailleerde status van voltooide update-implementaties te krijgen met behulp van [updatebeheer](automation-update-management.md). Als u Configuration Manager gebruikt voor het rapporteren van de naleving van updates, maar niet voor het beheren van update-implementaties met uw Windows-servers, kunt u door gaan met de rapportage aan Configuration Manager terwijl beveiligings updates met de Updatebeheer worden beheerd.

## <a name="prerequisites"></a>Vereisten

* U moet [updatebeheer](automation-update-management.md) toevoegen aan uw Automation-account.
* Windows-servers die momenteel worden beheerd door uw Configuration Manager-omgeving, moeten ook rapporteren aan de Log Analytics-werk ruimte waar ook Updatebeheer is ingeschakeld.
* Deze functie is ingeschakeld in Configuration Manager huidige branch versie 1606 en hoger. Als u uw Configuration Manager centrale beheer site of een zelfstandige primaire site wilt integreren met Azure Monitor logboeken en verzamelingen importeren, controleert [u Configuration Manager verbinding maken met Azure monitor logboeken](../azure-monitor/platform/collect-sccm.md).  
* Windows-agents moeten worden geconfigureerd om te communiceren met een WSUS-server (Windows Server Update Services of toegang hebben tot Microsoft Update als ze geen beveiligingsupdates krijgen van Configuration Manager.

Hoe u clients die worden gehost in Azure IaaS met uw bestaande Configuration Manager-omgeving beheert, hangt hoofdzakelijk af van de verbinding die u hebt tussen Azure-datacenters en uw infrastructuur. Deze verbinding is van invloed op de ontwerpwijzigingen die u mogelijk moet aanbrengen in uw Configuration Manager-infrastructuur en de daarmee samenhangende kosten voor de ondersteuning van de benodigde wijzigingen. Om te begrijpen welke overwegingen u wilt evalueren voordat u doorgaat, leest u [Configuration Manager on Azure - Frequently Asked Questions](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking) (veelgestelde vragen).

## <a name="configuration"></a>Configuratie

### <a name="manage-software-updates-from-configuration-manager"></a>Software-updates beheren vanuit Configuration Manager

Voer de volgende stappen uit als u update-implementaties wilt blijven beheren vanuit Configuration Manager. Azure Automation maakt verbinding met Configuration Manager om updates toe te passen op de client computers die zijn verbonden met uw Log Analytics-werk ruimte. Update-inhoud is beschikbaar via de cache van de clientcomputer alsof de implementatie werd beheerd door Configuration Manager.

1. Maak een software-update-implementatie vanaf de site op het hoogste niveau in uw Configuration Manager-hiërarchie met behulp van het proces dat wordt beschreven in [software-updates implementeren](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates). De enige instelling die anders moet worden geconfigureerd dan bij een standaardimplementatie, is de optie **Software-updates niet installeren** om het downloadgedrag van het implementatiepakket te bepalen. Dit gedrag wordt in Updatebeheer beheerd door een geplande update-implementatie in de volgende stap te maken.

1. Selecteer **updatebeheer**in azure Automation. Maak een nieuwe implementatie aan de hand van de stappen die worden beschreven in [een update-implementatie maken](automation-tutorial-update-management.md#schedule-an-update-deployment) en selecteer **geïmporteerde groepen** op de vervolg keuzelijst **type** om de juiste Configuration Manager verzameling te selecteren. Houd rekening met de volgende belang rijke punten: a. Als er een onderhouds venster is gedefinieerd voor de geselecteerde Configuration Manager apparaten, voldoen de leden van de verzameling deze in plaats van **de waarde-instelling die is** gedefinieerd in de geplande implementatie.
    b. Leden van de doel verzameling moeten een verbinding hebben met internet (hetzij direct, via een proxy server of via de gateway van Log Analytics).

Na het volt ooien van de update-implementatie via Azure Automation, zullen de doel computers die lid zijn van de geselecteerde computer groep updates installeren op de geplande tijd van de lokale client cache. U kunt de [status van de update-implementatie bekijken](automation-tutorial-update-management.md#view-results-of-an-update-deployment) om de resultaten van uw implementatie te bewaken.

### <a name="manage-software-updates-from-azure-automation"></a>Software-updates beheren via Azure Automation

Voor het beheren van updates voor Windows Server-Vm's die zijn Configuration Manager-clients, moet u client beleid configureren om de Software Update Management-functie uit te scha kelen voor alle clients die worden beheerd door Updatebeheer. Standaard gelden clientinstellingen voor alle apparaten in de hiërarchie. Lees [hoe u client instellingen in Configuration Manager kunt configureren](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings)voor meer informatie over deze beleids instelling en hoe u deze kunt configureren.

Nadat u deze configuratie wijziging hebt uitgevoerd, maakt u een nieuwe implementatie volgens de stappen die worden beschreven in [een update-implementatie maken](automation-tutorial-update-management.md#schedule-an-update-deployment) en selecteert u **geïmporteerde groepen** in de vervolg keuzelijst **type** om de juiste Configuration Manager verzameling te selecteren.

## <a name="next-steps"></a>Volgende stappen

Maak een nieuwe implementatie door de stappen te volgen die worden beschreven in [een update-implementatie maken](automation-tutorial-update-management.md#schedule-an-update-deployment).