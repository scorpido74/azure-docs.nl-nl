---
title: Overzicht van FQDN-tags voor Azure Firewall
description: Een FQDN-tag vertegenwoordigt een groep volledig gekwalificeerde domeinnamen (FQDN's) die zijn gekoppeld aan bekende Microsoft-services.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 6396f8292a4c54f7fce237439f37c3e8156d59e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74169039"
---
# <a name="fqdn-tags-overview"></a>Overzicht van FQDN-tags

Een FQDN-tag vertegenwoordigt een groep volledig gekwalificeerde domeinnamen (FQDN's) die zijn gekoppeld aan bekende Microsoft-services. U een FQDN-tag in toepassingsregels gebruiken om het vereiste uitgaande netwerkverkeer via uw firewall toe te staan.

Als u bijvoorbeeld het netwerkverkeer van Windows Update handmatig door uw firewall wilt toestaan, moet u meerdere toepassingsregels maken per Microsoft-documentatie. Met FQDN-tags u een toepassingsregel maken, de **Windows-updates-tag** opnemen en nu kan netwerkverkeer naar Microsoft Windows Update-eindpunten door uw firewall stromen.

U uw eigen FQDN-tags niet maken en u ook niet opgeven welke FQDN's in een tag zijn opgenomen. Microsoft beheert de FQDN's die zijn voorzien van de FQDN-tag en werkt de tag bij wanneer FQDN's veranderen. 

<!--- screenshot of application rule with a FQDN tag.-->

In de volgende tabel ziet u de huidige FQDN-tags die u gebruiken. Microsoft onderhoudt deze tags en u verwachten dat er periodiek extra tags worden toegevoegd.

## <a name="current-fqdn-tags"></a>Huidige FQDN-tags

|FQDN-tag  |Beschrijving  |
|---------|---------|
|Windows Update     |Buitenuitgaan de toegang tot Microsoft Update toestaan zoals beschreven in [Hoe een firewall voor software-updates te configureren.](https://technet.microsoft.com/library/bb693717.aspx)|
|Windows Diagnostische gegevens|Buitengebonden toegang toestaan tot alle [Eindpunten van Windows Diagnostics](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Microsoft Active Protection Service (MAPS)|Laat uitgaande toegang tot [MAPS toestaan.](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/)|
|App-serviceomgeving (ASE)|Hiermee u uitgaande toegang tot ASE-platformverkeer. Deze tag heeft geen betrekking op klantspecifieke opslag- en SQL-eindpunten die door ASE zijn gemaakt. Deze moeten worden ingeschakeld via [Service Endpoints](../virtual-network/tutorial-restrict-network-access-to-resources.md) of handmatig worden toegevoegd.<br><br>Zie [Een app-serviceomgeving vergrendelen](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase)voor meer informatie over het integreren van Azure Firewall met ASE.|
|Azure Backup|Hiermee u uitgaande toegang tot de Azure Backup-services.|
|Azure HDInsight|Hiermee u uitgaande toegang bieden voor HDInsight-platformverkeer. Deze tag heeft geen betrekking op klantspecifieke opslag- of SQL-verkeer van HDInsight. Schakel deze in met [serviceeindpunten](../virtual-network/tutorial-restrict-network-access-to-resources.md) of voeg ze handmatig toe.|

> [!NOTE]
> Wanneer u FQDN-tag selecteert in een toepassingsregel, moet het protocol:poortveld worden ingesteld op **https**.

## <a name="next-steps"></a>Volgende stappen

Zie [Zelfstudie: Azure Firewall implementeren en configureren met behulp van de Azure-portal](tutorial-firewall-deploy-portal.md)voor meer informatie over het implementeren van een Azure Firewall.
