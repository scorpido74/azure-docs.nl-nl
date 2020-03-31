---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f322803d3484b4ec2d5449e19d67d75b35d6d92f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75751533"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="what-should-i-do-if-im-getting-a-certificate-mismatch-when-connecting-using-certificate-authentication"></a>Wat moet ik doen als ik een fout in het certificaat krijg wanneer ik verbinding maak met certificaatverificatie?

Schakel de controle **uit 'Controleer de identiteit van de server door het certificaat te valideren'** of **voeg de server FQDN samen met het certificaat toe** bij het handmatig maken van een profiel. U dit doen door **rasphone** uit een opdrachtprompt uit te voeren en het profiel uit de vervolgkeuzelijst te kiezen.

Het omzeilen van serveridentiteitsvalidatie wordt in het algemeen niet aanbevolen, maar met Azure-certificaatverificatie wordt hetzelfde certificaat gebruikt voor servervalidatie in het VPN-tunnelingprotocol (IKEv2/SSTP) en het EAP-protocol. Aangezien het servercertificaat en FQDN al zijn gevalideerd door het VPN-tunnelingprotocol, is het overbodig om hetzelfde opnieuw te valideren in EAP.

![point-to-site](./media/vpn-gateway-faq-p2s-all-include/servercert.png "Servercertificaat")

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>Kan ik mijn eigen interne PKI root CA gebruiken om certificaten te genereren voor Point-to-Site-connectiviteit?

Ja. Voorheen konen alleen zelfondertekende basiscertificaten worden gebruikt. U kunt nog steeds 20 basiscertificaten uploaden.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Kan ik certificaten uit Azure Key Vault gebruiken?

Nee.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Welke hulpprogramma's kan ik gebruiken om certificaten te maken?

U kunt uw Enterprise PKI-oplossing (uw interne PKI), Azure PowerShell, MakeCert en OpenSSL gebruiken.

### <a name="are-there-instructions-for-certificate-settings-and-parameters"></a><a name="certsettings"></a>Zijn er instructies voor het instellen van het certificaat en de parameters?

* **Interne PKI/Enterprise PKI-oplossing:** zie de stappen om [certificaten te genereren](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** zie het [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md)-artikel voor een stappenplan.

* **MakeCert:** zie het [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md)-artikel voor een stappenplan.

* **OpenSSL:** 

    * Bij het exporteren van certificaten, moet u het basiscertificaat naar Base64 converteren.

    * Voor het clientcertificaat:

      * Bij het maken van de persoonlijke sleutel, moet u de lengte 4096 opgeven.
      * Bij het maken van het certificaat moet u voor de paramter *-extensies* de waarde *usr_cert* opgeven.
