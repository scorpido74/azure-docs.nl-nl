---
title: Bijwerken naar dynamische nalevings controle voor regelgeving in uw Azure Security Center nalevings dashboard voor regelgeving | Microsoft Docs
description: Uw nalevings pakketten bijwerken
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
ms.openlocfilehash: 32ee0135d4d991a9afe229747f8cb66916a12e7f
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89277439"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>De set normen aanpassen in uw nalevings dashboard voor regelgeving

Azure Security Center vergelijkt voortdurend de configuratie van uw resources met vereisten in de industrie normen,-voor schriften en-benchmarks. Het **nalevings dashboard** van de regelgeving biedt inzicht in uw nalevings postuur op basis van de manier waarop u aan specifieke nalevings regelingen en vereisten voldoet.


## <a name="overview-of-compliance-packages"></a>Overzicht van nalevings pakketten

Industrie normen, regelgevings normen en benchmarks worden in Security Center weer gegeven als *nalevings pakketten*.  Elk pakket is een initiatief dat is gedefinieerd in Azure Policy. Als u de nalevings gegevens wilt zien die zijn toegewezen als evaluaties in uw dash board, voegt u een nalevings pakket toe aan de beheer groep of het abonnement vanuit de pagina **beveiligings beleid** . (Meer informatie over Azure Policy en initiatieven bij het [werken met beveiligings beleid](tutorial-security-policy.md).)

Wanneer u een standaard of Bench Mark hebt voor het geselecteerde bereik, wordt het initiatief aan het bereik toegewezen en wordt de standaard weer gegeven in het dash board nalevings beleid met alle gekoppelde nalevings gegevens die zijn toegewezen als evaluaties. U kunt ook samenvattings rapporten downloaden voor een van de standaarden die zijn voor bereid.

Micro soft houdt ook de regelgevings normen zelf bij en verbetert de dekking in een aantal pakketten in de loop van de tijd. Wanneer micro soft nieuwe inhoud voor het initiatief uitbrengt (nieuwe beleids regels die zijn toegewezen aan meer besturings elementen in de standaard), wordt de extra inhoud automatisch weer gegeven in het dash board.

> [!TIP]
> Een standaard die in de loop van de tijd van micro soft uitvalt, is **Azure CIS 1.1.0 (nieuw)** (meer formeel, de [CIS-Microsoft Azure basis Bench Mark-benchmark versie 1.1.0](https://www.cisecurity.org/benchmark/azure/)). U moet dit toevoegen aan uw dash board naast ' Azure CIS 1.1.0 ', de weer gave van Azure CIS die standaard is geconfigureerd in elke Security Center omgeving. Dit pakket is afhankelijk van een statische set regels. Het nieuwere pakket bevat meer beleids regels en wordt automatisch bijgewerkt na verloop van tijd. Werk bij naar het nieuwe dynamische pakket, zoals hieronder wordt beschreven.


## <a name="available-packages"></a>Beschik bare pakketten

U kunt standaarden toevoegen zoals NIST SP 800-53 R4, SWIFT CSP CSCF-v2020, UK Official en UK NHS, Canada Federal PBMM en Azure CIS 1.1.0 (New)-een meer volledige weer gave van Azure CIS 1.1.0. 

Daarnaast kunt u **Azure Security Bench Mark**, de door micro soft ontworpen, specifieke Azure-richt lijnen voor beveiliging en naleving aanbevolen procedures toevoegen op basis van algemene nalevings kaders. (Meer[informatie over Azure Security Bench Mark](https://docs.microsoft.com/azure/security/benchmarks/introduction).)

Er worden extra standaarden ondersteund in het dash board, zodra deze beschikbaar komen. 


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Een regelgevings norm toevoegen aan uw dash board

In de volgende stappen wordt uitgelegd hoe u een pakket kunt toevoegen om te controleren of u voldoet aan een van de ondersteunde regelgevings standaarden.

> [!NOTE]
> Alleen gebruikers die eigenaar of Inzender zijn, hebben de benodigde machtigingen om nalevings standaarden toe te voegen. 

1. Selecteer op de zijbalk van Security Center **reglementaire naleving** om het nalevings dashboard voor regelgeving te openen. Hier kunt u de nalevings standaarden zien die momenteel zijn toegewezen aan de momenteel geselecteerde abonnementen.   

1. Selecteer aan de bovenkant van de pagina **nalevings beleid beheren**. De pagina beleids beheer wordt weer gegeven.

1. Selecteer het abonnement of de beheer groep waarvoor u de nalevings postuur wilt beheren. 

    > [!TIP]
    > U kunt het beste het hoogste bereik selecteren waarvoor de standaard geldt, zodat de nalevings gegevens worden geaggregeerd en bijgehouden voor alle geneste resources. 

1. Als u de standaarden wilt toevoegen die relevant zijn voor uw organisatie, klikt u op **meer standaarden toevoegen**. 

1. Op de pagina **nalevings normen voor regelgeving toevoegen** kunt u zoeken naar pakketten voor een van de beschik bare standaard waarden. Enkele van de beschik bare normen zijn:

    - **Azure Security-benchmark**
    - **NIST SP 800-53 R4**
    - **SWIFT CSP CSCF-v2020**
    - **UKO en UK NHS**
    - **Canada PBMM**
    
    ![Regulerende pakketten toevoegen aan het dash board nalevings beleid van de Azure Security Center](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Selecteer **toevoegen** en voer alle benodigde gegevens in voor het specifieke initiatief, zoals Scope, para meters en herstel.

1. Selecteer op de zijbalk van Security Center opnieuw **regelgevende naleving** om terug te gaan naar het dash board voor nalevings vereisten.
    * Uw nieuwe standaard wordt weer gegeven in uw lijst met industrie & reglementaire normen. 
    * Als u **Azure CIS 1.1.0 (nieuw)** hebt toegevoegd, blijft de oorspronkelijke *statische* weer gave van uw Azure CIS 1.1.0-naleving ook ernaast. Deze kan in de toekomst automatisch worden verwijderd.

    > [!NOTE]
    > Het kan enkele uren duren voordat een nieuw toegevoegde standaard wordt weer gegeven in het dash board voor compatibiliteit.

    [![Dash board nalevings regelgeving met oude en nieuwe Azure CIS](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u **nalevings pakketten kunt toevoegen** om uw naleving met aanvullende standaarden te bewaken. 

Raadpleeg de volgende artikelen voor meer gerelateerde materialen: 

- [Azure Security-benchmark](https://docs.microsoft.com/azure/security/benchmarks/introduction)
- [Security Center-nalevings dashboard voor regelgeving](security-center-compliance-dashboard.md)
- [Werken met beveiligingsbeleid](tutorial-security-policy.md)