---
title: 'Snelstart: een private cloud maken'
titleSuffix: Azure VMware Solutions by CloudSimple
description: Meer informatie over het maken en configureren van een Private Cloud met Azure VMware Solutions by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7460490dbd45862f4269d25e3910373700ec9a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564717"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Snelstart - Een Private Cloud-omgeving configureren

In dit artikel leert u hoe u een CloudSimple Private Cloud maakt en uw Private Cloud-omgeving instelt.

## <a name="before-you-begin"></a>Voordat u begint

Netwerkvereisten [bekijken](cloudsimple-network-checklist.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .

## <a name="create-a-private-cloud"></a>Een privécloud maken

Een Private Cloud is een geïsoleerde VMware-stack die ESXi-hosts, vCenter, vSAN en NSX ondersteunt.

Private Clouds worden beheerd via de CloudSimple portal. Ze hebben hun eigen vCenter server in een eigen beheerdomein. De stack draait op speciale knooppunten en geïsoleerde bare metal hardware knooppunten.

1. Selecteer **Alle services**.
2. Zoek naar **CloudSimple Services**.
3. Selecteer de CloudSimple-service waarop u uw Private Cloud wilt maken.
4. Klik **in Overzicht**op **Privécloud maken** om een nieuw browsertabblad voor CloudSimple-portal te openen.  Meld u desgevraagd aan met uw Azure-aanmeldingsreferenties.  

    ![Private Cloud maken vanuit Azure](media/create-private-cloud-from-azure.png)

5. Geef in de CloudSimple-portal een naam voor uw Private Cloud.
6. Selecteer de **locatie** van uw private cloud.
7. Selecteer **Knooppunttype,** in overeenstemming met wat u op Azure hebt ingericht.
8. Het **aantal nodes opgeven**.  Er zijn ten minste drie knooppunten nodig om een Private Cloud te maken.

    ![Private Cloud maken - Basisinformatie](media/create-private-cloud-basic-info.png)

9. Klik **op Volgende: Geavanceerde opties**.
10. Voer het CIDR-bereik in voor vSphere/vSAN-subnetten. Zorg ervoor dat het CIDR-bereik niet overlapt met een van uw on-premises of andere Azure-subnetten (virtuele netwerken) of met het gateway-subnet.

    **CIDR-bereikopties:** /24, /23, /22 of /21. Een CIDR-bereik van /24 ondersteunt maximaal 26 knooppunten, een /23 CIDR-bereik ondersteunt maximaal 58 knooppunten en een CIDR-bereik van /22 en /21 ondersteunt 64 knooppunten (het maximum aantal knooppunten in een private cloud).  Zie [VLAN's en subnetten overzicht](cloudsimple-vlans-subnets.md)voor meer informatie en VLAN's en subnetten.

      > [!IMPORTANT]
      > IP-adressen in het vSphere/vSAN CIDR-bereik zijn gereserveerd voor gebruik door de Private Cloud-infrastructuur.  Gebruik het IP-adres in dit bereik niet op een virtuele machine.

11. Klik **op Volgende: Controleren en maken**.
12. Bekijk de instellingen. Als u instellingen wilt wijzigen, klikt u op **Vorige**.
13. Klik **op Maken**.

Private Cloud provisioning proces begint.  Het kan tot twee uur duren voordat de Private Cloud is ingericht.

## <a name="launch-cloudsimple-portal"></a>CloudSimple-portal starten

U hebt toegang tot de CloudSimple-portal vanuit Azure-portal.  De CloudSimple-portal wordt gestart met uw Azure-aanmeldingsreferenties met behulp van Single Sign-On (SSO).  Als u toegang hebt tot de CloudSimple-portal, moet u de **cloudsimple-serviceautorisatietoepassing autoriseren.**  Zie [Toestemming voor CloudSimple Service Authorization-toepassing](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application)voor meer informatie over het verlenen van machtigingen.

1. Selecteer **Alle services**.
2. Zoek naar **CloudSimple Services**.
3. Selecteer de CloudSimple-service waarop u uw Private Cloud wilt maken.
4. Klik in het overzicht op **Ga naar de CloudSimple-portal om** een nieuw browsertabblad voor CloudSimple-portal te openen.  Meld u desgevraagd aan met uw Azure-aanmeldingsreferenties.  

    ![CloudSimple-portal starten](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Point-to-Site VPN maken

Een Point-to-Site VPN-verbinding is de eenvoudigste manier om vanaf uw computer verbinding te maken met uw private cloud. Gebruik point-to-site VPN-verbinding als u op afstand verbinding maakt met de Private Cloud.  Volg de onderstaande stappen voor snelle toegang tot uw Private Cloud.  Toegang tot de CloudSimple-regio vanuit uw on-premises netwerk kan worden gedaan met behulp van [Site-to-Site VPN](vpn-gateway.md) of [Azure ExpressRoute.](on-premises-connection.md)

### <a name="create-gateway"></a>Gateway maken

1. Start CloudSimple-portal en selecteer **Netwerk**.
2. Selecteer **VPN-gateway**.
3. Klik op **Nieuwe VPN-gateway**.

    ![Een VPN-gateway maken](media/create-vpn-gateway.png)

4. Geef **voor gatewayconfiguratie**de volgende instellingen op en klik op **Volgende**.

    * Selecteer **Point-to-Site VPN** als gatewaytype.
    * Voer een naam in om de gateway te identificeren.
    * Selecteer de Azure-locatie waar uw CloudSimple-service is geïmplementeerd.
    * Geef het clientsubnet op voor de Point-to-Site-gateway.  DHCP-adressen worden van dit subnet gegeven wanneer u verbinding maakt.

5. Geef **voor Verbinding/Gebruiker**de volgende instellingen op en klik op **Volgende**.

    * Als u alle huidige en toekomstige gebruikers automatisch toegang wilt geven tot de Private Cloud via deze Point-to-Site-gateway, selecteert u **Alle gebruikers automatisch toevoegen.** Wanneer u deze optie selecteert, worden alle gebruikers in de lijst Met gebruikers automatisch geselecteerd. U de automatische optie overschrijven door afzonderlijke gebruikers in de lijst uit te schakelen.
    * Als u alleen individuele gebruikers wilt selecteren, klikt u op de selectievakjes in de lijst Gebruiker.

6. Met de sectie VLAN's/Subnetten u de VLAN's/subnetten voor de gateway en verbindingen beheren en gebruikers opgeven.

    * Met **opties Automatisch toevoegen** stelt u het algemene beleid voor deze gateway in. De instellingen zijn van toepassing op de huidige gateway. De instellingen kunnen worden overschreven in het gebied **Selecteren.**
    * Selecteer **BeheerVLINEn/Subnetten van privéclouds toevoegen**.
    * Als u alle door de gebruiker gedefinieerde VLAN's/subnetten wilt toevoegen, klikt u op **Door de gebruiker gedefinieerde VLAN's/Subnetten toevoegen**.
    * De **instellingen selecteren** overschrijven de algemene instellingen onder Automatisch **toevoegen**.

7. Klik **op Volgende** om de instellingen te bekijken. Klik op de pictogrammen Bewerken om wijzigingen aan te brengen.
8. Klik **op Maken** om de VPN-gateway te maken.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Verbinding maken met CloudSimple met Point-to-Site VPN

VPN-client is nodig om vanaf uw computer verbinding te maken met CloudSimple.  Download [OpenVPN-client](https://openvpn.net/community-downloads/) voor Windows of [Viscosity](https://www.sparklabs.com/viscosity/download/) voor macOS en OS X.

1. Start CloudSimple-portal en selecteer **Netwerk**.
2. Selecteer **VPN-gateway**.
3. Klik in de lijst met VPN-gateways op de Point-to-Site VPN-gateway.
4. Selecteer **Gebruikers**.
5. Klik **op Mijn VPN-configuratie downloaden**.

    ![VPN-configuratie downloaden](media/download-p2s-vpn-configuration.png)

6. Importeer de configuratie op uw VPN-client.

    * Instructies voor [het importeren van configuratie op Windows-client](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instructies voor [het importeren van configuratie op macOS of OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Maak verbinding met CloudSimple.

## <a name="create-a-vlan-for-your-workload-vms"></a>Een VLAN maken voor uw workload VM's

Nadat u een Private Cloud hebt gemaakt, maakt u een VLAN waar u uw workload/applicatie VM's implementeert.

1. Selecteer **Netwerk**in de CloudSimple-portal .
2. Klik **op VLAN/Subnetten**.
3. Klik **op VLAN/Subnet maken**.

    ![VLAN/Subnet maken](media/create-new-vlan-subnet.png)

4. Selecteer de **Private Cloud** voor het nieuwe VLAN/subnet.
5. Selecteer een VLAN-id in de lijst.  
6. Voer een subnetnaam in om het subnet te identificeren.
7. Geef het subnet CIDR-bereik en masker op.  Dit bereik mag niet overlappen met bestaande subnetten.
8. Klik **op Verzenden**.

    ![VLAN/Subnet-details maken](media/create-new-vlan-subnet-details.png)

Het VLAN/subnet wordt gemaakt.  U deze VLAN-id nu gebruiken om een gedistribueerde poortgroep te maken op uw Private Cloud vCenter.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Uw omgeving verbinden met een virtueel Azure-netwerk

CloudSimple biedt u een ExpressRoute-circuit voor uw Private Cloud. U uw virtuele netwerk op Azure verbinden met het ExpressRoute-circuit. Volg de stappen in [Azure Virtual Network Connection met ExpressRoute](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/)voor meer informatie over het instellen van de verbinding.

## <a name="sign-in-to-vcenter"></a>Aanmelden bij vCenter

U zich nu aanmelden bij vCenter om virtuele machines en beleidsregels in te stellen.

1. Als u toegang wilt krijgen tot vCenter, start u vanaf de CloudSimple-portal. Klik op de startpagina onder **Algemene taken**op **VSphere-client starten**.  Selecteer de Private Cloud en klik op **VSphere-client starten** in de private cloud.

    ![VSphere-client starten](media/launch-vcenter-from-cloudsimple-portal.png)

2. Selecteer uw favoriete vSphere-client om toegang te krijgen tot vCenter en meld u aan met uw gebruikersnaam en wachtwoord.  De standaardinstellingen zijn:
    * Gebruikersnaam: **CloudOwner\@cloudsimple.local**
    * Wachtwoord: **CloudSimple123!**  

De vCenter-schermen in de volgende procedures zijn afkomstig van de vSphere -client (HTML5).

## <a name="change-your-vcenter-password"></a>Uw vCenter-wachtwoord wijzigen

CloudSimple raadt u aan uw wachtwoord te wijzigen wanneer u zich voor het eerst aanmeldt bij vCenter.  
Het wachtwoord dat u instelt, moet aan de volgende vereisten voldoen:

* Maximale levensduur: wachtwoord moet elke 365 dagen worden gewijzigd
* Hergebruik beperken: gebruikers kunnen geen van de vorige vijf wachtwoorden opnieuw gebruiken
* Lengte: 8 - 20 tekens
* Speciaal teken: Ten minste één speciaal teken
* Alfabetische tekens: ten minste één hoofdletterteken, A-Z, en ten minste één kleine letters, a-z
* Getallen: ten minste één numeriek teken, 0-9
* Maximaal identieke aangrenzende tekens: Drie

    Voorbeeld: CC of CCC is acceptabel als onderdeel van het wachtwoord, maar CCCC niet.

Als u een wachtwoord instelt dat niet aan de vereisten voldoet:

* als u de vSphere Flash-client gebruikt, wordt een fout
* Als u de HTML5-client gebruikt, wordt er geen fout gemeld. De client accepteert de wijziging niet en het oude wachtwoord blijft werken.

## <a name="access-nsx-manager"></a>Toegang tot NSX-manager

NSX-manager wordt geïmplementeerd met een standaardwachtwoord. 

* Gebruikersnaam: **beheerder**
* Wachtwoord: **CloudSimple123!**

U vindt de volledig gekwalificeerde domeinnaam (FQDN) en IP-adres van NSX-manager op cloudsimple portal.

1. Start CloudSimple-portal en selecteer **Resources**.
2. Klik op de Private Cloud, die u wilt gebruiken.
3. **VSphere-beheernetwerk selecteren**
4. Gebruik het FQDN- of IP-adres van **NSX Manager** en maak verbinding via een webbrowser.

    ![NSX Manager FQDN zoeken](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>Een poortgroep maken

Ga als lid van de groep gedistribueerde poorten in vSphere:

1. Volg de instructies in 'Een gedistribueerde poortgroep toevoegen' in [vSphere Networking Guide.](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf)
2. Geef bij het instellen van de gedistribueerde poortgroep de VLAN-id op die is gemaakt in [Een VLAN maken voor uw workloadVM's.](#create-a-vlan-for-your-workload-vms)

## <a name="next-steps"></a>Volgende stappen

* [VMware-VM's in Azure gebruiken](quickstart-create-vmware-virtual-machine.md)
* [Verbinding maken met on-premises netwerk via Azure ExpressRoute](on-premises-connection.md)
* [Site-to-Site VPN instellen vanaf on-premises](vpn-gateway.md)
