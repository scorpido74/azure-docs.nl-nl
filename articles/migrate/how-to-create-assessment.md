---
title: Een beoordeling maken met Azure Migrate Server Assessment | Microsoft Documenten
description: Beschrijft hoe u een beoordeling maakt met het azure migrate server assessment-hulpprogramma
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: cffde2a677650387dffd19733e082ff7002ccb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68229100"
---
# <a name="create-an-assessment"></a>Een evaluatie maken

In dit artikel wordt beschreven hoe u een beoordeling maakt voor on-premises Vm's vMware of Hyper-V VM's met Azure Migrate: Server Assessment.

[Azure Migreren](migrate-services-overview.md) helpt u te migreren naar Azure. Azure Migrate biedt een gecentraliseerde hub om detectie, beoordeling en migratie van on-premises infrastructuur, toepassingen en gegevens naar Azure bij te houden. De hub biedt Azure-hulpprogramma's voor beoordeling en migratie, evenals isv-aanbiedingen (onafhankelijke softwareleveranciers) van derden. 

## <a name="before-you-start"></a>Voordat u begint

- Zorg ervoor dat u een Azure Migrate-project hebt [gemaakt.](how-to-add-tool-first-time.md)
- Als u al een project hebt gemaakt, controleert u of u het hulpprogramma Azure Migreren: Serverbeoordeling hebt [toegevoegd.](how-to-assess.md)
- Als u een beoordeling wilt maken, moet u een Azure Migrate-toestel instellen voor [VMware](how-to-set-up-appliance-vmware.md) of [Hyper-V.](how-to-set-up-appliance-hyper-v.md) Het toestel detecteert on-premises machines en stuurt metagegevens en prestatiegegevens naar Azure Migrate: Server Assessment. [Meer informatie](migrate-appliance.md).


## <a name="assessment-overview"></a>Evaluatieoverzicht
Er zijn twee typen beoordelingen die u maken met Azure Migrate: Serverassessment.

**Beoordeling** | **Details** | **Gegevens**
--- | --- | ---
**Op prestaties gebaseerd** | Beoordelingen op basis van verzamelde prestatiegegevens | **Aanbevolen VM-grootte:** gebaseerd op cpu- en geheugengebruiksgegevens.<br/><br/> **Aanbevolen schijftype (standaard of premium beheerde schijf):** op basis van de IOPS en doorvoer van de on-premises schijven.
**Als on-premises** | Beoordelingen op basis van on-premises dimensionering. | **Aanbevolen VM-grootte:** op basis van de on-premises VM-grootte<br/><br> **Aanbevolen schijftype:** op basis van de instelling voor het opslagtype die u voor de beoordeling selecteert.

[Meer informatie](concepts-assessment-calculation.md) over beoordelingen.

## <a name="run-an-assessment"></a>Een evaluatie uitvoeren

Voer een beoordeling als volgt uit:

1. Bekijk de [aanbevolen procedures](best-practices-assessment.md) voor het maken van beoordelingen.
2. Klik op het tabblad **Servers** in **Azure Migreren:** tegel Serverbeoordeling op **Beoordelen**.

    ![Evalueren](./media/how-to-create-assessment/assess.png)

