---
title: Overzicht van SSL-beleid voor Azure-toepassing gateway
description: Meer informatie over het configureren van SSL-beleid voor Azure-toepassing gateway en het verminderen van de overhead voor versleuteling en ontsleuteling van een back-endserver.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 6b3d52f1f6bd0390ab6ccafa80b2979cb0e498fd
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130407"
---
# <a name="application-gateway-ssl-policy-overview"></a>Overzicht van Application Gateway SSL-beleid

U kunt Azure-toepassing gateway gebruiken om het SSL-certificaat beheer te centraliseren en de overhead voor versleuteling en ontsleuteling van een back-endserver te verminderen. Met deze gecentraliseerde SSL-verwerking kunt u ook een centraal SSL-beleid opgeven dat geschikt is voor de beveiligings vereisten van uw organisatie. Zo kunt u voldoen aan nalevings vereisten en beveiligings richtlijnen en aanbevolen procedures.

Het SSL-beleid bevat de controle over de SSL-protocol versie, evenals de coderings suites en de volg orde waarin de code ringen worden gebruikt tijdens een SSL-handshake. Application Gateway biedt twee mechanismen voor het beheren van het SSL-beleid. U kunt een vooraf gedefinieerd beleid of een aangepast beleid gebruiken.

## <a name="predefined-ssl-policy"></a>Vooraf gedefinieerd SSL-beleid

Application Gateway heeft drie vooraf gedefinieerde beveiligings beleidsregels. U kunt uw gateway met een van deze beleids regels configureren om het juiste beveiligings niveau te verkrijgen. De beleids namen worden door het jaar en de maand waarin ze zijn geconfigureerd, gemarkeerd. Elk beleid biedt verschillende SSL-protocol versies en coderings suites. U wordt aangeraden het meest recente SSL-beleid te gebruiken om de beste SSL-beveiliging te garanderen.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Eigenschap  |Waarde  |
|---|---|
|Naam     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Standaard| Waar (als er geen vooraf gedefinieerd beleid is opgegeven) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Eigenschap  |Waarde  |
|   ---      |  ---       |
|Naam     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Standaard| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Eigenschap  |Waarde  |
|---|---|
|Naam     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Standaard| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>Aangepast SSL-beleid

Als er een vooraf gedefinieerd SSL-beleid moet worden geconfigureerd voor uw vereisten, moet u uw eigen aangepaste SSL-beleid definiëren. Met een aangepast SSL-beleid hebt u volledige controle over de minimale SSL-protocol versie voor ondersteuning, evenals de ondersteunde coderings suites en hun prioriteits volgorde.
 
### <a name="ssl-protocol-versions"></a>SSL-protocol versies

* SSL 2,0 en 3,0 zijn standaard uitgeschakeld voor alle toepassings gateways. Deze protocol versies kunnen niet worden geconfigureerd.
* Een aangepast SSL-beleid biedt u de mogelijkheid om een van de volgende drie protocollen te selecteren als de minimale SSL-protocol versie voor uw gateway: TLSv1_0, TLSv1_1 en TLSv1_2.
* Als er geen SSL-beleid is gedefinieerd, worden alle drie de protocollen (TLSv1_0, TLSv1_1 en TLSv1_2) ingeschakeld.

### <a name="cipher-suites"></a>Coderings suites

Application Gateway ondersteunt de volgende coderings suites van waaruit u uw aangepaste beleid kunt kiezen. De volg orde van de coderings suites bepaalt de prioriteits volgorde tijdens SSL-onderhandeling.


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

> [!NOTE]
> De SSL-coderings suites die voor de verbinding worden gebruikt, zijn ook gebaseerd op het type certificaat dat wordt gebruikt. In client-naar-toepassings gateway-verbindingen worden de gebruikte coderings suites gebaseerd op het type server certificaten op de Application Gateway-listener. In Application Gateway naar back-end-pool verbindingen worden de gebruikte coderings suites gebaseerd op het type server certificaten op de back-endservers.

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over het configureren van een SSL-beleid, raadpleegt u [SSL-beleid configureren voor een toepassings gateway](application-gateway-configure-ssl-policy-powershell.md).
