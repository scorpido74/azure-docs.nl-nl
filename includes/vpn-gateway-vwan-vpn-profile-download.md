---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/31/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fc2393cfe87e2639ce40e66e6053d4d430518719
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87515301"
---
## <a name="1-download-the-file"></a>1. down load het bestand

Voer de volgende opdrachten uit. Kopieer de URL van het resultaat naar uw browser om het profiel-zip-bestand te downloaden.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Pak het zip-bestand uit

Pak het gecomprimeerde bestand uit. Het bestand bevat de volgende mappen:

* AzureVPN
* Algemeen
* OpenVPN (als u de OpenVPN hebt ingeschakeld met **Azure** -certificaat **-of RADIUS-verificatie** -instellingen op de gateway). Zie [een Tenant maken](../articles/vpn-gateway/openvpn-azure-ad-tenant.md)voor VPN gateway. Zie voor virtuele WAN [-VWAN een Tenant maken](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="3-retrieve-information"></a>3. informatie ophalen

Ga in de map **AzureVPN** naar het ***azurevpnconfig.xml*** -bestand en open het met Klad blok. Noteer de tekst tussen de volgende tags.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Profiel Details

Wanneer u een verbinding toevoegt, gebruikt u de gegevens die u in de vorige stap hebt verzameld voor de pagina Profiel Details. De velden komen overeen met de volgende informatie:

   * **Doel groep:** Identificeert de resource van de ontvanger waarvoor het token is bedoeld
   * **Verlener:** Identificeert de Security Token Service (STS) die het token heeft verzonden, evenals de Azure AD-Tenant
   * **Tenant:** Bevat een onveranderbare, unieke id van de Directory-Tenant die het token heeft uitgegeven
   * **FQDN:** De Fully Qualified Domain Name (FQDN) op de Azure VPN-gateway
   * **ServerSecret:** De vooraf gedeelde VPN gateway-sleutel

## <a name="folder-contents"></a>Mapinhoud

* De **algemene map** bevat het open bare-server certificaat en het VpnSettings.xml-bestand. Het VpnSettings.xml bestand bevat informatie die nodig is voor het configureren van een algemene client.

* Het gedownloade zip-bestand kan ook **WindowsAmd64** -en **WindowsX86** -mappen bevatten. Deze mappen bevatten het installatie programma voor SSTP en IKEv2 voor Windows-clients. U moet beheerders rechten hebben op de client om ze te installeren.
