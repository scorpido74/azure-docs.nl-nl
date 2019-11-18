---
title: Verwijder TLS 1,0 en 1,1 van gebruik met Azure cache voor redis
description: Meer informatie over het verwijderen van TLS 1,0 en 1,1 vanuit uw toepassing tijdens het communiceren met Azure cache voor redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 585828855cdbd3a585ced1aa6803482c3f26f0f4
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121577"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Verwijder TLS 1,0 en 1,1 van gebruik met Azure cache voor redis

Er is sprake van een brede push naar het exclusieve gebruik van Transport Layer Security (TLS) versie 1,2 of hoger. TLS-versies 1,0 en 1,1 zijn bekend als vatbaar voor aanvallen zoals BEAST en POODLE, en om andere veelvoorkomende zwakke plekken en bloot stellingen (CVE) te hebben. Ze bieden ook geen ondersteuning voor de moderne versleutelings methoden en coderings suites die worden aanbevolen door PCI-nalevings normen (Payment Card Industry). In deze [blog van TLS-beveiliging](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) worden enkele van deze beveiligings problemen uitvoeriger beschreven.

Hoewel geen van deze overwegingen onmiddellijk een probleem vormt, raden we u aan om TLS 1,0 en 1,1 binnenkort niet meer te gebruiken. Azure cache voor redis stopt met de ondersteuning van deze TLS-versies op 31 maart 2020. Na deze datum moet uw toepassing TLS 1,2 of hoger gebruiken om met uw cache te communiceren.

Dit artikel bevat algemene richt lijnen over het detecteren van afhankelijkheden voor deze eerdere TLS-versies en het verwijderen van deze toepassingen uit uw toepassing.

## <a name="check-whether-your-application-is-already-compliant"></a>Controleren of uw toepassing al compatibel is

De eenvoudigste manier om erachter te komen of uw toepassing werkt met TLS 1,2 is door de **minimale TLS-versie** waarde in te stellen op TLS 1,2 op een test-of faserings cache die wordt gebruikt. De instelling **minimale TLS-versie** bevindt zich in de [Geavanceerde instellingen](cache-configure.md#advanced-settings) van uw cache-exemplaar in het Azure Portal. Als de toepassing blijft functioneren zoals verwacht na deze wijziging, is deze waarschijnlijk compatibel. Mogelijk moet u bepaalde redis-client bibliotheken die door uw toepassing worden gebruikt, specifiek configureren om TLS 1,2 in te scha kelen, zodat ze verbinding kunnen maken met Azure cache voor redis via dat beveiligings protocol.

## <a name="configure-your-application-to-use-tls-12"></a>Uw toepassing configureren voor het gebruik van TLS 1,2

De meeste toepassingen gebruiken redis-client bibliotheken om communicatie met de caches af te handelen. Hier vindt u instructies voor het configureren van een aantal populaire client Bibliotheken, in verschillende programmeer talen en frameworks, voor het gebruik van TLS 1,2.

### <a name="net-framework"></a>.NET Framework

Redis .NET-clients gebruiken standaard de oudste TLS-versie op .NET Framework 4.5.2 of eerder en gebruiken de meest recente versie van TLS op .NET Framework 4,6 of hoger. Als u een oudere versie van .NET Framework gebruikt, kunt u TLS 1,2 hand matig inschakelen:

* **Stack Exchange. redis:** Stel `ssl=true` en `sslprotocls=tls12` in het connection string in.
* **ServiceStack. redis:** Volg de [instructies voor ServiceStack. redis](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

Redis .NET core-clients gebruiken standaard de meest recente TLS-versie.

### <a name="java"></a>Java

Redis Java-clients gebruiken TLS 1,0 op Java versie 6 of lager. Jedis, sla en Radisson kunnen geen verbinding maken met Azure cache voor redis als TLS 1,0 is uitgeschakeld in de cache. Er is momenteel geen tijdelijke oplossing.

Op Java 7 of hoger gebruiken redis-clients standaard geen TLS 1,2, maar kunnen ze hiervoor worden geconfigureerd. Sla en Radisson bieden nu geen ondersteuning voor deze configuratie. Ze worden onderbroken als de cache alleen TLS 1,2-verbindingen accepteert. Met jedis kunt u de onderliggende TLS-instellingen opgeven met het volgende code fragment:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1", "TLSv1.1", "TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

### <a name="nodejs"></a>Node.js

Het knoop punt redis en IORedis gebruiken standaard TLS 1,2.

### <a name="php"></a>PHP

Predis in PHP 7 werkt niet omdat PHP 7 alleen TLS 1,0 ondersteunt. Op PHP 7.2.1 of eerder maakt Predis standaard gebruik van TLS 1,0 of 1,1. U kunt TLS 1,2 opgeven wanneer u het client exemplaar maakt:

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

Op PHP 7,3 of hoger maakt Predis gebruik van de meest recente TLS-versie.

PhpRedis biedt geen ondersteuning voor TLS op een PHP-versie.

### <a name="python"></a>Python

Redis-py maakt standaard gebruik van TLS 1,2.

### <a name="go"></a>GO

Redigo maakt standaard gebruik van TLS 1,2.

## <a name="additional-information"></a>Aanvullende informatie

- [Azure-cache configureren voor redis](cache-configure.md)
