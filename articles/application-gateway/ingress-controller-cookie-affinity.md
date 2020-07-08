---
title: Affiniteit op basis van cookies met Application Gateway inschakelen
description: Dit artikel bevat informatie over het inschakelen van op cookies gebaseerde affiniteit met een Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3af2705fedbb9c19d4f128e8e997d3fa73f8b5a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807972"
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
