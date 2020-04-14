---
title: TLS 1.0 en 1.1 uit gebruik nemen met Azure Cache voor Redis
description: Meer informatie over het verwijderen van TLS 1.0 en 1.1 uit uw toepassing wanneer u communiceert met Azure Cache voor Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 809fbe85a9783777d5dbef86357bd5a386bd6f81
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261229"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>TLS 1.0 en 1.1 uit gebruik nemen met Azure Cache voor Redis

Er is een industriebrede push naar het exclusieve gebruik van Transport Layer Security (TLS) versie 1.2 of hoger. TLS-versies 1.0 en 1.1 staan erom bekend gevoelig te zijn voor aanvallen zoals BEAST en POODLE, en andere Common Vulnerabilities and Exposures (CVE) zwakke punten te hebben. Ze ondersteunen ook niet de moderne versleutelingsmethoden en cipher suites aanbevolen door Payment Card Industry (PCI) compliance standaarden. Deze [TLS security blog](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) legt een aantal van deze kwetsbaarheden in meer detail.

Als onderdeel van deze inspanning brengen we de volgende wijzigingen aan in Azure Cache voor Redis:

* **Fase 1:** We configureren de standaard minimale TLS-versie als 1.2 voor nieuw gemaakte cache-exemplaren. (Dit was vroeger TLS 1.0.) Bestaande cache-exemplaren worden op dit moment niet bijgewerkt. U [de minimale TLS-versie indien](cache-configure.md#access-ports) nodig wijzigen naar 1.0 of 1.1 voor achterwaartse compatibiliteit. Deze wijziging kan worden uitgevoerd via de Azure-portal of andere beheer-API's.
* **Fase 2:** We stoppen met het ondersteunen van TLS-versies 1.0 en 1.1. Na deze wijziging moet uw toepassing TLS 1.2 of hoger gebruiken om met uw cache te communiceren.

Bovendien, als onderdeel van deze verandering, zullen we het verwijderen van ondersteuning voor oudere, onveilige cypher suites.  Onze ondersteunde cypher suites zijn beperkt tot het volgende wanneer de cache is geconfigureerd met een minimale TLS-versie van 1.2.

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

In dit artikel vindt u algemene richtlijnen voor het detecteren van afhankelijkheden van deze eerdere TLS-versies en deze uit uw toepassing verwijderen.

De datumwaarop deze wijzigingen van kracht worden, zijn:

| Cloud               | Begindatum fase 1 | Begindatum fase 2      |
|---------------------|--------------------|-------------------------|
| Azure (globaal)      |  13 januari 2020  | 11 mei 2020 (verlengd) |
| Azure Government    |  13 maart 2020    | 11 mei 2020            |
| Azure Duitsland       |  13 maart 2020    | 11 mei 2020            |
| Azure China         |  13 maart 2020    | 11 mei 2020            |

## <a name="check-whether-your-application-is-already-compliant"></a>Controleren of uw toepassing al voldoet

De eenvoudigste manier om erachter te komen of uw toepassing werkt met TLS 1.2 is door de **minimale TLS-versiewaarde** in te stellen op TLS 1.2 op een test of faseringscache die wordt gebruikt. De instelling **Voor minimale TLS-versie** bevindt zich in de [geavanceerde instellingen](cache-configure.md#advanced-settings) van uw cache-instantie in de Azure-portal. Als de toepassing blijft functioneren zoals verwacht na deze wijziging, is deze waarschijnlijk compatibel. Mogelijk moet u bepaalde Redis-clientbibliotheken configureren die door uw toepassing specifiek worden gebruikt om TLS 1.2 in te schakelen, zodat ze verbinding kunnen maken met Azure Cache voor Redis via dat beveiligingsprotocol.

## <a name="configure-your-application-to-use-tls-12"></a>Uw toepassing configureren om TLS 1.2 te gebruiken

De meeste toepassingen gebruiken Redis-clientbibliotheken om communicatie met hun caches te verwerken. Hier vindt u instructies voor het configureren van enkele van de populaire clientbibliotheken, in verschillende programmeertalen en frameworks, om TLS 1.2 te gebruiken.

### <a name="net-framework"></a>.NET Framework

Redis .NET-clients gebruiken standaard de vroegste TLS-versie op .NET Framework 4.5.2 of eerder en gebruiken de nieuwste TLS-versie op .NET Framework 4.6 of hoger. Als u een oudere versie van .NET Framework gebruikt, u TLS 1.2 handmatig inschakelen:

* **StackExchange.Redis:** Instellen `ssl=true` `sslprotocols=tls12` en in de verbindingstekenreeks.
* **ServiceStack.Redis:** Volg de [instructies van ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) en vereist servicestack.Redis v5.6 op zijn minst.

### <a name="net-core"></a>.NET Core

Redis .NET Core clients gebruiken standaard de nieuwste TLS-versie.

### <a name="java"></a>Java

Redis Java-clients gebruiken TLS 1.0 op Java versie 6 of eerder. Jedis, Lettuce en Redisson kunnen geen verbinding maken met Azure Cache voor Redis als TLS 1.0 is uitgeschakeld in de cache. Upgrade uw Java-framework om nieuwe TLS-versies te gebruiken.

Voor Java 7 gebruiken Redis-clients TLS 1.2 niet standaard, maar kunnen ze ervoor worden geconfigureerd. Met Jedis u de onderliggende TLS-instellingen opgeven met het volgende codefragment:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

De Lettuce- en Redisson-clients ondersteunen nog geen acceptatie van de TLS-versie, dus ze breken als de cache alleen TLS 1.2-verbindingen accepteert. Fixes voor deze clients worden beoordeeld, dus neem contact op met deze pakketten voor een bijgewerkte versie met deze ondersteuning.

In Java 8 wordt TLS 1.2 standaard gebruikt en hoeft u in de meeste gevallen geen updates voor uw clientconfiguratie nodig te hebben. Test voor de zekerheid uw toepassing.

### <a name="nodejs"></a>Node.js

Node Redis en IORedis gebruiken TLS 1.2 standaard.

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis Predis
 
* Versies eerder dan PHP 7: Predis ondersteunt alleen TLS 1.0. Deze versies werken niet met TLS 1.2; je moet upgraden om TLS 1.2 te gebruiken.
 
* PHP 7.0 tot PHP 7.2.1: Predis gebruikt standaard alleen TLS 1.0 of 1.1. U de volgende tijdelijke oplossing gebruiken om TLS 1.2 te gebruiken. Geef TLS 1.2 op wanneer u de clientinstantie maakt:

  ``` PHP
  $redis=newPredis\Client([
      'scheme'=>'tls',
      'host'=>'host',
      'port'=>6380,
      'password'=>'password',
      'ssl'=>[
          'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
      ],
  ]);
  ```

* PHP 7.3 en later versies: Predis maakt gebruik van de nieuwste TLS-versie.

#### <a name="phpredis"></a>PhpRedis (PhpRedis)

PhpRedis ondersteunt TLS niet op een PHP-versie.

### <a name="python"></a>Python

Redis-py maakt standaard gebruik van TLS 1.2.

### <a name="go"></a>GO

Redigo maakt standaard gebruik van TLS 1.2.

## <a name="additional-information"></a>Aanvullende informatie

- [Azure-cache configureren voor Redis](cache-configure.md)
