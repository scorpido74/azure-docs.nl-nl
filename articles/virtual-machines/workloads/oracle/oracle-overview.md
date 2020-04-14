---
title: Oracle-oplossingen op Microsoft Azure | Microsoft Documenten
description: Meer informatie over opties voor het implementeren van Oracle-toepassingen en -oplossingen op Microsoft Azure, inclusief volledig werken op Azure-infrastructuur of het gebruik van cross-cloudconnectiviteit met Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: mimckitt
ms.openlocfilehash: 8c266986ac795be48f3a8439afc5ae4752ff3ed7
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263247"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Overzicht van Oracle-toepassingen en -oplossingen op Azure

In dit artikel worden mogelijkheden geïntroduceerd om Oracle-oplossingen uit te voeren met behulp van Azure-infrastructuur. Zie ook gedetailleerde introducties van beschikbare [Oracle VM-afbeeldingen](oracle-vm-solutions.md) in de Azure Marketplace en de mogelijkheid om Azure te [verbinden met Oracle Cloud Infrastructure (OCI).](oracle-oci-overview.md)

## <a name="oracle-databases-on-azure-infrastructure"></a>Oracle-databases op Azure-infrastructuur

Oracle-databases uitvoeren op Azure-infrastructuur met Oracle Database op Oracle Linux-afbeeldingen die beschikbaar zijn in de Azure Marketplace:

* Oracle Database 12.1, 12.2 en 18.3 Enterprise Edition 

* Oracle Database 12.1, 12.2 en 18.3 Standard Edition 

U er ook voor kiezen om Oracle Database in te stellen op een niet-Oracle Linux-afbeelding die beschikbaar is in Azure, een oplossing te baseren op een aangepaste afbeelding die u helemaal opnieuw in Azure maakt of een aangepaste afbeelding uploaden vanuit uw on-premises omgeving.

Configureer optioneel met meerdere aangesloten schijven en verbeter de databaseprestaties door Oracle Automated Storage Management (ASM) te installeren.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Toepassingen op Oracle Linux en WebLogic Server

Voer bedrijfstoepassingen uit in Azure op ondersteunde Oracle-besturingssystemen. De volgende afbeeldingen zijn beschikbaar in de Azure Marketplace:

* Oracle WebLogic Server 12.1.2

* Oracle Linux met de Unbreakable Enterprise Kernel (UEK) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 en 7.6 

## <a name="high-availability-and-disaster-recovery-options"></a>Opties voor hoge beschikbaarheid en noodherstel

* Configureer [Oracle Data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956), Active Data Guard met [FSFO,](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html) [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) of [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html) op Azure-infrastructuur in combinatie met [beschikbaarheidszones](../../../availability-zones/az-overview.md) voor hoge beschikbaarheid in de regio. U deze configuraties ook instellen in meerdere Azure-regio's voor extra beschikbaarheid en herstel na noodgevallen.

* Gebruik [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) om noodherstel voor uw Oracle Linux VM's in Azure en uw on-premises of fysieke servers te orkestreren en te beheren. 

* Oracle Real Application Clusters (RAC) inschakelen in Azure met [Azure VMWare Solution](https://docs.azure.cloudsimple.com/oracle-rac/) of [FlashGrid SkyCluster.](https://www.flashgrid.io/oracle-rac-in-azure/)

## <a name="backup-oracle-workloads"></a>Back-upmaken van Oracle-workloads

* Maak een back-up van uw Oracle VM's met [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview)

* Maak een back-up van uw Oracle Database met Oracle RMAN en gebruik optioneel [Azure Blob Fuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux) om een zeer [redudant Azure Blob Storage-account](https://docs.microsoft.com/azure/storage/common/storage-redundancy) te monteren en uw RMAN-back-ups erop te schrijven voor extra tolerantie.

## <a name="integration-of-azure-with-oci"></a>Integratie van Azure met OCI

Oracle Applications uitvoeren in Azure-infrastructuur, verbonden met backenddatabases in Oracle Cloud Infrastructure (OCI). Deze oplossing maakt gebruik van de volgende mogelijkheden: 

* **Cross-cloud netwerken** - Gebruik de directe interconnect die beschikbaar is tussen Azure ExpressRoute en Oracle FastConnect om verbindingen met hoge bandbreedte, privé en lage latentie tot stand te brengen tussen de toepassing en de databaselaag.
* **Geïntegreerde identiteit** - Stel federatieve identiteit in tussen Azure AD en Oracle IDCS om één identiteitsbron voor de oplossingen te maken. Schakel één aanmelding in om resources in OCI en Azure te beheren.

### <a name="deploy-oracle-applications-on-azure"></a>Oracle-toepassingen implementeren op Azure

Gebruik Terraform-sjablonen om Azure-infrastructuur in te stellen en Oracle-toepassingen te installeren. 

> [!IMPORTANT]
> Oracle zal deze toepassingen certificeren om in Azure uit te voeren wanneer het de Azure / Oracle Cloud interconnect-oplossing in mei 2020 gebruikt.

* E-Business Suite
* JD Edwards EnterpriseOne
* Peoplesoft
* Oracle Retail-toepassingen
* Oracle Hyperion Financieel Beheer

Implementeer ook aangepaste toepassingen in Azure die verbinding maken met OCI en andere Azure-services.

### <a name="set-up-oracle-databases-in-oci"></a>Oracle-databases instellen in OCI

Gebruik Oracle Database Cloud Services (Autonomous Database, RAC, Exadata, DBaaS, Single Node) in combinatie met Oracle Applications die in Azure worden uitgevoerd. Meer informatie over [oci-databaseopties](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licentieverlening

De implementatie van Oracle Applications in Azure is gebaseerd op een "bring your own license"-model. Er wordt aangenomen dat u een goede licentie hebt om Oracle-software te gebruiken en dat u een huidige ondersteuningsovereenkomst met Oracle hebt. Oracle heeft licentiemobiliteit gegarandeerd van on-premises naar Azure. Zie de [veelgestelde vragen](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)over Oracle-Azure .

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het implementeren van [Oracle VM-afbeeldingen](oracle-vm-solutions.md) in azure-infrastructuur.

* Meer informatie over het [verbinden van Azure met OCI](oracle-oci-overview.md).

* Bekijk de [Oracle on Azure-overzichtssessie](https://myignite.techcommunity.microsoft.com/sessions/82915) van Ignite 2019. 
