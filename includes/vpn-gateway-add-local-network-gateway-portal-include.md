---
title: Include-bestand
description: Include-bestand
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bc9e18427bb3b8094911d2ac7f285d271ecd1a21
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "91025060"
---
1. Selecteer **Een resource maken** in het menu van de [Azure-portal](https://portal.azure.com).

   ![Resource](./media/vpn-gateway-add-local-network-gateway-portal-include/azure-portal-create-resource.png)
2. Typ **Lokale netwerkgateway** in het veld**Marketplace doorzoeken** en druk **Enter** om te zoeken. Er wordt dan een lijst met resultaten geretourneerd. Klik op **Lokale netwerkgateway** om de pagina te openen en klik op de knop **Maken** om de pagina **Lokale netwerkgateway maken** te openen.

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway-ip.png" alt-text="Een lokaal netwerkgateway-IP-adres maken":::

3. Op de **pagina Lokale netwerkgateway maken** geeft u de waarden voor uw lokale netwerkgateway op.

   - **Naam:** geef een naam op voor uw lokale netwerkgateway.
   - **Eindpunt:** Selecteer het eindpunttype voor het on-premises VPN-apparaat - **IP-adres** of **FQDN (Fully Qualified Domain Name)** .
      - **IP-adres**: Als u een statisch openbaar IP-adres hebt toegewezen van uw internetprovider voor uw VPN-apparaat, selecteert u de IP-adresoptie en vult u het IP-adres in zoals wordt weergegeven in het voorbeeld. Dit is het openbare IP-adres van het VPN-apparaat waarmee u via Azure VPN-gateway verbinding wilt maken. Als u op dit moment niet beschikt over het IP-adres, kunt u de waarden uit de schermafbeelding gebruiken. U moet dan wel later terugkeren om uw tijdelijke IP-adres te vervangen door het openbare IP-adres van uw VPN-apparaat. Anders kan er geen verbinding worden gemaakt vanuit Azure.
      - **FQDN**: Als u een dynamisch IP-adres hebt dat na een bepaalde periode verandert, meestal bepaald door uw internetprovider, kunt u een constante DNS-naam gebruiken met een dynamisch DNS-service-naar-punt naar uw huidige openbaar IP-adres van uw VPN-apparaat. Uw Azure VPN-gateway zet de FQDN om, om te bepalen met welk openbaar IP-adres verbinding moet worden gemaakt. De schermopname hieronder geeft een voorbeeld weer van het gebruik van een FQDN in plaats van een IP-adres.
   - **Adresruimte** verwijst naar de adresbereiken voor het netwerk dat dit lokale netwerk vertegenwoordigt. U kunt meerdere adresruimtebereiken toevoegen. Zorg ervoor dat de bereiken die u hier opgeeft, niet overlappen met bereiken van andere netwerken waarmee u verbinding wilt maken. Azure stuurt het adresbereik dat u opgeeft, door naar het IP-adres van het on-premises VPN-apparaat. *Gebruik hier uw eigen waarden als u verbinding wilt maken met uw on-premises site, niet de waarden die worden weergegeven in het voorbeeld*.
   - **BGP-instellingen configureren:** gebruik deze alleen bij het configureren van BGP. Selecteer deze anders niet.
   - **Abonnement:** controleer of het juiste abonnement wordt weergegeven.
   - **Resourcegroep:** selecteer de resourcegroep die u wilt gebruiken. U kunt een nieuwe resourcegroep maken of een resourcegroep selecteren die u al hebt gemaakt.
   - **Locatie:** De locatie is hetzelfde als **Regio** in andere instellingen. Selecteer de locatie waarin dit object wordt gemaakt. U kunt dezelfde locatie selecteren als die van uw VNet, maar dat is niet vereist.

   Dit is dezelfde pagina, maar met FQDN gemarkeerd:

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-gateway-fqdn.png" alt-text="Een FQDN voor een lokale netwerkgateway maken":::

   > [!NOTE]
   >
   > * Azure VPN ondersteunt slechts een IPv4-adres voor elke FQDN. Als de domeinnaam meerdere IP-adressen omzet, gebruikt Azure VPN Gateway het eerste IP-adres dat door de DNS-servers wordt geretourneerd. Om de onzekerheid te beperken, raden we aan dat uw FQDN altijd een enkel IPv4-adres omzet. IPv6 wordt niet ondersteund.
   > * Azure VPN Gateway behoudt een DNS-cache die elke 5 minuten wordt vernieuwd. De gateway probeert alleen de FQDN's voor onderbroken tunnels om te zetten. Als u de gateway opnieuw instelt, wordt ook de FQDN-omzetting geactiveerd.
   >

4. Wanneer u klaar bent met het opgeven van de waarden selecteert u **Maken** onderaan de pagina om de lokale netwerkgateway te maken.
