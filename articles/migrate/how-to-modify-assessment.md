---
title: Beoordelingen aanpassen voor Azure Migrate Server Assessment | Microsoft Documenten
description: Beschrijft hoe u beoordelingen aanpassen die zijn gemaakt met Azure Migrate Server Assessment
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 2cfac978b0a5af20e9e2fa1e32a7361488f20fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68234272"
---
# <a name="customize-an-assessment"></a>Een beoordeling aanpassen

In dit artikel wordt beschreven hoe beoordelingen die zijn gemaakt door Azure Migrate Server Assessment kunnen worden aangepast.

[Azure Migrate](migrate-services-overview.md) biedt een centrale hub om detectie, beoordeling en migratie van uw on-premises apps en workloads en private/public cloud VM's naar Azure bij te houden. De hub biedt Azure Migrate-tools voor beoordeling en migratie, evenals isv-aanbiedingen (onafhankelijke softwareleveranciers) van derden.

U het hulpprogramma azure migrate server assessment gebruiken om beoordelingen te maken voor on-premises Vm's vMware en Hyper-VMs, ter voorbereiding op migratie naar Azure.

## <a name="about-assessments"></a>Over beoordelingen

Er zijn twee typen beoordelingen die u uitvoeren met Azure Migrate Server Assessment.

**Beoordeling** | **Details** | **Gegevens**
--- | --- | ---
**Op prestaties gebaseerd** | Beoordelingen op basis van verzamelde prestatiegegevens | **Aanbevolen VM-grootte:** gebaseerd op cpu- en geheugengebruiksgegevens.<br/><br/> **Aanbevolen schijftype (standaard of premium beheerde schijf):** op basis van de IOPS en doorvoer van de on-premises schijven.
**Als on-premises** | Beoordelingen op basis van on-premises dimensionering. | **Aanbevolen VM-grootte:** op basis van de on-premises VM-grootte<br/><br> **Aanbevolen schijftype:** op basis van de instelling voor het opslagtype die u voor de beoordeling selecteert.


## <a name="how-is-an-assessment-done"></a>Hoe wordt een beoordeling uitgevoerd?

