---
title: Azure VMware Solution by CloudSimple - VPN configureren tussen on-premises en Private Cloud
description: Beschrijft hoe u een Site-to-Site- of Point-to-Site VPN-verbinding configureert tussen uw on-premises netwerk en uw CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d000d8390375466232c7daac2a4a056ef424be79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087138"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>Een VPN-verbinding configureren met uw CloudSimple Private Cloud

Met VPN-gateways u verbinding maken met het CloudSimple-netwerk vanaf uw on-premises netwerk en vanaf een clientcomputer op afstand.  In dit artikel vindt u informatie over het instellen van VPN-gateways via de CloudSimple-portal.  Een VPN-verbinding tussen uw on-premises netwerk en uw CloudSimple-netwerk biedt toegang tot het vCenter en workloads op uw Private Cloud. CloudSimple ondersteunt zowel Point-to-Site VPN als Site-to-Site VPN gateways.

## <a name="vpn-gateway-types"></a>VPN-gatewaytypen

* **Point-to-Site VPN-verbinding** is de eenvoudigste manier om verbinding te maken met uw Private Cloud vanaf uw computer. Gebruik Point-to-Site VPN-connectiviteit om op afstand verbinding te maken met de Private Cloud.
* **Met een VPN-verbinding** van site naar site u uw Private Cloud-workloads instellen om toegang te krijgen tot on-premises services. U ook on-premises Active Directory gebruiken als identiteitsbron voor het verifiëren naar uw Private Cloud vCenter.  Momenteel wordt **het op beleid gebaseerde VPN-type** ondersteund.

In een regio u één Site-to-Site VPN-gateway en één Point-to-Site VPN-gateway maken.

## <a name="point-to-site-vpn"></a>Punt-naar-site-VPN

Zie [Point-to-Site VPN-gateway maken](vpn-gateway.md#create-point-to-site-vpn-gateway)om een Point-to-Site VPN-gateway te maken.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Verbinding maken met CloudSimple met Point-to-Site VPN

VPN-client is nodig om vanaf uw computer verbinding te maken met CloudSimple.  Download [OpenVPN-client](https://openvpn.net/community-downloads/) voor Windows of [Viscosity](https://www.sparklabs.com/viscosity/download/) voor macOS en OS X.

1. Start CloudSimple-portal en selecteer **Netwerk**.
2. Selecteer **VPN-gateway**.
3. Klik in de lijst met VPN-gateways op de Point-to-Site VPN-gateway.
4. Selecteer **Gebruikers**.
5. Klik op **Mijn VPN-configuratie downloaden**

    ![VPN-configuratie downloaden](media/download-p2s-vpn-configuration.png)

6. De configuratie op uw VPN-client importeren

    * Instructies voor [het importeren van configuratie op Windows-client](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instructies voor [het importeren van configuratie op macOS of OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Maak verbinding met cloudsimple VPN-gateway.

Voorbeeld hieronder toont het importeren van verbinding met **Viscosity Client**.

#### <a name="import-connection-on-viscosity-client"></a>Verbinding importeren op viscositeitclient

1. Haal de inhoud van de VPN-configuratie uit het gedownloade .zip-bestand.

2. Open Viscositeit op uw computer.

3. Klik **+** op het pictogram en selecteer **Verbinding** > importeren**uit bestand**.

    ![VPN-configuratie importeren uit bestand](media/import-p2s-vpn-config.png)

4. Selecteer het OpenVPN-configuratiebestand (.ovpn) voor het protocol dat u wilt gebruiken en klik op **Openen**.

    ![VPN](media/import-p2s-vpn-config-choose-ovpn.png)

De verbinding wordt nu weergegeven in het menu Viscosity.

#### <a name="connect-to-the-vpn"></a>Maak verbinding met het VPN.

Als u verbinding wilt maken met VPN via de Viscosity OpenVPN-client, selecteert u de verbinding in het menu. Het menupictogram wordt bijgewerkt om aan te geven dat de verbinding tot stand is gebracht.

![VPN](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>Verbinding maken met meerdere privéclouds

Een Point-to-Site VPN-verbinding lost de DNS-namen op van de eerste Private Cloud die u maakt. Wanneer u toegang wilt tot andere Private Clouds, moet u de DNS-server op uw VPN-client bijwerken.

1. [CloudSimple-portal starten.](access-cloudsimple-portal.md)

2. Navigeer naar **Resources** > **Private Clouds** en selecteer de Private Cloud waarmee u verbinding wilt maken.

3. Kopieer op de **overzichtspagina** van de Private Cloud het IP-adres van de Private Cloud DNS-server onder **Basisgegevens**.

    ![Private Cloud DNS-servers](media/private-cloud-dns-server.png)

4. Klik met de rechtermuisknop op het pictogram Viscositeit in de systeemlade van uw computer en selecteer **Voorkeuren**.

    ![VPN](media/vis00.png)

5. Selecteer de CloudSimple VPN-verbinding.

    ![VPN-verbinding](media/viscosity-client.png)

6. Klik **op Bewerken** om de verbindingseigenschappen te wijzigen.

    ![VPN-verbinding bewerken](media/viscosity-edit-connection.png)

7. Klik op het tabblad **Netwerken** en voer de IP-adressen van de Private ```cloudsimple.io```Cloud DNS-server in, gescheiden door een komma of ruimte en het domein als .  Selecteer **DNS-instellingen negeren die door de VPN-server worden verzonden.**

    ![VPN-netwerken](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Als u verbinding wilt maken met uw eerste Private Cloud, verwijdert u deze instellingen en maakt u verbinding met de VPN-server.

## <a name="site-to-site-vpn"></a>Site-naar-site-VPN

Zie [Site-to-Site VPN-gateway maken](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)als u een VPN-gateway voor site-to-Site wilt maken.  Site-to-Site VPN-verbinding van uw on-premises netwerk naar uw Private Cloud biedt deze voordelen.  

* Toegankelijkheid van uw Private Cloud vCenter vanaf elk werkstation in uw on-premises netwerk
* Uw on-premises Active Directory gebruiken als vCenter-identiteitsbron
* Handige overdracht van VM-sjablonen, ISO's en andere bestanden van uw on-premises bronnen naar uw Private Cloud vCenter
* Toegankelijkheid van workloads die op uw private cloud worden uitgevoerd vanuit uw on-premises netwerk

Zie [Een VPN-verbinding](high-availability-vpn-connection.md)met hoge beschikbaarheid configureren als u uw on-premises VPN-gateway in de modus met hoge beschikbaarheid wilt instellen.

> [!IMPORTANT]
>    1. Stel TCP MSS Clamping in op 1200 op uw VPN-apparaat. Of als uw VPN-apparaten mss-klemmen niet ondersteunen, u de MTU op de tunnelinterface ook instellen op 1240 bytes.
> 2. Nadat Site-to-Site VPN is ingesteld, stuurt u de DNS-verzoeken voor *.cloudsimple.io door naar de DNS-servers van de Private Cloud.  Volg de instructies in [On-Premises DNS Setup](on-premises-dns-setup.md).
