---
title: Laagbeveiliging transporteren in Azure HDInsight
description: Tls (Transport layer security) en secure sockets layer (SSL) zijn cryptografische protocollen die communicatiebeveiliging bieden via een computernetwerk.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: fbe602581ebcea6385fb9cc9953d8e48272ce429
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771961"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Laagbeveiliging transporteren in Azure HDInsight

Verbindingen met het HDInsight-cluster via `https://CLUSTERNAME.azurehdinsight.net` het eindpunt van het openbare cluster worden geproxieerd via clustergatewayknooppunten. Deze verbindingen worden beveiligd met behulp van een protocol genaamd TLS. Het afdwingen van hogere versies van TLS op gateways verbetert de beveiliging voor deze verbindingen. Zie [Het TLS 1.0-probleem oplossen](https://docs.microsoft.com/security/solving-tls1-problem)voor meer informatie over waarom u nieuwere versies van TLS moet gebruiken.

Standaard accepteren Azure HDInsight-clusters TLS 1.2-verbindingen op openbare HTTPS-eindpunten en oudere versies voor achterwaartse compatibiliteit. U de minimale TLS-versie beheren die wordt ondersteund op de gatewayknooppunten tijdens het maken van het cluster met behulp van de Azure-portal of een Resource Manager-sjabloon. Selecteer voor de portal de TLS-versie op het tabblad **Beveiliging + netwerken** tijdens het maken van het cluster. Gebruik de eigenschap **minSupportedTlsVersion** voor een resourcemanagersjabloon tijdens de implementatietijd. Zie [HDInsight minimum TLS 1.2 Quickstart-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls)voor een voorbeeldsjabloon . Deze eigenschap ondersteunt drie waarden: "1.0", "1.1" en "1.2", die overeenkomen met respectievelijk TLS 1.0+, TLS 1.1+ en TLS 1.2+.

> [!IMPORTANT]
> Vanaf 30 juni 2020 zal Azure HDInsight TLS 1.2 of hoger versies afdwingen voor alle HTTPS-verbindingen. We raden u aan ervoor te zorgen dat al uw klanten klaar zijn om TLS 1.2 of latere versies te verwerken. Zie [Azure HDInsight TLS 1.2 Enforcement](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Een virtueel netwerk plannen voor Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md)
* [Virtuele netwerken maken voor Azure HDInsight-clusters.](hdinsight-create-virtual-network.md)
* [Netwerkbeveiligingsgroepen](../virtual-network/security-overview.md).
