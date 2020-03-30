---
title: Ken de voorwaarden van SAP HANA op Azure (Grote Exemplaren) | Microsoft Documenten
description: Ken de voorwaarden van SAP HANA op Azure (Large Instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6785ae821f701121185f0064c6317c69d50191ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617049"
---
# <a name="know-the-terms"></a>Inzicht in de voorwaarden

Verschillende veelvoorkomende definities worden veel gebruikt in de architectuur- en technische implementatiegids. Let op de volgende termen en hun betekenissen:

- **IaaS**: Infrastructure as a service.
- **PaaS**: Platform as a service.
- **SaaS**: Software as a service.
- **SAP-component**: een afzonderlijke SAP-toepassing, zoals ERP Central Component (ECC), Business Warehouse (BW), Solution Manager of Enterprise Portal (EP). SAP-componenten kunnen worden gebaseerd op traditionele ABAP- of Java-technologieën of een niet-NetWeaver-toepassing, zoals Bedrijfsobjecten.
- **SAP-omgeving**: Een of meer SAP-componenten zijn logisch gegroepeerd om een bedrijfsfunctie uit te voeren, zoals ontwikkeling, kwaliteitsborging, training, disaster recovery of productie.
- **SAP-landschap**: Verwijst naar de volledige SAP-assets in uw IT-landschap. Het SAP-landschap omvat alle productie- en niet-productieomgevingen.
- **SAP-systeem**: De combinatie van DBMS-laag en -toepassingslaag van bijvoorbeeld een SAP ERP-ontwikkelingssysteem, een SAP BW-testsysteem en een SAP CRM-productiesysteem. Azure-implementaties bieden geen ondersteuning voor het verdelen van deze twee lagen tussen on-premises en Azure. Een SAP-systeem wordt on-premises geïmplementeerd of geïmplementeerd in Azure. U de verschillende systemen van een SAP-landschap implementeren in Azure of on-premises. U bijvoorbeeld de SAP CRM-ontwikkelings- en testsystemen implementeren in Azure terwijl u het SAP CRM-productiesysteem on-premises implementeert. Voor SAP HANA op Azure (Large Instances) is het de bedoeling dat u de SAP-toepassingslaag van SAP-systemen host in VM's en de bijbehorende SAP HANA-instantie op een eenheid in de SAP HANA op Azure -stempel (Large Instances).
- **Big Instance-stempel:** een hardware-infrastructuurstack die SAP HANA TDI-gecertificeerd is en is toegewijd aan het uitvoeren van SAP HANA-exemplaren binnen Azure.
- **SAP HANA op Azure (grote exemplaren):** Officiële naam voor de aanbieding in Azure om HANA-exemplaren uit te voeren op SAP HANA TDI-gecertificeerde hardware die is geïmplementeerd in large instance-stempels in verschillende Azure-regio's. De gerelateerde term *HANA Large Instance* is een afkorting voor *SAP HANA op Azure (Large Instances)* en wordt veel gebruikt in deze handleiding voor technische implementatie.
- **Cross-premises**: beschrijft een scenario waarbij VM's worden geïmplementeerd op een Azure-abonnement met site-to-site-, multi-site- of Azure ExpressRoute-connectiviteit tussen on-premises datacenters en Azure. In veelvoorkomende Azure-documentatie worden dit soort implementaties ook beschreven als cross-premises scenario's. De reden voor de verbinding is om on-premises domeinen, on-premises Azure Active Directory/OpenLDAP en on-premises DNS uit te breiden naar Azure. Het on-premises landschap wordt uitgebreid naar de Azure-assets van de Azure-abonnementen. Met deze extensie kunnen de VM's deel uitmaken van het on-premises domein. 

   Domeingebruikers van het on-premises domein hebben toegang tot de servers en kunnen services uitvoeren op die VM's (zoals DBMS-services). Communicatie en naamoplossing tussen VM's die on-premises en door Azure geïmplementeerde VM's zijn geïmplementeerd, is mogelijk. Dit scenario is typerend voor de manier waarop de meeste SAP-assets worden geïmplementeerd. Zie [Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) en Maak een virtueel netwerk met een [site-to-site-verbinding met behulp van de Azure-portal voor](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)meer informatie.
- **Tenant**: Een klant die is geïmplementeerd in HANA Large Instance-stempel wordt geïsoleerd in een *tenant.* Een tenant is geïsoleerd in de netwerk-, opslag- en rekenlaag van andere tenants. Opslag- en rekeneenheden die aan de verschillende tenants zijn toegewezen, kunnen elkaar niet zien of met elkaar communiceren op het stempelniveau van HANA Large Instance. Een klant kan ervoor kiezen om implementaties in verschillende tenants te hebben. Zelfs dan is er geen communicatie tussen huurders op het HANA Large Instance-stempelniveau.
- **SKU categorie**: Voor HANA Large Instance worden de volgende twee categorieën SKU's aangeboden:
    - **Klasse I**: S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 en S224m
    - **Klasse II**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm en S960m
- **Stempel:** definieert de interne implementatiegrootte van Microsoft van HANA Large Instances. Voordat HANA Large Instance-eenheden kunnen worden geïmplementeerd, moet een HANA Large Instance-stempel bestaande uit compute-, netwerk- en opslagracks worden geïmplementeerd op een datacenterlocatie. Een dergelijke implementatie wordt een HANA Large instance-stempel genoemd of van Revisie 4 (zie hieronder) waarop we het alternatief van de term **van Grote instantierij** gebruiken
- **Revisie**: Er zijn twee verschillende stempelrevisies voor HANA Large Instance stempels. Deze verschillen in architectuur en de nabijheid van Azure virtual machine hosts
    - "Revisie 3" (Rev 3): is het oorspronkelijke ontwerp dat werd ingezet vanaf het midden van het jaar 2016
    - "Revisie 4" (Rev 4): is een nieuw ontwerp dat dichter bij Azure virtual machine hosts en met die lagere netwerklatentie tussen Azure VM's en HANA Large Instance-eenheden kan bieden 

Er zijn verschillende extra bronnen beschikbaar voor het implementeren van een SAP-workload in de cloud. Als u van plan bent een implementatie van SAP HANA in Azure uit te voeren, moet u ervaring hebben met en op de hoogte zijn van de principes van Azure IaaS en de implementatie van SAP-workloads op Azure IaaS. Zie [SAP-oplossingen gebruiken op virtuele Azure-machines](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) voor meer informatie voordat u verdergaat met SAP-oplossingen op virtuele Azure-machines. 

**Volgende stappen**
- [Hli-certificering doorverwijzen](hana-certification.md)