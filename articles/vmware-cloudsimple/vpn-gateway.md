---
title: Azure VMware-oplossing door CloudSimple - Een VPN-gateway instellen
description: Beschrijft hoe u Point-to-Site VPN-gateway en Site-to-Site VPN-gateway instellen en verbindingen maken tussen uw on-premises netwerk en uw CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8b7e238333196381524d189904871fe5933c906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279492"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>VPN-gateways instellen op cloudeenvoudig-netwerk

Met VPN-gateways u verbinding maken met het CloudSimple-netwerk vanaf uw on-premises netwerk en vanaf een clientcomputer op afstand. Een VPN-verbinding tussen uw on-premises netwerk en uw CloudSimple-netwerk biedt toegang tot het vCenter en workloads op uw Private Cloud. CloudSimple ondersteunt zowel Site-to-Site VPN als Point-to-Site VPN gateways.

## <a name="vpn-gateway-types"></a>VPN-gatewaytypen

* **Met een VPN-verbinding** van site naar site u uw Private Cloud-workloads instellen om toegang te krijgen tot on-premises services. U ook on-premises Active Directory gebruiken als identiteitsbron voor het verifiëren naar uw Private Cloud vCenter.  Momenteel wordt alleen **het vpn-type op basis van beleid** ondersteund.
* **Point-to-Site VPN-verbinding** is de eenvoudigste manier om verbinding te maken met uw Private Cloud vanaf uw computer. Gebruik Point-to-Site VPN-connectiviteit om op afstand verbinding te maken met de Private Cloud. Zie [Een VPN-verbinding](set-up-vpn.md)configureren voor het installeren van een client voor een Point-to-Site VPN-verbinding.

In een regio u één Point-to-Site VPN-gateway en één Site-to-Site VPN-gateway maken.

## <a name="automatic-addition-of-vlansubnets"></a>Automatische toevoeging van VLAN/subnetten

CloudSimple VPN-gateways bieden beleid voor het toevoegen van VLAN/subnetten aan VPN-gateways.  Met beleid u verschillende regels opgeven voor het beheer van VLAN/subnetten en door de gebruiker gedefinieerde VLAN/subnetten.  Regels voor beheer VLAN/subnetten zijn van toepassing op nieuwe Private Clouds die u maakt.  Met regels voor door de gebruiker gedefinieerde VLN's/subnetten u automatisch nieuwe VLAN/subnetten toevoegen aan bestaande of nieuwe Private Clouds Voor een Site-to-Site VPN-gateway definieert u het beleid voor elke verbinding.

Het beleid voor het toevoegen van VLAN's/subnetten aan VPN-gateways is van toepassing op zowel Site-to-Site VPN- als Point-to-Site VPN-gateways.

## <a name="automatic-addition-of-users"></a>Automatische toevoeging van gebruikers

Met een Point-to-Site VPN-gateway u een automatisch optellenvoor nieuwe gebruikers definiëren. Standaard hebben alle eigenaren en bijdragers van het abonnement toegang tot de CloudSimple-portal.  Gebruikers worden alleen gemaakt wanneer de CloudSimple-portal voor de eerste keer wordt gelanceerd.  Als u **Regels automatisch toevoegen selecteert,** heeft elke nieuwe gebruiker toegang tot het CloudSimple-netwerk via point-to-site VPN-verbinding.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>Een Site-to-Site VPN-gateway instellen

1. [Toegang tot de CloudSimple-portal](access-cloudsimple-portal.md) en selecteer **Netwerk**.
2. Selecteer **VPN-gateway**.
3. Klik op **Nieuwe VPN-gateway**.

    ![Een VPN-gateway maken](media/create-vpn-gateway.png)

