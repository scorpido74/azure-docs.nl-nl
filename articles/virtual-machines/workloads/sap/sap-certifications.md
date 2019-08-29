---
title: Microsoft Azure certificeringen voor SAP | Microsoft Docs
description: De lijst met huidige configuraties en certificeringen van SAP op het Azure-platform is bijgewerkt.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: 38e63d8ca7d9db5247bd5fe07fbe59452a106cf6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098656"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>SAP-certificeringen en-configuraties die worden uitgevoerd op Microsoft Azure

SAP en micro soft hebben een lange geschiedenis van het samen werken in een sterk partnerschap met wederzijdse voor delen voor hun klanten. Micro soft werkt voortdurend het platform bij en verzendt nieuwe certificerings gegevens naar SAP om ervoor te zorgen dat Microsoft Azure het beste platform is waarop uw SAP-workloads worden uitgevoerd. In de volgende tabellen worden de door Azure ondersteunde configuraties en de lijst met groeiende SAP-certificeringen beschreven. 

## <a name="sap-hana-certifications"></a>SAP HANA-certificeringen
Referentie

- [SAP Hana gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) voor SAP Hana ondersteuning voor systeem eigen Azure-VM'S en Hana grote instanties.

| SAP-product | Ondersteund besturingssysteem | Aanbiedingen voor Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (inclusief de HANA-client software, bestaande uit SQLODBC, ODBO-Windows only, ODBC, JDBC-Stuur Programma's, HANA Studio en HANA-data base) | Red Hat Enterprise Linux, SUSE Linux Enterprise | VM-serie van de D-serie |
| Bedrijf 1 in HANA | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [SAP HANA gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Gecontroleerde Beschik baarheid voor GS5. Volledige ondersteuning voor M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> SAP HANA op Azure (grote instanties) [SAP Hana gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suite op HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, SAP HANA op Azure (grote instanties) <br /> [SAP HANA gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise voor BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> SAP HANA op Azure (grote instanties) [SAP Hana gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> SAP HANA op Azure (grote exemplaren) <br /> [SAP HANA gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Houd er rekening mee dat SAP de term ' clustering ' gebruikt in [SAP Hana gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) als synoniem voor ' scale-out ' en niet voor een clustering met hoge Beschik baarheid

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver-certificeringen
Microsoft Azure is gecertificeerd voor de volgende SAP-producten, met volledige ondersteuning van Microsoft en SAP.
Referentie

- [1928533-SAP-toepassingen op Azure: Ondersteunde producten en Azure VM-](https://launchpad.support.sap.com/#/notes/1928533) typen voor alle toepassingen op basis van SAP netweave, waaronder SAP TREX, SAP LiveCache en SAP-inhouds server. En alle data bases, met uitzonde ring van SAP HANA.


| SAP-product | Gastbesturingssysteem | RDBMS | Typen virtuele machines |
| --- | --- | --- | --- |
| SAP Business Suite-software | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (alleen Windows en Oracle Linux), DB2, SAP ASE |A5 to A11, D11 to D14, DS11 to DS14, DS11_v2 to DS15_v2, GS1 to GS5, D2s_v3 to D64s_v3, E2s_v3 to E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (alleen Windows en Oracle Linux), DB2, SAP ASE |A5 to A11, D11 to D14, DS11 to DS14, DS11_v2 to DS15_v2, GS1 to GS5, D2s_v3 to D64s_v3, E2s_v3 to E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |
| SAP Business Objects BI | Windows |N/A |A5 to A11, D11 to D14, DS11 to DS14, DS11_v2 to DS15_v2, GS1 to GS5, D2s_v3 to D64s_v3, E2s_v3 to E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (alleen Windows en Oracle Linux), DB2, SAP ASE |A5 to A11, D11 to D14, DS11 to DS14, DS11_v2 to DS15_v2, GS1 to GS5, D2s_v3 to D64s_v3, E2s_v3 to E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Andere SAP-werk belasting ondersteund op Azure

| SAP-product | Gastbesturingssysteem | RDBMS | Typen virtuele machines |
| --- | --- | --- | --- |
| SAP Business One op SQL Server | Windows  | SQL Server | Alle typen netwerkweaver gecertificeerde VM<br /> [SAP-notitie #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10,01 MS SP08 | Windows en Linux | | Alle typen netwerkweaver gecertificeerde VM<br /> SAP-notitie #2451795 |
| SAP Business Objects BI-platform | Windows en Linux | | SAP-notitie #2145537 |
| SAP Data Services 4.2 | | | SAP-notitie #2288344 |
| SAP Hybris commerce platform 5. x en 6. x | Windows | SQL Server, Oracle | Alle typen netwerkweaver gecertificeerde VM<br /> [Hybris-wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