2. Geef **in Servers beoordelen**een naam op voor de beoordeling.
3. Klik op **Alles weergeven** om de evaluatie-eigenschappen te controleren.

    ![Beoordelingseigenschappen](./media/how-to-create-assessment//view-all.png)

3. Selecteer in **Een groep selecteren of maken**de optie Nieuw **maken**en geef een groepsnaam op. Een groep verzamelt een of meer VM's voor beoordeling.
4. Selecteer vm's die u aan de groep wilt toevoegen in **Machines toevoegen.**
5. Klik **op Beoordeling maken** om de groep te maken en voer de beoordeling uit.

    ![Een evaluatie maken](./media/how-to-create-assessment//assessment-create.png)

6. Nadat de beoordeling is gemaakt, bekijkt u deze in **Servers** > **Azure Migrate: Server Assessment** > **Assessments**.
7. Klik op **Evaluatie exporteren** om deze te downloaden als een Excel-bestand.



## <a name="review-an-assessment"></a>Een beoordeling beoordelen

Een beoordeling beschrijft:

- **Azure-gereedheid**: of VM's geschikt zijn voor migratie naar Azure.
- **Schatting van de maandelijkse kosten:** de geschatte maandelijkse reken- en opslagkosten voor het uitvoeren van de VM's in Azure.
- **Schatting van de maandelijkse opslagkosten**: Geschatte kosten voor schijfopslag na migratie.

### <a name="view-an-assessment"></a>Bekijk een beoordeling

1. Klik in **Servers voor migratiedoelen** >  **Servers**op **Beoordelingen** in **Azure Migreren: Serverbeoordeling**.
2. Klik in **Assessments**op een beoordeling om deze te openen.

    ![Evaluatiesamenvatting](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure-gereedheid controleren

1. Controleer in **Azure-gereedheid**of VM's klaar zijn voor migratie naar Azure.
2. Controleer de VM-status:
    - **Klaar voor Azure:** Azure Migrate beveelt in de beoordeling een VM-grootte en kostenramingen voor VM's aan.
    - **Klaar met voorwaarden**: Toont problemen en voorgestelde herstel.
    - **Niet klaar voor Azure:** toont problemen en voorgestelde herstel.
    - **Gereedheid onbekend**: wordt gebruikt wanneer Azure Migrate de gereedheid niet kan beoordelen vanwege problemen met de beschikbaarheid van gegevens.

2. Klik op een **Azure-gereedheidsstatus.** U de gereedheidsgegevens van vm's bekijken en inzoomen op VM-details, waaronder rekenkracht, opslag en netwerkinstellingen.



### <a name="review-cost-details"></a>Kostendetails bekijken

In deze weergave worden de geschatte reken- en opslagkosten weergegeven voor het uitvoeren van VM's in Azure.

1. Bekijk de maandelijkse reken- en opslagkosten. De kosten worden geaggregeerd voor alle VM's in de beoordeelde groep.

    - Kostenramingen zijn gebaseerd op de grootteaanbevelingen voor een machine en de schijven en eigenschappen ervan.
    - Geschatte maandelijkse kosten voor rekenkracht en opslag worden weergegeven.
    - De kostenschatting is voor het uitvoeren van de on-premises VM's als IaaS VM's. Azure Migrate Server Assessment houdt geen rekening met paas- of SaaS-kosten.

2. U schattingen van de maandelijkse opslagkosten bekijken. Deze weergave toont geaggregeerde opslagkosten voor de beoordeelde groep, verdeeld over verschillende soorten opslagschijven.
3. U inzoomen om details voor specifieke VM's te bekijken.


### <a name="review-confidence-rating"></a>Betrouwbaarheidsclassificatie controleren

Wanneer u op prestaties gebaseerde beoordelingen uitvoert, wordt een betrouwbaarheidsbeoordeling toegewezen aan de beoordeling.

![Betrouwbaarheidsclassificatie](./media/how-to-create-assessment/confidence-rating.png)

- Een beoordeling van 1-sterren (laagste) tot 5-sterren (hoogste) wordt toegekend.
- Met de betrouwbaarheidsclassificatie u de betrouwbaarheid inschatten van de aanbevelingen die door de beoordeling worden gegeven.
- De betrouwbaarheidsbeoordeling is gebaseerd op de beschikbaarheid van gegevenspunten die nodig zijn om de beoordeling te berekenen.

Vertrouwensbeoordelingen voor een beoordeling zijn als volgt.

**Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
--- | ---
0%-20% | 1 ster
21%-40% | 2 sterren
41%-60% | 3 sterren
61%-80% | 4 sterren
81%-100% | 5 sterren




## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik [van afhankelijkheidstoewijzing](how-to-create-group-machine-dependencies.md) om groepen met een hoog vertrouwen te maken.
- [Meer informatie](concepts-assessment-calculation.md) over hoe evaluaties worden berekend.
