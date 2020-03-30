---
title: Cookiegebaseerde affiniteit inschakelen met Application Gateway
description: In dit artikel vindt u informatie over het inschakelen van cookiegebaseerde affiniteit met een Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: adacd805e736524fd7956c4bbc0ad402980b4cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795981"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Op cookies gebaseerde affiniteit inschakelen met een toepassingsgateway
Zoals beschreven in de [Azure Application Gateway Documentation](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings), ondersteunt Application Gateway cookie gebaseerde affiniteit, wat betekent dat het volgende verkeer van een gebruikerssessie naar dezelfde server kan leiden voor verwerking.

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
