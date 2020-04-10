---
title: 'Snelstart: een Python-app maken - Azure-cache voor Redis'
description: In deze quickstart leert u hoe u een Python-app maakt die Azure-cache voor Redis gebruikt.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- seo-python-october2019
ms.date: 11/05/2019
ms.openlocfilehash: 6ce3dd1cbb694988af3555765342a1c4ca3850b5
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010848"
---
# <a name="quickstart-create-a-python-app-that-uses-azure-cache-for-redis"></a>Snelstart: een Python-app maken die Azure-cache voor Redis gebruikt

In dit artikel neemt u Azure Cache voor Redis op in een Python-app om toegang te hebben tot een beveiligde, speciale cache die toegankelijk is vanuit elke toepassing binnen Azure.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)
- [Python 2 of 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Een instantie van Azure Cache voor Redis maken
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Redis-py installeren

[Redis-py](https://github.com/andymccurdy/redis-py) is een Python-interface voor Azure Cache voor Redis. Gebruik de Python pakketten tool, *pip*, om de *redis-py* pakket te installeren van een opdracht prompt. 

In het volgende voorbeeld *wordt pip3* voor Python 3 gebruikt om *redis-py* op Windows 10 te installeren via een opdrachtprompt van administrator.

![De redis-py Python-interface installeren in Azure Cache voor Redis](./media/cache-python-get-started/cache-python-install-redis-py.png)

## <a name="read-and-write-to-the-cache"></a>Lezen en schrijven naar de cache

Voer Python uit vanaf de opdrachtregel en test uw cache met behulp van de volgende code. Vervang `<Your Host Name>` `<Your Access Key>` en met de waarden uit uw Azure-cache voor bijvoorbeeld Redis. Uw hostnaam is van de DNS-naam van het formulier * \<>.redis.cache.windows.net*.

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

> [!IMPORTANT]
> Voor Azure Cache voor Redis versie 3.0 of hoger wordt tls/SSL-certificaatcontrole afgedwongen. ssl_ca_certs moet expliciet worden ingesteld wanneer u verbinding maakt met Azure Cache voor Redis. Voor RedHat Linux ssl_ca_certs zich in de */etc/pki/tls/certs/ca-bundle.crt* certificaatmodule.

## <a name="create-a-python-sample-app"></a>Een Voorbeeld-app van Python maken

Maak een nieuw tekstbestand, voeg het volgende script toe en sla het bestand op als *PythonApplication1.py*. Vervang `<Your Host Name>` `<Your Access Key>` en met de waarden uit uw Azure-cache voor bijvoorbeeld Redis. Uw hostnaam is van de DNS-naam van het formulier * \<>.redis.cache.windows.net*.

```python
import redis

myHostname = "<Your Host Name>"
myPassword = "<Your Access Key>"

r = redis.StrictRedis(host=myHostname, port=6380,
                      password=myPassword, ssl=True)

result = r.ping()
print("Ping returned : " + str(result))

result = r.set("Message", "Hello!, The cache is working with Python!")
print("SET Message returned : " + str(result))

result = r.get("Message")
print("GET Message returned : " + result.decode("utf-8"))

result = r.client_list()
print("CLIENT LIST returned : ")
for c in result:
    print("id : " + c['id'] + ", addr : " + c['addr'])
```

Ren *PythonApplication1.py* met Python. U ziet resultaten zoals het volgende voorbeeld:

![Python-script uitvoeren om cachetoegang te testen](./media/cache-python-get-started/cache-python-completed.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met de Azure-brongroep en resources die u in deze quickstart hebt gemaakt, u deze verwijderen om kosten te voorkomen.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep is onomkeerbaar en de resourcegroep en alle bronnen daarin worden definitief verwijderd. Als u uw Azure Cache voor Redis-exemplaar hebt gemaakt in een bestaande brongroep die u wilt behouden, u alleen de cache verwijderen door **Verwijderen** te selecteren op de pagina **Cacheoverzicht.** 

Ga als eerste voor het verwijderen van de brongroep en de Redis-cache voor Azure:

1. Zoek in de [Azure-portal](https://portal.azure.com)naar **resourcegroepen**en selecteer deze .
1. Voer in het tekstvak **Filteren op naam** de naam in van de brongroep die uw cache-instantie bevat en selecteer deze vervolgens in de zoekresultaten. 
1. Selecteer **Resourcegroep verwijderen** op de pagina van de resourcegroep.
1. Typ de naam van de brongroep en selecteer **Verwijderen**.
   
   ![Uw brongroep voor Azure-cache voor Redis verwijderen](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Maak een eenvoudige ASP.NET-web-app die gebruikmaakt van Azure Cache voor Redis.](./cache-web-app-howto.md)

