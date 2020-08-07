---
title: Wat is Oracle WebLogic Server op Azure?
description: Meer informatie over het uitvoeren van Oracle WebLogic Server op Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: edburns
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2020
ms.author: edburns
ms.openlocfilehash: fe247e75040f658beb94a66176f802993268a7d8
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851852"
---
# <a name="what-is-oracle-weblogic-server-on-azure"></a>Wat is Oracle WebLogic Server op Azure?

Op deze pagina worden oplossingen beschreven voor het uitvoeren van een WebLogic-Server (WLS) op Azure Virtual Machines.  Deze oplossingen zijn gezamenlijk ontwikkeld door Oracle en micro soft.

Oracle WebLogic Server is de eerste Cloud systeem eigen, zakelijke Java-platform toepassings server voor het ontwikkelen en implementeren van gedistribueerde zakelijke toepassingen met meerdere lagen. Azure WebLogic Server biedt u de mogelijkheid om Cloud computing te gebruiken.  U krijgt meer keuze en flexibiliteit voor WebLogic-migratie, waaronder lift en verschuiving van uw Java et-toepassingen naar Azure Cloud.   WLS op Azure levert grote gevolgen met een kleine moeite. De aanbiedingen bieden u de mogelijkheid om uw LOB-toepassingen (line-of-Business) snel te starten.  Elke aanbieding biedt automatisch virtuele netwerk-, opslag-en Linux-resources.  Als er geen poging is gedaan, wordt de WebLogic-Server geïnstalleerd.  WLS op Azure stelt beveiliging in met een netwerk beveiligings groep, taak verdeling met Azure-app gateway, verificatie met Azure Active Directory en maakt automatisch verbinding met uw bestaande data base.

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="U kunt Azure Portal gebruiken om WebLogic Server te implementeren op Azure":::

Er zijn vier aanbiedingen beschikbaar om te voldoen aan verschillende scenario's: Eén knoop punt zonder een beheer server, één knoop punt met een beheer server, cluster en dynamisch cluster.  Probeer de aanbiedingen, ze zijn gratis beschikbaar.

_Deze aanbiedingen zijn uw eigen licentie_. Er wordt ervan uitgegaan dat u de juiste licenties voor Oracle al hebt en een juiste licentie hebt voor het uitvoeren van aanbiedingen in Microsoft Azure.

_Als u nauw keurig wilt werken met uw migratie scenario's met het technische team dat deze aanbiedingen ontwikkelt, selecteert u de knop [contact opnemen](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) _ in [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Programma managers, Architects en technici komen binnenkort terug en beginnen samen te werken.

## <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic-Server met één knoop punt

Deze aanbieding biedt een enkele virtuele machine en installeert WLS hierop. Er wordt geen domein gemaakt of de beheer server wordt gestart. Eén knoop punt is handig voor scenario's met een zeer aangepaste domein configuratie.

## <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic Server met de-beheer server

Deze aanbieding biedt een enkele virtuele machine en installeert WLS hierop. Er wordt een domein gemaakt en de beheer server wordt gestart, waarmee u het domein kunt beheren.

## <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server-cluster

Met deze aanbieding maakt u een Maxi maal beschikbaar cluster van virtuele WLS-machines. De-beheer server en alle beheerde servers worden standaard gestart, zodat u het domein kunt beheren.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Dynamische Oracle WebLogic Server-cluster

Met deze aanbieding maakt u een Maxi maal beschik bare en schaalbaar dynamisch cluster van virtuele WLS-machines. De-beheer server en alle beheerde servers worden standaard gestart, zodat u het domein kunt beheren.

## <a name="next-steps"></a>Volgende stappen

Verken de aanbiedingen in azure Marketplace.

> [!div class="nextstepaction"]
> [Oracle WebLogic-Server met één knoop punt](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server met de-beheer server](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server-cluster](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Dynamische Oracle WebLogic Server-cluster](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
