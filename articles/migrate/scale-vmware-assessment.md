---
title: Grote aantallen VMware VM's beoordelen op migratie naar Azure met Azure Migrate
description: Beschrijft hoe u grote aantallen VMware VM's beoordelen op migratie naar Azure met behulp van de Azure Migrate service.e
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: d404583b1bad474a5e24e8c7cf060aeb80d610bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336858"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Grote aantallen VMware VM's beoordelen op migratie naar Azure


In dit artikel wordt beschreven hoe u grote aantallen (1000-35.000) on-premises Vm's vMware beoordelen voor migratie naar Azure, met behulp van het hulpprogramma azure migrate server assessment.

[Azure Migrate](migrate-services-overview.md) biedt een hub met hulpprogramma's waarmee u apps, infrastructuur en workloads ontdekken, beoordelen en migreren naar Microsoft Azure. De hub bevat Azure Migrate-hulpprogramma's en isv-aanbiedingen (independent software vendor) van derden. 

In dit artikel leert u het volgende:
> [!div class="checklist"]
> * Plan voor beoordeling op schaal.
> * Configureer Azure-machtigingen en bereid VMware voor op beoordeling.
> * Maak een Azure Migrate-project en maak een beoordeling.
> * Bekijk de beoordeling terwijl u van plan bent migratie.


> [!NOTE]
> Als u een proof-of-concept wilt uitproberen om een paar VM's te beoordelen voordat u op schaal beoordeelt, volgt u onze [zelfstudiereeks](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>Plan voor beoordeling

Bij de planning voor de beoordeling van een groot aantal VMware VM's, zijn er een paar dingen om over na te denken:

- **Azure Migrate-projecten plannen:** zoek uit hoe azure-migratieprojecten kunnen worden geïmplementeerd. Als uw datacenters zich bijvoorbeeld in verschillende regio's bevinden of als u detectie-, beoordelings- of migratiegerelateerde metagegevens in een andere geografie moet opslaan, hebt u mogelijk meerdere projecten nodig. 
- **Apparaten plannen**: Azure Migrate gebruikt een on-premises Azure Migrate-toestel, geïmplementeerd als VMware VM, om vm's voortdurend te ontdekken. Het toestel controleert omgevingswijzigingen, zoals het toevoegen van VM's, schijven of netwerkadapters. Het stuurt ook metagegevens en prestatiegegevens over hen naar Azure. Je moet uitzoeken hoeveel apparaten je moet implementeren.
- **Accounts plannen voor detectie**: het Azure Migrate-toestel gebruikt een account met toegang tot vCenter Server om VM's te detecteren voor beoordeling en migratie. Als u meer dan 10.000 VM's ontdekt, stelt u meerdere accounts in.


## <a name="planning-limits"></a>Planningslimieten
 
Gebruik de limieten die in deze tabel zijn samengevat voor planning.

**Planning** | **Grenzen**
--- | --- 
**Azure-migratieprojecten** | Beoordeel tot 35.000 VM's in een project.
**Azure Migrate-apparaat** | Een toestel kan tot 10.000 VM's ontdekken op een vCenter-server.<br/> Een toestel kan alleen verbinding maken met één vCenter-server.<br/> Een toestel kan alleen worden gekoppeld aan één Azure Migrate-project.<br/>  Elk aantal apparaten kan worden gekoppeld aan één Azure Migrate-project. <br/><br/> 
**Groep** | U maximaal 35.000 VM's in één groep toevoegen.
**Azure-migratiebeoordeling** | U maximaal 35.000 VM's beoordelen in één beoordeling.

Met deze limieten in het achterhoofd, hier zijn enkele voorbeeld implementaties:


