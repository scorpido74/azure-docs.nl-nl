---
title: DHCP maken en beheren
description: In dit artikel wordt uitgelegd hoe u DHCP beheert in azure VMware-oplossing.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: cb74ed4474cc14081e59142f2f60915fccd47559
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461053"
---
# <a name="how-to-create-and-manage-dhcp-in-azure-vmware-solution"></a>DHCP in azure VMware-oplossing maken en beheren

NSX-T biedt de mogelijkheid om DHCP te configureren voor uw privécloud. Als u NSX-T gebruikt voor het hosten van uw DHCP-server, raadpleegt u [DHCP-server maken](#create-dhcp-server). Zie anders de [DHCP Relay-service maken](#create-dhcp-relay-service)als u een externe DHCP-server van derden in uw netwerk hebt.

## <a name="create-dhcp-server"></a>DHCP-server maken

Gebruik de volgende stappen om een DHCP-server te configureren op NSX-T.

1. Ga in NSX Manager naar het tabblad **netwerken** en selecteer **DHCP**. 
1. Selecteer **server toevoegen** en geef vervolgens de server naam en het IP-adres op. 
1. Selecteer **Opslaan**.

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="DHCP-server toevoegen" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>De DHCP-server verbinden met de laag-1-gateway.

1. Selecteer **laag 1-gateways**, de gateway uit de lijst en selecteer vervolgens **bewerken**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="DHCP-server toevoegen" border="true":::

1. Selecteer **geen IP-toewijzings set** om een subnet toe te voegen.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="DHCP-server toevoegen" border="true":::

1. Selecteer de **lokale DHCP-server** voor het **type**. 
1. Selecteer **standaard DHCP** voor de **DHCP-server** en selecteer vervolgens **Opslaan**.


1. Selecteer **Opslaan**in het venster **Tier-1-gateway** . 
1. Selecteer **sluiten** om de bewerking te volt ooien.

### <a name="add-a-network-segment"></a>Een netwerk segment toevoegen

Wanneer u de DHCP-server hebt gemaakt, moet u er netwerk segmenten aan toevoegen.

1. Selecteer in NSX-T het tabblad **netwerken** en selecteer **segmenten** onder **connectiviteit**. 
1. Selecteer **segment toevoegen** en noem het segment en de verbinding met de gateway van de laag 1. 
1. Selecteer **subnetten instellen** om een nieuw subnet te configureren. 

   :::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="DHCP-server toevoegen" border="true":::

1. Selecteer in het venster **subnetten instellen** de optie **subnet toevoegen**. 
1. Voer het IP-adres van de gateway en het DHCP-bereik in en selecteer **toevoegen** en vervolgens **Toep assen**

1. Selecteer **Opslaan** om het nieuwe netwerk segment toe te voegen.

## <a name="create-dhcp-relay-service"></a>DHCP Relay-service maken

1. Selecteer het tabblad **netwerken** en selecteer **DHCP**in het vak **IP-beheer**. 
1. Selecteer **server toevoegen**. 
1. Selecteer DHCP-Relay voor het **server type** en voer de server naam en het IP-adres voor de relay-server in. 
1. Selecteer **Opslaan**.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="DHCP-server toevoegen" border="true":::

1. Selecteer **laag-1 gateways** onder **connectiviteit**. 
1. Selecteer de verticale beletsel tekens op de laag-1-gateway en selecteer **bewerken**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="DHCP-server toevoegen" border="true":::

1. Selecteer **geen IP-toewijzings set** om de toewijzing van IP-adressen te definiëren.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="DHCP-server toevoegen" border="true":::

1. Selecteer de **DHCP relay-server** voor het **type**.
1. Selecteer de DHCP relay-server voor **DHCP-Relay**. 
1. Selecteer **Opslaan**.


## <a name="specify-a-dhcp-range-ip-on-a-segment"></a>Een IP-adres van een DHCP-bereik opgeven in een segment

> [!NOTE]
> Deze configuratie is vereist voor het realiseren van DHCP-Relay-functionaliteit op het DHCP-client segment. 

1. Onder **connectiviteit**selecteert u **segmenten**. 
1. Selecteer de verticale ellipsen en selecteer **bewerken**. 

   >[!TIP]
   >Als u een nieuw segment wilt toevoegen, selecteert u **segment toevoegen**.

1. Voeg details over het segment toe. 
1. Selecteer de waarde onder **subnetten instellen** om het subnet toe te voegen of te wijzigen.

   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="DHCP-server toevoegen" border="true":::

1. Selecteer de verticale ellipsen en kies **bewerken**. Als u een nieuw subnet wilt maken, selecteert u **subnet toevoegen** om een gateway te maken en een DHCP-bereik te configureren. 
1. Geef het bereik van de IP-adres groep **op en selecteer Toep assen**en selecteer vervolgens **Opslaan** .

   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="DHCP-server toevoegen" border="true":::

   Er wordt een DHCP-Server groep toegewezen aan het segment.

   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="DHCP-server toevoegen" border="true":::
