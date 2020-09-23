---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f708052e6c1b474d1447a352d10eb1defe311fc4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "91025178"
---
1. Klik in de portal op **+Een resource maken**.
2. In het zoekvak typt u **Lokale netwerkgateway**. Druk vervolgens op **Enter** om te zoeken. Er wordt dan een lijst met resultaten geretourneerd. Klik op **Lokale netwerkgateway** om de pagina te openen en klik op de knop **Maken** om de pagina **Lokale netwerkgateway maken** te openen.

   :::image type="content" source="./media/vpn-gateway-add-lng-rm-portal-include/create-local-gateway-ip.png" alt-text="De lokale netwerkgateway maken":::

3. Op de **pagina Lokale netwerkgateway maken** geeft u de waarden voor uw lokale netwerkgateway op.

   - **Naam:** geef een naam op voor uw lokale netwerkgateway.
   - **Eind punt:** Selecteer het eindpunt type voor het on-premises VPN-apparaat, het **IP-adres** of de **FQDN-naam (Fully Qualified Domain Name)**.
      - **IP-adres**: als u een statisch openbaar IP-adres hebt dat is toegewezen door uw Internet provider voor uw VPN-apparaat, selecteert u de optie IP-adres en vult u het IP-adres in, zoals wordt weer gegeven in het voor beeld. Dit is het open bare IP-adres van het VPN-apparaat waarmee u verbinding wilt maken met Azure VPN-gateway. Als u op dit moment niet beschikt over het IP-adres, kunt u de waarden uit de schermafbeelding gebruiken. U moet dan wel later terugkeren om uw tijdelijke IP-adres te vervangen door het openbare IP-adres van uw VPN-apparaat. Anders kan er geen verbinding worden gemaakt vanuit Azure.
      - **FQDN**: als u een dynamisch openbaar IP-adres hebt dat na bepaalde tijd kan worden gewijzigd, meestal bepaald door uw Internet provider, kunt u een constante DNS-naam gebruiken met een dynamische DNS-service om te verwijzen naar uw huidige open bare IP-adres van uw VPN-apparaat. Uw Azure VPN-gateway verhelpt de FQDN om het open bare IP-adres te bepalen waarmee verbinding moet worden gemaakt. In een scherm afbeelding hieronder ziet u een voor beeld van het gebruik van FQDN in plaats van IP-adres.
   - **Adresruimte** verwijst naar de adresbereiken voor het netwerk dat dit lokale netwerk vertegenwoordigt. U kunt meerdere adresruimtebereiken toevoegen. Zorg ervoor dat de bereiken die u hier opgeeft, niet overlappen met bereiken van andere netwerken waarmee u verbinding wilt maken. Azure stuurt het adresbereik dat u opgeeft, door naar het IP-adres van het on-premises VPN-apparaat. *Gebruik hier uw eigen waarden als u verbinding wilt maken met uw on-premises site, niet de waarden die worden weergegeven in het voorbeeld*.
   - **BGP-instellingen configureren:** gebruik deze alleen bij het configureren van BGP. Selecteer deze anders niet.
   - **Abonnement:** controleer of het juiste abonnement wordt weergegeven.
   - **Resourcegroep:** selecteer de resourcegroep die u wilt gebruiken. U kunt een nieuwe resourcegroep maken of een resourcegroep selecteren die u al hebt gemaakt.
   - **Locatie:** selecteer de locatie waarin dit object wordt gemaakt. U kunt dezelfde locatie selecteren als die van uw VNet, maar dat is niet vereist.

    Dit is dezelfde pagina, maar met FQDN gemarkeerd:
   
   :::image type="content" source="./media/vpn-gateway-add-lng-rm-portal-include/create-local-gateway-fqdn.png" alt-text="De lokale netwerk gateway maken-FQDN":::
   
   > [!NOTE]
   >
   > * Azure VPN ondersteunt slechts één IPv4-adres voor elke FQDN. Als de domein naam wordt omgezet in meerdere IP-adressen, gebruikt Azure VPN Gateway het eerste IP-adres dat wordt geretourneerd door de DNS-servers. Om de onzekerheid te elimineren, wordt aangeraden de FQDN-naam altijd om te zetten naar één IPv4-adres. IPv6 wordt niet ondersteund.
   > * Azure VPN Gateway houdt een DNS-cache elke vijf minuten vernieuwd. De gateway probeert alleen de FQDN voor niet-verbonden tunnels te herleiden. Als u de gateway opnieuw instelt, wordt ook de FQDN-omzetting geactiveerd.
   >

4. Wanneer u klaar bent met het opgeven van de waarden, selecteert u de knop **maken** om de gateway te maken.
