---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c61378510fbfc8bdc13f35ba1063a0d9316d88e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066222"
---
## <a name="1-download-the-file"></a>1. Het bestand downloaden

Voer de volgende opdrachten uit. Kopieer de resultaat-URL naar uw browser om het zip-bestand van het profiel te downloaden.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Haal het zip-bestand

Pak het gecomprimeerde bestand uit. Het bestand bevat de volgende mappen:

* AzureVPN AzureVPN
* Algemeen
* OpenVPN (Als u de verificatie-instellingen voor OpenVPN en Azure AD op de gateway hebt ingeschakeld. Zie [Een tenant maken voor](../articles/vpn-gateway/openvpn-azure-ad-tenant.md)VPN-gateway. Zie Een tenant maken voor Virtueel WAN [- VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="3-retrieve-information"></a>3. Informatie ophalen

Navigeer in de **AzureVPN-map** naar het bestand ***azurevpnconfig.xml*** en open het met Kladblok. Noteer de tekst tussen de volgende tags.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Profieldetails

Wanneer u een verbinding toevoegt, gebruikt u de gegevens die u in de vorige stap hebt verzameld voor de pagina profieldetails. De velden komen overeen met de volgende gegevens:

   * **Publiek:** Hiermee wordt de ontvangerbron geïdentificeerd waarvoor het token is bedoeld
   * **Uitgever:** Hiermee identificeert u de STS (Security Token Service) die het token heeft uitgestoten, evenals de Azure AD-tenant
   * **Huurder:** Bevat een onveranderlijke, unieke id van de directorytenant die het token heeft uitgegeven
   * **FQDN:** De volledig gekwalificeerde domeinnaam (FQDN) op de Azure VPN-gateway
   * **ServerSecret: ServerSecret: ServerSecret: ServerSecret** De vpn-gateway vooraf gedeelde sleutel

## <a name="folder-contents"></a>Inhoud van de map

* De **algemene map** bevat het public server certificaat en het VpnSettings.xml-bestand. Het bestand VpnSettings.xml bevat informatie die nodig is om een generieke client te configureren.

* Het gedownloade zip-bestand kan ook **WindowsAmd64-** en **WindowsX86-mappen** bevatten. Deze mappen bevatten het installatieprogramma voor SSTP en IKEv2 voor Windows-clients. U hebt beheerdersrechten op de client nodig om ze te installeren.