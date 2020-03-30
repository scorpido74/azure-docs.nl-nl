---
title: Assessment best practices in Azure Migrate Server Assessment
description: Tips voor het maken van beoordelingen met Azure Migrate Server Assessment.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: de6953b6648613595bc9975b17941b3a453a6d60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185986"
---
# <a name="best-practices-for-creating-assessments"></a>Aanbevolen procedures voor het maken van beoordelingen

[Azure Migrate](migrate-overview.md) biedt een hub met hulpprogramma's waarmee u apps, infrastructuur en workloads ontdekken, beoordelen en migreren naar Microsoft Azure. De hub bevat Azure Migrate-hulpprogramma's en isv-aanbiedingen (independent software vendor) van derden.

In dit artikel worden aanbevolen procedures samengevat bij het maken van beoordelingen met behulp van het hulpprogramma azure migrate server assessment.

## <a name="about-assessments"></a>Over beoordelingen

Beoordelingen die u maakt met Azure Migrate Server Assessment zijn een point-in-time momentopname van gegevens. Er zijn twee typen beoordelingen in Azure Migrate.

**Beoordelingstype** | **Details** | **Gegevens**
--- | --- | ---
**Op prestaties gebaseerd** | Beoordelingen die aanbevelingen doen op basis van verzamelde prestatiegegevens | Vm-grootte aanbeveling is gebaseerd op CPU en geheugen gebruik gegevens.<br/><br/> Schijftypeaanbeveling (standaard HDD/SSD of premium-managed schijven) is gebaseerd op het IOPS en de doorvoer van de on-premises schijven.
**As-is on-premises** | Beoordelingen die geen prestatiegegevens gebruiken om aanbevelingen te doen. | Vm-grootteaanbeveling is gebaseerd op de on-premises VM-grootte<br/><br> Het aanbevolen schijftype is gebaseerd op wat u selecteert in de instelling voor opslagtype voor de beoordeling.

### <a name="example"></a>Voorbeeld
Als u bijvoorbeeld een on-premises VM hebt met vier cores bij een gebruik van 20% en geheugen van 8 GB met 10% gebruik, zijn de beoordelingen als volgt:

- **Prestatiegebaseerde beoordeling**:
    - Identificeert effectieve kernen en geheugen op basis van kerngebruik (4 x 0,20 = 0,8) en geheugen (8 GB x 0,10 = 0,8) gebruik.
    - Hiermee past u de comfortfactor toe die is opgegeven in beoordelingseigenschappen (laten we zeggen 1,3x) om de waarden te krijgen die moeten worden gebruikt voor het dimensioneren. 
    - Beveelt de dichtstbijzijnde VM-grootte in Azure aan die ~ 1,04-cores (0,8 x 1,3) en ~ 1,04 GB (0,8 x 1,3) geheugen kan ondersteunen.

- **As-is (als on-premises) beoordeling**:
    -  Beveelt een VM met vier cores aan; 8 GB geheugen.

## <a name="best-practices-for-creating-assessments"></a>Aanbevolen procedures voor het maken van beoordelingen

Het Azure Migrate-toestel profileert continu uw on-premises omgeving en verzendt metagegevens en prestatiegegevens naar Azure. Volg deze aanbevolen procedures voor beoordelingen van servers die met een toestel zijn ontdekt:

- **As-is-assessments maken:** U as-is-beoordelingen maken zodra uw machines worden weergegeven in de Azure Migrate-portal.
- **Prestatiegebaseerde beoordeling maken:** Na het instellen van detectie raden we u aan ten minste een dag te wachten voordat u een prestatiegebaseerde beoordeling uitvoert:
    - Het verzamelen van prestatiegegevens kost tijd. Het wachten op ten minste een dag zorgt ervoor dat er voldoende prestatiegegevenspunten zijn voordat u de beoordeling uitvoert.
    - Wanneer u prestatiegebaseerde beoordelingen uitvoert, moet u ervoor zorgen dat u uw omgeving profileert voor de beoordelingsduur. Als u bijvoorbeeld een beoordeling maakt met een prestatieduur ingesteld op één week, moet u ten minste een week na de detectie wachten op alle gegevenspunten die moeten worden verzameld. Als je dat niet doet, krijgt de beoordeling geen beoordeling van vijf sterren.
- **Beoordelingen opnieuw berekenen:** Aangezien beoordelingen point-in-time-momentopnamen zijn, worden ze niet automatisch bijgewerkt met de meest recente gegevens. Als u een beoordeling wilt bijwerken met de meest recente gegevens, moet u deze opnieuw berekenen.

Volg deze aanbevolen procedures voor beoordelingen van servers die via . CSV-bestand:

