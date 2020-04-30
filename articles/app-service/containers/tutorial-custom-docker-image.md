---
title: 'Zelf studie: een aangepaste installatie kopie maken en uitvoeren'
description: Meer informatie over het bouwen van een aangepaste Linux-installatie kopie die kan worden uitgevoerd op Azure App Service, deze implementeren in azure-container registers en deze uitvoeren in App Service.
keywords: azure-app-service, web-app, linux, docker, container
author: msangapu-msft
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 2609ff908b3c2f872cb63d3dcd7dcd481d316484
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82085855"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>Zelf studie: een aangepaste installatie kopie maken en uitvoeren in App Service vanuit een persoonlijk REGI ster

[App service](app-service-linux-intro.md) biedt ingebouwde docker-installatie kopieën op Linux met ondersteuning voor specifieke versies, zoals PHP 7,3 en node. js 10,14. App Service maakt gebruik van de docker-container technologie voor het hosten van zowel ingebouwde installatie kopieën als aangepaste installatie kopieën als een platform als een service. In deze zelf studie leert u hoe u een aangepaste installatie kopie maakt en deze uitvoert in App Service. Dit patroon is handig als de taal van keuze niet is inbegrepen in de ingebouwde installatiekopieën, of wanneer uw toepassing een specifieke configuratie vereist die niet door de ingebouwde installatiekopieën wordt verschaft.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een aangepaste installatie kopie implementeren in een persoonlijk container register
> * De aangepaste installatie kopie uitvoeren in App Service
> * Omgevingsvariabelen configureren
> * De installatie kopie bijwerken en opnieuw implementeren
> * Toegang tot diagnostische logboeken
> * Verbinding maken met de container via SSH

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* [Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)

## <a name="download-the-sample"></a>Het voorbeeld downloaden

Voer de volgende opdracht uit in een terminalvenster. Hiermee wordt de voorbeeld-app-opslagplaats gekloond naar uw lokale computer en navigeert u naar de map met de voorbeeldcode.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>De installatiekopie maken van het Docker-bestand

