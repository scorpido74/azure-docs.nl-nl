---
title: Azure Automation Updatebeheer integreren met Windows-eind punt Configuration Manager
description: In dit artikel wordt beschreven hoe u micro soft endpoint Configuration Manager configureert met Updatebeheer voor het implementeren van software-updates voor Manager-clients.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: ae8c4f09c0133dde7b0a73b7c2fcd0a28aa22ae3
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84013194"
---
# <a name="integrate-update-management-with-windows-endpoint-configuration-manager"></a>Updatebeheer integreren met Windows-eind punt Configuration Manager

Klanten die hebben geïnvesteerd in micro soft endpoint Configuration Manager om Pc's, servers en mobiele apparaten te beheren, zijn ook afhankelijk van de kracht en de verval datum van het beheer van software-updates als onderdeel van hun software-update beheer cyclus.

U kunt beheerde Windows-servers rapporteren en bijwerken door software-update-implementaties te maken en voor te bereiden in Windows endpoint Configuration Manager en gedetailleerde status van voltooide update-implementaties te krijgen met behulp van [updatebeheer](automation-update-management.md). Als u Windows endpoint Configuration Manager gebruikt voor het rapporteren van update vereisten, maar niet voor het beheren van update-implementaties met uw Windows-servers, kunt u door gaan met de rapportage aan Configuration Manager terwijl beveiligings updates worden beheerd met Azure Automation Updatebeheer.

## <a name="prerequisites"></a>Vereisten

* U moet [Azure Automation updatebeheer](automation-update-management.md) toevoegen aan uw Automation-account.
* Windows-servers die momenteel worden beheerd door uw Windows-eind punt Configuration Manager omgeving moeten ook rapporteren aan de Log Analytics-werk ruimte waar ook Updatebeheer is ingeschakeld.
* Deze functie is ingeschakeld in het Windows-eind punt Configuration Manager versie 1606 van de huidige vertakking. Als u uw Windows-eind punt Configuration Manager centrale beheer site of een zelfstandige primaire site met Azure Monitor logboeken en import verzamelingen wilt integreren, controleert [u Configuration Manager verbinding maken met Azure monitor logboeken](../azure-monitor/platform/collect-sccm.md).  
* Windows-agents moeten worden geconfigureerd om te communiceren met een Windows Server Update Services-server (WSUS) of toegang hebben tot Microsoft Update als ze geen beveiligings updates ontvangen van Windows-eind punt Configuration Manager.

Hoe u clients beheert die worden gehost in azure IaaS met uw bestaande Windows-eindpunt Configuration Manager-omgeving, is in de eerste plaats afhankelijk van de verbinding tussen Azure-data centers en uw infra structuur. Deze verbinding is van invloed op de ontwerp wijzigingen die u mogelijk moet aanbrengen in uw Windows-eind punt Configuration Manager-infra structuur en gerelateerde kosten ter ondersteuning van die nood zakelijke wijzigingen. Om te begrijpen welke overwegingen u wilt evalueren voordat u doorgaat, leest u [Configuration Manager on Azure - Frequently Asked Questions](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking) (veelgestelde vragen).

## <a name="manage-software-updates-from-windows-endpoint-configuration-manager"></a>Software-updates beheren via Windows-eind punt Configuration Manager

Voer de volgende stappen uit als u wilt door gaan met het beheren van update-implementaties van Windows endpoint Configuration Manager. Azure Automation maakt verbinding met Windows-eind punt Configuration Manager om updates toe te passen op de client computers die zijn verbonden met uw Log Analytics-werk ruimte. Update-inhoud is beschikbaar via de cache van de client computer alsof de implementatie door Windows endpoint Configuration Manager is beheerd.

1. Maak een software-update-implementatie vanaf de site op het hoogste niveau in uw Windows-eind punt Configuration Manager-hiërarchie met behulp van het proces dat wordt beschreven in [software-updates implementeren](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates). De enige instelling die anders moet worden geconfigureerd dan bij een standaardimplementatie, is de optie **Software-updates niet installeren** om het downloadgedrag van het implementatiepakket te bepalen. Dit gedrag wordt in Updatebeheer beheerd door een geplande update-implementatie in de volgende stap te maken.

1. Selecteer **updatebeheer**in azure Automation. Maak een nieuwe implementatie aan de hand van de stappen die worden beschreven in [een update-implementatie maken](automation-tutorial-update-management.md#schedule-an-update-deployment) en selecteer **geïmporteerde groepen** op de vervolg keuzelijst **type** om de juiste Windows-eindpunt Configuration Manager verzameling te selecteren. Houd rekening met de volgende belang rijke punten: a. Als er een onderhouds venster is gedefinieerd voor het geselecteerde Windows-eind punt Configuration Manager-apparaat, voldoen de leden van de verzameling deze in plaats van **de waarde-** instelling die in de geplande implementatie is gedefinieerd.
    b. Leden van de doel verzameling moeten een verbinding hebben met internet (hetzij direct, via een proxy server of via de gateway van Log Analytics).

Na het volt ooien van de update-implementatie via Azure Automation, zullen de doel computers die lid zijn van de geselecteerde computer groep updates installeren op de geplande tijd van de lokale client cache. U kunt de [status van de update-implementatie bekijken](automation-tutorial-update-management.md#check-deployment-status) om de resultaten van uw implementatie te bewaken.

## <a name="manage-software-updates-from-azure-automation"></a>Software-updates beheren via Azure Automation

Voor het beheren van updates voor Windows Server-Vm's die Windows endpoint Configuration Manager-clients zijn, moet u client beleid configureren om de Software Update Management-functie uit te scha kelen voor alle clients die worden beheerd door Updatebeheer. Standaard gelden clientinstellingen voor alle apparaten in de hiërarchie. Lees [hoe u client instellingen in Configuration Manager kunt configureren](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings)voor meer informatie over deze beleids instelling en hoe u deze kunt configureren.

Nadat u deze configuratie wijziging hebt uitgevoerd, maakt u een nieuwe implementatie aan de hand van de stappen die worden beschreven in [een update-implementatie maken](automation-tutorial-update-management.md#schedule-an-update-deployment) en selecteert u **geïmporteerde groepen** in de vervolg keuzelijst **type** om de juiste Windows-eindpunt Configuration Manager verzameling te selecteren.

## <a name="next-steps"></a>Volgende stappen

Zie [een update-implementatie plannen](automation-tutorial-update-management.md#schedule-an-update-deployment)voor meer informatie over het instellen van een integratie schema.