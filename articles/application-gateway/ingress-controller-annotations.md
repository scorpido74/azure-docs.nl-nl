---
title: Annotaties van Application Gateway Ingress Controller
description: Dit artikel bevat documentatie over de annotaties die specifiek zijn voor de Application Gateway Ingress Controller.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f54381ddcd11a2e4a24d30d812468da85b5403de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335828"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Annotaties voor Application Gateway Ingress Controller 

## <a name="introductions"></a>Introducties

De Kubernetes Ingress-bron kan worden geannoteerd met willekeurige sleutel-/waardeparen. AGIC is afhankelijk van annotaties voor het programmeren van Application Gateway-functies, die niet configureerbaar zijn via de Ingress YAML. Binnenkomende annotaties worden toegepast op alle HTTP-instelling, backendpools en listeners die zijn afgeleid van een binnenkomende bron.

## <a name="list-of-supported-annotations"></a>Lijst met ondersteunde annotaties

Om een Ingress-bron te laten waarnemen door AGIC, moet deze **worden geannoteerd** met `kubernetes.io/ingress.class: azure/application-gateway`. Alleen dan zal AGIC werken met de Ingress-bron in kwestie.

| Annotatiesleutel | Waardetype | Standaardwaarde | Toegestane waarden
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32`(seconden) | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32`(seconden) | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>Voorvoegsel backendpad

Met deze annotatie kan het backendpad dat is opgegeven in een binnenkomende bron worden herschreven met voorvoegsel dat in deze annotatie is opgegeven. Hierdoor kunnen gebruikers services blootstellen waarvan de eindpunten anders zijn dan eindpuntnamen die worden gebruikt om een service in een binnenkomende bron bloot te leggen.

### <a name="usage"></a>Gebruik

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>Voorbeeld

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-bkprefix
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```
In het bovenstaande voorbeeld hebben we een `go-server-ingress-bkprefix` binnenkomende resource `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` gedefinieerd met de naam annotatie . De annotatie vertelt toepassingsgateway om een HTTP-instelling te maken, `/hello` die `/test/`een padvoorvoegsel moet overschrijven voor het pad naar .

> [!NOTE] 
> In het bovenstaande voorbeeld hebben we slechts één regel gedefinieerd. De annotaties zijn echter van toepassing op de gehele binnenkomende bron, dus als een gebruiker meerdere regels had gedefinieerd, wordt het voorvoegpadvoorvoegsel ingesteld voor elk van de opgegeven paden. Als een gebruiker dus andere regels wil met verschillende padvoorvoegsels (zelfs voor dezelfde service), moeten ze verschillende invallende resources definiëren.

## <a name="tls-redirect"></a>TLS-omleiding

Application Gateway [kan worden geconfigureerd](https://docs.microsoft.com/azure/application-gateway/application-gateway-redirect-overview) om HTTP URL's automatisch door te verwijzen naar hun HTTPS-tegenhangers. Wanneer deze annotatie aanwezig is en TLS goed is geconfigureerd, maakt de Kubernetes Ingress-controller een [routeringsregel met een omleidingsconfiguratie](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal#add-a-routing-rule-with-a-redirection-configuration) en past deze de wijzigingen toe op uw Application Gateway. De omleiding die `301 Moved Permanently`is gemaakt, is HTTP .

### <a name="usage"></a>Gebruik

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>Voorbeeld

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-redirect
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
   - hosts:
     - www.contoso.com
     secretName: testsecret-tls
  rules:
  - host: www.contoso.com
    http:
      paths:
      - backend:
          serviceName: websocket-repeater
          servicePort: 80
```

## <a name="connection-draining"></a>Verbinding aftappen

`connection-draining`: Met deze annotatie kunnen gebruikers aangeven of ze het aftappen van verbindingen moeten inschakelen.
`connection-draining-timeout`: Met deze annotatie kunnen gebruikers een time-out opgeven waarna Application Gateway de aanvragen voor het drainerende backend-eindpunt beëindigt.

### <a name="usage"></a>Gebruik

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>Voorbeeld

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-drain
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/connection-draining: "true"
    appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="cookie-based-affinity"></a>Cookie gebaseerde affiniteit

Deze annotatie maakt het mogelijk om aan te geven of cookie gebaseerde affiniteit in te schakelen.

### <a name="usage"></a>Gebruik

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>Voorbeeld

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-affinity
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="request-timeout"></a>Time-out van aanvraag

Met deze annotatie u de time-out van het verzoek in enkele seconden opgeven waarna Application Gateway de aanvraag niet in de weg staat als het antwoord niet wordt ontvangen.

### <a name="usage"></a>Gebruik

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
```

### <a name="example"></a>Voorbeeld

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/request-timeout: "20"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="use-private-ip"></a>Privé-IP gebruiken

Deze annotatie stelt ons in staat om aan te geven of dit eindpunt op Private IP van Application Gateway moet worden blootgesteld.

> [!NOTE]
> * Application Gateway ondersteunt niet meerdere IP's op dezelfde poort (bijvoorbeeld: 80/443). Ingress met annotatie `appgw.ingress.kubernetes.io/use-private-ip: "false"` `appgw.ingress.kubernetes.io/use-private-ip: "true"` en `HTTP` een andere met op zal leiden tot AGIC te mislukken bij het bijwerken van de Application Gateway.
> * Voor Application Gateway die geen privé-IP heeft, `appgw.ingress.kubernetes.io/use-private-ip: "true"` wordt Ingresses met genegeerd. Dit zal worden weerspiegeld in de controller logs en `NoPrivateIP` ingress gebeurtenissen voor degenen die binnendringen met waarschuwing.


### <a name="usage"></a>Gebruik
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

### <a name="example"></a>Voorbeeld
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/use-private-ip: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="backend-protocol"></a>Backend-protocol

Deze annotatie stelt ons in staat om het protocol te specificeren dat Application Gateway moet gebruiken tijdens het praten met de Pods. Ondersteunde protocollen: `http`,`https`

> [!NOTE]
> * Hoewel zelfondertekende certificaten worden ondersteund op Application Gateway, `https` ondersteunen AGIC momenteel alleen wanneer Pods certificaat gebruiken dat is ondertekend door een bekende CA.
> * Zorg ervoor dat u poort 80 niet gebruikt met HTTPS en poort 443 met HTTP op de Pods.

### <a name="usage"></a>Gebruik
```yaml
appgw.ingress.kubernetes.io/backend-protocol: "https"
```

### <a name="example"></a>Voorbeeld
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-protocol: "https"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 443
```