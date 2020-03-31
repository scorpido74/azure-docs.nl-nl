---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9648f4c16e8d266bbdd504d4a7599b67a5c846b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73522664"
---
1. Selecteer in het menu [azure portal](https://portal.azure.com) de optie Een **resource maken**. 

   ![Een resource maken in de Azure-portal](./media/vpn-gateway-add-local-network-gateway-portal-include/azure-portal-create-resource.png)
2. Typ lokaal **netwerkgateway**in het veld Zoeken in het veld Zoeken in de **marktplaats** en druk vervolgens op **Enter** om te zoeken. Er wordt dan een lijst met resultaten geretourneerd. Klik op **Lokale netwerkgateway** om de pagina te openen en klik op de knop **Maken** om de pagina **Lokale netwerkgateway maken** te openen.

   ![De lokale netwerkgateway maken](./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway.png "De lokale netwerkgateway maken")

3. Op de **pagina Lokale netwerkgateway maken** geeft u de waarden voor uw lokale netwerkgateway op.

   - **Naam:** geef een naam op voor uw lokale netwerkgateway.
   - **IP-adres:** dit is het openbare IP-adres van het VPN-apparaat waarmee u via Azure verbinding wilt maken. Geef een geldig openbaar IP-adres op. Als u op dit moment niet beschikt over het IP-adres, kunt u de waarden uit de schermafbeelding gebruiken. U moet dan wel later terugkeren om uw tijdelijke IP-adres te vervangen door het openbare IP-adres van uw VPN-apparaat. Anders kan er geen verbinding worden gemaakt vanuit Azure.
   - **Adresruimte** verwijst naar de adresbereiken voor het netwerk dat dit lokale netwerk vertegenwoordigt. U kunt meerdere adresruimtebereiken toevoegen. Zorg ervoor dat de bereiken die u hier opgeeft, niet overlappen met bereiken van andere netwerken waarmee u verbinding wilt maken. Azure stuurt het adresbereik dat u opgeeft, door naar het IP-adres van het on-premises VPN-apparaat. *Gebruik hier uw eigen waarden als u verbinding wilt maken met uw on-premises site, niet de waarden die worden weergegeven in het voorbeeld*.
   - **BGP-instellingen configureren:** gebruik deze alleen bij het configureren van BGP. Selecteer deze anders niet.
   - **Abonnement:** controleer of het juiste abonnement wordt weergegeven.
   - **Resourcegroep:** selecteer de resourcegroep die u wilt gebruiken. U kunt een nieuwe resourcegroep maken of een resourcegroep selecteren die u al hebt gemaakt.
   - **Locatie:** De locatie is hetzelfde als **Regio** in andere instellingen. Selecteer de locatie waarin dit object wordt gemaakt. U kunt dezelfde locatie selecteren als die van uw VNet, maar dat is niet vereist.

4. Wanneer u klaar bent met het opgeven van de waarden klikt u op **Maken** onderaan de pagina om de lokale netwerkgateway te maken.