4. Geef **voor gatewayconfiguratie**de volgende instellingen op en klik op **Volgende**.

    * Selecteer **Site-to-Site VPN** als gatewaytype.
    * Voer een naam in om de gateway te identificeren.
    * Selecteer de Azure-locatie waar uw CloudSimple-service is geïmplementeerd.
    * Schakel optioneel Hoge beschikbaarheid in.

    ![Vpn-gateway van site naar site maken](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > Voor het inschakelen van hoge beschikbaarheid vereist uw on-premises VPN-apparaat om verbinding te maken met twee IP-adressen. Deze optie kan niet worden uitgeschakeld zodra vpn-gateway is geïmplementeerd.

5. Maak de eerste verbinding vanuit uw on-premises netwerk en klik op **Volgende**.

    * Voer een naam in om de verbinding te identificeren.
    * Voer voor het peer IP-adres het openbare IP-adres van uw on-premises VPN-gateway in.
    * Voer de peer-id van uw on-premises VPN-gateway in.  De peer-id is meestal het openbare IP-adres van uw on-premises VPN-gateway.  Als u een specifieke id op uw gateway hebt geconfigureerd, voert u de id in.
    * Kopieer de gedeelde sleutel die u wilt gebruiken voor verbinding vanaf uw on-premises VPN-gateway.  Als u de standaardgedeelde sleutel wilt wijzigen en een nieuwe wilt opgeven, klikt u op het pictogram bewerken.
    * Voer **voor on-premises voorvoegsels**de on-premises CIDR-voorvoegsels in die toegang krijgen tot het CloudSimple-netwerk.  U meerdere CIDR-voorvoegsels toevoegen wanneer u de verbinding maakt.

    ![Vpn-gatewayverbinding site-naar-site maken](media/create-vpn-gateway-s2s-connection.png)

6. Schakel de VLAN/subnetten in op uw Private Cloud-netwerk die toegankelijk zijn vanuit het on-premises netwerk en klik op **Volgende**.

    * Als u een beheerVLAN/subnet wilt toevoegen, schakelt u **HET BEHEER VAN VLAN's/Subnetten van Private Clouds in.**  Management subnet is vereist voor vMotion en vSAN subnetten.
    * Als u vMotion-subnetten wilt toevoegen, schakelt u **VMotion-netwerk van privéclouds toevoegen**in.
    * Als u vSAN-subnetten wilt toevoegen, schakelt u **vSAN-subnet van Private Clouds toevoegen**in.
    * Selecteer of deselecteer specifieke VLAN's.

    ![Verbinding maken](media/create-vpn-gateway-s2s-connection-vlans.png)

7. Controleer de instellingen en klik op **Verzenden**.

    ![Site-to-Site VPN-gateway-beoordeling en maak](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>Point-to-Site VPN-gateway maken

1. [Toegang tot de CloudSimple-portal](access-cloudsimple-portal.md) en selecteer **Netwerk**.
2. Selecteer **VPN-gateway**.
3. Klik op **Nieuwe VPN-gateway**.

    ![Een VPN-gateway maken](media/create-vpn-gateway.png)

4. Geef **voor gatewayconfiguratie**de volgende instellingen op en klik op **Volgende**.

    * Selecteer **Point-to-Site VPN** als gatewaytype.
    * Voer een naam in om de gateway te identificeren.
    * Selecteer de Azure-locatie waar uw CloudSimple-service is geïmplementeerd.
    * Geef het clientsubnet op voor de Point-to-Site-gateway.  DHCP-adressen worden van het subnet van de client gegeven wanneer u verbinding maakt.

5. Geef **voor Verbinding/Gebruiker**de volgende instellingen op en klik op **Volgende**.

    * Als u alle huidige en toekomstige gebruikers automatisch toegang wilt geven tot de Private Cloud via de Point-to-Site-gateway, selecteert u **Alle gebruikers automatisch toevoegen.** Wanneer u de optie selecteert, worden alle gebruikers in de gebruikerslijst automatisch geselecteerd. U de automatische optie overschrijven door afzonderlijke gebruikers in de lijst uit te schakelen.
    * Als u afzonderlijke gebruikers wilt selecteren, klikt u op de selectievakjes in de gebruikerslijst.

6. Met de sectie VLAN's/Subnetten u de VLAN's/subnetten voor de gateway en verbindingen beheren en gebruikers opgeven.

    * Met **opties Automatisch toevoegen** stelt u het globale beleid voor de gateway in. De instellingen zijn van toepassing op de huidige gateway. De instellingen kunnen worden overschreven in het gebied **Selecteren.**
    * Selecteer **BeheerVLINEn/Subnetten van privéclouds toevoegen**. 
    * Als u alle door de gebruiker gedefinieerde VLAN's/subnetten wilt toevoegen, klikt u op **Door de gebruiker gedefinieerde VLAN's/Subnetten toevoegen**.
    * De **instellingen selecteren** overschrijven de algemene instellingen onder Automatisch **toevoegen**.

7. Klik **op Volgende** om de instellingen te bekijken. Klik op de pictogrammen Bewerken om wijzigingen aan te brengen.
8. Klik **op Maken** om de VPN-gateway te maken.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>Clientsubnet en protocollen voor Point-to-Site VPN-gateway

De Point-to-Site VPN-gateway maakt TCP- en UDP-verbindingen mogelijk.  Kies het protocol dat u wilt gebruiken wanneer u verbinding maakt vanaf uw computer door de TCP- of UDP-configuratie te selecteren.

Het geconfigureerde clientsubnet wordt gebruikt voor zowel TCP- als UDP-clients.  Het CIDR-voorvoegsel is verdeeld in twee subnetten, een voor TCP en een voor UDP-clients. Kies het voorvoegselmasker op basis van het aantal VPN-gebruikers dat gelijktijdig verbinding maakt.  

In de volgende tabel wordt het aantal gelijktijdige clientverbindingen voor voorvoegselmasker weergegeven.

| Voorvoegselmasker | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| Aantal gelijktijdige TCP-verbindingen | 124 | 60 | 28 | 12 | 4 |
| Aantal gelijktijdige UDP-verbindingen | 124 | 60 | 28 | 12 | 4 |

Zie Verbinding maken [met CloudSimple met Point-to-Site VPN](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn)als u verbinding wilt maken met Point-to-Site VPN.
