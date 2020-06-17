---
title: 'Zelfstudie: Een aangepaste installatiekopie maken en uitvoeren'
description: Ontdek hoe u een aangepaste Linux-installatiekopie maakt die kan worden uitgevoerd in Azure App Service, de installatiekopie implementeert in Azure-containerregisters en deze uitvoert in App Service.
keywords: azure-app-service, web-app, linux, docker, container
author: msangapu-msft
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc, seodec18, tracking-python
ms.openlocfilehash: d9c7b9b296aaf287d185cd3e7544e40d9cdef2f5
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561111"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>Zelfstudie: Een aangepaste installatiekopie maken en uitvoeren in App Service vanuit een privéregister

[App Service](app-service-linux-intro.md) biedt ingebouwde Docker-installatiekopieën in Linux met ondersteuning voor specifieke versies, zoals PHP 7.3 en Node.js 10.14. App Service gebruikt de Docker-containertechnologie voor het hosten van zowel ingebouwde installatiekopieën als aangepaste installatiekopieën als een platform as a service. In deze zelfstudie leert u hoe u een aangepaste installatiekopie maakt en deze uitvoert in App Service. Dit patroon is handig als de taal van keuze niet is inbegrepen in de ingebouwde installatiekopieën, of wanneer uw toepassing een specifieke configuratie vereist die niet door de ingebouwde installatiekopieën wordt verschaft.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een aangepaste installatiekopie in een privécontainerregister implementeren
> * De aangepaste installatiekopie uitvoeren in App Service
> * Omgevingsvariabelen configureren
> * De installatiekopie bijwerken en opnieuw implementeren
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

