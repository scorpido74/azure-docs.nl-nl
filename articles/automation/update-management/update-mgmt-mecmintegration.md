---
title: Azure Automation Updatebeheer integreren met Windows-eind punt Configuration Manager
description: In dit artikel wordt beschreven hoe u micro soft endpoint Configuration Manager configureert met Updatebeheer voor het implementeren van software-updates voor Manager-clients.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 4eccd53fbf3b883d6e3ba6d2c7c80ddd4ae188af
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450158"
---
# <a name="integrate-update-management-with-windows-endpoint-configuration-manager"></a>Updatebeheer integreren met Windows-eind punt Configuration Manager

Klanten die hebben geïnvesteerd in micro soft endpoint Configuration Manager om Pc's, servers en mobiele apparaten te beheren, zijn ook afhankelijk van de kracht en de verval datum van het beheer van software-updates als onderdeel van hun software-update beheer cyclus.

U kunt beheerde Windows-servers rapporteren en bijwerken door software-update-implementaties te maken en voor te bereiden in Windows endpoint Configuration Manager en gedetailleerde status van voltooide update-implementaties te krijgen met behulp van [updatebeheer](update-mgmt-overview.md). Als u Windows endpoint Configuration Manager gebruikt voor het rapporteren van update naleving, maar niet voor het beheren van update-implementaties met uw Windows-servers, kunt u door gaan met rapportage aan eind punt Configuration Manager terwijl beveiligings updates worden beheerd met Azure Automation Updatebeheer.

>[!NOTE]
>Hoewel Updatebeheer update-evaluatie en patching van Windows Server 2008 R2 ondersteunt, biedt het geen ondersteuning voor clients die worden beheerd door het eind punt Configuration Manager het uitvoeren van dit besturings systeem.

## <a name="prerequisites"></a>Vereisten

* U moet [Azure Automation updatebeheer](update-mgmt-overview.md) toevoegen aan uw Automation-account.
* Windows-servers die momenteel worden beheerd door uw Windows-eind punt Configuration Manager omgeving moeten ook rapporteren aan de Log Analytics-werk ruimte waar ook Updatebeheer is ingeschakeld.
* Deze functie is ingeschakeld in het Windows-eind punt Configuration Manager versie 1606 van de huidige vertakking. Als u uw Windows-eind punt Configuration Manager centrale beheer site of een zelfstandige primaire site met Azure Monitor logboeken en import verzamelingen wilt integreren, controleert [u Configuration Manager verbinding maken met Azure monitor logboeken](../../azure-monitor/platform/collect-sccm.md).  
* Windows-agents moeten worden geconfigureerd om te communiceren met een Windows Server Update Services-server (WSUS) of toegang hebben tot Microsoft Update als ze geen beveiligings updates ontvangen van Windows-eind punt Configuration Manager.

Hoe u clients beheert die worden gehost in azure IaaS met uw bestaande Windows-eindpunt Configuration Manager-omgeving, is in de eerste plaats afhankelijk van de verbinding tussen Azure-data centers en uw infra structuur. Deze verbinding is van invloed op de ontwerp wijzigingen die u mogelijk moet aanbrengen in uw Windows-eind punt Configuration Manager-infra structuur en gerelateerde kosten ter ondersteuning van die nood zakelijke wijzigingen. Om te begrijpen welke overwegingen u wilt evalueren voordat u doorgaat, leest u [Configuration Manager on Azure - Frequently Asked Questions](/configmgr/core/understand/configuration-manager-on-azure#networking) (veelgestelde vragen).

## <a name="manage-software-updates-from-windows-endpoint-configuration-manager"></a>Software-updates beheren via Windows-eind punt Configuration Manager

Voer de volgende stappen uit als u wilt door gaan met het beheren van update-implementaties van Windows endpoint Configuration Manager. Azure Automation maakt verbinding met Windows-eind punt Configuration Manager om updates toe te passen op de client computers die zijn verbonden met uw Log Analytics-werk ruimte. Update-inhoud is beschikbaar via de cache van de client computer alsof de implementatie door Windows endpoint Configuration Manager is beheerd.

1. Maak een software-update-implementatie vanaf de site op het hoogste niveau in uw Windows-eind punt Configuration Manager-hiërarchie met behulp van het proces dat wordt beschreven in [software-updates implementeren](/configmgr/sum/deploy-use/deploy-software-updates). De enige instelling die anders moet worden geconfigureerd dan bij een standaardimplementatie, is de optie **Software-updates niet installeren** om het downloadgedrag van het implementatiepakket te bepalen. Dit gedrag wordt in Updatebeheer beheerd door een geplande update-implementatie in de volgende stap te maken.

2. Selecteer **updatebeheer**in azure Automation. Maak een nieuwe implementatie aan de hand van de stappen die worden beschreven in [een update-implementatie maken](update-mgmt-deploy-updates.md#schedule-an-update-deployment) en selecteer **geïmporteerde groepen** op de vervolg keuzelijst **type** om de juiste Windows-eindpunt Configuration Manager verzameling te selecteren. Houd rekening met de volgende belangrijke punten:

    a. Als er een onderhouds venster is gedefinieerd voor het geselecteerde Windows-eind punt Configuration Manager-apparaat, voldoen de leden van de verzameling deze in plaats van **de waarde-** instelling die in de geplande implementatie is gedefinieerd.

    b. Leden van de doel verzameling moeten een verbinding hebben met internet (hetzij direct, via een proxy server of via de gateway van Log Analytics).

Na het volt ooien van de update-implementatie via Azure Automation, zullen de doel computers die lid zijn van de geselecteerde computer groep updates installeren op de geplande tijd van de lokale client cache. U kunt de [status van de update-implementatie bekijken](update-mgmt-deploy-updates.md#check-deployment-status) om de resultaten van uw implementatie te bewaken.

## <a name="manage-software-updates-from-azure-automation"></a>Software-updates beheren via Azure Automation

Voor het beheren van updates voor Windows Server-Vm's die Windows endpoint Configuration Manager-clients zijn, moet u client beleid configureren om de Software Update Management-functie uit te scha kelen voor alle clients die worden beheerd door Updatebeheer. Standaard gelden clientinstellingen voor alle apparaten in de hiërarchie. Lees [hoe u client instellingen in Configuration Manager kunt configureren](/configmgr/core/clients/deploy/configure-client-settings)voor meer informatie over deze beleids instelling en hoe u deze kunt configureren.

Nadat u deze configuratie wijziging hebt uitgevoerd, maakt u een nieuwe implementatie aan de hand van de stappen die worden beschreven in [een update-implementatie maken](update-mgmt-deploy-updates.md#schedule-an-update-deployment) en selecteert u **geïmporteerde groepen** in de vervolg keuzelijst **type** om de juiste Windows-eindpunt Configuration Manager verzameling te selecteren.

## <a name="next-steps"></a>Volgende stappen

Zie [een update-implementatie plannen](update-mgmt-deploy-updates.md#schedule-an-update-deployment)voor meer informatie over het instellen van een integratie schema.
