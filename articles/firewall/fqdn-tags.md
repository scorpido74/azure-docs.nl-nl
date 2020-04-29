---
title: Overzicht van FQDN-labels voor Azure Firewall
description: Een FQDN-label vertegenwoordigt een groep FQDN-namen (FULLy Qualified Domain names) die zijn gekoppeld aan bekende micro soft-Services.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/24/2020
ms.author: victorh
ms.openlocfilehash: f4fcadb6d930f7863c733dad105432a64127b59b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232646"
---
# <a name="fqdn-tags-overview"></a>Overzicht van FQDN-labels

Een FQDN-label vertegenwoordigt een groep FQDN-namen (FULLy Qualified Domain names) die zijn gekoppeld aan bekende micro soft-Services. U kunt een FQDN-tag in toepassings regels gebruiken om het vereiste uitgaande netwerk verkeer via uw firewall toe te staan.

Als u bijvoorbeeld hand matig Windows Update netwerk verkeer via uw firewall wilt toestaan, moet u meerdere toepassings regels maken per micro soft-documentatie. Met FQDN-Tags kunt u een toepassings regel maken, de tag **Windows-updates** toevoegen en nu netwerk verkeer naar micro soft Windows Update-eind punten door lopen van uw firewall.

U kunt geen eigen FQDN-Tags maken en u hoeft niet op te geven welke FQDN-namen zijn opgenomen in een tag. Micro soft beheert de FQDN-code die is opgenoemd door de FQDN-tag en werkt de tag bij als FQDN-wijzigingen. 

<!--- screenshot of application rule with a FQDN tag.-->

In de volgende tabel ziet u de huidige FQDN-Tags die u kunt gebruiken. Micro soft onderhoudt deze tags en u kunt verwachten dat er periodiek extra tags worden toegevoegd.

## <a name="current-fqdn-tags"></a>Huidige FQDN-Tags

|FQDN-label  |Beschrijving  |
|---------|---------|
|Windows Update     |Uitgaande toegang tot Microsoft Update toestaan, zoals beschreven in [een firewall configureren voor software-updates](https://technet.microsoft.com/library/bb693717.aspx).|
|Windows Diagnostische gegevens|Uitgaande toegang tot alle [Windows diagnostische eind punten](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints)toestaan.|
|Microsoft Active Protection Service (kaarten)|Uitgaande toegang tot [kaarten](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/)toestaan.|
|App Service Environment (ASE)|Hiermee staat u uitgaande toegang tot ASE-platform verkeer toe. Dit label geldt niet voor klantspecifieke opslag-en SQL-eind punten die zijn gemaakt door ASE. Deze moeten worden ingeschakeld via [service-eind punten](../virtual-network/tutorial-restrict-network-access-to-resources.md) of hand matig worden toegevoegd.<br><br>Zie [een app service Environment vergren delen](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase)voor meer informatie over het integreren van Azure Firewall met ASE.|
|Azure Backup|Hiermee wordt uitgaande toegang tot de Azure Backup Services toegestaan.|
|Azure HDInsight|Hiermee staat u uitgaande toegang toe voor verkeer van HDInsight-platform. Dit label geldt niet voor klantspecifieke opslag of SQL-verkeer van HDInsight. Schakel deze optie in met [service-eind punten](../virtual-network/tutorial-restrict-network-access-to-resources.md) of voeg ze hand matig toe.|
|WindowsVirtualDesktop (WVD)|Hiermee staat u het verkeer van een virtueel Windows-bureaublad platform toe. Dit label geldt niet voor implementatie-specifieke opslag en Service Bus eind punten die zijn gemaakt door WVD. Daarnaast zijn er regels voor DNS-en KMS-netwerken vereist. Voor meer informatie over het integreren van Azure Firewall met WVD raadpleegt [u Azure firewall gebruiken voor het beveiligen van Windows-implementaties van virtuele Bureau bladen](protect-windows-virtual-desktop.md). 

> [!NOTE]
> Wanneer u de FQDN-tag in een toepassings regel selecteert, moet het veld Protocol: poort worden ingesteld op **https**.

## <a name="next-steps"></a>Volgende stappen

Zie [zelf studie: Azure firewall implementeren en configureren met behulp van de Azure Portal](tutorial-firewall-deploy-portal.md)voor meer informatie over het implementeren van een Azure firewall.
