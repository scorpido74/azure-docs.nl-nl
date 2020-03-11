---
title: Aantekeningen in de controller van Application Gateway
description: Dit artikel bevat documentatie over de aantekeningen die specifiek zijn voor de Application Gateway ingangs controller.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a3583a5efd120733ce7f6b71a7594b5636593f99
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373484"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Aantekeningen voor de Application Gateway ingangs controller 

## <a name="introductions"></a>Inleidingen

De Kubernetes ingress-resource kan worden voorzien van aantekeningen met wille keurige sleutel/waarde-paren. AGIC maakt gebruik van annotaties voor Program Application Gateway-functies, die niet kunnen worden geconfigureerd via de ingangs YAML. Ingangs aantekeningen worden toegepast op alle HTTP-instellingen, back-endservers en listeners die zijn afgeleid van een ingangs bron.

## <a name="list-of-supported-annotations"></a>Lijst met ondersteunde aantekeningen

Voor een ingangs resource die wordt waargenomen door AGIC, **moet deze zijn voorzien van aantekeningen** bij `kubernetes.io/ingress.class: azure/application-gateway`. AGIC werkt alleen met de ingangs bron in kwestie.

| Aantekening sleutel | Waardetype | Standaardwaarde | Toegestane waarden
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#ssl-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32` (seconden) | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32` (seconden) | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>Voor voegsel van back-uppad

Met deze aantekening kan het backend-pad dat is opgegeven in een ingangs bron, worden herschreven met het voor voegsel dat is opgegeven in deze aantekening. Hiermee kunnen gebruikers services weer geven waarvan de eind punten afwijken van de namen van eind punten die worden gebruikt om een service in een ingangs bron beschikbaar te maken.

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
In het bovenstaande voor beeld hebben we een ingangs resource gedefinieerd met de naam `go-server-ingress-bkprefix` met een aantekening `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"`. De aantekening vertelt toepassings gateway om een HTTP-instelling te maken, die een overschrijving voor het pad voor het pad `/hello` naar `/test/`heeft.

> [!NOTE] 
> In het bovenstaande voor beeld is slechts één regel gedefinieerd. De aantekeningen zijn echter wel van toepassing op de volledige ingangs bron, dus als een gebruiker meerdere regels heeft gedefinieerd, zou het voor voegsel van de back-end voor elk van de opgegeven paden worden ingesteld. Dus als een gebruiker andere regels met verschillende paden voor voegsels wil (zelfs voor dezelfde service), moeten ze verschillende bronnen voor binnenkomend verkeer definiëren.

## <a name="ssl-redirect"></a>SSL-omleiding

Application Gateway [kunnen worden geconfigureerd](https://docs.microsoft.com/azure/application-gateway/application-gateway-redirect-overview) om http-url's automatisch om te leiden naar hun HTTPS-equivalenten. Als deze aantekening aanwezig is en TLS op de juiste wijze is geconfigureerd, maakt Kubernetes ingangs controller een [routerings regel met een omleidings configuratie](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal#add-a-routing-rule-with-a-redirection-configuration) en worden de wijzigingen toegepast op uw Application Gateway. De omleiding die u hebt gemaakt, wordt HTTP `301 Moved Permanently`.

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

## <a name="connection-draining"></a>Verbinding verbreken

`connection-draining`: met deze aantekening kunnen gebruikers opgeven of het verbreken van de verbinding moet worden ingeschakeld.
`connection-draining-timeout`: met deze aantekening kunnen gebruikers een time-out opgeven Application Gateway waarna de aanvragen worden beëindigd naar het eind punt voor de back-end.

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

## <a name="cookie-based-affinity"></a>Affiniteit op basis van cookies

Met deze aantekening kunt u opgeven of de affiniteit op basis van cookies moet worden ingeschakeld.

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

Met deze aantekening kan de time-out van de aanvraag in seconden worden opgegeven, waarna Application Gateway de aanvraag mislukt als er geen antwoord wordt ontvangen.

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

Met deze aantekening kan ons opgeven of dit eind punt op privé-IP van Application Gateway wordt weer gegeven.

> [!NOTE]
> * Application Gateway biedt geen ondersteuning voor meerdere IP-adressen op dezelfde poort (bijvoorbeeld: 80/443). Inkomend verkeer met aantekeningen `appgw.ingress.kubernetes.io/use-private-ip: "false"` en een andere met `appgw.ingress.kubernetes.io/use-private-ip: "true"` op `HTTP` zorgt ervoor dat AGIC het bijwerken van de Application Gateway mislukt.
> * Voor Application Gateway dat geen persoonlijk IP-adres heeft, wordt Ingresses met `appgw.ingress.kubernetes.io/use-private-ip: "true"` genegeerd. Dit wordt weer gegeven in de controller logboeken en ingangs gebeurtenissen voor deze ingresses met `NoPrivateIP` waarschuwing.


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

## <a name="backend-protocol"></a>Back-end-protocol

Met deze aantekening kunnen we het protocol opgeven dat Application Gateway moet gebruiken terwijl u met het Peule praat. Ondersteunde protocollen: `http`, `https`

> [!NOTE]
> * Hoewel zelfondertekende certificaten op Application Gateway worden ondersteund, ondersteunt AGIC alleen `https` wanneer het een certificaat gebruikt dat is ondertekend door een bekende certificerings instantie.
> * Zorg ervoor dat u geen gebruik maakt van poort 80 met HTTPS en poort 443 met HTTP op het gehele bereik.

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