Bekijk in de Git-opslagplaats het _Docker-bestand_. Dit bestand beschrijft de Python-omgeving die vereist is om uw toepassing uit te voeren. De installatiekopie stelt bovendien een [SSH](https://www.ssh.com/ssh/protocol/)-server in voor veilige communicatie tussen de container en de host. De laatste regel in het _Dockerfile_, `ENTRYPOINT ["init.sh"]`, roept `init.sh` om de SSH-service en python-server te starten.

```Dockerfile
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222

#service SSH start
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

Bouw de docker-installatie kopie `docker build` met de opdracht.

```bash
docker build --tag mydockerimage .
```

Test of de build werkt door de Docker-container uit te voeren. Geef de [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) opdracht op en geef de naam en het label van de installatie kopie door. Zorg ervoor dat u de poort specificeert met behulp van het argument `-p`.

```bash
docker run -p 8000:8000 mydockerimage
```

Controleer of de web-app en de container goed werken door naar `http://localhost:8000` te bladeren.

![Web-app lokaal testen](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>App in Azure implementeren

Als u een app wilt maken die gebruikmaakt van de installatie kopie die u zojuist hebt gemaakt, voert u Azure CLI-opdrachten uit waarmee een resource groep wordt gemaakt, wordt de installatie kopie gepusht en wordt vervolgens de web-app voor het App Service plan gemaakt om deze uit te voeren.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>Een Azure Container Registry maken

Gebruik in het Cloud Shell de [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) opdracht om een Azure container Registry te maken.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>Aanmelden bij Azure Container Registry

Als u een installatie kopie naar het REGI ster wilt pushen, moet u zich verifiëren met het persoonlijke REGI ster. Gebruik in de Cloud Shell de [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) opdracht om de referenties op te halen uit het REGI ster dat u hebt gemaakt.

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

De uitvoer toont twee wacht woorden samen met de gebruikers naam.

<pre>
{
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "&lt;registry-username&gt;"
}
</pre>

Meld u vanuit uw lokale terminal venster aan bij de Azure Container Registry met behulp van de `docker login` opdracht, zoals wordt weer gegeven in het volgende voor beeld. Vervang * \<Azure-container-Registry-name>* en * \<REGI ster-gebruikers naam>* door waarden voor het REGI ster. Wanneer u hierom wordt gevraagd, typt u een van de wacht woorden uit de vorige stap.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Bevestig dat de aanmelding is geslaagd.

### <a name="push-image-to-azure-container-registry"></a>Installatiekopie pushen naar Azure Container Registry

Label uw lokale installatie kopie voor de Azure Container Registry. Bijvoorbeeld:
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Push de installatiekopie met behulp van de opdracht `docker push`. Tag de installatiekopie met de naam van het register, gevolgd door de naam van de installatiekopie en de tag.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Ga terug naar de Cloud Shell en controleer of de push is geslaagd.

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

U moet de volgende uitvoer ophalen.

<pre>
[
  "mydockerimage"
]
</pre>

### <a name="create-app-service-plan"></a>Een App Service-plan maken

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>Een web-app maken

Maak in de Cloud Shell een [web-app](app-service-linux-intro.md) in het `myAppServicePlan`App Service-plan met de opdracht [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Vervang _ \<app-name>_ door een unieke app-naam en _ \<Azure-container-Registry-name>_ met de register naam.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-container-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Wanneer de web-app is gemaakt, toont de Azure CLI soortgelijke uitvoer als in het volgende voorbeeld:

<pre>
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="configure-registry-credentials-in-web-app"></a>Register referenties configureren in de web-app

Als App Service de persoonlijke installatie kopie wilt ophalen, heeft deze informatie nodig over het REGI ster en de installatie kopie. Geef de [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) opdracht op In de Cloud shell. Vervang * \<app-name>*, * \<Azure-container-Registry-name>*, _ \<REGI ster-gebruikers naam>_ en _ \<wacht woord>_.

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> Wanneer u een ander REGI ster dan docker hub `--docker-registry-server-url` gebruikt, moet dit `https://` worden geformatteerd als gevolgd door de Fully Qualified Domain name van het REGI ster.
>

### <a name="configure-environment-variables"></a>Omgevingsvariabelen configureren

De meeste docker-installatie kopieën gebruiken aangepaste omgevings variabelen, zoals een andere poort dan 80. U vertelt App Service over de poort die uw installatie kopie gebruikt met behulp van de `WEBSITES_PORT` app-instelling. De GitHub-pagina voor het [Python-voorbeeld in deze zelfstudie](https://github.com/Azure-Samples/docker-django-webapp-linux) laat zien dat u `WEBSITES_PORT` in moet stellen op _8000_.

Als u de app-instellingen wilt [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) instellen, gebruikt u de opdracht in de Cloud shell. App-instellingen zijn hoofdlettergevoelig en door spaties gescheiden.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>De web-app testen

Controleer of de web-app werkt door erheen te bladeren (`http://<app-name>.azurewebsites.net`).

> [!NOTE]
> De eerste keer dat u de app opent, kan het enige tijd duren omdat App Service de hele afbeelding moet ophalen. Als er een time-out optreedt voor de browser, vernieuwt u gewoon de pagina.

![Poortconfiguratie voor web-app testen](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>De web-app bijwerken en opnieuw implementeren

Open in uw lokale Git-opslagplaats app/templates/app/index.html. Zoek naar het eerste HTML-element en wijzig het in.

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
      </div>
    </div>
  </nav>
```

Nadat u het Python-bestand hebt gewijzigd en dit hebt opgeslagen, moet u de nieuwe Docker-installatiekopie opnieuw opbouwen en pushen. Start de web-app opnieuw om de wijzigingen door te voeren. Gebruik dezelfde opdrachten die u eerder in deze zelfstudie hebt gebruikt. U kunt [de installatie kopie bouwen vanuit het docker-bestand](#build-the-image-from-the-docker-file) en de [push-installatie kopie naar Azure container Registry](#push-image-to-azure-container-registry). De web-app testen door de instructies te volgen in [De web-app testen](#test-the-web-app).

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>SSH-verbindingen inschakelen

SSH maakt veilige communicatie tussen een container en een client mogelijk. Als u SSH-verbinding met uw container wilt inschakelen, moet uw aangepaste installatie kopie hiervoor worden geconfigureerd. Laten we eens kijken naar de voorbeeld opslagplaats die al de benodigde configuratie heeft.

* In de [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile)wordt de SSH-server geïnstalleerd met de volgende code en worden ook de aanmeldings referenties ingesteld.

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Deze configuratie staat geen externe verbindingen naar de container toe. SSH is alleen beschikbaar via de Kudu/SCM-Site. De kudu/SCM-site wordt geverifieerd met uw Azure-account.

* De [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) kopieert het [sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config) -bestand in de opslag plaats naar de */etc/ssh/* -map.

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* De [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) geeft poort 2222 weer in de container. Het is een interne poort die alleen toegankelijk is voor containers in het brugnetwerk van een particulier virtueel netwerk. 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* Met het script voor de [invoer](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5) wordt de SSH-server gestart.

    ```bash
    #!/bin/bash
    service ssh start
    ```

### <a name="open-ssh-connection-to-container"></a>SSH-verbinding met de container openen

SSH-verbinding is alleen beschikbaar via de kudu-site, die toegankelijk `https://<app-name>.scm.azurewebsites.net`is op.

Als u wilt verbinden, bladert u naar `https://<app-name>.scm.azurewebsites.net/webssh/host` en meldt u zich aan met uw Azure-account.

U wordt vervolgens omgeleid naar een pagina die een interactieve console weergeeft.

U wilt controleren of bepaalde toepassingen worden uitgevoerd in de container. Om de container te controleren en de actieve processen te verifiëren, geeft u de opdracht `top` bij de opdrachtprompt.

```bash
top
```

De opdracht `top` toont alle actieve processen in een container.

```
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

Gefeliciteerd! U hebt een aangepaste Linux-container geconfigureerd in App Service.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

Wat u hebt geleerd:

> [!div class="checklist"]
> * Een aangepaste installatie kopie implementeren in een persoonlijk container register
> * De aangepaste installatie kopie uitvoeren in App Service
> * Omgevingsvariabelen configureren
> * De installatie kopie bijwerken en opnieuw implementeren
> * Toegang tot diagnostische logboeken
> * Verbinding maken met de container via SSH

Ga door naar de volgende zelfstudie om te leren hoe u een aangepaste DNS-naam aan uw app kunt toewijzen.

> [!div class="nextstepaction"]
> [Zelf studie: aangepaste DNS-naam toewijzen aan uw app](../app-service-web-tutorial-custom-domain.md)

U kunt ook andere resources bekijken:

> [!div class="nextstepaction"]
> [Een aangepaste container configureren](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Zelf studie: WordPress-app met meerdere containers](tutorial-multi-container-app.md)
