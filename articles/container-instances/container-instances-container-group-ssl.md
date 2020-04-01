---
title: SSL inschakelen met zijspancontainer
description: Een SSL- of TLS-eindpunt maken voor een containergroep die wordt uitgevoerd in Azure Container Instances door Nginx in een zijspancontainer uit te voeren
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 43b39c7c13d6d5e52aae2ce1706e4880ab27d225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294946"
---
# <a name="enable-an-ssl-endpoint-in-a-sidecar-container"></a>Een SSL-eindpunt inschakelen in een zijspancontainer

In dit artikel ziet u hoe u een [containergroep maakt](container-instances-container-groups.md) met een toepassingscontainer en een zijspancontainer waarop een SSL-provider wordt uitgevoerd. Door een containergroep met een afzonderlijk SSL-eindpunt in te stellen, schakelt u SSL-verbindingen voor uw toepassing in zonder uw toepassingscode te wijzigen.

U stelt een voorbeeldcontainergroep in bestaande uit twee containers:
* Een toepassingscontainer die een eenvoudige web-app uitvoert met behulp van de openbare Microsoft [aci-helloworld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) afbeelding. 
* Een zijspancontainer met de openbare [Nginx-afbeelding,](https://hub.docker.com/_/nginx) geconfigureerd om SSL te gebruiken. 

In dit voorbeeld stelt de containergroep poort 443 voor Nginx alleen bloot met zijn openbare IP-adres. Nginx stuurt HTTPS-verzoeken naar de bijbehorende web-app, die intern luistert op poort 80. U het voorbeeld aanpassen voor container-apps die op andere poorten luisteren. 

Zie [Volgende stappen](#next-steps) voor andere benaderingen voor het inschakelen van SSL in een containergroep.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

U de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om dit artikel te voltooien. Als u het lokaal wilt gebruiken, wordt versie 2.0.55 of hoger aanbevolen. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

Om Nginx in te stellen als SSL-provider, hebt u een SSL-certificaat nodig. In dit artikel ziet u hoe u een zelfondertekend SSL-certificaat maakt en instelt. Voor productiescenario's moet u een certificaat verkrijgen bij een certificaatautoriteit.

Als u een zelfondertekend SSL-certificaat wilt maken, gebruikt u de [OpenSSL-tool](https://www.openssl.org/) die beschikbaar is in Azure Cloud Shell en veel Linux-distributies, of gebruikt u een vergelijkbare clienttool in uw besturingssysteem.

Maak eerst een certificaataanvraag (.csr-bestand) in een lokale werkmap:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Volg de aanwijzingen om de identificatie-informatie toe te voegen. Voer voor Algemene naam de hostnaam in die aan het certificaat is gekoppeld. Wanneer u om een wachtwoord wordt gevraagd, drukt u op Enter zonder te typen om het toevoegen van een wachtwoord over te slaan.

Voer de volgende opdracht uit om het zelfondertekende certificaat (.crt-bestand) uit de certificaataanvraag te maken. Bijvoorbeeld:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

U ziet nu drie bestanden in de`ssl.csr`map: de`ssl.key`certificaataanvraag ( ), de`ssl.crt`privésleutel ( ) en het zelfondertekende certificaat ( ). U `ssl.key` gebruikt `ssl.crt` en in latere stappen.

## <a name="configure-nginx-to-use-ssl"></a>Nginx configureren om SSL te gebruiken

### <a name="create-nginx-configuration-file"></a>Nginx-configuratiebestand maken

In deze sectie maakt u een configuratiebestand voor Nginx om SSL te gebruiken. Begin met het kopiëren van de `nginx.conf`volgende tekst naar een nieuw bestand met de naam . In Azure Cloud Shell u Visual Studio Code gebruiken om het bestand in uw werkmap te maken:

```console
code nginx.conf
```

Zorg `location`ervoor dat `proxy_pass` u de juiste poort voor uw app instelt. In dit voorbeeld stellen we poort `aci-helloworld` 80 in voor de container.

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Base64-coderen geheimen en configuratiebestand

Base64-codeer het Nginx-configuratiebestand, het SSL-certificaat en de SSL-sleutel. In de volgende sectie voert u de gecodeerde inhoud in in een YAML-bestand dat wordt gebruikt om de containergroep te implementeren.

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Containergroep implementeren

Implementeer nu de containergroep door de containerconfiguraties in een [YAML-bestand](container-instances-multi-container-yaml.md)op te geven.

### <a name="create-yaml-file"></a>YAML-bestand maken

Kopieer de volgende YAML naar `deploy-aci.yaml`een nieuw bestand met de naam . In Azure Cloud Shell u Visual Studio Code gebruiken om het bestand in uw werkmap te maken:

```console
code deploy-aci.yaml
```

Voer de inhoud van de base64-gecodeerde `secret`bestanden in indien aangegeven onder . `cat` Elk van de bestanden met basis64 om de inhoud ervan te zien. Tijdens de implementatie worden deze bestanden toegevoegd aan een [geheim volume](container-instances-volume-secret.md) in de containergroep. In dit voorbeeld wordt het geheime volume gemonteerd op de Nginx-container.

```YAML
api-version: 2018-10-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>De containergroep implementeren

Maak een resourcegroep met de opdracht [AZ-groep maken:](/cli/azure/group#az-group-create)

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Implementeer de containergroep met de opdracht [AZ-container maken](/cli/azure/container#az-container-create) en passeer het YAML-bestand als argument.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Implementatiestatus weergeven

Als u de status van de implementatie wilt weergeven, gebruikt u de volgende opdracht [az-containershow:](/cli/azure/container#az-container-show)

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

Voor een succesvolle implementatie is de uitvoer vergelijkbaar met de volgende:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   nginx, mcr.microsoft.com/azuredocs/aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>SSL-verbinding verifiëren

Gebruik uw browser om naar het openbare IP-adres van de containergroep te navigeren. Het IP-adres in `52.157.22.76`dit voorbeeld is, dus de URL is **https://52.157.22.76**. U moet HTTPS gebruiken om de lopende toepassing te bekijken, vanwege de Nginx-serverconfiguratie. Pogingen om verbinding te maken via HTTP mislukken.

![Schermafbeelding van browser met toepassing die wordt uitgevoerd in een exemplaar van een Azure-container](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Omdat in dit voorbeeld een zelfondertekend certificaat wordt gebruikt en niet een certificaat van een certificaatautoriteit, wordt in de browser een beveiligingswaarschuwing weergegeven wanneer u verbinding maakt met de site via HTTPS. Mogelijk moet u de waarschuwing accepteren of de browser- of certificaatinstellingen aanpassen om door te gaan naar de pagina. Dit gedrag is verwacht.

>

## <a name="next-steps"></a>Volgende stappen

In dit artikel ziet u hoe u een Nginx-container instelt om SSL-verbindingen in te schakelen met een web-app die wordt uitgevoerd in de containergroep. U dit voorbeeld aanpassen voor apps die luisteren op andere poorten dan poort 80. U het Nginx-configuratiebestand ook bijwerken om serververbindingen op poort 80 (HTTP) automatisch om te leiden om HTTPS te gebruiken.

Terwijl dit artikel Nginx in de zijspan gebruikt, u een andere SSL-provider gebruiken, zoals [Caddy.](https://caddyserver.com/)

Als u uw containergroep implementeert in een [virtueel Azure-netwerk,](container-instances-vnet.md)u andere opties overwegen om een SSL-eindpunt in te schakelen voor een backendcontainerinstantie, waaronder:

* [Azure-functies proxy's](../azure-functions/functions-proxies.md)
* [Azure API Management](../api-management/api-management-key-concepts.md)
* [Azure Application Gateway](../application-gateway/overview.md) - zie een [voorbeeldimplementatiesjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet).