**vCenter-server** | **VM's op server** | **Aanbeveling** | **Actie**
---|---|---
Eén | < 10.000 | Eén Azure Migrate-project.<br/> Eén apparaat.<br/> Eén vCenter-account voor detectie. | Stel toestel in, maak verbinding met vCenter Server met een account.
Eén | > 10.000 | Eén Azure Migrate-project.<br/> Meerdere apparaten.<br/> Meerdere vCenter-accounts. | Stel een apparaat in voor elke 10.000 VM's.<br/><br/> Stel vCenter-accounts in en verdeel de voorraad om de toegang voor een account te beperken tot minder dan 10.000 VM's.<br/> Verbind elk toestel met vCenter-server met een account.<br/> U afhankelijkheden analyseren tussen machines die met verschillende apparaten worden gedetecteerd.
Meerdere | < 10.000 |  Eén Azure Migrate-project.<br/> Meerdere apparaten.<br/> Eén vCenter-account voor detectie. | Apparaten instellen, verbinding maken met vCenter Server met een account.<br/> U afhankelijkheden analyseren tussen machines die met verschillende apparaten worden gedetecteerd.
Meerdere | > 10.000 | Eén Azure Migrate-project.<br/> Meerdere apparaten.<br/> Meerdere vCenter-accounts. | Als vCenter Server-detectie < 10.000 VM's, stelt u een toestel in voor elke vCenter-server.<br/><br/> Als vCenter Server detectie > 10.000 VM's, stelt u een toestel in voor elke 10.000 VM's.<br/> Stel vCenter-accounts in en verdeel de voorraad om de toegang voor een account te beperken tot minder dan 10.000 VM's.<br/> Verbind elk toestel met vCenter-server met een account.<br/> U afhankelijkheden analyseren tussen machines die met verschillende apparaten worden gedetecteerd.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Detectie plannen in een omgeving met meerdere huurders

Als u een omgeving met meerdere tenant's wilt, u de detectie op de vCenterserver uitvoeren.

- U het detectiebereik van het toestel instellen op een vCenter Server-datacenters, clusters of mappen met clusters, hosts of mappen met hosts of afzonderlijke VM's.
- Als uw omgeving wordt gedeeld tussen tenants en u elke tenant afzonderlijk wilt ontdekken, u toegang tot het vCenter-account dat het toestel gebruikt voor detectie, openen. 
    - U het bereik per VM-map bekijken als de tenants hosts delen. Azure Migrate kan vm's niet ontdekken als het vCenter-account toegang heeft op vCenter VM-mapniveau. Als u uw detectie wilt scopen met VM-mappen, u dit doen door ervoor te zorgen dat het vCenter-account alleen-lezen toegang heeft toegewezen op VM-niveau. [Meer informatie](set-discovery-scope.md).

## <a name="prepare-for-assessment"></a>Voorbereiden op beoordeling

Bereid Azure en VMware voor op serverbeoordeling. 

1. Controleer [de ondersteuningsvereisten en -beperkingen van VMware.](migrate-support-matrix-vmware.md)
2. Machtigingen instellen voor uw Azure-account voor interactie met Azure Migrate.
3. VMware voorbereiden op beoordeling.

Volg de instructies in [deze zelfstudie](tutorial-prepare-vmware.md) om deze instellingen te configureren.


## <a name="create-a-project"></a>Een project maken

Ga als volgt te werk in overeenstemming met uw planningsvereisten:

1. Maak een Azure Migrate-projecten.
2. Voeg het hulpprogramma voor azure migrateserverbeoordeling toe aan de projecten.

[Meer informatie](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Een beoordeling maken en beoordelen

1. Maak beoordelingen voor VMware VM's.
1. Bekijk de beoordelingen ter voorbereiding van migratieplanning.


Volg de instructies in [deze zelfstudie](tutorial-assess-vmware.md) om deze instellingen te configureren.
    

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u het volgende:
 
> [!div class="checklist"] 
> * Gepland om Azure Migrate-beoordelingen voor VMware VM's te schalen
> * Azure en VMware voorbereid voor beoordeling
> * Een Azure Migrate-project maken en beoordelingen uitvoeren
> * Herziene beoordelingen ter voorbereiding van migratie.

[Nu, leren hoe](concepts-assessment-calculation.md) beoordelingen worden berekend, en hoe beoordelingen te [wijzigen](how-to-modify-assessment.md).
