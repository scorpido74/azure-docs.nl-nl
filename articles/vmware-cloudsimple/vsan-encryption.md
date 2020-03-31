---
title: Azure VMware Solution by CloudSimple - VSAN-versleuteling configureren voor Private Cloud
description: Beschrijft hoe u de vSAN-softwareversleutelingsfunctie configureert, zodat uw CloudSimple Private Cloud kan werken met een sleutelbeheerserver die in uw virtuele Azure-netwerk wordt uitgevoerd.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 638b60bd3612fa25350ecef0a738fea75c2f53d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020638"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>VSAN-versleuteling configureren voor CloudSimple Private Cloud

U de functie voor vSAN-softwareversleuteling configureren, zodat uw CloudSimple Private Cloud kan werken met een sleutelbeheerserver die in uw virtuele Azure-netwerk wordt uitgevoerd.

VMware vereist het gebruik van een externe KMIP 1.1-compatibele third-party key management server (KMS) tool bij het gebruik van vSAN-versleuteling. U gebruikmaken van alle ondersteunde KMS die is gecertificeerd door VMware en beschikbaar is voor Azure.

In deze handleiding wordt beschreven hoe u HyTrust KeyControl KMS gebruiken in een virtueel Azure-netwerk. Een soortgelijke aanpak kan worden gebruikt voor elke andere gecertificeerde KMS-oplossing van derden voor vSAN.

Voor deze KMS-oplossing moet u:

* Installeer, configureer en beheer een VMware-gecertificeerd KMS-hulpprogramma van derden in uw virtuele Azure-netwerk.
* Geef uw eigen licenties voor de KMS-tool.
* Configureer en beheer vSAN-versleuteling in uw Private Cloud met behulp van het KMS-hulpprogramma van derden dat wordt uitgevoerd in uw virtuele Azure-netwerk.

## <a name="kms-deployment-scenario"></a>KMS-implementatiescenario

Het KMS-servercluster wordt uitgevoerd in uw virtuele Azure-netwerk en is IP-bereikbaar vanuit het VCenter private cloud via de geconfigureerde Azure ExpressRoute-verbinding.

