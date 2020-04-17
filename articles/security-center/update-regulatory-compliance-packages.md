---
title: Bijwerken naar dynamische nalevingscontrole op regelgeving in uw azure security center-compliancedashboard | Microsoft Documenten
description: Uw nalevingspakketten voor regelgeving bijwerken
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: fa5027ed285456247891c84e559b74a14237f553
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537777"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard"></a>Update naar dynamische compliancepakketten in uw dashboard voor naleving van de regelgeving

Azure Security Center vergelijkt de configuratie van uw resources voortdurend met vereisten in industriestandaarden, -voorschriften en -benchmarks. Het **compliance dashboard** biedt inzicht in uw nalevingshouding op basis van hoe u voldoet aan specifieke nalevingscontroles en -vereisten.

Een standaard waarvoor u uw nalevingshouding bijhouden, is [Azure CIS 1.1.0](https://www.cisecurity.org/benchmark/azure/) (meer formeel de CIS Microsoft Azure Foundations Benchmark versie 1.1.0). 

De weergave van Azure CIS die in eerste instantie in uw compliancedashboard wordt weergegeven, is gebaseerd op een statische set regels die is opgenomen in Het Beveiligingscentrum.

Met de **functie dynamische nalevingspakketten** verbetert Security Center automatisch de dekking van industriestandaarden in de loop van de tijd. Compliancepakketten zijn in wezen initiatieven die zijn gedefinieerd in Azure Policy. Ze kunnen worden toegewezen aan uw geselecteerde bereik (abonnement, beheergroep, enzovoort). Als u nalevingsgegevens wilt zien die zijn toegewezen als beoordelingen in uw dashboard, voegt u een nalevingspakket toe aan uw beheergroep of abonnement vanuit het beveiligingsbeleid. Als u een nalevingspakket toevoegt, wordt het compliance-initiatief voor regelgeving effectief aan uw geselecteerde scope toegevoegd. Op deze manier u nieuw gepubliceerde regelgevingsinitiatieven volgen als nalevingsnormen in uw dashboard. Wanneer Microsoft nieuwe inhoud voor het initiatief uitbrengt (nieuw beleid dat wordt toegewezen aan meer besturingselementen in de standaard), wordt de extra inhoud automatisch weergegeven in uw dashboard.

Het dynamische nalevingspakket voor de Azure CIS-benchmark, **Azure CIS 1.1.0 (nieuw),** verbetert de oorspronkelijke *statische* versie door:

* Meer beleid opnemen
* Automatisch updaten met nieuwe dekking als deze wordt toegevoegd 

Update naar het nieuwe dynamische pakket zoals hieronder beschreven.

## <a name="adding-a-dynamic-compliance-package"></a>Een dynamisch compliancepakket toevoegen

In de volgende stappen wordt uitgelegd hoe u het dynamische pakket toevoegt om te controleren of u voldoet aan de Azure CIS-benchmark v1.1.0.   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Update naar het Azure CIS 1.1.0 (nieuw) dynamisch compliancepakket 

1. Open de pagina **Beveiligingsbeleid.** Op deze pagina ziet u het aantal beheergroepen, abonnementen, werkruimten en uw beheergroepstructuur.

1. Selecteer de abonnements- of beheergroep waarvoor u de nalevingshouding van de regelgeving wilt beheren. We raden u aan de hoogste scope te selecteren waarvoor de standaard van toepassing is, zodat nalevingsgegevens worden samengevoegd en bijgehouden voor alle geneste resources. 

1. In de sectie Industrie& regelgevingsstandaarden ziet u dat Azure CIS 1.1.0 kan worden bijgewerkt voor nieuwe inhoud. Klik **nu op Bijwerken**. 

1. Klik optioneel op **Meer standaarden toevoegen** om de pagina **Nalevingsnormen toevoegen** te openen. Daar u handmatig zoeken naar **Azure CIS 1.1.0 (Nieuw)** en dynamische pakketten voor andere nalevingsstandaarden zoals **NIST SP 800-53 R4,** SWIFT **CSP CSCF-v2020,** **UKO en UK NHS**en Canada **PBMM**.
    
    > [!TIP]
    > Alleen gebruikers die eigenaar of beleidsmedewerker zijn, hebben de benodigde machtigingen om nalevingsnormen toe te voegen. 

    ![Regelgevende pakketten toevoegen aan het dashboard voor naleving van de regelgeving van Azure Security Center](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. Selecteer in de zijbalk van het Beveiligingscentrum **de optie Naleving van** de regelgeving om het nalevingsdashboard voor regelgeving te openen. 
    * Azure CIS 1.1.0 (Nieuw) wordt nu weergegeven in uw lijst met industrie&-regelgevingsstandaarden. 
    * De oorspronkelijke *statische* weergave van uw Azure CIS 1.1.0-naleving blijft ook ernaast. Het kan in de toekomst automatisch worden verwijderd.

    > [!NOTE]
    > Het kan enkele uren duren voordat een nieuw toegevoegde standaard in het compliancedashboard wordt weergegeven.


    [![Dashboard voor naleving van de regelgeving met oud en nieuw Azure CIS](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>Volgende stappen

In dit artikel heb je geleerd:

* De **standaarden** in uw dashboard voor naleving van de regelgeving upgraden naar de nieuwe *dynamische* pakketten
* **Nalevingspakketten toevoegen** om te controleren of u voldoet aan aanvullende normen. 

Zie voor ander gerelateerd materiaal de volgende artikelen: 

- [Dashboard voor naleving van de regelgeving van het beveiligingscentrum](security-center-compliance-dashboard.md)
- [Werken met beveiligingsbeleid](tutorial-security-policy.md)
- [Beveiligingsaanbevelingen beheren in Azure Security Center](security-center-recommendations.md) : lees hoe u aanbevelingen gebruiken in Azure Security Center om uw Azure-bronnen te beschermen.