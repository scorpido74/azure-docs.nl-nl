---
title: Het gebruik van TLS 1,0 en 1,1 met Azure cache voor redis verwijderen | Microsoft Docs
description: Meer informatie over het verwijderen van TLS 1,0 en 1,1 vanuit uw toepassing tijdens het communiceren met Azure cache voor redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: d1d44467d310b87d306ea7401e66784d684a1bf3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901896"
---
# <a name="remove-use-of-tls-10-and-11-with-azure-cache-for-redis"></a>Het gebruik van TLS 1,0 en 1,1 met Azure cache voor redis verwijderen

Er is een push op de hele industrie gericht op het gebruik van TLS 1,2 of hoger. TLS-versies 1,0 en 1,1 zijn bekend als vatbaar voor aanvallen zoals BEAST en POODLE en hebben andere veelvoorkomende zwakke plekken en bloot stellingen (CVE). Ze bieden ook geen ondersteuning voor de moderne versleutelings methoden en coderings suites die worden aanbevolen door PCI-nalevings standaarden. In deze [blog van TLS-beveiliging](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) worden enkele van deze beveiligings problemen in meer details uitgelegd.

Hoewel geen van deze onmiddellijk direct problemen oplevert, kunt u overwegen om TLS 1,0 en 1,1 zo snel mogelijk te verplaatsen. Azure cache voor redis stopt met de ondersteuning van deze TLS-versies vanaf 31 maart 2020. Uw toepassing moet ten minste TLS 1,2 gebruiken om te communiceren met uw cache na deze datum.

Dit artikel bevat algemene richt lijnen voor het detecteren en verwijderen van deze afhankelijkheden uit uw toepassing.

## <a name="check-if-your-application-is-already-compliant"></a>Controleren of uw toepassing al compatibel is

De eenvoudigste manier om erachter te komen of uw toepassing werkt met TLS 1,2 is door de minimale TLS-versie in te stellen op een test-of faserings cache die wordt gebruikt voor TLS 1,2. U kunt de instelling minimale TLS-versie vinden in de [Geavanceerde instellingen](cache-configure.md#advanced-settings) van uw cache-exemplaar in het Azure Portal. Als de toepassing blijft werken zoals verwacht na deze wijziging, is deze waarschijnlijk compatibel. Sommige redis-client bibliotheken die door onze toepassing worden gebruikt, moeten mogelijk specifiek worden geconfigureerd voor het inschakelen van TLS 1,2 om verbinding te maken met Azure cache voor redis via dat beveiligings protocol.

## <a name="configure-your-application-to-use-tls-12"></a>Uw toepassing configureren voor het gebruik van TLS 1,2

De meeste toepassingen gebruiken redis-client bibliotheken om communicatie met de caches af te handelen. Hieronder vindt u instructies voor het configureren van een aantal populaire client bibliotheken in verschillende programmeer talen en frameworks voor het gebruik van TLS 1,2.

### <a name="net-framework"></a>.NET Framework

Redis .NET-clients gebruiken standaard de laagste TLS-versie op .NET Framework 4.5.2 of lager en de hoogste TLS-versie op 4,6 of hoger. Als u een oudere versie van .NET Framework gebruikt, kunt u TLS 1,2 hand matig inschakelen:

* Stack Exchange. redis: Stel `ssl=true` en `sslprotocls=tls12` in het connection string in.
* ServiceStack. redis: Volg [deze instructies](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

Redis .NET core-clients gebruiken standaard de hoogste TLS-versie.

### <a name="java"></a>Java

Redis Java-clients gebruiken TLS 1,0 in Java versie 6 of lager. Jedis, sla en Radisson kunnen geen verbinding maken met Azure cache voor redis als TLS 1,0 is uitgeschakeld in de cache. Er is momenteel geen tijdelijke oplossing.

Op Java 7 of hoger gebruiken redis-clients standaard geen TLS 1,2, maar kunnen ze wel worden geconfigureerd. Sla en Radisson bieden nu geen ondersteuning voor dit recht. Ze worden onderbroken als de cache alleen TLS 1,2-verbindingen accepteert. Met jedis kunt u de onderliggende TLS-instellingen opgeven met het volgende code fragment:

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

Predis in PHP 7 werkt niet omdat de laatste alleen TLS 1,0 ondersteunt. Op PHP 7.2.1 of lager maakt Predis standaard gebruik van TLS 1,0 of 1,1. U kunt TLS 1,2 opgeven bij het instantiëren van de client:

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
