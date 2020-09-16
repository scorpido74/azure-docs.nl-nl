---
title: Include-bestand
description: Include-bestand
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/03/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 059d15090204c2fce0fddd4b80f4954755ea8f65
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89449664"
---
1. Selecteer **Een resource maken** in het menu van de [Azure-portal](https://portal.azure.com). 

   ![Een resource maken in Azure Portal](./media/vpn-gateway-add-local-network-gateway-portal-include/azure-portal-create-resource.png)
2. Typ **Lokale netwerkgateway** in het veld**Marketplace doorzoeken** en druk **Enter** om te zoeken. Er wordt dan een lijst met resultaten geretourneerd. Klik op **Lokale netwerkgateway** om de pagina te openen en klik op de knop **Maken** om de pagina **Lokale netwerkgateway maken** te openen.

   ![De lokale netwerkgateway maken](./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway.png "De lokale netwerkgateway maken")

3. Op de **pagina Lokale netwerkgateway maken** geeft u de waarden voor uw lokale netwerkgateway op.

   - **Naam:** geef een naam op voor uw lokale netwerkgateway.
   - **IP-adres:** dit is het openbare IP-adres van het VPN-apparaat waarmee u via Azure verbinding wilt maken. Geef een geldig openbaar IP-adres op. Als u op dit moment niet beschikt over het IP-adres, kunt u de waarden uit de schermafbeelding gebruiken. U moet dan wel later terugkeren om uw tijdelijke IP-adres te vervangen door het openbare IP-adres van uw VPN-apparaat. Anders kan er geen verbinding worden gemaakt vanuit Azure.
   - **Adresruimte** verwijst naar de adresbereiken voor het netwerk dat het lokale netwerkobject vertegenwoordigt (uw on-premises netwerk). U voegt de adresruimten toe die u wilt omleiden naar uw on-premises netwerk. U kunt meerdere adresruimtebereiken toevoegen. Zorg ervoor dat de bereiken die u hier opgeeft, niet overlappen met bereiken van andere netwerken waarmee u verbinding wilt maken. Azure stuurt het adresbereik dat u opgeeft, door naar het IP-adres van het on-premises VPN-apparaat. *Gebruik hier uw eigen waarden als u verbinding wilt maken met uw on-premises site, niet de waarden die worden weergegeven in het voorbeeld*.
   - **BGP-instellingen configureren:** gebruik deze alleen bij het configureren van BGP. Selecteer deze anders niet.
   - **Abonnement:** controleer of het juiste abonnement wordt weergegeven.
   - **Resourcegroep:** selecteer de resourcegroep die u wilt gebruiken. U kunt een nieuwe resourcegroep maken of een resourcegroep selecteren die u al hebt gemaakt.
   - **Locatie:** De locatie is hetzelfde als **Regio** in andere instellingen. Selecteer de locatie waarin dit object wordt gemaakt. U kunt dezelfde locatie selecteren als die van uw VNet, maar dat is niet vereist.

4. Wanneer u klaar bent met het opgeven van de waarden klikt u op **Maken** onderaan de pagina om de lokale netwerkgateway te maken.
