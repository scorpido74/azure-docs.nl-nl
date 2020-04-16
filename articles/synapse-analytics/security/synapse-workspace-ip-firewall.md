---
title: IP-firewallregels configureren in Azure Synapse Analytics
description: Een artikel waarin u ip-firewallregels configureren in Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: f36a76187538d93b57d2d3f5973408f141271f67
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424109"
---
# <a name="azure-synapse-analytics-ip-firewall-rules-preview"></a>IP-firewallregels voor Azure Synapse Analytics (voorbeeld)

In dit artikel worden IP-firewallregels uitgelegd en leert u hoe u deze configureren in Azure Synapse Analytics.

## <a name="ip-firewall-rules"></a>IP-firewallregels

IP-firewallregels verlenen of weigeren toegang tot uw Synapse-werkruimte op basis van het oorspronkelijke IP-adres van elke aanvraag. U IP-firewallregels configureren voor uw werkruimte. IP-firewallregels die op werkruimteniveau zijn geconfigureerd, zijn van toepassing op alle openbare eindpunten van de werkruimte (SQL-pools, SQL on-demand en Ontwikkeling).

## <a name="create-and-manage-ip-firewall-rules"></a>IP-firewallregels maken en beheren

Er zijn twee manieren waarop IP-firewallregels worden toegevoegd aan een Synapse-werkruimte. Als u een IP-firewall aan uw werkruimte wilt toevoegen, selecteert u **Beveiliging + netwerken** en schakelt u Verbindingen toestaan vanaf alle IP-adressen tijdens het maken van **werkruimtes.**

![IP-configuratie azure portal Synapse workspace.](./media/synpase-workspace-ip-firewall/ip-firewall-1.png)

![IP-configuratie azure portal Synapse workspace.](./media/synpase-workspace-ip-firewall/ip-firewall-2.png)

U ook IP-firewallregels toevoegen aan een Synapse-werkruimte nadat de werkruimte is gemaakt. Selecteer **Firewalls** onder **Beveiliging** vanuit Azure-portal. Als u een nieuwe IP-firewallregel wilt toevoegen, geeft u deze een naam, IP starten en IP beëindigen. Selecteer **Opslaan** wanneer u klaar bent.

![IP-configuratie van Azure Synapse-werkruimte in Azure-portal.](./media/synpase-workspace-ip-firewall/ip-firewall-3.png)

## <a name="connecting-to-synapse-from-your-own-network"></a>Verbinding maken met Synapse vanuit uw eigen netwerk

U verbinding maken met uw Synapse-werkruimte via Synapse Studio. U SQL Server Management Studio (SSMS) ook gebruiken om verbinding te maken met de SQL-bronnen (SQL-pools en SQL on-demand) in uw werkruimte.

Zorg ervoor dat de firewall op uw netwerk en lokale computer uitgaande communicatie op TCP-poorten 80, 443 en 1443 voor Synapse Studio mogelijk maakt.

Ook moet u uitgaande communicatie op UDP-poort 53 toestaan voor Synapse Studio. Als u verbinding wilt maken met behulp van tools zoals SSMS en Power BI, moet u uitgaande communicatie toestaan op TCP-poort 1433.

Als u de standaardinstelling voor het omleidingsverbindingsbeleid gebruikt, moet u mogelijk uitgaande communicatie toestaan op extra poorten. Meer informatie over verbindingsbeleid vindt u hier.

## <a name="next-steps"></a>Volgende stappen

Een [Azure Synapse Workspace maken](../quickstart-create-workspace.md)

Een Azure Synapse-werkruimte maken met een [VNet voor beheerde werkruimte](./synapse-workspace-managed-vnet.md)