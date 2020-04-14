---
title: Azure ExpressRoute verbinden met Oracle Cloud Infrastructure | Microsoft Documenten
description: Verbind Azure ExpressRoute met Oracle Cloud Infrastructure (OCI) FastConnect om cross-cloud Oracle-toepassingsoplossingen mogelijk te maken
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/16/2020
ms.author: mimckitt
ms.openlocfilehash: d85c0fc8986adfa00559eab1c49a79daacdeb33f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263179"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Een directe verbinding tussen Azure en Oracle Cloud Infrastructure instellen  

Om een [geïntegreerde multi-cloudervaring](oracle-oci-overview.md)te creëren, bieden Microsoft en Oracle directe interconnectie tussen Azure en Oracle Cloud Infrastructure (OCI) via [ExpressRoute](../../../expressroute/expressroute-introduction.md) en [FastConnect.](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) Via de ExpressRoute en FastConnect-interconnectie kunnen klanten een lage latentie, hoge doorvoer, directe privéconnectiviteit tussen de twee clouds ervaren.

> [!IMPORTANT]
> Oracle zal deze toepassingen certificeren om in Azure uit te voeren wanneer het de Azure / Oracle Cloud interconnect-oplossing in mei 2020 gebruikt.
> * E-Business Suite
> * JD Edwards EnterpriseOne
> * Peoplesoft
> * Oracle Retail-toepassingen
> * Oracle Hyperion Financieel Beheer

De volgende afbeelding toont een overzicht op hoog niveau van de interconnectie:

