---
title: 'Quickstart: Azure cache gebruiken voor redis met Java'
description: In deze snelstart maakt u een nieuwe Java-app die gebruikmaakt van Azure Cache voor Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 29275a5e-2e39-4ef2-804f-7ecc5161eab9
ms.service: cache
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 05/23/2018
ms.author: yegu
ms.custom: mvc, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 019defe782f8258acfd508745d36a0a6599d2e61
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273909"
---
# <a name="quickstart-use-azure-cache-for-redis-with-java"></a>Quickstart: Azure cache gebruiken voor redis met Java

In deze Snelstartgids neemt u Azure cache voor redis op in een Java-app met behulp van de [jedis](https://github.com/xetorthio/jedis) redis-client om toegang te hebben tot een beveiligde, toegewezen cache die toegankelijk is vanuit elke toepassing in Azure.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
- [Apache Maven](https://maven.apache.org/download.cgi)

## <a name="create-an-azure-cache-for-redis"></a>Een Azure Cache voor Redis-instantie maken

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Voeg omgevingsvariabelen toe voor **HOST NAME** en **Primary** (primaire toegangssleutel). U gebruikt deze variabelen vanuit uw code in plaats van de gevoelige gegevens rechtstreeks op te nemen in uw code.

```CMD 
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

## <a name="create-a-new-java-app"></a>Een nieuwe Java-app maken

Met behulp van Maven een nieuwe snelstartgids-app maken:

```CMD
mvn archetype:generate -DarchetypeGroupId=org.apache.maven.archetypes -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.3 -DgroupId=example.demo -DartifactId=redistest -Dversion=1.0
```

Ga naar de nieuwe projectmap *redistest*.

Open het bestand *pom.xml* en voeg een afhankelijkheid toe voor [Jedis](https://github.com/xetorthio/jedis):

```xml
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>2.9.0</version>
        <type>jar</type>
        <scope>compile</scope>
    </dependency>
```

Sla het bestand *pom.xml* op.

Open *App.java* en vervang de code door de volgende code:

```java
package example.demo;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisShardInfo;

/**
 * Redis test
 *
 */
public class App 
{
    public static void main( String[] args )
    {

        boolean useSsl = true;
        String cacheHostname = System.getenv("REDISCACHEHOSTNAME");
        String cachekey = System.getenv("REDISCACHEKEY");

        // Connect to the Azure Cache for Redis over the SSL port using the key.
        JedisShardInfo shardInfo = new JedisShardInfo(cacheHostname, 6380, useSsl);
        shardInfo.setPassword(cachekey); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);      

        // Perform cache operations using the cache connection object...

        // Simple PING command        
        System.out.println( "\nCache Command  : Ping" );
        System.out.println( "Cache Response : " + jedis.ping());

        // Simple get and put of integral data types into the cache
        System.out.println( "\nCache Command  : GET Message" );
        System.out.println( "Cache Response : " + jedis.get("Message"));

        System.out.println( "\nCache Command  : SET Message" );
        System.out.println( "Cache Response : " + jedis.set("Message", "Hello! The cache is working from Java!"));

        // Demonstrate "SET Message" executed as expected...
        System.out.println( "\nCache Command  : GET Message" );
        System.out.println( "Cache Response : " + jedis.get("Message"));

        // Get the client list, useful to see if connection list is growing...
        System.out.println( "\nCache Command  : CLIENT LIST" );
        System.out.println( "Cache Response : " + jedis.clientList());

        jedis.close();
    }
}
```

Deze code laat zien hoe u verbinding kunt maken met een instantie van Azure Cache voor Redis via de omgevingsvariabelen voor hostnaam en sleutel. Met de code wordt ook een tekenreekswaarde opgeslagen in de cache en daaruit opgehaald. De opdrachten `PING` en `CLIENT LIST` worden ook uitgevoerd. 

Sla *App.java* op.

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

Voer de volgende Maven-opdracht uit om de app te bouwen en uit te voeren:

```CMD
mvn compile
mvn exec:java -D exec.mainClass=example.demo.App
```

In het onderstaande voorbeeld ziet u dat de `Message`-sleutel eerder een waarde in de cache had, die was ingesteld met behulp van de Redis Console in Azure Portal. De app heeft die waarde in de cache bijgewerkt. De app heeft ook de opdrachten `PING` en `CLIENT LIST` uitgevoerd.

![Azure cache voor redis-app voltooid](./media/cache-java-get-started/azure-cache-redis-complete.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u verder wilt gaan met de volgende zelfstudie, kunt u de resources die in deze snelstart zijn gemaakt behouden en opnieuw gebruiken.

Als u niet verder wilt met de voorbeeldtoepassing uit de snelstart, kunt u de Azure-resources verwijderen die in deze snelstart zijn gemaakt om kosten te voorkomen. 

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle bijbehorende resources worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. Als u de resources voor het hosten van dit voorbeeld in een bestaande resourcegroep hebt gemaakt en deze groep ook resources bevat die u wilt behouden, kunt u elke resource afzonderlijk verwijderen via hun respectievelijke blade.
>

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer **Resourcegroepen**.

1. Typ in het tekstvak **filteren op naam** de naam van de resource groep. In de instructies voor dit artikel is een resourcegroep met de naam *TestResources* gebruikt. Selecteer in de lijst met resultaten van de resource groep de optie **...** en vervolgens de **resource groep verwijderen**.

   ![De Azure-resource groep is verwijderd](./media/cache-java-get-started/azure-cache-redis-delete-resource-group.png)

1. U wordt gevraagd om het verwijderen van de resourcegroep te bevestigen. Typ de naam van de resource groep die u wilt bevestigen en selecteer **verwijderen**.

Na enkele ogenblikken worden de resourcegroep en alle resources in de groep verwijderd.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u Azure Cache voor Redis gebruikt vanuit een Java-toepassing. Ga verder met de volgende snelstart om Azure Cache voor Redis te gebruiken met een ASP.NET-web-app.

> [!div class="nextstepaction"]
> [Maak een ASP.NET-web-app die gebruikmaakt van Azure Cache voor Redis.](./cache-web-app-howto.md)
