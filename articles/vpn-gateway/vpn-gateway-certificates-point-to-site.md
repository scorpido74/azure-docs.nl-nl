---
title: 'Certificaten genereren en exporteren voor P2S: PowerShell'
titleSuffix: Azure VPN Gateway
description: Maak een zelfondertekend rootcertificaat, exporteer de openbare sleutel en genereer clientcertificaten met PowerShell op Windows 10 of Windows Server 2016.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: cherylmc
ms.openlocfilehash: f28e76e9dcaf1331fd26a2321cd4deca1027e693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279336"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Certificaten genereren en exporteren voor Point-to-Site met PowerShell

Point-to-Site-verbindingen gebruiken certificaten om te verifiëren. In dit artikel ziet u hoe u een zelfondertekend rootcertificaat maakt en clientcertificaten genereert met PowerShell op Windows 10 of Windows Server 2016. Als u op zoek bent naar verschillende certificaatinstructies, zie [Certificaten - Linux](vpn-gateway-certificates-point-to-site-linux.md) of Certificaten - [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

U moet de stappen in dit artikel uitvoeren op een computer met Windows 10 of Windows Server 2016. De PowerShell-cmdlets die u gebruikt om certificaten te genereren, maken deel uit van het besturingssysteem en werken niet op andere versies van Windows. De Windows 10- of Windows Server 2016-computer is alleen nodig om de certificaten te genereren. Zodra de certificaten zijn gegenereerd, u ze uploaden of installeren op een ondersteund besturingssysteem voor klanten.

Als u geen toegang hebt tot een Windows 10- of Windows Server 2016-computer, u [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) gebruiken om certificaten te genereren. De certificaten die u met beide methoden genereert, kunnen op elk [ondersteund](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) besturingssysteem van de client worden geïnstalleerd.

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install-an-exported-client-certificate"></a><a name="install"></a>Een geëxporteerd clientcertificaat installeren

Elke client die via een P2S-verbinding verbinding maakt met vNet, vereist dat een clientcertificaat lokaal wordt geïnstalleerd.

Zie [Een clientcertificaat installeren voor Point-to-Site-verbindingen](point-to-site-how-to-vpn-client-install-azure-cert.md)als u een clientcertificaat wilt installeren.

## <a name="next-steps"></a>Volgende stappen

Ga verder met uw Point-to-Site-configuratie.

* Zie [P2S configureren met native Azure-certificaatverificatie](vpn-gateway-howto-point-to-site-resource-manager-portal.md)voor implementatiemodelstappen voor **Resource Manager.**
* Zie [Een Point-to-Site VPN-verbinding configureren op een VNet (klassiek)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)voor **klassieke** implementatiemodelstappen.