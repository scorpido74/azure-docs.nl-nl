---
title: Ondersteunde VM-SKU's voor Azure Policy
description: Een artikel waarin de ondersteunde VM SKU's (door Publisher, Image Offer en Image SKU) worden beschreven die worden ondersteund voor het ingebouwde Azure-beleid dat door Backup wordt geleverd
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1b6a94b0f57ecfea946654c6cae38ac335335e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980112"
---
# <a name="supported-vm-skus-for-azure-policy"></a>Ondersteunde VM-SKU's voor Azure Policy

Azure Backup biedt een ingebouwd beleid (met Azure Policy) dat kan worden toegewezen aan **alle Azure VM's op een opgegeven locatie binnen een abonnement of resourcegroep.** Wanneer dit beleid is toegewezen aan een bepaald bereik, worden alle nieuwe VM's die in dat bereik zijn gemaakt, automatisch geconfigureerd voor back-up naar een **bestaande kluis op dezelfde locatie en een abonnement.** In de onderstaande tabel vindt u alle VM SKU's die door dit beleid worden ondersteund.

### <a name="supported-vms"></a>**Ondersteunde VM's**

**Beleidsnaam:** Back-up configureren op VM's van een locatie naar een bestaande centrale kluis op dezelfde locatie

Afbeeldingsuitgever | Afbeeldingsaanbieding | Afbeelding SKU
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2008 R2 SP (2008-R2-SP1-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012-datacenter (datacenter 2012)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012-datacenter (2012-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 R2-datacenter (datacenter 2012-R2)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 R2-datacenter (2012-R2-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016-datacenter (datacenter 2016)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016-datacenter - Servercore (datacenter-server-core 2016)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016-datacenter - Servercore (2016-Datacenter-Server-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016-datacenter (2016-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Datacenterservercore van Windows Server 2019 met containers (2016-Datacenter-met-containers)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Extern bureaublad-sessiehost 2016 (2016-Datacenter-met-RDSH)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019-datacenter (datacenter 2019)
MicrosoftWindowsServer | WindowsServer | Datacenterservercore van Windows Server 2019 (datacenter-core 2019)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Datacenter Server Core van Windows Server 2019 (2019-Datacenter-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | Datacenterservercore van Windows Server 2019 met containers (2019-Datacenter-Core-met-containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Datacenter servercore van Windows Server 2019 met containers (2019-Datacenter-Core-met-containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter (2019-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter with Containers (2019-Datacenter-met-containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter with Containers (2019-Datacenter-met-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (zh-cn) (2019-Datacenter-zhcn)
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-met-Containers-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1803-met-Containers-smalldisk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | Alle Beeld SKU's
MicrosoftSQLServer | SQL2016SP1-WS2016 | Alle Beeld SKU's
MicrosoftSQLServer | SQL2016-WS2016  | Alle Beeld SKU's
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | Alle Beeld SKU's
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | Alle Beeld SKU's
MicrosoftSQLServer | SQL2016-WS2012R2 | Alle Beeld SKU's
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Alle Beeld SKU's
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | Alle Beeld SKU's
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | Alle Beeld SKU's
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | Alle Beeld SKU's
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | Alle Beeld SKU's
MicrosoftRServer | MLServer-WS2016 | Alle Beeld SKU's
MicrosoftVisualStudio | VisualStudio (VisualStudio) | Alle Beeld SKU's
MicrosoftVisualStudio | Windows | Alle Beeld SKU's
MicrosoftDynamicsAX (MicrosoftDynamicsAX) | Dynamics  | Pre-Req-AX7-Onebox-U8
microsoft-advertenties | windows-data-science-vm | Alle Beeld SKU's
MicrosoftWindowsDesktop | Windows-10 | Alle Beeld SKU's
RedHat | RHEL | 6.7, 6.8, 6.9, 6.10, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7
RedHat | RHEL-SAP-HANA | 6.7, 7.2, 7.3
SUSE | SLES | 12.X
SUSE | SLES-HPC | 12.X
SUSE | SLES-HPC-Prioriteit | 12.X
SUSE | SLES-SAP | 12.X
SUSE | SLES-SAP-BYOS | 12.X
SUSE | SLES-prioriteit | 12.X
SUSE | SLES-BYOS | 12.X
SUSE | SLES-SAPCAL | 12.X
SUSE | SLES-Standaard | 12.X
Canonical | UbuntuServer | 14.04.0-LTS
Canonical | UbuntuServer | 14.04.1-LTS
Canonical | UbuntuServer | 14.04.2-LTS
Canonical | UbuntuServer | 14.04.3-LTS
Canonical | UbuntuServer | 14.04.4-LTS
Canonical | UbuntuServer | 14.04.5-DAILY-LTS
Canonical | UbuntuServer | 14.04.5-LTS
Canonical | UbuntuServer | 16.04-DAILY-LTS
Canonical | UbuntuServer | 16.04-LTS
Canonical | UbuntuServer | 16.04.0-LTS
Canonical | UbuntuServer | 18.04-DAILY-LTS
Canonical | UbuntuServer | 18.04-LTS
Oracle | Oracle-Linux | 6.8, 6.9, 6.10, 7.3, 7.4, 7.5, 7.6
OpenLogic | CentOS | 6.X, 7.X
OpenLogic | CentOS–LVM | 6.X, 7.X
OpenLogic | CentOS–SRIOV | 6.X, 7.X
cloudera | cloudera-centos-os | 7.X
