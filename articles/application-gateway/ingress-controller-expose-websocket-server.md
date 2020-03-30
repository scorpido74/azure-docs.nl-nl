---
title: Een WebSocket-server blootstellen aan application gateway
description: In dit artikel vindt u informatie over het blootstellen van een WebSocket-server aan Application Gateway met een ingress-controller voor AKS-clusters.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1f068c9d98a827afd16da01bdc40cbb6ca5dc465
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297829"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Een WebSocket-server blootstellen aan application gateway

Zoals beschreven in de Application Gateway v2-documentatie - biedt het [native ondersteuning voor de WebSocket- en HTTP/2-protocollen.](features.md#websocket-and-http2-traffic) Houd er rekening mee dat er voor zowel Application Gateway als de Kubernetes Ingress geen door de gebruiker configureerbare instelling is om websocket-ondersteuning selectief in te schakelen of uit te schakelen.

De Kubernetes-implementatie YAML hieronder toont de minimale configuratie die wordt gebruikt om een WebSocket-server te implementeren, wat hetzelfde is als het implementeren van een gewone webserver:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

Aangezien aan alle vereisten is voldaan en u een Application Gateway hebt die wordt beheerd door een Kubernetes-ingress in uw AKS, zou de bovenstaande `ws.contoso.com` implementatie resulteren in een WebSockets-server die wordt blootgesteld op poort 80 van het openbare IP-adres van uw Toepassingsgateway en het domein.

Met de volgende cURL-opdracht wordt de implementatie van de WebSocket-server getest:
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>WebSocket-statussondes

Als uw implementatie niet expliciet statussondes definieert, probeert Application Gateway een HTTP GET op het eindpunt van uw WebSocket-server.
Afhankelijk van de server implementatie[(hier is er een waar we van houden)](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)WebSocket specifieke headers kunnen nodig zijn (bijvoorbeeld).`Sec-Websocket-Version`
Aangezien Application Gateway geen WebSocket-headers toevoegt, is de statussonderespons van `400 Bad Request`de Statussonde van de Application Gateway van uw WebSocket-server waarschijnlijk .
Als gevolg application gateway zal markeren uw pods als ongezond, wat uiteindelijk zal resulteren in een `502 Bad Gateway` voor de consumenten van de WebSocket server.
Om dit te voorkomen moet u mogelijk een HTTP GET-handler toevoegen voor een statuscontrole aan uw server (bijvoorbeeld`/health` die retourneert). `200 OK`
