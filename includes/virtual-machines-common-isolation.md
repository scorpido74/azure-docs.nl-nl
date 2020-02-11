---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 3aeb0369ee4a04dbbe89a4d0684b2a6c97378d13
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2020
ms.locfileid: "77123171"
---
Azure Compute biedt virtuele machine grootten die zijn geïsoleerd voor een specifiek hardwaretype en die zijn toegewezen aan één klant.  Deze virtuele-machine grootten zijn het meest geschikt voor werk belastingen die een hoge mate van isolatie van andere klanten vereisen voor workloads met elementen zoals naleving en regelgeving.  Klanten kunnen er ook voor kiezen om de resources van deze geïsoleerde virtuele machines verder te onderverdelen met behulp [van Azure-ondersteuning voor geneste virtuele machines](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Het gebruik van een geïsoleerde grootte garandeert dat uw virtuele machine de enige is die op dat specifieke Server exemplaar wordt uitgevoerd.  De huidige, geïsoleerde virtuele-machine-aanbiedingen omvatten:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

[Hier](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)vindt u meer informatie over elke beschik bare, geïsoleerde grootte.

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>D15_v2/DS15_v2-isolatie buiten gebruik stellen op 15 mei 2020
**Bijwerken op 10 februari 2020: de buiten gebruiks telling van de werk tijd ' isolatie ' is verlengd tot 15 mei 2020 '**

De speciale Azure-host is nu GA, waarmee u de virtuele Linux-en Windows-machines van uw organisatie kunt uitvoeren op fysieke servers met één Tenant. We zijn van plan om geïsoleerde Azure-Vm's volledig te vervangen door de toegewezen Azure-host. Na **15 mei 2020** zijn de Azure-vm's van D15_v2/DS15_v2 niet langer hardware geïsoleerd.

## <a name="how-does-this-affect-me"></a>Wat betekent dit voor mij?
Na 15 mei 2020 bieden we niet langer een isolatie garantie voor uw virtuele D15_v2/DS15_v2 Azure-machines. 

## <a name="what-actions-should-i-take"></a>Welke acties moet ik ondernemen?
Als er geen hardwarematige isolatie vereist is, hoeft u geen actie te ondernemen. 

Als u voor de isolatie een vereiste hebt, vóór 15 mei 2020, moet u het volgende doen:

• Uw workload [migreren](https://azure.microsoft.com/blog/introducing-azure-dedicated-host) naar een toegewezen Azure-host.

• [Vraag toegang](https://aka.ms/D15iRequestAccess) tot een D15i_v2 en DS15I_V2 Azure VM om dezelfde prijs prestaties te verkrijgen. Deze optie is alleen beschikbaar voor de scenario's voor betalen per gebruik en een gereserveerde instantie van één jaar.    

• [Migreer](https://azure.microsoft.com/blog/resize-virtual-machines/) uw werk belasting naar een andere geïsoleerde virtuele machine van Azure. 

Zie hieronder voor meer informatie:

## <a name="timeline"></a>Tijdlijn
| Date | Bewerking | 
| --- | --- |
| 18 november 2019 | Beschik baarheid van D/DS15i_v2 (PAYG, 1-jarige RI) |
| 14 mei 2020  | Laatste dag tot aankoop D/DS15i_v2 1 jaar RI | 
| 15 mei 2020   | D/DS15_v2-isolatie garantie verwijderd | 
| 15 mei 2021  | D/DS15i_v2 buiten gebruik stellen (alle klanten, met uitzonde ring van wie de 3 jaar RI van D/DS15_v2 heeft gekocht vóór 18 november 2019)| 
| 17 november 2022  | D/DS15i_v2 buiten gebruik stellen als er 3 jaar een RIs is uitgevoerd (voor klanten die 3-jarig RI van D/DS15_v2 hebben gekocht vóór 18 november 2019) | 

## <a name="faq"></a>Veelgestelde vragen
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>V: is de grootte D/DS15_v2 worden buiten gebruik gesteld?
**A**: Nee, alleen de functie ' isolatie ' wordt buiten gebruik gesteld. Als u geen isolatie nodig hebt, hoeft u geen actie te ondernemen.

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>V: is de grootte D/DS15i_v2 worden buiten gebruik gesteld?
**A**: Ja, de grootte is alleen beschikbaar tot 15 mei 2021. Voor klanten die 3 jaar RIs op D/DS15_v2 hebben gekocht vóór 18 november 2019, is tot en met 17 november 2022 toegang tot D/DS15i_v2.

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>V: Waarom worden de nieuwe D/DS15i_v2-grootten niet weer gegeven in de portal?
**A**: als u een huidige d/DS15_v2-klant bent en de nieuwe d/DS15i_v2-grootte wilt gebruiken, vult u dit [formulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u) in

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>V: Waarom zie ik geen quota voor de nieuwe D/DS15i_v2 grootten?
**A**: als u een huidige d/DS15_v2-klant bent en de nieuwe d/DS15i_v2-grootte wilt gebruiken, vult u dit [formulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u) in

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>V: wanneer zijn de andere geïsoleerde grootten buiten gebruik gesteld?
**A**: de officiële buiten gebruik stellen van de grootten is een periode van 12 maanden.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>V: is er sprake van uitval tijd wanneer mijn VM-land op een niet-geïsoleerde hardware wordt gehouden?
**A**: als u geen isolatie nodig hebt, hoeft u geen actie te ondernemen en ziet u geen uitval tijd.

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>V: worden er kosten in rekening gebracht voor het verplaatsen naar een niet-geïsoleerde virtuele machine?
**A**: Nee 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>V: Ik heb 1 of 3 jaar al een gereserveerd exemplaar van D15_v2 of Ds15_v2 aangeschaft. Hoe wordt de korting toegepast op mijn VM-gebruik?
**A**: RIs aangeschaft vóór 18 november 2019 zal automatisch de dekking uitbreiden naar de nieuwe geïsoleerde VM-serie. 

| &AMP; |  Flexibiliteit van de instantie grootte | Geschiktheid voor deel |   
| --- | --- | --- |
|   D15_v2  |   Uit     |   D15_v2 en D15i_v2 |    
|   D15_v2  |   Aan  |   D15_v2 reeksen en D15i_v2 ontvangen alle voor delen van de RI. |    
|   D14_v2  |   Aan  |   D15_v2 reeksen en D15i_v2 ontvangen alle voor delen van de RI. |    
 
Dit geldt ook voor de Dsv2-serie.
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>V: Ik wil extra gereserveerde instanties kopen voor dv2. Welke moet ik kiezen?
**A**: alle RIs die na 18 november 2019 zijn aangeschaft, hebben het volgende gedrag. 

| &AMP; |  Flexibiliteit van de instantie grootte | Geschiktheid voor deel |   
| --- | --- | --- |
| D15_v2 |  Uit |   Alleen D15_v2  
| D15_v2 |  Aan |    D15_v2 serie ontvangt het voor deel van de RI. Het nieuwe D15i_v2 komt niet in aanmerking voor RI-voor deel van dit type RI. | 
| D15i_v2 |     Uit | Alleen D15i_v2 |  
| D15i_v2 |     Aan  | Alleen D15i_v2 | 
 
De flexibiliteit van de instantie grootte kan niet worden gebruikt om te worden toegepast op andere grootten, zoals D2_v2, D4_v2 of D15_v2. Hetzelfde geldt voor de Dsv2-serie.  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>V: kan ik een nieuwe RI van drie jaar kopen voor D15i_v2 en DS15i_v2?
**A**: Helaas, er is slechts 1 jaar RI beschikbaar voor nieuwe aankopen.
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>V: kan ik mijn bestaande reserve kopie van D15_v2/DS15_v2 verplaatsen naar een gereserveerde instantie voor grootte
**A**: deze actie is niet nodig omdat het voor deel van toepassing is op zowel geïsoleerde als niet-geïsoleerde grootten. Maar Azure biedt ondersteuning voor het wijzigen van bestaande D15_v2/DS15_v2 gereserveerde instanties in D15i_v2/DS15i_v2. Gebruik voor alle andere gereserveerde instanties van dv2/Dsv2 de bestaande gereserveerde instantie of koop nieuwe gereserveerde instanties voor de geïsoleerde grootten.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>V: Ik ben een Azure Service Fabric klant die afhankelijk is van de laag met Silver of Gold-duurzaamheid. Is dit van invloed op mijn wijzigingen?
**A**: Nee. De garanties die worden geboden door de [duurzaamheids lagen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) van service Fabric blijven werken, zelfs na deze wijziging. Als u voor andere redenen fysieke hardware-isolatie nodig hebt, moet u mogelijk nog een van de hierboven beschreven acties uitvoeren. 
