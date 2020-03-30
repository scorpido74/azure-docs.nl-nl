---
title: Certificaten genereren en exporteren voor VPN-verbindingen van gebruikers | Azure Virtual WAN
description: Maak een zelfondertekend rootcertificaat, exporteer de openbare sleutel en genereer clientcertificaten met PowerShell op Windows 10 of Windows Server 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: 0303bac88f34c895a4a680cd5bff0e9d1513d2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059918"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>Certificaten genereren en exporteren voor VPN-verbindingen van gebruikers

GebruikersVPN-verbindingen (point-to-site) gebruiken certificaten om te verifiëren. In dit artikel ziet u hoe u een zelfondertekend rootcertificaat maakt en clientcertificaten genereert met PowerShell op Windows 10 of Windows Server 2016.

U moet de stappen in dit artikel uitvoeren op een computer met Windows 10 of Windows Server 2016. De PowerShell-cmdlets die u gebruikt om certificaten te genereren, maken deel uit van het besturingssysteem en werken niet op andere versies van Windows. De Windows 10- of Windows Server 2016-computer is alleen nodig om de certificaten te genereren. Zodra de certificaten zijn gegenereerd, u ze uploaden of installeren op een ondersteund besturingssysteem voor klanten.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Volgende stappen

Doorgaan met de [virtuele WAN-stappen voor de VPN-verbinding van de gebruiker](virtual-wan-about.md)