Bekijk in de Git-opslagplaats het _Docker-bestand_. Dit bestand beschrijft de Python-omgeving die vereist is om uw toepassing uit te voeren. De installatiekopie stelt bovendien een [SSH](https://www.ssh.com/ssh/protocol/)-server in voor veilige communicatie tussen de container en de host. Met de laatste regel in het _Docker-bestand_, `ENTRYPOINT ["init.sh"]`, wordt `init.sh` aangeroepen om de SSH-service en Python-server te starten.

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

Maak de Docker-installatiekopie met de opdracht `docker build`.

```bash
docker build --tag mydockerimage .
```

Test of de build werkt door de Docker-container uit te voeren. Geef de opdracht [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) en geef de naam en het label van de afbeelding door. Zorg ervoor dat u de poort specificeert met behulp van het argument `-p`.

```bash
docker run -p 8000:8000 mydockerimage
```

Controleer of de web-app en de container goed werken door naar `http://localhost:8000` te bladeren.

![Web-app lokaal testen](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>App in Azure implementeren

Als u een app wilt maken die gebruikmaakt van de installatiekopie die u zojuist hebt gemaakt, voert u Azure CLI-opdrachten uit waarmee een resourcegroep wordt gemaakt, de installatiekopie wordt gepusht en vervolgens de web-app voor het App Service-plan wordt gemaakt om deze uit te voeren.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>Een Azure Container Registry maken

Gebruik in de Cloud Shell de opdracht [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) voor het maken van een Azure Container Registry.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>Aanmelden bij Azure Container Registry

Als u een installatiekopie naar het register wilt pushen, moet u zich verifiëren bij het privéregister. Gebruik de opdracht [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) in Cloud Shell om de referenties op te halen uit het register dat u hebt gemaakt.

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

In de uitvoer worden twee wachtwoorden en de gebruikersnaam vermeld.

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

Meld u vanuit uw lokale terminalvenster aan bij Azure Container Registry met behulp van de opdracht `docker login`, zoals wordt weergegeven in het volgende voorbeeld. Vervang *\<azure-container-registry-name>* en *\<registry-username>* door waarden voor uw register. Wanneer u hierom wordt gevraagd, voert u een van de wachtwoorden uit de vorige stap in.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Controleer of de aanmelding is gelukt.

### <a name="push-image-to-azure-container-registry"></a>Installatiekopie pushen naar Azure Container Registry

Tag uw lokale installatiekopie voor Azure Container Registry. Bijvoorbeeld:
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Push de installatiekopie met behulp van de opdracht `docker push`. Tag de installatiekopie met de naam van het register, gevolgd door de naam van de installatiekopie en de tag.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Controleer in Cloud Shell of de push is geslaagd.

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

U zou de volgende uitvoer moeten zien.

<pre>
[
  "mydockerimage"
]
</pre>

### <a name="create-app-service-plan"></a>Een App Service-plan maken

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>Een web-app maken

Maak in de Cloud Shell een [web-app](app-service-linux-intro.md) in het `myAppServicePlan`App Service-plan met de opdracht [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Vervang _\<app-name>_ door een unieke app-naam en _\<azure-container-registry-name>_ door de naam van uw register.

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

### <a name="configure-registry-credentials-in-web-app"></a>Registerreferenties configureren in de web-app

App Service heeft informatie over uw register en installatiekopie nodig om de privé-installatiekopie op te halen. Geef deze in Cloud Shell op met de opdracht [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Vervang *\<app-name>* , *\<azure-container-registry-name>* , _\<registry-username>_ en _\<password>_ .

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> Wanneer u een ander register dan Docker Hub gebruikt, moet `--docker-registry-server-url` worden opgemaakt als `https://`, gevolgd door de Fully Qualified Domain Name van het register.
>

### <a name="configure-environment-variables"></a>Omgevingsvariabelen configureren

De meeste Docker-installatiekopieën gebruiken aangepaste omgevingsvariabelen, zoals een andere poort dan poort 80. U geeft met de app-instelling `WEBSITES_PORT` aan App Service door welke poort wordt gebruikt door uw installatiekopie. De GitHub-pagina voor het [Python-voorbeeld in deze zelfstudie](https://github.com/Azure-Samples/docker-django-webapp-linux) laat zien dat u `WEBSITES_PORT` in moet stellen op _8000_.

Gebruik de opdracht [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in Cloud Shell om de app-instellingen in te stellen. App-instellingen zijn hoofdlettergevoelig en door spaties gescheiden.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>De web-app testen

Controleer of de web-app werkt door erheen te bladeren (`http://<app-name>.azurewebsites.net`).

> [!NOTE]
> De eerste keer dat u de app opent, kan het enige tijd duren omdat App Service de hele installatiekopie moet ophalen. Als er een time-out optreedt voor de browser, vernieuwt u gewoon de pagina.

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

Nadat u het Python-bestand hebt gewijzigd en dit hebt opgeslagen, moet u de nieuwe Docker-installatiekopie opnieuw opbouwen en pushen. Start de web-app opnieuw om de wijzigingen door te voeren. Gebruik dezelfde opdrachten die u eerder in deze zelfstudie hebt gebruikt. U kunt de volgende artikelen raadplegen: [De installatiekopie maken met het Docker-bestand](#build-the-image-from-the-docker-file) en [De installatiekopie naar Azure Container Registry pushen](#push-image-to-azure-container-registry). De web-app testen door de instructies te volgen in [De web-app testen](#test-the-web-app).

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>SSH-verbindingen inschakelen

SSH maakt veilige communicatie tussen een container en een client mogelijk. Als u een SSH-verbinding met uw container wilt inschakelen, moet uw aangepaste installatiekopie hiervoor worden geconfigureerd. Laten we eens naar de voorbeeldopslagplaats kijken die al over de benodigde configuratie beschikt.

* In het [Docker-bestand](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile) wordt met de volgende code de SSH-server geïnstalleerd en worden ook de aanmeldingsreferenties ingesteld.

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Deze configuratie staat geen externe verbindingen naar de container toe. SSH is alleen beschikbaar via de Kudu/SCM-Site. De Kudu/SCM-site wordt geverifieerd met uw Azure-account.

* Het [Docker-bestand](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) kopieert het bestand [sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config) in de opslagplaats naar de map */etc/ssh/* .

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* In het [Docker-bestand](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) wordt bepaald dat aan poort 2222 in de container wordt geluisterd. Het is een interne poort die alleen toegankelijk is voor containers in het brugnetwerk van een particulier virtueel netwerk. 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* De SSH-server wordt gestart door het [invoerscript](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5).

    ```bash
    #!/bin/bash
    service ssh start
    ```

### <a name="open-ssh-connection-to-container"></a>SSH-verbinding met de container openen

De SSH-verbinding is alleen beschikbaar via de Kudu-site, die u kunt vinden op `https://<app-name>.scm.azurewebsites.net`.

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
> * Een aangepaste installatiekopie in een privécontainerregister implementeren
> * De aangepaste installatiekopie uitvoeren in App Service
> * Omgevingsvariabelen configureren
> * De installatiekopie bijwerken en opnieuw implementeren
> * Toegang tot diagnostische logboeken
> * Verbinding maken met de container via SSH

Ga door naar de volgende zelfstudie om te leren hoe u een aangepaste DNS-naam aan uw app kunt toewijzen.

> [!div class="nextstepaction"]
> [Zelfstudie: Een aangepaste DNS-naam aan uw app toewijzen](../app-service-web-tutorial-custom-domain.md)

U kunt ook andere resources bekijken:

> [!div class="nextstepaction"]
> [Een aangepaste container configureren](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Zelfstudie: WordPress-app met meerdere containers](tutorial-multi-container-app.md)
