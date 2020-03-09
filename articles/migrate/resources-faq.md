---
title: Veelgestelde vragen over Azure Migrate
description: Krijg antwoorden op veelgestelde vragen over de Azure Migrate-service.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: c85c5c6e11beb0178139dad152f56f420b2ac26f
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2020
ms.locfileid: "78926733"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: algemene vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Migrate. Als u vragen hebt nadat u dit artikel hebt gelezen, kunt u ze in het [Azure migrate-forum](https://aka.ms/AzureMigrateForum)plaatsen. U kunt ook deze artikelen bekijken:

- Vragen over het [Azure migrate apparaat](common-questions-appliance.md)
- Vragen over de [visualisatie van detectie, beoordeling en afhankelijkheid](common-questions-discovery-assessment.md)

## <a name="what-is-azure-migrate"></a>Wat is Azure Migrate?

Azure Migrate biedt een centrale hub voor het bijhouden van de detectie, evaluatie en migratie van uw on-premises apps en werk belastingen, en persoonlijke en open bare Cloud-Vm's naar Azure. De hub biedt Azure Migrate tools voor evaluatie en migratie en ISV-aanbiedingen van derden. [Meer informatie](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>Wat kan ik doen met Azure Migrate?

Gebruik Azure Migrate om on-premises infra structuur, toepassingen en gegevens te detecteren, te evalueren en te migreren naar Azure. Azure Migrate ondersteunt de evaluatie en migratie van on-premises virtuele VMware-machines, Hyper-V-Vm's, fysieke servers, andere gevirtualiseerde Vm's, data bases, Web-apps en virtuele Bureau bladen. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Wat is het verschil tussen Azure Migrate en Azure Site Recovery?

[Azure migrate](migrate-services-overview.md) biedt een gecentraliseerde hub voor evaluatie en migratie naar Azure. 

[Azure site Recovery](../site-recovery/site-recovery-overview.md) is een oplossing voor nood herstel. 

Het Azure Migrate: hulp programma voor server migratie maakt gebruik van enige back-end-Site Recovery functionaliteit voor het migreren van een aantal on-premises machines.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Wat is het verschil tussen Azure Migrate: Server analyse en de kaart Toolkit?

Server evaluatie biedt een evaluatie van de voor bereiding op de migratie en de evaluatie van werk belastingen voor migratie naar Azure. De [micro soft Assessment and planning (kaart) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) helpt bij andere taken, waaronder migratie planning voor nieuwere versies van Windows-besturings systemen voor clients en servers, en het bijhouden van software gebruik. Voor deze scenario's kunt u de kaart Toolkit blijven gebruiken.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Wat is het verschil tussen server evaluatie en het Site Recovery Deployment Planner?

Server evaluatie is een hulp programma voor migratie planning. De Site Recovery Deployment Planner is een hulp programma voor herstel na nood gevallen.

Kies uw hulp programma op basis van wat u wilt doen:

- **On-premises migratie naar Azure plannen**: als u van plan bent om uw on-premises servers naar Azure te migreren, gebruikt u Server evaluatie voor migratie planning. Server evaluatie evalueert on-premises workloads en biedt hulp middelen en hulpprogram ma's waarmee u kunt migreren. Nadat het migratie plan is geïmplementeerd, kunt u hulpprogram ma's zoals Azure Migrate gebruiken: Server migratie om de machines te migreren naar Azure.
- **Herstel na nood geval naar Azure plannen**: als u van plan bent om herstel na nood gevallen van on-premises naar azure met site Recovery in te stellen, gebruikt u de site Recovery Deployment planner. De Deployment Planner biedt een diep gaande Site Recovery-specifieke evaluatie van uw on-premises omgeving voor nood herstel. Het bevat aanbevelingen met betrekking tot herstel na nood gevallen, zoals replicatie en failover.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Hoe werkt de migratie van servers met Site Recovery?

- Als u Azure Migrate: Server migratie voor het uitvoeren van *een niet* -werkende migratie van on-premises virtuele VMware-machines, is migratie standaard naar Azure migrate en wordt site Recovery niet gebruikt.
- Als u Azure Migrate: Server migratie gebruikt voor het uitvoeren van een *op agents gebaseerde* migratie van VMware-vm's, of als u virtuele Hyper-V-machines of fysieke servers migreert, Azure migrate: Server migratie maakt gebruik van de Azure site Recovery replicatie-engine.

## <a name="which-geographies-are-supported"></a>Welke geografische gebieden worden ondersteund?

- **VMware-vm's**: bekijk de Azure migrate [ondersteunde geographs](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) voor VMware-vm's.
- **Virtuele Hyper-v-machines**: bekijk de Azure migrate [ondersteunde geographs](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v) voor virtuele Hyper-v-machines.

## <a name="how-do-i-get-started"></a>Hoe ga ik aan de slag?

Bepaal het hulp programma dat u nodig hebt en voeg het hulp programma toe aan een Azure Migrate-project. 

Een ISV-hulp programma of-overwerker toevoegen:

1. Ga aan de slag met het verkrijgen van een licentie of Meld u aan voor een gratis proef versie, in overeenstemming met het hulp programma beleid. Licentie verlening voor hulpprogram ma's is in overeenstemming met het ISV-of hulp programma licentie model.
2. In elk hulp programma is er een optie om verbinding te maken met Azure Migrate. Volg de instructies en documentatie van het hulp programma om verbinding te maken met het hulp programma met Azure Migrate.

U kunt uw migratie traject volgen vanuit het Azure Migrate project, in Azure en in andere hulpprogram ma's.

## <a name="how-do-i-delete-a-project"></a>Een project Hoe kan ik verwijderen?

Meer informatie over het [verwijderen van een project](how-to-delete-project.md). 

## <a name="next-steps"></a>Volgende stappen

Lees het [Azure migrate overzicht](migrate-services-overview.md).
