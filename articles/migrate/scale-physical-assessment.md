---
title: Grote aantallen fysieke servers beoordelen voor migratie naar Azure met Azure Migrate | Microsoft Documenten
description: Beschrijft hoe u grote aantallen fysieke servers beoordelen op migratie naar Azure met behulp van de Azure Migrate-service.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/19/2020
ms.author: hamusa
ms.openlocfilehash: a19a1b6e7416667079ab07fc5440ee8828c26bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76294368"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>Grote aantallen fysieke servers beoordelen op migratie naar Azure

In dit artikel wordt beschreven hoe u grote aantallen on-premises fysieke servers beoordelen op migratie naar Azure, met behulp van het hulpprogramma azure migrate server assessment.

[Azure Migrate](migrate-services-overview.md) biedt een hub met hulpprogramma's waarmee u apps, infrastructuur en workloads ontdekken, beoordelen en migreren naar Microsoft Azure. De hub bevat Azure Migrate-hulpprogramma's en isv-aanbiedingen (independent software vendor) van derden. 


In dit artikel leert u het volgende:
> [!div class="checklist"]
> * Plan voor beoordeling op schaal.
> * Configureer Azure-machtigingen en bereid fysieke servers voor op beoordeling.
> * Maak een Azure Migrate-project en maak een beoordeling.
> * Bekijk de beoordeling terwijl u van plan bent migratie.


> [!NOTE]
> Als u een proof-of-concept wilt uitproberen om een paar servers te beoordelen voordat u op schaal beoordeelt, volgt u onze [zelfstudiereeks.](tutorial-prepare-physical.md)

## <a name="plan-for-assessment"></a>Plan voor beoordeling

Bij de planning voor de beoordeling van een groot aantal fysieke servers, zijn er een paar dingen om over na te denken:

- **Azure Migrate-projecten plannen:** zoek uit hoe azure-migratieprojecten kunnen worden geïmplementeerd. Als uw datacenters zich bijvoorbeeld in verschillende regio's bevinden of als u detectie-, beoordelings- of migratiegerelateerde metagegevens in een andere geografie moet opslaan, hebt u mogelijk meerdere projecten nodig.
- **Apparaten plannen**: Azure Migrate gebruikt een on-premises Azure Migrate-toestel, geïmplementeerd op een Windows-machine, om voortdurend servers te detecteren voor beoordeling en migratie. Het toestel controleert omgevingswijzigingen, zoals het toevoegen van VM's, schijven of netwerkadapters. Het stuurt ook metagegevens en prestatiegegevens over hen naar Azure. Je moet uitzoeken hoeveel apparaten je moet inzetten.


## <a name="planning-limits"></a>Planningslimieten
 
Gebruik de limieten die in deze tabel zijn samengevat voor planning.

**Planning** | **Grenzen**
--- | --- 
**Azure-migratieprojecten** | Beoordeel tot 35.000 servers in een project.
**Azure Migrate-apparaat** | Een toestel kan tot 250 servers detecteren.<br/> Een toestel kan alleen worden gekoppeld aan één Azure Migrate-project.<br/> Elk aantal apparaten kan worden gekoppeld aan één Azure Migrate-project. <br/><br/> 
**Groep** | U maximaal 35.000 servers in één groep toevoegen.
**Azure-migratiebeoordeling** | U maximaal 35.000 servers in één beoordeling beoordelen.


## <a name="other-planning-considerations"></a>Andere planningsoverwegingen

- Als u de detectie van het toestel wilt starten, moet u elke fysieke server selecteren. 

## <a name="prepare-for-assessment"></a>Voorbereiden op beoordeling

Bereid Azure en fysieke servers voor op serverbeoordeling. 

1. Controleer [de vereisten en beperkingen voor fysieke serverondersteuning](migrate-support-matrix-physical.md).
2. Machtigingen instellen voor uw Azure-account voor interactie met Azure Migrate.
3. Bereid de fysieke servers voor.

Volg de instructies in [deze zelfstudie](tutorial-prepare-physical.md) om deze instellingen te configureren.

## <a name="create-a-project"></a>Een project maken

Ga als volgt te werk in overeenstemming met uw planningsvereisten:

1. Maak een Azure Migrate-project.
2. Voeg het hulpprogramma voor azure migrateserverbeoordeling toe aan de projecten.

[Meer informatie](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Een beoordeling maken en beoordelen

1. Maak beoordelingen voor fysieke servers.
1. Bekijk de beoordelingen ter voorbereiding van migratieplanning.

[Meer informatie](tutorial-assess-physical.md) over het maken en beoordelen van beoordelingen.
    

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u het volgende:
 
> [!div class="checklist"] 
> * Gepland om Azure Migrate-beoordelingen voor fysieke servers te schalen.
> * Bereid Azure en fysieke servers voor beoordeling.
> * Een Azure Migrate-project gemaakt en beoordelingen uitvoeren.
> * Herziene beoordelingen ter voorbereiding van migratie.

[Nu, leren hoe](concepts-assessment-calculation.md) beoordelingen worden berekend, en hoe beoordelingen te [wijzigen](how-to-modify-assessment.md).
