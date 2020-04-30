---
title: IP-firewall regels configureren in azure Synapse Analytics
description: Een artikel waarin u de IP-firewall regels in azure Synapse Analytics kunt configureren
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: f36a76187538d93b57d2d3f5973408f141271f67
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424109"
---
# <a name="azure-synapse-analytics-ip-firewall-rules-preview"></a>IP-firewall regels voor Azure Synapse Analytics (preview-versie)

In dit artikel worden de IP-firewall regels uitgelegd en leert u hoe u deze configureert in azure Synapse Analytics.

## <a name="ip-firewall-rules"></a>IP-firewall regels

Met IP-firewall regels kan toegang tot uw Synapse-werk ruimte worden verleend of geweigerd op basis van het oorspronkelijke IP-adres van elke aanvraag. U kunt IP-firewall regels voor uw werk ruimte configureren. De IP-firewall regels die op het niveau van de werk ruimte zijn geconfigureerd, zijn van toepassing op alle open bare eind punten van de werk ruimte (SQL-groepen, SQL on-demand en Development).

## <a name="create-and-manage-ip-firewall-rules"></a>IP-firewall regels maken en beheren

Er zijn twee manieren waarop IP-firewall regels worden toegevoegd aan een Synapse-werk ruimte. Als u een IP-firewall wilt toevoegen aan uw werk ruimte, selecteert u **beveiliging en netwerken** en schakelt u **verbindingen van alle IP-adressen toestaan** tijdens het maken van de werk ruimte in.

![IP-configuratie van de Synapse-werk ruimte Azure Portal.](./media/synpase-workspace-ip-firewall/ip-firewall-1.png)

![IP-configuratie van de Synapse-werk ruimte Azure Portal.](./media/synpase-workspace-ip-firewall/ip-firewall-2.png)

U kunt ook IP-firewall regels toevoegen aan een Synapse-werk ruimte nadat de werk ruimte is gemaakt. Selecteer **firewalls** onder **beveiliging** van Azure Portal. Als u een nieuwe IP-firewall regel wilt toevoegen, geeft u deze een naam, start IP-adres en laatste IP-adres. Selecteer **Opslaan** wanneer u klaar bent.

![IP-configuratie van de Azure Synapse-werk ruimte in Azure Portal.](./media/synpase-workspace-ip-firewall/ip-firewall-3.png)

## <a name="connecting-to-synapse-from-your-own-network"></a>Verbinding maken met Synapse vanuit uw eigen netwerk

U kunt verbinding maken met uw Synapse-werk ruimte met behulp van Synapse Studio. U kunt ook SQL Server Management Studio (SSMS) gebruiken om verbinding te maken met de SQL-resources (SQL-groepen en SQL on-demand) in uw werk ruimte.

Zorg ervoor dat de firewall op uw netwerk en lokale computer uitgaande communicatie toestaat op TCP-poorten 80, 443 en 1443 voor Synapse Studio.

U moet ook uitgaande communicatie toestaan op UDP-poort 53 voor Synapse Studio. Als u verbinding wilt maken met hulpprogram ma's als SSMS en Power BI, moet u uitgaande communicatie toestaan op TCP-poort 1433.

Als u de standaard beleids instelling voor omleidings verbindingen gebruikt, moet u mogelijk uitgaande communicatie toestaan op extra poorten. Meer informatie over verbindings beleid vindt u hier.

## <a name="next-steps"></a>Volgende stappen

Een [Azure Synapse-werk ruimte](../quickstart-create-workspace.md) maken

Een Azure Synapse-werk ruimte maken met een [beheerde werk ruimte VNet](./synapse-workspace-managed-vnet.md)