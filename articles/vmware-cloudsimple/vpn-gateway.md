---
title: Azure VMware-oplossingen (AVS)-een VPN-gateway instellen
description: Hierin wordt beschreven hoe u punt-naar-site-VPN-gateway en site-naar-site-VPN-gateway instelt en verbindingen maakt tussen uw on-premises netwerk en de Privécloud van uw AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8731f7a9ff7f2cab7516e43c62ddc1aac2668168
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016762"
---
# <a name="set-up-vpn-gateways-on-avs-network"></a>VPN-gateways instellen op het AVS-netwerk

Met VPN-gateways kunt u op afstand verbinding maken met het AVS-netwerk vanaf uw on-premises netwerk en vanaf een client computer. Een VPN-verbinding tussen uw on-premises netwerk en uw AVS-netwerk biedt toegang tot de vCenter en werk belastingen op uw AVS-Privécloud. AVS ondersteunt zowel site-naar-site VPN als punt-naar-site VPN-gateways.

## <a name="vpn-gateway-types"></a>VPN-gateway typen

* Met **site-naar-site-VPN-** verbinding kunt u uw werk belastingen in de privécloud instellen voor toegang tot on-premises Services. U kunt ook on-premises Active Directory gebruiken als een id-bron voor verificatie bij uw automatische AVS-Cloud-vCenter. Op dit moment wordt alleen **op beleid gebaseerd VPN-** type ondersteund.
* **Punt-naar-site-VPN-** verbinding is de eenvoudigste manier om vanaf uw computer verbinding te maken met uw persoonlijke AVS-Cloud. Gebruik een punt-naar-site-VPN-verbinding om extern verbinding te maken met de AVS-privécloud. Zie [een VPN-verbinding configureren voor de privécloud van uw AVS](set-up-vpn.md)voor meer informatie over het installeren van een client voor een punt-naar-site-VPN-verbinding.

In een regio kunt u één punt-naar-site-VPN-gateway en een site-naar-site-VPN-gateway maken.

## <a name="automatic-addition-of-vlansubnets"></a>Automatische toevoeging van VLAN/subnetten

AVS VPN-gateways bieden beleids regels voor het toevoegen van VLAN/subnetten aan VPN-gateways. Met beleids regels kunt u verschillende regels opgeven voor het beheer van VLAN/subnetten en door de gebruiker gedefinieerde VLAN/subnetten. Regels voor het beheer van VLAN/subnetten zijn van toepassing op elke nieuwe Privécloud die u maakt. Regels voor door de gebruiker gedefinieerde VLAN'S/subnetten bieden u de mogelijkheid om automatisch nieuwe VLANs/subnetten toe te voegen aan bestaande of nieuwe automatische AVS-Clouds voor een site-naar-site VPN-gateway. u definieert het beleid voor elke verbinding.

Het beleid voor het toevoegen van VLAN'S/subnetten aan VPN-gateways is van toepassing op site-naar-site VPN-en punt-naar-site VPN-gateways.

## <a name="automatic-addition-of-users"></a>Automatische toevoeging van gebruikers

Met een punt-naar-site-VPN-gateway kunt u een beleid voor automatische toevoeging voor nieuwe gebruikers definiëren. Standaard hebben alle eigen aren en mede werkers van het abonnement toegang tot de AVS-Portal. Gebruikers worden alleen gemaakt wanneer de AVS-portal voor de eerste keer wordt gestart. Als u regels **automatisch toevoegen** selecteert, kunnen nieuwe gebruikers toegang krijgen tot het AVS-netwerk met behulp van punt-naar-site-VPN-verbinding.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>Een site-naar-site-VPN-gateway instellen

1. [Open de AVS-Portal](access-cloudsimple-portal.md) en selecteer **netwerk**.
2. Selecteer **VPN gateway**.
3. Klik op **nieuw VPN gateway**.

    ![Een VPN-gateway maken](media/create-vpn-gateway.png)

