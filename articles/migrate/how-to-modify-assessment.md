---
title: Beoordelingen aanpassen voor de evaluatie van Azure Migrate server | Microsoft Docs
description: Hierin wordt beschreven hoe u de evaluaties aanpast die zijn gemaakt met Azure Migrate server evaluatie
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 2cfac978b0a5af20e9e2fa1e32a7361488f20fbe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "68234272"
---
# <a name="customize-an-assessment"></a>Een beoordeling aanpassen

In dit artikel wordt beschreven hoe u de evaluaties aanpast die zijn gemaakt door Azure Migrate server Assessment.

[Azure migrate](migrate-services-overview.md) biedt een centrale hub voor het bijhouden van de detectie, beoordeling en migratie van uw on-premises apps en workloads, en persoonlijke/open bare Cloud-Vm's naar Azure. De hub biedt Azure Migrate-hulpprogram ma's voor evaluatie en migratie, evenals onafhankelijke ISV'S-aanbiedingen (Independent Software Vendor) van derden.

U kunt het hulp programma voor het evalueren van Azure Migrate-server gebruiken om evaluaties te maken voor on-premises virtuele VMware-machines en virtuele Hyper-V-machines, in voor bereiding op de migratie naar Azure.

## <a name="about-assessments"></a>Over evaluaties

Er zijn twee soorten evaluaties die u kunt uitvoeren met behulp van Azure Migrate server-evaluatie.

**Beoordeling** | **Nadere** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties op basis van verzamelde prestatie gegevens | **Aanbevolen VM-grootte**: op basis van gegevens van CPU en geheugen gebruik.<br/><br/> **Aanbevolen schijf type (Standard of Premium Managed disk)**: op basis van de IOPS en door Voer van de on-premises schijven.
**Als on-premises** | Evaluaties op basis van on-premises grootte. | **Aanbevolen VM-grootte**: op basis van de on-PREMISes VM-grootte<br/><br> **Aanbevolen schijf type**: op basis van de instelling voor het opslag type die u voor de evaluatie selecteert.


## <a name="how-is-an-assessment-done"></a>Hoe wordt een evaluatie uitgevoerd?

