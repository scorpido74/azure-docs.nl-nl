---
title: Een WebSocket-server beschikbaar maken voor Application Gateway
description: Dit artikel bevat informatie over het beschikbaar maken van een WebSocket-server voor het Application Gateway van de ingangs controller voor AKS-clusters.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: 08a12a2de0c4912913058291206772bbcb2c4a44
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513430"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Een WebSocket-server beschikbaar maken voor Application Gateway

Zoals beschreven in de documentatie over Application Gateway v2, biedt het [systeem eigen ondersteuning voor de WebSocket-en http/2-protocollen](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic). Houd er rekening mee dat voor zowel Application Gateway als de Kubernetes-inkomend is, er geen door de gebruiker geconfigureerde instelling is om WebSocket-ondersteuning selectief in of uit te scha kelen.

De Kubernetes-implementatie YAML hieronder toont de minimale configuratie die wordt gebruikt voor het implementeren van een WebSocket-server. Dit is hetzelfde als het implementeren van een reguliere webserver:
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

Als aan alle vereisten wordt voldaan en u een Application Gateway hebt die wordt beheerd door een Kubernetes in uw AKS, zou de bovenstaande implementatie ertoe leiden dat een websockets-server wordt weer gegeven op poort 80 van het open bare IP-adres van de Application Gateway en de `ws.contoso.com` domeinen.

Met de volgende krul opdracht wordt de WebSocket-Server implementatie getest:
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>WebSocket-status tests

Als uw implementatie niet expliciet status controles definieert, probeert Application Gateway een HTTP-GET uit te voeren op het WebSocket-server eindpunt.
Afhankelijk van de server implementatie ([Dit is een](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)van de meest leuke) WebSocket-specifieke headers zijn mogelijk vereist (`Sec-Websocket-Version` voor instantie).
Omdat Application Gateway geen WebSocket-headers toevoegt, wordt de status test van de Application Gateway van de WebSocket-server waarschijnlijk `400 Bad Request`.
Als gevolg Application Gateway wordt uw peul als slecht gemarkeerd, wat uiteindelijk resulteert in een `502 Bad Gateway` voor de consumenten van de WebSocket-server.
Als u dit wilt voor komen, moet u mogelijk een HTTP GET-handler toevoegen voor een status controle aan uw server (`/health` voor instantie, die `200 OK`retourneert).
