---
title: 'Certificaten genereren en exporteren voor Point-to-Site: Linux: CLI'
description: Maak een zelfondertekend rootcertificaat, exporteer de openbare sleutel en genereer clientcertificaten met de Linux (strongSwan) CLI.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: a0f996ff2805da4dd5af400642eef2506c228d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779753"
---
# <a name="generate-and-export-certificates"></a>Certificaten genereren en exporteren

Point-to-Site-verbindingen gebruiken certificaten om te verifiëren. In dit artikel ziet u hoe u een zelfondertekend rootcertificaat maakt en clientcertificaten genereert met behulp van de Linux CLI en strongSwan. Als u op zoek bent naar verschillende certificaatinstructies, raadpleegt u de artikelen [Powershell](vpn-gateway-certificates-point-to-site.md) of [MakeCert.](vpn-gateway-certificates-point-to-site-makecert.md) Zie de stappen in het artikel [Clientconfiguratie](point-to-site-vpn-client-configuration-azure-cert.md#install) voor informatie over het installeren van strongSwan met behulp van de GUI in plaats van CLI.

## <a name="install-strongswan"></a>Installeer sterkSwan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Certificaten genereren en exporteren

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Volgende stappen

Ga verder met uw Point-to-Site-configuratie om [VPN-clientconfiguratiebestanden](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)te maken en te installeren.
