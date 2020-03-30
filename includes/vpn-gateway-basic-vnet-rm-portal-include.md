---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/03/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d2cf1a2e2ab9cf2d6e35aa12b5b0f8ddc04ad0e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301935"
---
U hebt door deze stappen te volgen een VNet gemaakt met behulp van het Resource Manager-implementatiemodel en de Azure-portal. Zie [Overzicht van virtuele netwerken](../articles/virtual-network/virtual-networks-overview.md) voor meer informatie over virtuele netwerken.

>[!NOTE]
>Wanneer u een virtueel netwerk gebruikt als onderdeel van een cross-premises architectuur, moet u coördineren met uw on-premises netwerkbeheerder om een IP-adresbereik uit te werken dat u specifiek voor dit virtuele netwerk gebruiken. Als er een dubbel adresbereik bestaat aan beide zijden van de VPN-verbinding, wordt verkeer niet correct gerouteerd. Als u dit virtuele netwerk wilt verbinden met een ander virtueel netwerk, kan de adresruimte bovendien niet overlappen met het andere virtuele netwerk. Houd hier rekening mee als u uw netwerkconfiguratie gaan plannen.
>
>

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Typ *virtueel netwerk* **in Zoekbronnen, service en documenten (G+/).**

   ![Pagina Virtuele netwerkbron zoeken](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Pagina virtuele netwerkbron zoeken")
1. Selecteer **Virtueel netwerk** in de Resultaten van **Marketplace.**

   ![Virtueel netwerk selecteren](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Pagina virtuele netwerkbron zoeken")
1. Selecteer op de pagina **Virtueel netwerk** de optie **Maken**.

   ![virtuele netwerkpagina](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Selecteer Maken")
1. Zodra u **Maken**selecteert, wordt de pagina **Virtueel netwerk maken** geopend.
1. Configureer op het tabblad **Basisbeginselen** de VNet-instellingen **voor projectgegevens** **en instantiedetails.**

   ![Tabblad Basisbeginselen](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "Tabblad Basisinformatie") Wanneer u de velden invult, ziet u een groen vinkje wanneer de tekens die u in voert in het veld zijn gevalideerd. Sommige waarden worden automatisch ingevuld. Deze kunt u door uw eigen waarden vervangen:

   - **Abonnement:** controleer of het vermelde abonnement de juiste is. U kunt abonnementen wijzigen met behulp van de vervolgkeuzelijst.
   - **Resourcegroep:** selecteer een bestaande resourcegroep of klik op **Nieuw maken** om een nieuwe groep te maken. Zie overzicht azure resource [manager](../articles/azure-resource-manager/management/overview.md#resource-groups)voor meer informatie over resourcegroepen.
   - **Naam:** Voer de naam voor uw virtuele netwerk in.
   - **Regio**: Selecteer de locatie voor uw VNet. De locatie bepaalt waar de resources die u naar dit VNet implementeert, zich bevinden.

1. Configureer de waarden op het tabblad **IP-adressen.** De waarden in de onderstaande voorbeelden zijn voor demonstratiedoeleinden. Pas deze waarden aan op basis van de instellingen die u nodig hebt.

   ![Tabblad IP-adressen](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "Tabblad IP-adressen")  
   - **IPv4-adresruimte**: Standaard wordt er automatisch een adresruimte gemaakt. U op de adresruimte klikken om deze aan te passen aan uw eigen waarden. U ook extra adresruimten toevoegen.
   - **IPv6**: Als voor uw configuratie IPv6-adresruimte is vereist, schakelt u het vak **IPv6-adresruimte toevoegen** in om die gegevens in te voeren.
   - **Subnet**: Als u de standaardadresruimte gebruikt, wordt er automatisch een standaardsubnet gemaakt. Als u de adresruimte wijzigt, moet u een subnet toevoegen. Selecteer **+ Subnet toevoegen** om het **subnetvenster Toevoegen** te openen. Configureer de volgende instellingen en selecteer **Toevoegen** om de waarden toe te voegen:
      - **Subnetnaam**: In dit voorbeeld hebben we het subnet "FrontEnd" genoemd.
      - **Subnetadresbereik**: Het adresbereik voor dit subnet.

1. Laat op dit moment de standaardwaarden achter op het tabblad **Beveiliging:**

   - **DDoS-bescherming**: Basic
   - **Firewall**: Uitgeschakeld
1. Selecteer **Controleren + maken** om de virtuele netwerkinstellingen te valideren.
1. Nadat de instellingen zijn gevalideerd, selecteert u **Maken**.