Een beoordeling die in Azure Migrate server beoordeling is uitgevoerd, heeft drie fasen. De evaluatie begint met een geschiktheids analyse, gevolgd door het aanpassen van de grootte, en ten slotte een maandelijkse schatting van de kosten. Een machine wordt alleen verplaatst naar een latere fase als deze de voor gaande wordt door gegeven. Als een machine bijvoorbeeld de Azure-geschiktheids controle niet kan uitvoeren, wordt deze gemarkeerd als ongeschikt voor Azure en wordt de grootte en de kosten voor de berekening niet gewijzigd. [Meer informatie.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-assessment"></a>Waaruit bestaat een evaluatie?

**Eigenschap** | **Nadere**
--- | ---
**Doel locatie** | De Azure-locatie waarnaar u wilt migreren.<br/> Server analyse biedt momenteel ondersteuning voor deze doel regio's: Australië-oost, Australië-zuidoost, Brazilië-zuid, Canada-centraal, Canada-oost, Centraal-India, centraal VS, China-oost, China-noord, Azië-oost, VS-Oost, Oost-VS2, Duitsland-centraal, Duitsland-noordoost, Japan-Oost, Japan-West, Korea-centraal, Korea-Zuid, Noord-Centraal VS, Europa-Noord, Zuid-Centraal VS, Zuidoost-Azië, India-zuid, UK-zuid, UK-west US Gov-Arizona , US Gov-Texas, US Gov-Virginia, VS-West-Centraal, Europa-west, West-India, VS-West en West-VS2.
**Opslagtype** | U kunt deze eigenschap gebruiken om het type schijven op te geven dat u wilt verplaatsen in Azure.<br/><br/> Voor de inrichtings grootte kunt u het type doel opslag opgeven als een Premium-beheerde schijf, Standard-SSD-beheerde schijven of Standard-HDD-beheerde schijven. Voor een schaal op basis van prestaties kunt u het type doel schijf opgeven als automatische, Premium beheerde schijven, Standard-HDD-beheerde schijven of Standard-SSD-beheerde schijven.<br/><br/> Wanneer u het opslag type opgeeft als automatisch, wordt de aanbevolen schijf uitgevoerd op basis van de prestatie gegevens van de schijven (IOPS en door Voer). Als u het opslag type opgeeft als Premium/standaard, wordt een schijf-SKU aanbevolen binnen het geselecteerde opslag type. Als u een VM-SLA met één exemplaar van 99,9% wilt uitvoeren, kunt u het opslag type opgeven als Premium-beheerde schijven. Dit zorgt ervoor dat alle schijven in de evaluatie worden aanbevolen als Premium-beheerde schijven. Azure
**Gereserveerde instanties (RI)** | Met deze eigenschap kunt u opgeven of u [gereserveerde instanties](https://azure.microsoft.com/pricing/reserved-vm-instances/) in azure hebt, kosten ramingen in de beoordeling worden uitgevoerd in RI-kortingen. Gereserveerde instanties worden momenteel alleen ondersteund voor de aanbieding betalen naar gebruik in Azure Migrate.
**Criterium voor het aanpassen van de grootte** | Het criterium dat moet worden gebruikt om virtuele machines met een juiste grootte te gebruiken voor Azure. U kunt de grootte van de virtuele machines aanpassen op *basis van de prestaties* , zonder rekening te *houden*met de prestatie geschiedenis.
**Prestatiegeschiedenis** | De duur die u moet overwegen om de prestatie gegevens van machines te evalueren. Deze eigenschap is alleen van toepassing als het formaat van het criterium *op basis van prestaties*is.
**Percentiel gebruik** | De percentielwaarde van de prestatievoorbeeldset waarmee rekening moet worden gehouden voor een juiste groottebepaling. Deze eigenschap is alleen van toepassing wanneer de grootte van het formaat *op basis van prestaties*is.
**VM-reeks** |     U kunt de VM-reeks opgeven die u wilt overwegen voor de juiste grootte. Als u bijvoorbeeld een productie omgeving hebt die u niet van plan bent om te migreren naar A-serie Vm's in azure, kunt u een-serie uitsluiten van de lijst of reeks en wordt de rechter grootte alleen uitgevoerd in de geselecteerde reeks.
**Comfortfactor** | Bij het evalueren van Azure Migrate-server wordt een buffer (comfort factor) in rekening gehouden tijdens de evaluatie. Deze buffer wordt toegepast boven op de gegevens over machinegebruik voor VM's (CPU, geheugen, schijf en netwerk). De comfortfactor houdt rekening met factoren zoals seizoensgebonden gebruik, een korte prestatiegeschiedenis en een mogelijke gebruikstoename in de toekomst.<br/><br/> Een VM met 10 kernen en een gebruik van 20% komt bijvoorbeeld gewoonlijk overeen met een VM met 2 kernen. Met een comfortfactor van 2,0x is het resultaat echter een VM met 4 kernen.
**Aanbieding** | De [Azure-aanbieding](https://azure.microsoft.com/support/legal/offer-details/) waarop u bent geregistreerd. Azure Migrate maakt dienovereenkomstig een schatting van de kosten.
**Valuta** | Factureringsvaluta.
**Korting (%)** | Een abonnement-specifieke korting die u bovenop de Azure-aanbieding ontvangt.<br/> De standaardinstelling is 0%.
**VM tijd actief** | Als uw Vm's niet 24x7 worden uitgevoerd in azure, kunt u de duur (aantal dagen per maand en het aantal uren per dag) opgeven waarvoor ze worden uitgevoerd en de kosten ramingen dienovereenkomstig worden uitgevoerd.<br/> De standaardwaarde is 31 dagen per maand en 24 uur per dag.
**Azure Hybrid Benefit** | Geef op of u Software Assurance hebt en in aanmerking komt voor [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Als deze optie is ingesteld op Ja, worden niet-Windows Azure-prijzen voor Windows-VM's gehanteerd. Standaard is Ja.


## <a name="edit-assessment-properties"></a>Beoordelings eigenschappen bewerken

Ga als volgt te werk om de evaluatie-eigenschappen te bewerken nadat u een evaluatie hebt gemaakt:

1. Klik in het Azure Migrate project op **servers**.
2. Klik in **Azure migrate: Server evaluatie**op het aantal evaluaties.
3. Klik in de **beoordeling**op de relevante evaluatie > **Eigenschappen bewerken**.
5. Pas de eigenschappen van de evaluatie aan overeenkomstig de bovenstaande tabel.
6. Klik op **Opslaan** om de evaluatie bij te werken.


U kunt ook de evaluatie-eigenschappen bewerken tijdens het maken van een evaluatie.


## <a name="next-steps"></a>Volgende stappen

[Meer informatie](concepts-assessment-calculation.md) over hoe evaluaties worden berekend.
