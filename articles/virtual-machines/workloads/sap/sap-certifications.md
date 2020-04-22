---
title: Microsoft Azure-certificeringen voor SAP | Microsoft Documenten
description: Bijgewerkte lijst met huidige configuraties en certificeringen van SAP op het Azure-platform.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/21/2020
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: c19471fee9235faffba12a12d9f92de77f60fd4d
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770526"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>SAP-certificeringen en -configuraties die worden uitgevoerd op Microsoft Azure

SAP en Microsoft hebben een lange geschiedenis van samenwerking in een sterk partnerschap dat wederzijdse voordelen heeft voor hun klanten. Microsoft werkt zijn platform voortdurend bij en verstrekt nieuwe certificeringsgegevens aan SAP om ervoor te zorgen dat Microsoft Azure het beste platform is om uw SAP-workloads uit te voeren. In de volgende tabellen worden door Azure ondersteunde configuraties en een lijst met groeiende SAP-certificeringen beschreven. Deze lijst is een overzichtslijst die hier en daar van de officiële SAPlijsten zou kunnen afwijken. Hoe u bij de gedetailleerde gegevens komen, wordt gedocumenteerd in het artikel [Welke SAP-software wordt ondersteund voor Azure-implementaties](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)

## <a name="sap-hana-certifications"></a>SAP HANA-certificeringen
Verwijzingen:

- [SAP HANA-gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) voor SAP HANA-ondersteuning voor native Azure VM's en HANA Large Instances.

| SAP-product | Ondersteund besturingssysteem | Azure-aanbiedingen |
| --- | --- | --- |
| SAP HANA Developer Edition (inclusief de HANA-clientsoftware bestaande uit SQLODBC, ODBO-Windows, ODBC, JDBC-stuurprogramma's, HANA-studio en HANA-database) | Red Hat Enterprise Linux, SUSE Linux Enterprise | D-Serie VM-familie |
| Business One op HANA | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64's <br /> [SAP HANA-gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Gecontroleerde beschikbaarheid voor GS5. Volledige ondersteuning voor M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, <br /> SAP HANA op Azure (Grote exemplaren) [SAP HANA Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suite op HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA op Azure (Large instances) [SAP HANA Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise voor BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA op Azure (Large instances) [SAP HANA Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA op Azure (grote exemplaren) <br /> [SAP HANA-gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Houd er rekening mee dat SAP de term 'clustering' in [SAP HANA Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) gebruikt als synoniem voor 'scale-out' en NIET voor hoge beschikbaarheid 'clustering'

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver-certificeringen
Microsoft Azure is gecertificeerd voor de volgende SAP-producten, met volledige ondersteuning van Microsoft en SAP.
Verwijzingen:

- [1928533 - SAP-toepassingen op Azure: ondersteunde producten en Azure VM-typen](https://launchpad.support.sap.com/#/notes/1928533) voor alle SAP NetWeaver-gebaseerde toepassingen, waaronder SAP TREX, SAP LiveCache en SAP Content Server. En alle databases, met uitzondering van SAP HANA.


| SAP-product | Gastbesturingssysteem | RDBMS | Typen virtuele machines |
| --- | --- | --- | --- |
| SAP Business Suite-software | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (alleen Windows en Oracle Linux), DB2, SAP ASE |A5 naar A11, D11 naar D14, DS11 naar DS14, DS11_v2 naar DS15_v2, GS1 naar GS5, D2s_v3 naar D64s_v3, D2as_v4 naar D64as_v4, E2s_v3 naar E64s_v3, E2as_v4 naar E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2 |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (alleen Windows en Oracle Linux), DB2, SAP ASE |A5 naar A11, D11 naar D14, DS11 naar DS14, DS11_v2 naar DS15_v2, GS1 naar GS5, D2s_v3 naar D64s_v3, D2as_v4 naar D64as_v4, E2s_v3 naar E64s_v3, E2as_v4 naar E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2 |
| SAP BusinessObjects BI | Windows |N.v.t. |A5 naar A11, D11 naar D14, DS11 naar DS14, DS11_v2 naar DS15_v2, GS1 naar GS5, D2s_v3 naar D64s_v3, D2as_v4 naar D64as_v4, E2s_v3 naar E64s_v3, E2as_v4 naar E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (alleen Windows en Oracle Linux), DB2, SAP ASE |A5 naar A11, D11 naar D14, DS11 naar DS14, DS11_v2 naar DS15_v2, GS1 naar GS5, D2s_v3 naar D64s_v3, D2as_v4 naar D64as_v4, E2s_v3 naar E64s_v3, E2as_v4 naar E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Andere SAP-workload die wordt ondersteund op Azure

| SAP-product | Gastbesturingssysteem | RDBMS | Typen virtuele machines |
| --- | --- | --- | --- |
| SAP Business One op SQL Server | Windows  | SQL Server | Alle NetWeaver-gecertificeerde VM-typen<br /> [SAP Note #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10,01 MS SP08 | Windows en Linux | | Alle NetWeaver Certified VM-typen<br /> SAP-notitie #2451795 |
| SAP Business Objects BI-platform | Windows en Linux | | SAP-notitie #2145537 |
| SAP Data Services 4.2 | | | SAP-notitie #2288344 |
| SAP Hybris Commerce Platform  | Windows | SQL Server, Oracle | Alle NetWeaver-gecertificeerde VM-typen <br /> [Hybris Wiki](https://cxwiki.sap.com/display/cloudss/Using+the+hybris+Platform+with+the+Cloud) |
| SAP Hybris Commerce Platform  | SLES 12 of recenter | SAP HANA | Alle NetWeaver-gecertificeerde VM-typen <br /> [Hybris Wiki](https://cxwiki.sap.com/display/cloudss/Using+the+hybris+Platform+with+the+Cloud) |
| SAP Hybris Commerce Platform  | RHEL 7 of meer recent | SAP HANA | Alle NetWeaver-gecertificeerde VM-typen <br /> [Hybris Wiki](https://cxwiki.sap.com/display/cloudss/Using+the+hybris+Platform+with+the+Cloud) |
| SAP (Hybris) Commerce Platform 1811 en hoger  | Windows, SLES of RHEL | SQL Azure DB | Alle NetWeaver-gecertificeerde VM-typen <br /> [Hybris Documentatie](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html) |
