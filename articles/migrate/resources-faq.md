---
title: Veelgestelde vragen over Azure Migrate
description: Krijg antwoorden op veelgestelde vragen over de Azure Migrate-service.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: baf01c0a0d5c6154305f7137c24deb0365b5e812
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062095"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: algemene vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Migrate. Als u na het lezen van dit artikel nog meer query's hebt uitgevoerd, plaatst u deze op het [Azure migrate forum](https://aka.ms/AzureMigrateForum). Als u andere vragen hebt, raadpleegt u de volgende artikelen:

- [Vragen](common-questions-appliance.md) over het Azure migrate apparaat.
- [Vragen](common-questions-discovery-assessment.md) over de visualisatie van detectie, beoordeling en afhankelijkheid.


## <a name="what-is-azure-migrate"></a>Wat is Azure Migrate?

Azure Migrate biedt een centrale hub voor het bijhouden van detectie, evaluatie en migratie van uw on-premises apps en werk belastingen, en persoonlijke/open bare Cloud-Vm's naar Azure. De hub biedt Azure Migrate-hulpprogram ma's voor evaluatie en migratie, evenals onafhankelijke ISV'S-aanbiedingen (Independent Software Vendor) van derden. [Meer informatie](migrate-services-overview.md).


## <a name="what-can-i-do-with-azure-migrate"></a>Wat kan ik doen met Azure Migrate?

Gebruik Azure Migrate om on-premises infra structuur, toepassingen en gegevens te detecteren, te evalueren en te migreren naar Azure. Azure Migrate ondersteunt de evaluatie en migratie van on-premises virtuele VMware-machines, Hyper-V-Vm's, fysieke servers, andere gevirtualiseerde Vm's, data bases, Web-apps en virtuele Bureau bladen. 

## <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Wat is het verschil tussen Azure Migrate en Site Recovery?

Azure Migrate biedt een gecentraliseerde hub voor evaluatie en migratie naar Azure. [Azure site Recovery](../site-recovery/site-recovery-overview.md) is een oplossing voor nood herstel. Het Azure Migrate: hulp programma voor server migratie maakt gebruik van een back-end-Site Recovery functionaliteit voor het migreren van een of meer on-premises machines.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Wat is het verschil tussen Azure Migrate server evaluatie en de kaart Toolkit?

Server evaluatie biedt een evaluatie van de voor bereiding op de migratie en de evaluatie van werk belastingen voor migratie naar Azure. [Micro soft Assessment and planning (kaart) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) helpt bij andere taken, waaronder migratie planning voor nieuwere versies van Windows-besturings systemen voor clients en servers, en het bijhouden van software gebruik. Voor deze scenario's kunt u de kaart Toolkit blijven gebruiken.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Wat is het verschil tussen server evaluatie en het Site Recovery Deployment Planner?

Server evaluatie is een hulp programma voor migratie planning. De Site Recovery Deployment Planner is een hulp programma voor herstel na nood gevallen.

- **On-premises migratie naar Azure plannen**: als u van plan bent om uw on-premises servers naar Azure te migreren, gebruikt u Server evaluatie voor migratie planning. Het evalueert on-premises workloads en biedt hulp en hulpprogram ma's waarmee u kunt migreren. Nadat het migratie plan is geïmplementeerd, kunt u hulpprogram ma's, waaronder Azure Migrate server migratie, gebruiken om de machines te migreren naar Azure.
- **Herstel na nood geval naar Azure plannen**: als u van plan bent om herstel na nood gevallen van on-premises naar azure met site Recovery in te stellen, gebruikt u de site Recovery Deployment planner. De Deployment Planner biedt een diep gaande Site Recovery-specifieke evaluatie van uw on-premises omgeving voor nood herstel. Het biedt aanbevelingen voor herstel na nood gevallen, zoals replicatie en failover.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Hoe werkt de migratie van servers met Site Recovery?

- Als u Azure Migrate: Server migratie voor het uitvoeren van een niet-werkende migratie van on-premises virtuele VMware-machines, is de migratie van systeem eigen naar Azure Migrate en wordt Site Recovery niet gebruikt.
- Als u Azure Migrate: Server migratie gebruikt voor het uitvoeren van een op agents gebaseerde migratie van virtuele VMware-machines, of het migreren van virtuele Hyper-V-machines of fysieke servers, dan maakt Azure Migrate server migratie gebruik van de Azure Site Recovery replicatie-engine.


## <a name="which-geographies-are-supported"></a>Welke geografische gebieden worden ondersteund?

Bekijk de Azure Migrate ondersteunde geographs voor [VMware VM](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) en voor [virtuele Hyper-V-machines](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v).

## <a name="how-do-i-get-started"></a>Hoe ga ik aan de slag?

U kunt het hulp programma identificeren dat u nodig hebt en dit toevoegen aan een Azure Migrate-project. Als u een ISV-hulp programma of een indrijfprogramma toevoegt:
- Ga aan de slag door een licentie te verkrijgen of u aan te melden voor een gratis proef versie, in overeenstemming met het hulp programma beleid. Licentie verlening voor hulpprogram ma's is in overeenstemming met het ISV-of hulp programma licentie model.
- In elk hulp programma is er een optie om verbinding te maken met Azure Migrate. Volg de instructies en documentatie van het hulp programma om het hulp programma te verbinden met Azure Migrate.
U kunt uw migratie traject centraal volgen vanuit het Azure Migrate project, over Azure en andere hulpprogram ma's.

## <a name="how-do-i-delete-a-project"></a>Een project Hoe kan ik verwijderen?

[Meer informatie over het](how-to-delete-project.md) verwijderen van een project. 




## <a name="next-steps"></a>Volgende stappen
Lees het [Azure migrate overzicht](migrate-services-overview.md).
