---
title: 'Azure VPN-gateway: installeer een Point-to-Site-clientcertificaat'
description: Clientcert installeren voor P2S-certificaatverificatie - Windows, Mac, Linux.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: 787b8a34ed4b232b9e6cc033e67b1a8162c85f6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902849"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Clientcertificaten voor Verificatieverbindingen voor P2S-certificaten installeren

Alle clients die verbinding maken met een virtueel netwerk met Point-to-Site Azure-certificaatverificatie vereisen een clientcertificaat. Met dit artikel u een clientcertificaat installeren dat wordt gebruikt voor verificatie wanneer u verbinding maakt met een VNet via P2S.

## <a name="acquire-a-client-certificate"></a><a name="generate"></a>Een clientcertificaat verkrijgen

Het maakt niet uit van welk clientbesturingssysteem u verbinding wilt maken, u moet altijd over een clientcertificaat beschikken. U een clientcertificaat genereren uit een basiscertificaat dat is gegenereerd met behulp van een Enterprise CA-oplossing of een zelfondertekend rootcertificaat. Zie de [powershell-,](vpn-gateway-certificates-point-to-site.md) [makecert-](vpn-gateway-certificates-point-to-site-makecert.md)of [Linux-instructies](vpn-gateway-certificates-point-to-site-linux.md) voor stappen om een clientcertificaat te genereren. 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>Mac VPN-clients worden alleen ondersteund voor het Resource Manager-implementatiemodel. Ze worden niet ondersteund voor het klassieke implementatiemodel.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

Het Linux-clientcertificaat wordt op de client geïnstalleerd als onderdeel van de clientconfiguratie. Zie [Clientconfiguratie - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) voor instructies.

## <a name="next-steps"></a>Volgende stappen

Ga verder met de configuratiestappen Point-to-Site voor [het maken en installeren van VPN-clientconfiguratiebestanden.](point-to-site-vpn-client-configuration-azure-cert.md)