![.. /media/KMS-cluster in het virtuele Azure-netwerk](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>De oplossing implementeren

Het implementatieproces heeft de volgende stappen:

1. [Controleren of aan de vereisten is voldaan](#verify-prerequisites-are-met)
2. [CloudSimple-portal: ExpressRoute-peering-informatie verkrijgen](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Azure-portal: verbind uw virtuele netwerk met de Private Cloud](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Azure-portal: een HyTrust KeyControl-cluster implementeren in uw virtuele netwerk](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI: KMIP-server configureren](#hytrust-webui-configure-the-kmip-server)
6. [vCenter-gebruikersinterface: vSAN-versleuteling configureren om KMS-cluster te gebruiken in uw virtuele Azure-netwerk](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>Controleer of aan de vereisten is voldaan

Controleer het volgende voordat de implementatie wordt geïmplementeerd:

* De geselecteerde KMS-leverancier, het gereedschap en de versie staan op de compatibiliteitslijst van vSAN.
* De geselecteerde leverancier ondersteunt een versie van het hulpprogramma dat in Azure wordt uitgevoerd.
* De Azure-versie van het KMS-hulpprogramma is KMIP 1.1-compatibel.
* Er zijn al een Azure Resource Manager en een virtueel netwerk gemaakt.
* Er is al een CloudSimple Private Cloud gemaakt.

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>CloudSimple portal: ExpressRoute peering informatie verkrijgen

Om de installatie voort te zetten, hebt u de autorisatiesleutel en peer circuit URI voor ExpressRoute plus toegang tot uw Azure-abonnement nodig. Deze informatie is beschikbaar op de pagina Virtual Network Connection in de CloudSimple-portal. Zie Een [virtuele netwerkverbinding met de private cloud instellen](virtual-network-connection.md)voor instructies. Als u problemen hebt met het verkrijgen van de informatie, opent u een [ondersteuningsverzoek.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Azure-portal: verbind uw virtuele netwerk met uw private cloud

1. Maak een virtuele netwerkgateway voor uw virtuele netwerk door de instructies in [Een virtuele netwerkgateway voor ExpressRoute configureren met behulp van de Azure-portal](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)te volgen.
2. Koppel uw virtuele netwerk aan het CloudSimple ExpressRoute-circuit door de instructies in [Connect een virtueel netwerk te volgen met een ExpressRoute-circuit via de portal.](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)
3. Gebruik de cloudsimple ExpressRoute-circuitinformatie die u ontvangt in uw welkomste-mail van CloudSimple om uw virtuele netwerk te koppelen aan het CloudSimple ExpressRoute-circuit in Azure.
4. Voer de autorisatiesleutel en peer circuit URI in, geef de verbinding een naam en klik op **OK**.

![Geef CS-peercircuit URI bij het maken van het virtuele netwerk](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Azure-portal: een HyTrust KeyControl-cluster implementeren in Azure Resource Manager in uw virtuele netwerk

Als u een HyTrust KeyControl-cluster wilt implementeren in Azure Resource Manager in uw virtuele netwerk, voert u de volgende taken uit. Zie de [HyTrust documentatie](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0) voor meer informatie.

1. Maak een Azure-netwerkbeveiligingsgroep (nsg-hytrust) met opgegeven binnenkomende regels door de instructies in de HyTrust-documentatie te volgen.
2. Genereer een SSH-sleutelpaar in Azure.
3. Implementeer het eerste KeyControl-knooppunt vanuit de afbeelding in Azure Marketplace.  Gebruik de openbare sleutelvan het sleutelpaar dat is gegenereerd en selecteer **nsg-hytrust** als de netwerkbeveiligingsgroep voor het KeyControl-knooppunt.
4. Converteer het privé-IP-adres van KeyControl naar een statisch IP-adres.
5. SSH naar de KeyControl VM met behulp van haar openbare IP-adres en de prive-sleutel van de eerder genoemde sleutel paar.
6. Wanneer u wordt gevraagd in `No` de SSH-shell, selecteert u het knooppunt in te stellen als het eerste KeyControl-knooppunt.
7. Voeg extra KeyControl-knooppunten toe door stap 3-5 van deze procedure te herhalen en te selecteren `Yes` wanneer er wordt gevraagd om aan een bestaand cluster toe te voegen.

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI: de KMIP-server configureren

Ga naar https://*public-ip,* waar public-ip het openbare *IP-adres* is van de KeyControl-knooppunt VM. Volg deze stappen vanuit de [HyTrust-documentatie.](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

1. [Een KMIP-server configureren](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [Een certificaatbundel maken voor VMware-versleuteling](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>vCenter-gebruikersinterface: vSAN-versleuteling configureren om KMS-cluster te gebruiken in uw virtuele Azure-netwerk

Volg de HyTrust-instructies om [een KMS-cluster te maken in vCenter.](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)

![KMS-clusterdetails toevoegen in vCenter](media/vsan-config01.png)

Ga in vCenter naar **Cluster > Configureren** en selecteer **Algemene** optie voor vSAN. Schakel versleuteling in en selecteer het KMS-cluster dat eerder aan vCenter is toegevoegd.

![VSAN-versleuteling inschakelen en KMS-cluster configureren in vCenter](media/vsan-config02.png)

## <a name="references"></a>Verwijzingen

### <a name="azure"></a>Azure

[Een virtuele netwerkgateway voor ExpressRoute configureren met behulp van de Azure-portal](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[Een virtueel netwerk verbinden aan een ExpressRoute-circuit met behulp van de portal](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust HyTrust

[HyTrust DataControl en Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[Een KMPI-server configureren](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[Een certificaatbundel maken voor VMware-versleuteling](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[Het KMS-cluster maken in vSphere](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
