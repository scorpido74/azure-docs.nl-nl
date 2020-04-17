---
title: Veelgestelde vragen over Azure Migreren
description: Antwoorden op veelgestelde vragen over de Azure Migrate-service.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 9b71888b284fd9cc125def4758d1e3800a92acf2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530314"
---
# <a name="azure-migrate-common-questions"></a>Azure Migreren: veelgestelde vragen

In dit artikel worden veelgestelde vragen over Azure Migreren beantwoord. Als u vragen hebt nadat u dit artikel hebt gelezen, u deze plaatsen in het [Azure Migrate-forum.](https://aka.ms/AzureMigrateForum) U ook deze artikelen bekijken:

- Vragen over het [Azure Migrate-toestel](common-questions-appliance.md)
- Vragen over [detectie-, beoordelings- en afhankelijkheidsvisualisatie](common-questions-discovery-assessment.md)

## <a name="what-is-azure-migrate"></a>Wat is Azure Migrate?

Azure Migrate biedt een centrale hub om detectie, beoordeling en migratie van uw on-premises apps en workloads en private en public cloud VM's naar Azure bij te houden. De hub biedt Azure Migrate-hulpprogramma's voor beoordeling en migratie en ISV-aanbiedingen van derden. [Meer informatie](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>Wat kan ik doen met Azure Migrate?

Gebruik Azure Migrate om on-premises infrastructuur, toepassingen en gegevens te ontdekken, te beoordelen en te migreren naar Azure. Azure Migrate ondersteunt beoordeling en migratie van on-premises Vm's vMware, Hyper-V VM's, fysieke servers, andere gevirtualiseerde VM's, databases, web-apps en virtuele desktops. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Wat is het verschil tussen Azure Migrate en Azure Site Recovery?

[Azure Migrate](migrate-services-overview.md) biedt een gecentraliseerde hub voor beoordeling en migratie naar Azure. 

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) is een oplossing voor noodherstel. 

Het hulpprogramma Azure Migreren: Servermigratie gebruikt een aantal back-end siteherstelfunctionaliteit voor lift-and-shift-migratie van sommige on-premises machines.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Wat is het verschil tussen Azure Migrate: Server Assessment en de MAP Toolkit?

Serverbeoordeling biedt beoordeling om te helpen bij de gereedheid van migratie en evaluatie van workloads voor migratie naar Azure. De [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) helpt bij andere taken, waaronder migratieplanning voor nieuwere versies van Windows-client- en serverbesturingssystemen en het bijhouden van softwaregebruik. Voor deze scenario's blijft u de MAP Toolkit gebruiken.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Wat is het verschil tussen serverbeoordeling en de siteherstelimplementatieplanner?

Server assessment is een hulpprogramma voor migratieplanning. De Site Recovery Deployment Planner is een hulpprogramma voor noodherstelplanning.

Kies uw tool op basis van wat u wilt doen:

- **On-premises migratie naar Azure**plannen: als u van plan bent uw on-premises servers naar Azure te migreren, gebruikt u Serverbeoordeling voor migratieplanning. Server Assessment beoordeelt on-premises workloads en biedt richtlijnen en tools om u te helpen migreren. Nadat het migratieplan is uitgevoerd, u hulpprogramma's zoals Azure Migreren: Servermigratie gebruiken om de machines naar Azure te migreren.
- **Herstel na noodgevallen plannen voor Azure:** Als u noodherstel wilt instellen van on-premises naar Azure met Site recovery, gebruikt u de Site Recovery Deployment Planner. De Deployment Planner biedt een diepgaande, site recovery-specifieke beoordeling van uw on-premises omgeving met het oog op disaster recovery. Het biedt aanbevelingen met betrekking tot herstel na noodgevallen, zoals replicatie en failover.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Hoe werkt servermigratie met siteherstel?

- Als u Azure Migrate: Servermigratie gebruikt om een *agentloze* migratie van on-premises VMware VM's uit te voeren, is migratie native naar Azure Migrate en wordt Siteherstel niet gebruikt.
- Als u Azure Migrate: Servermigratie gebruikt om een op *agent gebaseerde* migratie van VMware VM's uit te voeren of als u Hyper-V VM's of fysieke servers migreert, gebruikt Azure Migrate: ServerMigratie de Azure Site Recovery-replicatieengine.

## <a name="which-geographies-are-supported"></a>Welke regio's worden ondersteund?

Bekijk de ondersteunde regio's voor [publieke](migrate-support-matrix.md#supported-geographies-public-cloud) en [overheidswolken.](migrate-support-matrix.md#supported-geographies-azure-government)

## <a name="how-do-i-get-started"></a>Hoe ga ik aan de slag?

Identificeer het hulpprogramma dat u nodig hebt en voeg het hulpprogramma toe aan een Azure Migrate-project. 

Ga als u een ISV-tool of Movere toevoegt:

1. Ga aan de slag door een licentie te verkrijgen of meld je aan voor een gratis proefperiode, in overeenstemming met het gereedschapsbeleid. Licenties voor gereedschappen zijn in overeenstemming met het ISV- of gereedschapslicentiemodel.
2. In elk hulpprogramma is er een optie om verbinding te maken met Azure Migrate. Volg de hulpprogramma-instructies en -documentatie om het hulpprogramma te verbinden met Azure Migrate.

U uw migratiereis volgen vanuit het Azure Migrate-project, in Azure en in andere hulpprogramma's.

## <a name="how-do-i-delete-a-project"></a>Hoe verwijder ik een project?

Meer informatie over het [verwijderen van een project](how-to-delete-project.md). 

## <a name="next-steps"></a>Volgende stappen

Lees het [overzicht Azure Migreren](migrate-services-overview.md).
