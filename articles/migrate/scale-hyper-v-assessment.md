---
title: Grote aantallen Hyper-V VM's beoordelen voor migratie naar Azure met Azure Migrate | Microsoft Documenten
description: Beschrijft hoe u grote aantallen Hyper-V VM's beoordelen op migratie naar Azure met behulp van de Azure Migrate-service.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: c1ae3a9ed8a775161aaf85ab2c91b1e43113d2e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70279442"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Grote aantallen Hyper-V VM's beoordelen op migratie naar Azure

In dit artikel wordt beschreven hoe u grote aantallen on-premises Hyper-V VM's beoordelen op migratie naar Azure, met behulp van het hulpprogramma Azure Migrate Server Assessment.

[Azure Migrate](migrate-services-overview.md) biedt een hub met hulpprogramma's waarmee u apps, infrastructuur en workloads ontdekken, beoordelen en migreren naar Microsoft Azure. De hub bevat Azure Migrate-hulpprogramma's en isv-aanbiedingen (independent software vendor) van derden. 


In dit artikel leert u het volgende:
> [!div class="checklist"]
> * Plan voor beoordeling op schaal.
> * Configureer Azure-machtigingen en bereid Hyper-V voor op beoordeling.
> * Maak een Azure Migrate-project en maak een beoordeling.
> * Bekijk de beoordeling terwijl u van plan bent migratie.


> [!NOTE]
> Als u een proof-of-concept wilt uitproberen om een paar VM's te beoordelen voordat u op schaal beoordeelt, volgt u onze [zelfstudiereeks](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>Plan voor beoordeling

Bij de planning voor de beoordeling van een groot aantal Hyper-V VM's, zijn er een paar dingen om over na te denken:

- **Azure Migrate-projecten plannen:** zoek uit hoe azure-migratieprojecten kunnen worden geïmplementeerd. Als uw datacenters zich bijvoorbeeld in verschillende regio's bevinden of als u detectie-, beoordelings- of migratiegerelateerde metagegevens in een andere geografie moet opslaan, hebt u mogelijk meerdere projecten nodig.
- **Apparaten plannen**: Azure Migrate gebruikt een on-premises Azure Migrate-toestel, geïmplementeerd als Een Hyper-V VM, om vm's voortdurend te ontdekken voor beoordeling en migratie. Het toestel controleert omgevingswijzigingen, zoals het toevoegen van VM's, schijven of netwerkadapters. Het stuurt ook metagegevens en prestatiegegevens over hen naar Azure. Je moet uitzoeken hoeveel apparaten je moet inzetten.


## <a name="planning-limits"></a>Planningslimieten
 
Gebruik de limieten die in deze tabel zijn samengevat voor planning.

**Planning** | **Grenzen**
--- | --- 
**Azure-migratieprojecten** | Beoordeel tot 35.000 VM's in een project.
**Azure Migrate-apparaat** | Een toestel kan tot 5000 VM's ontdekken.<br/> Een toestel kan verbinding maken met maximaal 300 Hyper-V-hosts.<br/> Een toestel kan alleen worden gekoppeld aan één Azure Migrate-project.<br/> Elk aantal apparaten kan worden gekoppeld aan één Azure Migrate-project. <br/><br/> 
**Groep** | U maximaal 35.000 VM's in één groep toevoegen.
**Azure-migratiebeoordeling** | U maximaal 35.000 VM's beoordelen in één beoordeling.



## <a name="other-planning-considerations"></a>Andere planningsoverwegingen

- Als u de detectie van het apparaat wilt starten, moet u elke Hyper-V-host selecteren. 
- Als u een omgeving met meerdere tenant's uitvoert, u momenteel niet alleen VM's ontdekken die van een specifieke tenant zijn. 

## <a name="prepare-for-assessment"></a>Voorbereiden op beoordeling

Bereid Azure en Hyper-V voor op serverbeoordeling. 

1. Controleer [de vereisten en beperkingen voor Hyper-V-ondersteuningsvereisten.](migrate-support-matrix-hyper-v.md)
2. Machtigingen instellen voor uw Azure-account voor interactie met Azure Migrate
3. Hyper-V-hosts en VM's voorbereiden

Volg de instructies in [deze zelfstudie](tutorial-prepare-hyper-v.md) om deze instellingen te configureren.

## <a name="create-a-project"></a>Een project maken

Ga als volgt te werk in overeenstemming met uw planningsvereisten:

1. Maak een Azure Migrate-projecten.
2. Voeg het hulpprogramma voor azure migrateserverbeoordeling toe aan de projecten.

[Meer informatie](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Een beoordeling maken en beoordelen

1. Maak beoordelingen voor Hyper-V VM's.
1. Bekijk de beoordelingen ter voorbereiding van migratieplanning.

[Meer informatie](tutorial-assess-hyper-v.md) over het maken en beoordelen van beoordelingen.
    

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u het volgende:
 
> [!div class="checklist"] 
> * Gepland om Azure Migrate-beoordelingen voor Hyper-V VM's te schalen
> * Azure en Hyper-V voorbereid voor beoordeling
> * Een Azure Migrate-project maken en beoordelingen uitvoeren
> * Herziene beoordelingen ter voorbereiding van migratie.

Leer [nu hoe](concepts-assessment-calculation.md) beoordelingen worden berekend en hoe [u beoordelingen wijzigen](how-to-modify-assessment.md)