Een beoordeling in Azure Migrate Server Assessment heeft drie fasen. Beoordeling begint met een geschiktheidsanalyse, gevolgd door het dimensioneren en ten slotte een maandelijkse kostenschatting. Een machine beweegt alleen mee naar een later stadium als deze de vorige passeert. Als een machine bijvoorbeeld niet voldoet aan de Azure-geschiktheidscontrole, wordt deze gemarkeerd als ongeschikt voor Azure en wordt de grootte en kosten niet uitgevoerd. [Meer informatie.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-assessment"></a>Waaruit bestaat een evaluatie?

**Eigenschap** | **Details**
--- | ---
**Doellocatie** | De Azure-locatie waarnaar u wilt migreren.<br/> Server Assessment ondersteunt momenteel deze doelregio's: Australië Oost, Australië Zuidoost, Brazilië Zuid, Canada Centraal, Canada Oost, Centraal-India, Centraal-VS, China-Oost, China Noord, Oost-Azië, Oost-VS, Oost-VS2, Duitsland Centraal, Duitsland Noordoost, Japan East, Japan West, Korea Central, Korea South, North Central US, North Europe, South Central US, Southeast Asia, South India, UK South, UK West, US Gov Arizona, US Gov Texas, US Gov Virginia, West Central US, West-Europa, West India, West US en West US2.
**Opslagtype** | U deze eigenschap gebruiken om in Azure het type schijven op te geven waarnaar u wilt verplaatsen.<br/><br/> Voor as-on-premises grootte u het doelopslagtype opgeven als door Premium beheerde schijven, standaard schijven met SSD-beheerde schijven of standaard schijven die door hdd's worden beheerd. Voor prestatiegebaseerde grootte u het doelschijftype opgeven als automatische schijven, schijven met een premium beheerde schijf, standaard schijven met hdd-beheerde schijven of standaard schijven die door ssd's worden beheerd.<br/><br/> Wanneer u het opslagtype als automatisch opgeeft, wordt de schijfaanbeveling uitgevoerd op basis van de prestatiegegevens van de schijven (IOPS en doorvoer). Als u het opslagtype als premium/standaard opgeeft, wordt in de beoordeling een schijf-SKU aanbevolen binnen het geselecteerde opslagtype. Als u een VM-SLA van 99,9% van één exemplaar wilt bereiken, u het opslagtype opgeven als schijven die door Premium worden beheerd. Dit zorgt ervoor dat alle schijven in de beoordeling worden aanbevolen als schijven die door Premium worden beheerd. Azure
**Gereserveerde exemplaren (RI)** | Met deze eigenschap u opgeven of u [Gereserveerde exemplaren](https://azure.microsoft.com/pricing/reserved-vm-instances/) in Azure hebt, worden kostenschattingen in de beoordeling vervolgens uitgevoerd met RI-kortingen. Gereserveerde exemplaren worden momenteel alleen ondersteund voor betalen per gebruik-aanbieding in Azure Migrate.
**Criterium voor het aanpassen van de grootte** | Het criterium dat moet worden gebruikt voor vm's van de juiste grootte voor Azure. U *prestatiegerichte* maatvoering uitvoeren of de VM's *als on-premises*aanpassen, zonder rekening te houden met de prestatiegeschiedenis.
**Prestatiegeschiedenis** | De duur die moet worden overwogen voor de evaluatie van de prestatiegegevens van machines. Deze eigenschap is alleen van toepassing wanneer het maatcriterium *op basis van prestaties is gebaseerd.*
**Percentiel gebruik** | De percentielwaarde van de prestatievoorbeeldset waarmee rekening moet worden gehouden voor een juiste groottebepaling. Deze eigenschap is alleen van toepassing wanneer de grootte *op basis van prestaties is gebaseerd.*
**VM-reeks** |     U kunt de VM-reeks opgeven die u wilt overwegen voor de juiste grootte. Als u bijvoorbeeld een productieomgeving hebt die u niet van plan bent te migreren naar VM's uit de A-serie in Azure, u A-reeksen uitsluiten van de lijst of serie en wordt de juiste grootte alleen in de geselecteerde reeks uitgevoerd.
**Comfortfactor** | Azure Migrate Server Assessment houdt rekening met een buffer (comfortfactor) tijdens de beoordeling. Deze buffer wordt toegepast boven op de gegevens over machinegebruik voor VM's (CPU, geheugen, schijf en netwerk). De comfortfactor houdt rekening met factoren zoals seizoensgebonden gebruik, een korte prestatiegeschiedenis en een mogelijke gebruikstoename in de toekomst.<br/><br/> Een VM met 10 kernen en een gebruik van 20% komt bijvoorbeeld gewoonlijk overeen met een VM met 2 kernen. Met een comfortfactor van 2,0x is het resultaat echter een VM met 4 kernen.
**Bieden** | De [Azure-aanbieding](https://azure.microsoft.com/support/legal/offer-details/) waarop u bent geregistreerd. Azure Migrate maakt dienovereenkomstig een schatting van de kosten.
**Valuta** | Factureringsvaluta.
**Korting (%)** | Een abonnement-specifieke korting die u bovenop de Azure-aanbieding ontvangt.<br/> De standaardinstelling is 0%.
**VM tijd actief** | Als uw VM's niet 24x7 in Azure worden uitgevoerd, u de duur (aantal dagen per maand en aantal uren per dag) opgeven waarvoor ze worden uitgevoerd en de kostenschattingen dienovereenkomstig worden uitgevoerd.<br/> De standaardwaarde is 31 dagen per maand en 24 uur per dag.
**Azure Hybrid Benefit** | Geef op of u softwarezekerheid hebt en in aanmerking komt voor [Azure Hybrid Benefit.](https://azure.microsoft.com/pricing/hybrid-use-benefit/) Als deze optie is ingesteld op Ja, worden niet-Windows Azure-prijzen voor Windows-VM's gehanteerd. Standaard is Ja.


## <a name="edit-assessment-properties"></a>Beoordelingseigenschappen bewerken

Ga als volgt te werk om beoordelingseigenschappen te bewerken na het maken van een beoordeling:

1. Klik in het Azure Migrate-project op **Servers**.
2. Klik in **Azure Migrate: Serverbeoordeling**op het aantal beoordelingen.
3. Klik **in Assessment**op de relevante beoordeling > Eigenschappen **bewerken**.
5. Pas de beoordelingseigenschappen aan in overeenstemming met de bovenstaande tabel.
6. Klik **op Opslaan** om de beoordeling bij te werken.


U beoordelingseigenschappen ook bewerken wanneer u een beoordeling maakt.


## <a name="next-steps"></a>Volgende stappen

[Meer informatie](concepts-assessment-calculation.md) over hoe evaluaties worden berekend.