![Cross-cloud netwerkverbinding](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Vereisten

* Als u de verbinding tussen Azure en OCI wilt tot stand brengen, moet u een actief Azure-abonnement en een actieve OCI-huurovereenkomst hebben.

* Connectiviteit is alleen mogelijk wanneer een Azure ExpressRoute-peeringlocatie zich in de nabijheid van of op dezelfde peeringlocatie bevindt als de OCI FastConnect. Zie [Beschikbaarheid van regio .](oracle-oci-overview.md#region-availability)

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Directe verbinding tussen ExpressRoute en FastConnect configureren

1. Maak een standaard ExpressRoute-circuit op uw Azure-abonnement onder een brongroep. 
    * Kies tijdens het maken van de ExpressRoute **Oracle Cloud FastConnect** als serviceprovider. Als u een ExpressRoute-circuit wilt maken, raadpleegt u de [stapsgewijze handleiding.](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)
    * Een Azure ExpressRoute-circuit biedt gedetailleerde bandbreedteopties, terwijl FastConnect 1, 2, 5 of 10 Gbps ondersteunt. Daarom wordt aanbevolen om een van deze overeenkomende bandbreedteopties te kiezen onder ExpressRoute.

    ![ExpressRoute-circuit maken](media/configure-azure-oci-networking/exr-create-new.png)
1. Noteer uw ExpressRoute **Service-sleutel.** U moet de sleutel leveren terwijl u uw FastConnect-circuit configureert.

    ![ExpressRoute-servicesleutel](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Er worden kosten in rekening gebracht voor ExpressRoute zodra het ExpressRoute-circuit is ingericht (zelfs als de **providerstatus** niet is **ingericht).**

1. Werk twee privé-IP-adresruimten van /30 uit die elkaar niet overlappen met uw azure virtuele netwerk of OCI virtual cloud network IP Address space. We verwijzen naar de eerste IP-adresruimte als primaire adresruimte en de tweede IP-adresruimte als secundaire adresruimte. Noteer de adressen die u nodig hebt bij het configureren van uw FastConnect-circuit.
1. Maak een Dynamic Routing Gateway (DRG). Dit heb je nodig bij het maken van je FastConnect-circuit. Zie de documentatie [van](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) de Dynamic Routing Gateway voor meer informatie.
1. Maak een FastConnect-circuit onder uw Oracle-tenant. Zie de [Oracle-documentatie](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)voor meer informatie.
  
    * Selecteer Onder De configuratie van FastConnect de optie **Microsoft Azure: ExpressRoute** als provider.
    * Selecteer de dynamische routeringsgateway die u in de vorige stap hebt ingericht.
    * Selecteer de bandbreedte die moet worden ingericht. Voor optimale prestaties moet de bandbreedte overeenkomen met de bandbreedte die is geselecteerd bij het maken van het ExpressRoute-circuit.
    * Plak **in Provider Service Key**de servicesleutel ExpressRoute.
    * Gebruik de eerste /30-privé-IP-adresruimte die in een vorige stap is uitgehouwen voor het **primaire BGP-IP-adres** en de tweede /30-privé-IP-adresruimte voor het **secundaire BGP-IP-adres.**
        * Wijs het eerste bruikbare adres van de twee bereiken toe voor het Oracle BGP IP-adres (Primair en Secundair) en het tweede adres aan het BGP-ip-adres van de klant (vanuit een FastConnect-perspectief). Het eerste bruikbare IP-adres is het tweede IP-adres in de /30-adresruimte (het eerste IP-adres is gereserveerd door Microsoft).
    * Klik **op Maken**.
1. Voltooi het koppelen van het FastConnect aan een virtueel cloudnetwerk onder uw Oracle-tenant via Dynamic Routing Gateway, met behulp van Route Table.
1. Navigeer naar Azure en zorg ervoor dat de **providerstatus** voor uw ExpressRoute-circuit is gewijzigd in **Ingericht** en dat een peering van het type **Azure-privé** is ingericht. Dit is een voorwaarde voor de volgende stappen.

    ![ExpressRoute-providerstatus](media/configure-azure-oci-networking/exr-provider-status.png)
1. Klik op het **privé-peering** van Azure. U ziet dat de peering-details automatisch zijn geconfigureerd op basis van de informatie die u hebt ingevoerd bij het instellen van uw FastConnect-circuit.

    ![Instellingen voor privé-peering](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Virtueel netwerk verbinden met ExpressRoute

1. Maak een virtuele netwerk- en virtuele netwerkgateway, als u dat nog niet hebt gedaan. Zie de [stapsgewijze handleiding](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)voor meer informatie.
1. Stel de verbinding tussen de virtuele netwerkgateway en het ExpressRoute-circuit in door het [Terraform-script](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) uit te voeren of door de opdracht PowerShell uit te voeren om [ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)te configureren.

Zodra u de netwerkconfiguratie hebt voltooid, u de geldigheid van uw configuratie verifiëren door te klikken op **ARP-records ophalen** en **routetabel ophalen** onder het beheer van het persoonlijke peeringblad van ExpressRoute-privé in de Azure-portal.

## <a name="automation"></a>Automation

Microsoft heeft Terraform-scripts gemaakt om geautomatiseerde implementatie van de netwerkverbinding mogelijk te maken. De Terraform-scripts moeten vóór uitvoering met Azure worden geverifieerd, omdat ze voldoende machtigingen voor het Azure-abonnement vereisen. Verificatie kan worden uitgevoerd met een [Azure Active Directory-serviceprincipal](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) of met de Azure CLI. Zie voor meer informatie de [Terraform documentatie.](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)

De Terraform scripts en gerelateerde documentatie om de inter-connect te implementeren zijn te vinden in deze [GitHub repository.](https://aka.ms/azureociinterconnecttf)

## <a name="monitoring"></a>Bewaking

Als u agents op beide clouds installeert, u gebruikmaken van Azure [Network Performance Monitor (NPM)](../../../expressroute/how-to-npm.md) om de prestaties van het end-to-end-netwerk te controleren. NPM helpt u om netwerkproblemen gemakkelijk te identificeren en helpt deze te elimineren.

## <a name="delete-the-interconnect-link"></a>De koppeling met verbinding verwijderen

Om de interconnect te verwijderen, moeten de volgende stappen worden gevolgd, in de opgegeven specifieke volgorde. Als u dit niet doet, zal dit resulteren in een "mislukte toestand" ExpressRoute-circuit.

1. Verwijder de ExpressRoute-verbinding. Verwijder de verbinding door op het pictogram **Verwijderen** op de pagina voor uw verbinding te klikken. Zie voor meer informatie de [ExpressRoute-documentatie.](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet)
1. Verwijder het Oracle FastConnect van de Oracle Cloud Console.
1. Zodra het Oracle FastConnect-circuit is verwijderd, u het Azure ExpressRoute-circuit verwijderen.

Op dit moment is het verwijderings- en deprovisioningproces voltooid.

## <a name="next-steps"></a>Volgende stappen

* Zie de [Oracle-documentatie](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)voor meer informatie over de cross-cloudverbinding tussen OCI en Azure.
* Gebruik [Terraform-scripts](https://aka.ms/azureociinterconnecttf) om infrastructuur voor gerichte Oracle-toepassingen via Azure te implementeren en de netwerkverbinding te configureren. 
