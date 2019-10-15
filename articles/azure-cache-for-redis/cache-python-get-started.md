---
title: 'Snelstartgids: een python-app maken die gebruikmaakt van Azure cache voor redis'
description: In deze snelstart leert u een Python-app te maken die gebruikmaakt van Azure Cache voor Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: v-lincan
ms.assetid: f186202c-fdad-4398-af8c-aee91ec96ba3
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 05/11/2018
ms.author: yegu
ms.custom:
- mvc
- seo-python-october2019
ms.openlocfilehash: 87c22d3497765fca6f0dcae445152e6e2923510e
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329867"
---
# <a name="quickstart-use-azure-cache-for-redis-with-python"></a>Snelstartgids: Azure cache gebruiken voor redis met python

In dit artikel neemt u Azure-cache op voor redis in een python-app om toegang te hebben tot een beveiligde, toegewezen cache die toegankelijk is vanuit elke toepassing in Azure.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
- [Python 2 of 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-on-azure"></a>Azure Cache voor Redis maken op Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Redis-py installeren

[Redis-py](https://github.com/andymccurdy/redis-py) is een Python-interface voor Azure Cache voor Redis. Gebruik het hulpprogramma voor Python-pakketten, *pip*, om het redis-py-pakket te installeren. 

In het volgende voor beeld wordt *PIP3* voor Python3 gebruikt om het redis-py-pakket in Windows 10 te installeren met behulp van een Visual Studio 2019-opdracht prompt voor ontwikkel aars die wordt uitgevoerd met verhoogde beheerders bevoegdheden.

```python
    pip3 install redis
```

![Redis-py installeren](./media/cache-python-get-started/cache-python-install-redis-py.png)


## <a name="read-and-write-to-the-cache"></a>Lezen en schrijven naar de cache

Voer Python uit en test met behulp van de cache vanaf de opdrachtregel. Vervang `<Your Host Name>` en `<Your Access Key>` door de waarden voor uw Azure Cache voor Redis-instantie. 

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>.redis.cache.windows.net',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

> [!IMPORTANT]
> Voor redis-versie is 3,0 of hoger, wordt de SSL-certificaat controle afgedwongen. ssl_ca_certs moet expliciet worden ingesteld wanneer verbinding wordt gemaakt met redis. In het geval van RH Linux kunt u ssl_ca_certs vinden in de certificaat module "/etc/PKI/TLS/certs/ca-Bundle.CRT".

## <a name="create-a-python-script"></a>Een Python-script maken

Maak een nieuw script-tekstbestand met de naam *PythonApplication1.py*.

Voer het volgende script voor *PythonApplication1.py* uit en sla het bestand op. Dit script zal de toegang tot de cache testen. Vervang `<Your Host Name>` en `<Your Access Key>` door de waarden voor uw Azure Cache voor Redis-instantie. 

```python
import redis

myHostname = "<Your Host Name>.redis.cache.windows.net"
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

Voer het script uit met behulp van Python.

![Python-test voltooid](./media/cache-python-get-started/cache-python-completed.png)


## <a name="clean-up-resources"></a>Resources opschonen

Als u verder wilt gaan met de volgende zelfstudie, kunt u de resources die in deze snelstart zijn gemaakt behouden en opnieuw gebruiken.

Als u niet verder wilt met de snelstart, kunt u de Azure-resources verwijderen die in deze snelstart zijn gemaakt om kosten te voorkomen. 

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle bijbehorende resources worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. Als u de resources voor het hosten van dit voorbeeld in een bestaande resourcegroep hebt gemaakt en deze groep ook resources bevat die u wilt behouden, kunt u elke resource afzonderlijk verwijderen via hun respectievelijke blade.
>

Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer **Resourcegroepen**.

Voer in het tekstvak **filteren op naam...** de naam van de resource groep in. In de instructies voor dit artikel is een resourcegroep met de naam *TestResources* gebruikt. Selecteer in de lijst met resultaten van de resource groep de optie **...** en vervolgens de **resource groep verwijderen**.

![Verwijderen](./media/cache-web-app-howto/cache-delete-resource-group.png)

U wordt gevraagd om het verwijderen van de resourcegroep te bevestigen. Voer de naam van de resource groep in om te bevestigen en selecteer **verwijderen**.

Na enkele ogenblikken worden de resourcegroep en alle resources in de groep verwijderd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Maak een eenvoudige ASP.NET-web-app die gebruikmaakt van Azure Cache voor Redis.](./cache-web-app-howto.md)

<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png
