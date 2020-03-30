---
title: SAP HANA-beschikbaarheid op Azure VM's - Overzicht | Microsoft Documenten
description: Beschrijft SAP HANA-bewerkingen op Azure native VM's.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bccaf45cf617bd31a584b6c73f3dd08877bc8587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71266062"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>SAP HANA hoge beschikbaarheid voor Virtuele Azure-machines

U tal van Azure-mogelijkheden gebruiken om bedrijfskritieke databases zoals SAP HANA te implementeren op Azure VM's. In dit artikel vindt u richtlijnen voor het bereiken van beschikbaarheid voor SAP HANA-exemplaren die worden gehost in Azure VM's. In het artikel worden verschillende scenario's beschreven die u implementeren door de Azure-infrastructuur te gebruiken om de beschikbaarheid van SAP HANA in Azure te vergroten. 

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u bekend bent met de basisbeginselen van Infrastructure as A Service (IaaS) in Azure, waaronder: 

- Virtuele machines of virtuele netwerken implementeren via de Azure-portal of PowerShell.
- Met behulp van de Azure cross-platform command-line interface (Azure CLI), inclusief de optie om Json-sjablonen (JavaScript Object Notation) te gebruiken.

In dit artikel wordt ook ervan uitgegaan dat u bekend bent met het installeren van SAP HANA-exemplaren en met het beheren en beheren van SAP HANA-exemplaren. Het is vooral belangrijk om vertrouwd te zijn met de setup en bewerkingen van HANA-systeemreplicatie. Dit omvat taken zoals back-up en herstel voor SAP HANA-databases.

Deze artikelen bieden een goed overzicht van het gebruik van SAP HANA in Azure:

- [Handmatige installatie van SAP HANA met één instantie op Azure VM's](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [SAP HANA-systeemreplicatie instellen in Azure VM's](sap-hana-high-availability.md)
- [Back-ups maken van SAP HANA in virtuele Azure-machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

Het is ook een goed idee om vertrouwd te zijn met deze artikelen over SAP HANA:

- [Hoge beschikbaarheid voor SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [VEELGESTELDE VRAGEN: Hoge beschikbaarheid voor SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Systeemreplicatie uitvoeren voor SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 SPS 01 Nieuw: Hoge beschikbaarheid](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Netwerkaanbevelingen voor SAP HANA-systeemreplicatie](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [SAP HANA-systeemreplicatie](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA-service automatisch opnieuw opstarten](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [SAP HANA-systeemreplicatie configureren](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Naast het feit dat u bekend bent met het implementeren van VM's in Azure, raden we u aan [de beschikbaarheid van virtuele Windows-machines in Azure te](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)beheren voordat u uw beschikbaarheidsarchitectuur definieert.

## <a name="service-level-agreements-for-azure-components"></a>Serviceniveauovereenkomsten voor Azure-componenten

Azure heeft verschillende beschikbaarheidsSLA's voor verschillende componenten, zoals netwerken, opslag en VM's. Alle SLA's zijn gedocumenteerd. Zie [Microsoft Azure Service Level Agreements voor](https://azure.microsoft.com/support/legal/sla/)meer informatie. 

[SLA voor virtuele machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) beschrijft drie verschillende SLA's, voor drie verschillende configuraties:

- Eén VM die [Azure premium SSD's](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) gebruikt voor de OS-schijf en alle gegevensschijven. Deze optie biedt een maandelijkse uptime van 99,9 procent.
- Meerdere (ten minste twee) VM's die zijn georganiseerd in een [Azure-beschikbaarheidsset.](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) Deze optie biedt een maandelijkse uptime van 99,95 procent.
- Meerdere (ten minste twee) VM's die zijn georganiseerd in een [Availablity Zone](https://docs.microsoft.com/azure/availability-zones/az-overview). Deze optie voorzag in een maandelijkse uptime van 99,99 procent.

Meet uw beschikbaarheidsvereiste aan de hand van de SLA's die Azure-componenten kunnen bieden. Kies vervolgens uw scenario's voor SAP HANA om uw vereiste beschikbaarheidsniveau te bereiken.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [sap HANA-beschikbaarheid binnen één Azure-regio](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region).
- Meer informatie over [de beschikbaarheid van SAP HANA in Azure-regio's.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 















  


