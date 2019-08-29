---
title: Ken de voor waarden van SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Ken de voor waarden van SAP HANA op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fec9f18404fc45f4cf69cc13b1602f818dbddfaf
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099742"
---
# <a name="know-the-terms"></a>Inzicht in de voorwaarden

Verschillende algemene definities worden veel gebruikt in de architectuur en technische implementatie handleiding. Houd rekening met de volgende voor waarden en hun betekenis:

- **IaaS**: Infra structuur als een service.
- **PaaS**: Platform as a service.
- **SaaS**: Software als een service.
- **SAP-onderdeel**: Een afzonderlijke SAP-toepassing, zoals het ERP Central-onderdeel (ECC), Business Warehouse (BW), Solution Manager of Enterprise Portal (EP). SAP-onderdelen kunnen worden gebaseerd op traditionele ABAP of Java-technologieën of op een toepassing die niet op netweave is gebaseerd, zoals bedrijfs objecten.
- **SAP-omgeving**: Een of meer SAP-onderdelen die logisch zijn gegroepeerd om een zakelijke functie uit te voeren, zoals ontwikkeling, kwaliteits bewaking, training, herstel na nood gevallen of productie.
- **SAP-landschap**: Verwijst naar de volledige SAP-assets in uw IT-landschap. Het SAP-landschap omvat alle productie-en niet-productie omgevingen.
- **SAP-systeem**: De combi natie van DBMS-laag en-toepassingslaag van, bijvoorbeeld een SAP ERP-ontwikkelings systeem, een SAP BW test systeem en een SAP CRM-productie systeem. Azure-implementaties bieden geen ondersteuning voor het delen van deze twee lagen tussen on-premises en Azure. Een SAP-systeem is on-premises geïmplementeerd of is geïmplementeerd in Azure. U kunt de verschillende systemen van een SAP-landschap implementeren in azure of on-premises. U kunt bijvoorbeeld het ontwikkel-en test systeem van SAP CRM in azure implementeren terwijl u het productie systeem van SAP CRM on-premises implementeert. Voor SAP HANA op Azure (grote instanties) is het bedoeld dat u de SAP-toepassingslaag van SAP-systemen in Vm's en het gerelateerde SAP HANA exemplaar host op een eenheid in de SAP HANA op Azure (grote exemplaren).
- **Stempel van grote instantie**: Een hardware-infrastructuur stack die SAP HANA TDI-gecertificeerd en toegewezen is om SAP HANA-exemplaren in azure uit te voeren.
- **SAP HANA op Azure (grote exemplaren):** Officiële naam voor de aanbieding in azure voor het uitvoeren van HANA-instanties in op SAP HANA TDI-gecertificeerde hardware die in verschillende Azure-regio's wordt geïmplementeerd in stem pels van grote instanties. De gerelateerde term *Hana grote instantie* is beperkt voor *SAP Hana op Azure (grote instanties)* en wordt veel gebruikt in deze technische implementatie handleiding.
- **Cross-premises**: Hierin wordt een scenario beschreven waarin Vm's worden geïmplementeerd naar een Azure-abonnement met site-naar-site-, multi site-of Azure ExpressRoute-connectiviteit tussen on-premises data centers en Azure. In algemene documentatie over Azure worden dit soort implementaties ook beschreven als scenario's voor cross-premises. De reden hiervoor is om on-premises domeinen, on-premises Azure Active Directory-OpenLDAP en on-premises DNS uit te breiden naar Azure. De on-premises liggende lands worden uitgebreid naar de Azure-assets van de Azure-abonnementen. Met deze uitbrei ding kunnen de Vm's deel uitmaken van het on-premises domein. 

   Domein gebruikers van het on-premises domein hebben toegang tot de servers en kunnen services uitvoeren op deze virtuele machines (zoals DBMS-Services). Communicatie en naam omzetting tussen Vm's die on-premises en door Azure geïmplementeerde Vm's zijn geïmplementeerd, is mogelijk. Dit scenario is een typische manier waarop de meeste SAP-assets worden geïmplementeerd. Zie [Azure VPN gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) en [een virtueel netwerk maken met een site-naar-site-verbinding met behulp van de Azure Portal](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie.
- **Tenant**: Een klant die is geïmplementeerd in HANA grote instantie stempel, wordt geïsoleerd in een *Tenant.* Een Tenant wordt geïsoleerd in het netwerk, de opslag en de compute-laag van andere tenants. Opslag-en reken eenheden die aan de verschillende tenants zijn toegewezen, kunnen elkaar niet zien of met elkaar communiceren op het niveau van de HANA-grote instantie stempel. Een klant kan ervoor kiezen om implementaties in verschillende tenants te hebben. Ook is er geen communicatie tussen tenants op het stempel niveau van de grootschalige grote instantie.
- **SKU-categorie**: Voor HANA grote instanties worden de volgende twee categorieën Sku's aangeboden:
    - **Klasse type I**: S72, S72m, S96, S144, S144m, S192, S192m en S192xm
    - **Klasse type II**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm en S960m
- **Stempel**: Hiermee wordt de micro soft interne implementatie grootte van HANA grote instanties gedefinieerd. Voordat de eenheden van de grote instanties van HANA kunnen worden geïmplementeerd, moet een HANA-stempel voor grote instanties die uit reken-, netwerk-en opslag racks bestaat, worden geïmplementeerd op een locatie van het Data Center. Een dergelijke implementatie heet een HANA grote instantie stempel of van revisie 4 (zie hieronder). Gebruik de alternatieve waarde voor de rij met **grote instanties**
- **Revisie**: Er zijn twee verschillende stempel revisies voor HANA grote instantie tempels. Deze verschillen qua architectuur en nabijheid van Azure virtual machine-hosts
    - Revisie 3 (Rev 3): is het oorspronkelijke ontwerp dat is geïmplementeerd vanaf medio het jaar 2016
    - "Revisie 4" (Rev 4): is een nieuw ontwerp dat dichter bij de hosts van virtuele Azure-machines kan zorgen en met die lagere netwerk latentie tussen virtuele machines van Azure en HANA grote instantie-eenheden. 

Er zijn verschillende extra resources beschikbaar voor het implementeren van een SAP-werk belasting in de Cloud. Als u van plan bent een implementatie van SAP HANA in azure uit te voeren, moet u zich bewust zijn van de beginselen van Azure IaaS en de implementatie van SAP-workloads op Azure IaaS. Zie [SAP-oplossingen op virtuele machines van Azure gebruiken](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) voor meer informatie voordat u doorgaat. 

**Volgende stappen**
- Zie [HLI certificering](hana-certification.md)