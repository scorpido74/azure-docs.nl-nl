---
title: 'Certificaten genereren en exporteren voor punt-naar-site: Linux: CLI'
description: Maak een zelfondertekend basis certificaat, Exporteer de open bare sleutel en Genereer client certificaten met behulp van de Linux-CLI (strongSwan).
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: c55c304ae71cba396266c83bcaaa727351dcd677
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87064618"
---
# <a name="generate-and-export-certificates"></a>Certificaten genereren en exporteren

Punt-naar-site-verbindingen gebruiken certificaten voor verificatie. In dit artikel wordt beschreven hoe u een zelfondertekend basis certificaat maakt en client certificaten genereert met behulp van de Linux CLI en strongSwan. Als u andere certificaat instructies zoekt, raadpleegt u de [Power shell](vpn-gateway-certificates-point-to-site.md) -of [makecert](vpn-gateway-certificates-point-to-site-makecert.md) -artikelen. Voor informatie over het installeren van strongSwan met behulp van de gebruikers interface in plaats van CLI, zie de stappen in het artikel [client configuratie](point-to-site-vpn-client-configuration-azure-cert.md#install) .

## <a name="install-strongswan"></a>StrongSwan installeren

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Certificaten genereren en exporteren

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Volgende stappen

Ga door met uw punt-naar-site-configuratie om [configuratie bestanden voor VPN-clients te maken en te installeren](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
