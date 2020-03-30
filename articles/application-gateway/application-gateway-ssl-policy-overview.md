---
title: Overzicht van TLS-beleid voor Azure Application Gateway
description: Meer informatie over het configureren van TLS-beleid voor Azure Application Gateway en het verminderen van de overhead voor versleuteling en decryptie vanuit een back-endserverfarm.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 871cb930e867002d8af1e7755de27d4873327543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257373"
---
# <a name="application-gateway-tls-policy-overview"></a>Overzicht van het TLS-beleidsoverzicht van Application Gateway

U Azure Application Gateway gebruiken om TLS/SSL-certificaatbeheer te centraliseren en de overhead voor versleuteling en decryptie van een back-endserverfarm te verminderen. Met deze gecentraliseerde TLS-verwerking u ook een centraal TLS-beleid opgeven dat is afgestemd op uw beveiligingsvereisten voor uw organisatie. Dit helpt u te voldoen aan nalevingsvereisten, beveiligingsrichtlijnen en aanbevolen praktijken.

Het TLS-beleid omvat de controle over de TLS-protocolversie, de ciphersuites en de volgorde waarin cijfers worden gebruikt tijdens een TLS-handshake. Application Gateway biedt twee mechanismen voor het beheren van TLS-beleid. U een vooraf gedefinieerd beleid of een aangepast beleid gebruiken.

## <a name="predefined-tls-policy"></a>Vooraf gedefinieerd TLS-beleid

Application Gateway heeft drie vooraf gedefinieerde beveiligingsbeleidsregels. U uw gateway configureren met een van deze beleidsregels om het juiste beveiligingsniveau te krijgen. De beleidsnamen worden geannoteerd per jaar en maand waarin ze zijn geconfigureerd. Elk beleid biedt verschillende TLS-protocolversies en cijfersuites. We raden u aan het nieuwste TLS-beleid te gebruiken om de beste TLS-beveiliging te garanderen.

### <a name="appgwsslpolicy20150501"></a>AppgwsslPolicy20150501

|Eigenschap  |Waarde  |
|---|---|
|Name     | AppgwsslPolicy20150501        |
|MinProtocolVersie     | TLSv1_0        |
|Standaard| True (als er geen vooraf gedefinieerd beleid is opgegeven) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppgwsslPolicy20170401
  
|Eigenschap  |Waarde  |
|   ---      |  ---       |
|Name     | AppgwsslPolicy20170401        |
|MinProtocolVersie     | TLSv1_1        |
|Standaard| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppgwsslPolicy20170401S

|Eigenschap  |Waarde  |
|---|---|
|Name     | AppgwsslPolicy20170401S        |
|MinProtocolVersie     | TLSv1_2        |
|Standaard| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>Aangepast TLS-beleid

Als een vooraf gedefinieerd TLS-beleid moet worden geconfigureerd voor uw vereisten, moet u uw eigen aangepaste TLS-beleid definiëren. Met een aangepast TLS-beleid hebt u volledige controle over de minimale TLS-protocolversie die u moet ondersteunen, evenals de ondersteunde ciphersuites en hun prioriteitsvolgorde.
 
### <a name="tlsssl-protocol-versions"></a>TLS/SSL-protocolversies

* SSL 2.0 en 3.0 zijn standaard uitgeschakeld voor alle toepassingsgateways. Deze protocolversies zijn niet configureerbaar.
* Een aangepast TLS-beleid geeft u de mogelijkheid om een van de volgende drie protocollen te selecteren als de minimale TLS-protocolversie voor uw gateway: TLSv1_0, TLSv1_1 en TLSv1_2.
* Als er geen TLS-beleid is gedefinieerd, zijn alle drie de protocollen (TLSv1_0, TLSv1_1 en TLSv1_2) ingeschakeld.

### <a name="cipher-suites"></a>Cipher suites

Application Gateway ondersteunt de volgende cipher suites waaruit u uw aangepaste beleid kiezen. Het bestellen van de cipher suites bepaalt de prioriteitsvolgorde tijdens TLS-onderhandeling.


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
> TLS cipher suites gebruikt voor de verbinding zijn ook gebaseerd op het type van het certificaat wordt gebruikt. In client-to-toepassingsgatewayverbindingen zijn de gebruikte ciphersuites gebaseerd op het type servercertificaten op de servergatewaylistener. In toepassingsgateway voor backendpoolverbindingen zijn de gebruikte ciphersuites gebaseerd op het type servercertificaten op de backendpoolservers.

## <a name="known-issue"></a>Bekend probleem
Application Gateway v2 ondersteunt momenteel niet de volgende cijfers:
- DHE-RSA-AES128-GCM-SHA256
- DHE-RSA-AES128-SHA
- DHE-RSA-AES256-GCM-SHA384
- DHE-RSA-AES256-SHA
- DHE-DSS-AES128-SHA256
- DHE-DSS-AES128-SHA
- DHE-DSS-AES256-SHA256
- DHE-DSS-AES256-SHA

## <a name="next-steps"></a>Volgende stappen

Zie [TLS-beleidsversies en cijfersuites configureren op Application Gateway](application-gateway-configure-ssl-policy-powershell.md)als u wilt leren bij het configureren van een TLS-beleid.