4. Geef voor **Gateway configuratie**de volgende instellingen op en klik op **volgende**.

    * Selecteer **site-naar-site-VPN** als het gateway type.
    * Voer een naam in om de gateway te identificeren.
    * Selecteer de Azure-locatie waar uw AVS-service is geïmplementeerd.
    * Schakel eventueel hoge Beschik baarheid in.

    ![Site-naar-site-VPN-gateway maken](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > Als u maximale Beschik baarheid wilt inschakelen, moet uw on-premises VPN-apparaat verbinding maken met twee IP-adressen. Deze optie kan niet worden uitgeschakeld als de VPN-gateway is geïmplementeerd.

5. Maak de eerste verbinding van uw on-premises netwerk en klik op **volgende**.

    * Voer een naam in om de verbinding aan te duiden.
    * Voer het open bare IP-adres van uw on-premises VPN-gateway in voor de peer-IP.
    * Voer de peer-id van uw on-premises VPN-gateway in. De peer-id is doorgaans het open bare IP-adres van uw on-premises VPN-gateway. Als u een specifieke id op uw gateway hebt geconfigureerd, voert u de id in.
    * Kopieer de gedeelde sleutel die moet worden gebruikt voor de verbinding van uw on-premises VPN-gateway. Als u de standaard gedeelde sleutel wilt wijzigen en een nieuwe wilt opgeven, klikt u op het pictogram bewerken.
    * Voor **on-premises voor voegsels**voert u de on-premises CIDR-voor voegsels in die toegang hebben tot het AVS-netwerk. U kunt meerdere CIDR-voor voegsels toevoegen wanneer u de verbinding maakt.

    ![Site-naar-site-VPN-gateway verbinding maken](media/create-vpn-gateway-s2s-connection.png)

6. Schakel de VLAN/subnetten in op uw automatische AVS-Cloud netwerk dat toegankelijk is vanuit het on-premises netwerk en klik op **volgende**.

    * Als u een management-VLAN/-subnet wilt toevoegen, schakelt u **vlan's voor beheer en subnetten van persoonlijke AVS-Clouds**in. Het subnet van het beheer is vereist voor vMotion-en vSAN-subnetten.
    * Als u vMotion-subnets wilt toevoegen, schakelt u het **toevoegen van VMotion-netwerk van AVS persoonlijke Clouds**in.
    * Als u vSAN-subnetten wilt toevoegen, schakelt u **vSAN subnet van persoonlijke Clouds toevoegen**in.
    * Selecteer een of meer specifieke VLAN'S.

    ![Verbinding maken](media/create-vpn-gateway-s2s-connection-vlans.png)

7. Controleer de instellingen en klik op **verzenden**.

    ![Controleren en maken van site-naar-site-VPN-gateway](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>Punt-naar-site-VPN-gateway maken

1. [Open de AVS-Portal](access-cloudsimple-portal.md) en selecteer **netwerk**.
2. Selecteer **VPN gateway**.
3. Klik op **nieuw VPN gateway**.

    ![Een VPN-gateway maken](media/create-vpn-gateway.png)

4. Geef voor **Gateway configuratie**de volgende instellingen op en klik op **volgende**.

    * Selecteer **punt-naar-site-VPN** als het gateway type.
    * Voer een naam in om de gateway te identificeren.
    * Selecteer de Azure-locatie waar uw AVS-service is geïmplementeerd.
    * Geef het client subnet op voor de punt-naar-site-gateway. Wanneer u verbinding maakt, wordt er een DHCP-adres van het client-subnet gegeven.

5. Geef voor **verbinding/gebruiker**de volgende instellingen op en klik op **volgende**.

    * Als u wilt dat alle huidige en toekomstige gebruikers automatisch toegang hebben tot de automatische AVS-Cloud via de punt-naar-site-gateway, selecteert u **automatisch alle gebruikers toevoegen**. Wanneer u de optie selecteert, worden alle gebruikers in de lijst met gebruikers automatisch geselecteerd. U kunt de automatische optie onderdrukken door afzonderlijke gebruikers in de lijst uit te scha kelen.
    * Als u afzonderlijke gebruikers wilt selecteren, klikt u op de selectie vakjes in de gebruikers lijst.

6. In het gedeelte VLAN'S/subnetten kunt u beheer-en gebruikers-VLAN'S of-subnetten voor de gateway en verbindingen opgeven.

    * Met de opties voor **automatisch toevoegen** wordt het globale beleid voor de gateway ingesteld. De instellingen zijn van toepassing op de huidige gateway. De instellingen kunnen worden overschreven in het gebied **selecteren** .
    * Selecteer **beheer-vlan's/subnetten van persoonlijke AVS-Clouds toevoegen**. 
    * Klik op door de gebruiker gedefinieerde vlan's **/subnetten toevoegen**om alle door de gebruiker gedefinieerde vlan's/subnetten toe te voegen.
    * De **selectie** -instellingen overschrijven de algemene instellingen onder **automatisch toevoegen**.

7. Klik op **volgende** om de instellingen te controleren. Klik op de pictogrammen bewerken om wijzigingen aan te brengen.
8. Klik op **maken** om de VPN-gateway te maken.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>Client subnet en-protocollen voor punt-naar-site VPN-gateway

De punt-naar-site VPN-gateway staat TCP-en UDP-verbindingen toe. Kies het protocol dat moet worden gebruikt wanneer u verbinding maakt vanaf uw computer door de TCP-of UDP-configuratie te selecteren.

Het geconfigureerde client subnet wordt gebruikt voor TCP-en UDP-clients. Het CIDR-voor voegsel is onderverdeeld in twee subnetten: één voor TCP en één voor UDP-clients. Kies het voor voegsel masker op basis van het aantal VPN-gebruikers dat gelijktijdig verbinding maakt. 

De volgende tabel geeft een overzicht van het aantal gelijktijdige client verbindingen voor voorvoegsel maskers.

| Voorvoegsel masker | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| Aantal gelijktijdige TCP-verbindingen | 124 | 60 | 28 | 12 | 4 |
| Aantal gelijktijdige UDP-verbindingen | 124 | 60 | 28 | 12 | 4 |

Als u verbinding wilt maken met behulp van punt-naar-site-VPN, raadpleegt [u verbinding maken met AVS via punt-naar-site-VPN](set-up-vpn.md#connect-to-avs-using-point-to-site-vpn).
