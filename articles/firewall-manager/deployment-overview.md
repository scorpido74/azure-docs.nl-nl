---
title: Overzicht van de implementatie van Azure Firewall Manager preview
description: Meer informatie over de implementatie stappen op hoog niveau die vereist zijn voor de preview-versie van Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: df87e652d2969d4ae12e97a2b455648cf39382c3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502028"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Overzicht van de implementatie van Azure Firewall Manager preview

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Er is meer dan één manier om Azure Firewall Manager-preview te implementeren, maar het volgende algemene proces wordt aanbevolen.

## <a name="prerequisites"></a>Vereisten

> [!IMPORTANT]
> Azure Firewall Manager-Preview moet expliciet worden ingeschakeld met de `Register-AzProviderFeature` Power shell-opdracht.
>Voer vanuit een Power shell-opdracht prompt de volgende opdrachten uit:
>
>```azure-powershell
>connect-azaccount
>Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
>```
>Het duurt Maxi maal 30 minuten voordat de functie registratie is voltooid. Voer de volgende opdracht uit om de status van de > registratie te controleren:
>
>`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`



## <a name="general-deployment-process"></a>Algemeen implementatie proces

1. Uw hub-en spoke-architectuur maken

   - Maak een beveiligde virtuele hub met behulp van Azure Firewall Manager en voeg virtuele netwerk verbindingen toe.<br>*or*<br>
   - Maak een virtuele WAN-hub en voeg virtuele netwerk verbindingen toe.
2. Beveiligings providers selecteren

   - Klaar tijdens het maken van een beveiligde virtuele hub.<br>*or*<br>
   - Een bestaande virtuele WAN-hub converteren naar een beveiligde virtuele hub.
3. Een firewall beleid maken en dit koppelen aan uw hub

   - Alleen van toepassing als Azure Firewall wordt gebruikt.
   - SECaaS-beleids regels (Security as a Service) van derden worden geconfigureerd via de beheer ervaring voor partners.
4. Router instellingen configureren om verkeer door te sturen naar uw beveiligde hub

   - Stuur eenvoudig verkeer naar uw beveiligde hub voor filteren en logboek registratie zonder door de gebruiker gedefinieerde routes (UDR) op spoke Virtual Networks met behulp van de instellings pagina route-instellingen voor beveiligde virtuele hub.

> [!NOTE]
> - U kunt niet meer dan één hub per virtuele WAN per regio hebben. Maar u kunt meerdere virtuele Wan's in de regio toevoegen om dit te doen.
> - U kunt geen overlappende IP-ruimten voor hubs in een vWAN hebben.
> - Uw hub VNet-verbindingen moeten zich in dezelfde regio bevinden als de hub.

## <a name="next-steps"></a>Volgende stappen

- [Zelf studie: uw Cloud netwerk beveiligen met Azure Firewall Manager Preview met behulp van de Azure Portal](secure-cloud-network.md)