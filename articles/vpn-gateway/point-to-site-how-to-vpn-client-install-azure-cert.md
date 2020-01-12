---
title: 'Azure VPN Gateway: een punt-naar-site-client certificaat installeren'
description: Installeer het client certificaat voor P2S-certificaat verificatie-Windows, Mac, Linux.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: 787b8a34ed4b232b9e6cc033e67b1a8162c85f6c
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902849"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Client certificaten installeren voor P2S-certificaat authenticatie verbindingen

Alle clients die verbinding maken met een virtueel netwerk met behulp van punt-naar-site-Azure-certificaat verificatie, vereisen een client certificaat. Dit artikel helpt u bij het installeren van een client certificaat dat wordt gebruikt voor verificatie wanneer u verbinding maakt met een VNet met behulp van P2S.

## <a name="generate"></a>Een client certificaat verkrijgen

Ongeacht het besturings systeem van de client waarmee u verbinding wilt maken, moet u altijd een client certificaat hebben. U kunt een client certificaat genereren van een basis certificaat dat is gegenereerd met een bedrijfs CA-oplossing of een zelfondertekend basis certificaat. Zie de [Power shell](vpn-gateway-certificates-point-to-site.md)-, [makecert](vpn-gateway-certificates-point-to-site-makecert.md)-of [Linux](vpn-gateway-certificates-point-to-site-linux.md) -instructies voor de stappen voor het genereren van een client certificaat. 

## <a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Mac

>[!NOTE]
>Mac VPN-clients worden alleen ondersteund voor het Resource Manager-implementatie model. Ze worden niet ondersteund voor het klassieke implementatie model.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="installlinux"></a>Linux

Het Linux-client certificaat wordt geïnstalleerd op de client als onderdeel van de client configuratie. Zie [client configuratie-Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) voor instructies.

## <a name="next-steps"></a>Volgende stappen

Ga door met de stappen voor punt-naar-site [-configuratie om configuratie bestanden voor VPN-clients te maken en te installeren](point-to-site-vpn-client-configuration-azure-cert.md).
