---
title: 'Snelstartgids: een python-app maken die gebruikmaakt van Azure cache voor redis'
description: In deze Quick Start leert u hoe u een python-app maakt die gebruikmaakt van Azure cache voor redis.
author: yegu-ms
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: yegu
ms.custom:
- mvc
- seo-python-october2019
ms.openlocfilehash: 1ac1767d1ac3f6333d73a1e1c66ff7250942d266
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122663"
---
# <a name="quickstart-create-a-python-app-that-uses-azure-cache-for-redis"></a>Snelstartgids: een python-app maken die gebruikmaakt van Azure cache voor redis

In dit artikel neemt u Azure-cache op voor redis in een python-app om toegang te hebben tot een beveiligde, toegewezen cache die toegankelijk is vanuit elke toepassing in Azure.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
- [Python 2 of 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Een instantie van Azure Cache voor Redis maken
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Redis-py installeren

[Redis-py](https://github.com/andymccurdy/redis-py) is een Python-interface voor Azure Cache voor Redis. Gebruik het hulp programma Python-pakketten, *PIP*, om het *redis-py-* pakket te installeren vanaf een opdracht prompt. 

In het volgende voor beeld gebruikt *PIP3* voor python 3 om *redis-py* op Windows 10 te installeren vanaf een opdracht prompt van de beheerder.

![Installeer de redis-py Python-interface naar Azure cache voor redis](./media/cache-python-get-started/cache-python-install-redis-py.png)

## <a name="read-and-write-to-the-cache"></a>Lezen en schrijven naar de cache

Voer python uit vanaf de opdracht regel en test uw cache met behulp van de volgende code. Vervang `<Your Host Name>` en `<Your Access Key>` door de waarden uit uw Azure-cache voor redis-exemplaar. De hostnaam is van het formulier *\<DNS-naam >. redis. cache. Windows. net*.

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
> Voor Azure cache voor redis versie 3,0 of hoger wordt de SSL-certificaat controle afgedwongen. ssl_ca_certs moet expliciet worden ingesteld bij het maken van verbinding met Azure cache voor redis. Voor RedHat Linux bevinden ssl_ca_certs zich in de */etc/PKI/TLS/certs/ca-Bundle.CRT* -certificaat module.

## <a name="create-a-python-sample-app"></a>Een python-voor beeld-app maken

Maak een nieuw tekst bestand, voeg het volgende script toe en sla het bestand op als *PythonApplication1.py*. Vervang `<Your Host Name>` en `<Your Access Key>` door de waarden uit uw Azure-cache voor redis-exemplaar. De hostnaam is van het formulier *\<DNS-naam >. redis. cache. Windows. net*.

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

Voer *PythonApplication1.py* uit met python. Als het goed is, ziet u de resultaten zoals in het volgende voor beeld:

![Python-script uitvoeren om de cache toegang te testen](./media/cache-python-get-started/cache-python-completed.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met de Azure-resource groep en de resources die u in deze Quick Start hebt gemaakt, kunt u ze verwijderen om kosten te voor komen.

> [!IMPORTANT]
> Het verwijderen van een resource groep is onomkeerbaar en de resource groep en alle resources hierin worden definitief verwijderd. Als u uw Azure-cache hebt gemaakt voor redis-exemplaar in een bestaande resource groep die u wilt blijven gebruiken, kunt u alleen de cache verwijderen door **verwijderen** te selecteren op de pagina **overzicht** van cache. 

De resource groep en de bijbehorende Redis Cache voor Azure-exemplaar verwijderen:

1. Zoek en selecteer in het [Azure Portal](https://portal.azure.com) **resource groepen**.
1. In het tekstvak **filteren op naam** voert u de naam in van de resource groep die uw cache-exemplaar bevat en selecteert u deze in de zoek resultaten. 
1. Selecteer **Resourcegroep verwijderen** op de pagina van de resourcegroep.
1. Typ de naam van de resource groep en selecteer vervolgens **verwijderen**.
   
   ![De resource groep voor Azure cache voor redis verwijderen](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Maak een eenvoudige ASP.NET-web-app die gebruikmaakt van Azure Cache voor Redis.](./cache-web-app-howto.md)

