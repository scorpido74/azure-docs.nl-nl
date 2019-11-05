---
title: Bijwerken naar dynamische nalevings controle voor regelgeving in uw Azure Security Center nalevings dashboard voor regelgeving | Microsoft Docs
description: Uw nalevings pakketten bijwerken (preview-versie)
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
ms.openlocfilehash: f76b5443d6c1e3fd2cebf87cba39ba0a6bbdeaee
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521785"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard-preview"></a>Bijwerken naar dynamische compliantie pakketten in uw nalevings dashboard voor regelgeving (preview-versie)

Azure Security Center vergelijkt voortdurend de configuratie van uw resources met vereisten in de industrie normen,-voor schriften en-benchmarks. Het **nalevings dashboard** van de regelgeving biedt inzicht in uw nalevings postuur op basis van de manier waarop u aan specifieke nalevings regelingen en vereisten voldoet.

Een standaard waarvoor u uw nalevings postuur kunt volgen, is [Azure CIS 1.1.0](https://www.cisecurity.org/benchmark/azure/) (meer formeel, de "CIS Microsoft Azure Stichting benchmark version 1.1.0"). 

De weer gave van Azure CIS die in eerste instantie wordt weer gegeven in uw nalevings dashboard, is afhankelijk van een statische set regels die is opgenomen in Security Center.

Met de functie **voor dynamische nalevings pakketten (preview)** verbetert Security Center automatisch de dekking van de industrie standaarden gedurende een bepaalde periode. Nalevings pakketten zijn in feite initiatieven gedefinieerd in Azure Policy. Ze kunnen worden toegewezen aan het geselecteerde bereik (abonnement, beheer groep, enzovoort). Als u de nalevings gegevens wilt zien die zijn toegewezen als evaluaties in uw dash board, voegt u een nalevings pakket toe aan de beheer groep of het abonnement vanuit het beveiligings beleid. Als u een nalevings pakket toevoegt, wordt het regelgevings nalevings initiatief op een effectieve manier toegewezen aan uw geselecteerde scope. Op deze manier kunt u nieuw gepubliceerde regelgevende initiatieven volgen als nalevings normen in uw dash board. Wanneer micro soft nieuwe inhoud voor het initiatief uitbrengt (nieuwe beleids regels die zijn toegewezen aan meer besturings elementen in de standaard), wordt de extra inhoud automatisch weer gegeven in het dash board.

Het dynamische compatibiliteits pakket voor de Azure CIS-benchmark test, **Azure CIS 1.1.0 (New)** , verbetert de oorspronkelijke *statische* versie door:

* Inclusief meer beleids regels
* Automatisch bijwerken met de nieuwe dekking wanneer deze wordt toegevoegd 

Werk bij naar het nieuwe dynamische pakket, zoals hieronder wordt beschreven.

## <a name="adding-a-dynamic-compliance-package"></a>Een dynamisch nalevings pakket toevoegen

In de volgende stappen wordt uitgelegd hoe u het dynamische pakket kunt toevoegen voor het bewaken van uw naleving met de Azure CIS Bench Mark v-1.1.0.   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Bijwerken naar het Azure CIS 1.1.0 (nieuw) dynamisch nalevings pakket 

1. Open de pagina **beveiligings beleid** . Op deze pagina wordt het aantal beheer groepen, abonnementen, werk ruimten en de structuur van de beheer groep weer gegeven.

1. Selecteer het abonnement of de beheer groep waarvoor u de nalevings postuur wilt beheren. U kunt het beste het hoogste bereik selecteren waarvoor de standaard geldt, zodat de nalevings gegevens worden geaggregeerd en bijgehouden voor alle geneste resources. 

1. In de sectie Industry & regelgevings normen (preview) ziet u dat Azure CIS 1.1.0 kan worden bijgewerkt voor nieuwe inhoud. Klik op **Nu bijwerken**. 

1. Klik desgewenst op **meer standaards toevoegen** om de pagina **regels voor nalevings beleid toevoegen** te openen. Daar kunt u hand matig zoeken naar **Azure CIS 1.1.0 (nieuw)** en dynamische pakketten voor andere nalevings standaarden zoals **NIST SP 800-53 R4**, **Swift CSP CSCF-V2020**, **UKO en UK NHS**en **Canada PBMM**.
    
    ![Regulerende pakketten toevoegen aan het dash board nalevings beleid van de Azure Security Center](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. Selecteer op de zijbalk van Security Center **reglementaire naleving** om het nalevings dashboard voor regelgeving te openen. 
    * Azure CIS 1.1.0 (nieuw) wordt nu weer gegeven in uw lijst met industrie & reglementaire normen. 
    * De oorspronkelijke *statische* weer gave van uw Azure CIS 1.1.0-compatibiliteit blijft ook ernaast. Deze kan in de toekomst automatisch worden verwijderd.

    > [!NOTE]
    > Het kan enkele uren duren voordat een nieuw toegevoegde standaard wordt weer gegeven in het dash board voor compatibiliteit.


    [![dash board naleving van regelgeving met oude en nieuwe Azure CIS](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u het volgende geleerd:

* **Een upgrade uitvoeren van de standaarden** die worden weer gegeven in het dash board voor naleving van regelgeving naar de nieuwe *dynamische* pakketten
* **Nalevings pakketten toevoegen** om te controleren of u voldoet aan de vereisten voor extra standaarden. 

Raadpleeg de volgende artikelen voor meer gerelateerde materialen: 

- [Security Center-nalevings dashboard voor regelgeving](security-center-compliance-dashboard.md)
- [Werken met beveiligings beleid](tutorial-security-policy.md)
- [Aanbevelingen voor beveiliging beheren in azure Security Center](security-center-recommendations.md) -meer informatie over het gebruik van aanbevelingen in azure Security Center om uw Azure-resources te beveiligen.
- Veelgestelde vragen over [Azure Security Center](security-center-faq.md) : Krijg antwoorden op veelgestelde Vraags over het gebruik van Security Center.