- **As-is-assessments maken:** U as-is-beoordelingen maken zodra uw machines worden weergegeven in de Azure Migrate-portal.
- **Prestatiegebaseerde beoordeling maken:** dit helpt om een betere kostenraming te krijgen, vooral als u overprovisioneerde servercapaciteit on-premises hebt. De nauwkeurigheid van de prestatiegebaseerde beoordeling is echter afhankelijk van de prestatiegegevens die door u voor de servers zijn opgegeven. 
- **Beoordelingen opnieuw berekenen:** Aangezien beoordelingen point-in-time-momentopnamen zijn, worden ze niet automatisch bijgewerkt met de meest recente gegevens. Als u een beoordeling wilt bijwerken met de meest recente geïmporteerde gegevens, moet u deze opnieuw berekenen.

## <a name="best-practices-for-confidence-ratings"></a>Aanbevolen procedures voor vertrouwensbeoordelingen

Wanneer u prestatiegebaseerde beoordelingen uitvoert, wordt een betrouwbaarheidsbeoordeling toegekend van 1-ster (laagste) tot 5-sterren (hoogste) aan de beoordeling. Ga als het gaat om een effectieve gebruik smaken de seinen:
- Azure Migrate Server Assessment heeft de gebruiksgegevens nodig voor VM CPU/Memory.
- Voor elke schijf die aan de on-premises VM is gekoppeld, heeft deze de lees-/schrijf-IOPS/doorvoergegevens nodig.
- Voor elke netwerkadapter die aan de VM is gekoppeld, heeft deze de in/out-gegevens van het netwerk nodig.

Afhankelijk van het percentage beschikbare gegevenspunten voor de geselecteerde duur wordt de betrouwbaarheidsbeoordeling voor een beoordeling gegeven zoals samengevat in de volgende tabel.

   **Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
   --- | ---
   0%-20% | 1 ster
   21%-40% | 2 sterren
   41%-60% | 3 sterren
   61%-80% | 4 sterren
   81%-100% | 5 sterren


## <a name="common-assessment-issues"></a>Gemeenschappelijke beoordelingskwesties

U als u een aantal veelvoorkomende omgevingsproblemen aanpakken die van invloed zijn op beoordelingen.

###  <a name="out-of-sync-assessments"></a>Out-of-sync-beoordelingen

Als u machines uit een groep toevoegt of verwijdert nadat u een beoordeling hebt gemaakt, wordt de beoordeling die u hebt **gemaakt, out-of-sync**gemarkeerd. Voer de beoordeling opnieuw uit (**Herberekening**) om de groepswijzigingen weer te geven.

### <a name="outdated-assessments"></a>Verouderde beoordelingen

Als er on-premises wijzigingen in VM's zijn die deel uitmaken van een beoordeelde groep, wordt de beoordeling **gemarkeerd als verouderd**. Voer de beoordeling opnieuw uit om de wijzigingen weer te geven.

### <a name="low-confidence-rating"></a>Lage betrouwbaarheidsscore

Een beoordeling heeft mogelijk niet alle gegevenspunten om een aantal redenen:

- U hebt uw omgeving niet geprofileerd gedurende de periode waarvoor u de evaluatie maakt. Als u bijvoorbeeld een *prestatiegebaseerde beoordeling* maakt waarbij de prestatieduur is ingesteld op één week, moet u ten minste een week wachten nadat u de detectie hebt gestart voor alle gegevenspunten die moeten worden verzameld. U altijd op **Opnieuw berekenen** klikken om de meest recente betrouwbaarheidsclassificatie te bekijken. Betrouwbaarheidsbeoordeling is alleen van toepassing wanneer u een *prestatiegebaseerde* beoordeling maakt.

- Er zijn enkele VM's uitgeschakeld geweest in de periode waarover de evaluatie wordt berekend. Als bepaalde VM's gedurende een tijdje uitgeschakeld zijn geweest, kunnen er voor deze periode geen prestatiegegevens worden verzameld.

- Er zijn enkele VM’s gemaakt nadat detectie in Server-evaluatie al was gestart. Als u bijvoorbeeld een evaluatie maakt voor de prestatiegeschiedenis van de laatste maand, maar er een week geleden enkele VM's in de omgeving zijn gemaakt. In dit geval zijn er voor de hele periode geen prestatiegegevens van de nieuwe VM’s beschikbaar, waardoor de betrouwbaarheidsclassificatie laag is.


## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over](concepts-assessment-calculation.md) hoe beoordelingen worden berekend.
- [Meer informatie over](how-to-modify-assessment.md) het aanpassen van een beoordeling.
