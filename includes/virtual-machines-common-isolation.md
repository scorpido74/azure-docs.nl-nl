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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123171"
---
Azure Compute biedt virtuele machineformaten die zijn geïsoleerd voor een specifiek hardwaretype en zijn toegewezen aan één klant.  Deze virtuele machineformaten zijn het meest geschikt voor workloads die een hoge mate van isolatie van andere klanten vereisen voor workloads met elementen zoals naleving en wettelijke vereisten.  Klanten kunnen er ook voor kiezen om de resources van deze geïsoleerde virtuele machines verder te verdelen door [Azure-ondersteuning te gebruiken voor geneste virtuele machines.](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)

Het gebruik van een geïsoleerde grootte garandeert dat uw virtuele machine de enige is die op die specifieke serverinstantie wordt uitgevoerd.  De huidige geïsoleerde virtuele machine aanbod omvatten:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

U hier meer te weten komen over elke beschikbare geïsoleerde [grootte.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>Met pensioen D15_v2/DS15_v2 isolement op 15 mei 2020
**Update op 10 februari 2020: De tijdlijn van de "isolatie" is verlengd tot 15 mei 2020"**

Azure Dedicated Host is nu GA, waarmee u de virtuele machines voor Linux en Windows van uw organisatie uitvoeren op fysieke servers met één tenant. We zijn van plan geïsoleerde Azure VM's volledig te vervangen door Azure Dedicated Host. Na **15 mei 2020** worden de D15_v2/DS15_v2 Azure VM's niet langer geïsoleerd.

## <a name="how-does-this-affect-me"></a>Wat betekent dit voor mij?
Na 15 mei 2020 bieden we geen isolatiegarantie meer voor uw D15_v2/DS15_v2 Azure virtuele machines. 

## <a name="what-actions-should-i-take"></a>Welke acties moet ik ondernemen?
Als hardwareisolatie niet voor u vereist is, hoeft u geen actie te ondernemen. 

Als isolatie voor u vereist is, vóór 15 mei 2020, moet u ofwel:

• [Migreer](https://azure.microsoft.com/blog/introducing-azure-dedicated-host) uw werkbelasting naar Azure Dedicated Host.

• [Vraag toegang](https://aka.ms/D15iRequestAccess) aan tot een D15i_v2 en DS15i_v2 Azure VM, om dezelfde prijsprestaties te krijgen. Deze optie is alleen beschikbaar voor pay-as-you-go en gereserveerde instantiescenario's van één jaar.    

• [Migreer](https://azure.microsoft.com/blog/resize-virtual-machines/) uw werkbelasting naar een andere azure geïsoleerde virtuele machine. 

Voor meer informatie zie hieronder:

## <a name="timeline"></a>Tijdlijn
| Date | Actie | 
| --- | --- |
| 18 november 2019 | Beschikbaarheid van D/DS15i_v2 (PAYG, 1 jaar RI) |
| 14 mei 2020  | Laatste dag om D/DS15i_v2 1-jarige RI te kopen | 
| 15 mei 2020   | D/DS15_v2 isolatiegarantie verwijderd | 
| 15 mei 2021  | D/DS15i_v2 met pensioen (alle klanten behalve die vóór 18 november 2019 3 jaar RI van D/DS15_v2 hebben gekocht)| 
| 17 november 2022  | D/DS15i_v2 met pensioen wanneer 3-jarige RI's zijn gedaan (voor klanten die voor 18 november 2019 3-jarige RI van D/DS15_v2 hebben gekocht) | 

## <a name="faq"></a>Veelgestelde vragen
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>V: Gaat de maat D/DS15_v2 met pensioen?
**A:** Nee, alleen "isolatie" functie gaat krijgen gepensioneerde. Als u geen isolatie nodig hebt, hoeft u geen actie te ondernemen.

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>V: Gaat de maat D/DS15i_v2 met pensioen?
**A**: Ja, de maat is alleen beschikbaar tot 15 mei 2021. Voor klanten die vóór 18 november 2019 3-jarige RI's op D/DS15_v2 hebben gekocht, geldt tot 17 november 2022 tot D/DS15i_v2.

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>V: Waarom zie ik de nieuwe D/DS15i_v2 maten niet in de portal?
**A**: Als u een huidige D/DS15_v2 klant bent en de nieuwe D/DS15i_v2 maten wilt gebruiken, vul dan dit [formulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u) in

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>V: Waarom zie ik geen quotum voor de nieuwe D/DS15i_v2 maten?
**A**: Als u een huidige D/DS15_v2 klant bent en de nieuwe D/DS15i_v2 maten wilt gebruiken, vul dan dit [formulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u) in

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>V: Wanneer gaan de andere geïsoleerde maten met pensioen?
**A**: We zullen 12 maanden voor de officiële ontmanteling van de maten herinneringen verstrekken.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>V: Is er een downtime wanneer mijn vm landt op een niet-geïsoleerde hardware?
**A:** Als u geen isolatie nodig hebt, hoeft u geen actie te ondernemen en ziet u geen downtime.

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>V: Zijn er kostenwijzigingen voor de overstap naar een niet-geïsoleerde virtuele machine?
**A**: Nee 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>V: Ik heb al 1- of 3-jarige gereserveerde instantie gekocht voor D15_v2 of Ds15_v2. Hoe wordt de korting toegepast op mijn VM-gebruik?
**A**: RI's die vóór 18 november 2019 zijn aangeschaft, breiden de dekking automatisch uit naar de nieuwe geïsoleerde VM-serie. 

| RI |  Flexibiliteit bij instantiegrootte | Subsidiabiliteit van het voordeel |   
| --- | --- | --- |
|   D15_v2  |   Uit     |   D15_v2 en D15i_v2 |    
|   D15_v2  |   Aan  |   D15_v2 series en D15i_v2 ontvangen allemaal het RI-voordeel. |    
|   D14_v2  |   Aan  |   D15_v2 series en D15i_v2 ontvangen allemaal het RI-voordeel. |    
 
Zo ook voor Dsv2 serie.
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>V: Ik wil extra gereserveerde exemplaren voor Dv2 aanschaffen. Welke moet ik kiezen?
**A**: Alle RI's die na 18 november 2019 zijn aangeschaft, hebben het volgende gedrag. 

| RI |  Flexibiliteit bij instantiegrootte | Subsidiabiliteit van het voordeel |   
| --- | --- | --- |
| D15_v2 |  Uit |   D15_v2 alleen.  
| D15_v2 |  Aan |    D15_v2 serie ontvangt het RI-voordeel. De nieuwe D15i_v2 komen niet in aanmerking voor RI-voordeel van dit RI-type. | 
| D15i_v2 |     Uit | D15i_v2 alleen. |  
| D15i_v2 |     Aan  | D15i_v2 alleen. | 
 
Instantiegrootte Flexibiliteit kan niet worden gebruikt voor andere formaten, zoals D2_v2, D4_v2 of D15_v2. Ook voor Dsv2 serie.  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>V: Kan ik een nieuwe 3-jarige RI kopen voor D15i_v2 en DS15i_v2?
**A**: Helaas nee, slechts 1 jaar RI is beschikbaar voor nieuwe aankoop.
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>V: Kan ik mijn bestaande D15_v2/DS15_v2 Reserveinstantie verplaatsen naar een geïsoleerde grootte gereserveerde instantie?
**A**: Deze actie is niet nodig, aangezien het voordeel van toepassing is op zowel geïsoleerde als niet-geïsoleerde maten. Azure ondersteunt echter het wijzigen van bestaande D15_v2/DS15_v2 gereserveerde instanties in D15i_v2/DS15i_v2. Gebruik voor alle andere Gereserveerde Dv2/Dsv2-exemplaren de bestaande gereserveerde instantie of koop nieuwe gereserveerde exemplaren voor de geïsoleerde grootte.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>V: Ik ben een Azure Service Fabric-klant die vertrouwt op de Silver- of Gold Sustainability Tiers. Heeft deze verandering invloed op mij?
**A:** Nee. De garanties van Service Fabric's [Sustainability Tiers](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) blijven ook na deze wijziging functioneren. Als u om andere redenen fysieke hardwareisolatie nodig hebt, moet u mogelijk nog steeds een van de hierboven beschreven acties uitvoeren. 
