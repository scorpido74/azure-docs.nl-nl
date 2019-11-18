---
title: 'Azure VPN Gateway: over P2S VPN-client profielen'
description: Zo kunt u werken met het client profiel bestand
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 5386cace7191be60534f0d2fbf4a85b592d1ecdd
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151637"
---
# <a name="about-p2s-vpn-client-profiles"></a>Over P2S VPN-client profielen

Het gedownloade profiel bestand bevat informatie die nodig is voor het configureren van een VPN-verbinding. Dit artikel helpt u bij het verkrijgen en begrijpen van de informatie die nodig is voor een VPN-client profiel.

## <a name="1-download-the-file"></a>1. down load het bestand

Voer de volgende opdrachten uit. Kopieer de URL van het resultaat naar uw browser om het profiel-zip-bestand te downloaden.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Pak het zip-bestand uit

Pak het gecomprimeerde bestand uit. Het bestand bevat de volgende mappen:

* AzureVPN
* Encarta
* OpenVPN (als u de instellingen voor OpenVPN en Azure AD-verificatie hebt ingeschakeld op de gateway. Zie [een Tenant maken](openvpn-azure-ad-tenant.md).)

## <a name="3-retrieve-information"></a>3. informatie ophalen

Ga in de map **AzureVPN** naar het bestand ***azurevpnconfig. XML*** en open het met Klad blok. Noteer de tekst tussen de volgende tags.

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

* De **map openvpn** bevat het *ovpn* -profiel dat moet worden gewijzigd om de sleutel en het certificaat op te laten bevatten. Zie [openvpn-clients configureren voor Azure VPN gateway](vpn-gateway-howto-openvpn-clients.md#windows)voor meer informatie.

* De **algemene map** bevat het open bare-server certificaat en het VpnSettings. XML-bestand. Het bestand VpnSettings. XML bevat informatie die nodig is voor het configureren van een algemene client.

* Het gedownloade zip-bestand kan ook **WindowsAmd64** -en **WindowsX86** -mappen bevatten. Deze mappen bevatten het installatie programma voor SSTP en IKEv2 voor Windows-clients. U moet beheerders rechten hebben op de client om ze te installeren.

## <a name="next-steps"></a>Volgende stappen

Zie [about Point-to-site](point-to-site-about.md)(Engelstalig) voor meer informatie over punt-naar-site.
