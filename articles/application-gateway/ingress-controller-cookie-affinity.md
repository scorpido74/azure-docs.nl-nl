---
title: Affiniteit op basis van cookies met Application Gateway inschakelen
description: Dit artikel bevat informatie over het inschakelen van op cookies gebaseerde affiniteit met een Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: cb1a403888f8dca78d1af7395095aa2bac39935e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513573"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Affiniteit op basis van cookies met een Application Gateway inschakelen
Zoals beschreven in de [documentatie over Azure-toepassing gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings), ondersteunt Application Gateway de op cookies gebaseerde affiniteit, wat betekent dat het volgende verkeer van een gebruikers sessie naar dezelfde server kan worden doorgestuurd voor verwerking.

## <a name="example"></a>Voorbeeld
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```
