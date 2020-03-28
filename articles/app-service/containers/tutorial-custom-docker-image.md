---
title: 'Zelfstudie: Een aangepaste afbeelding maken en uitvoeren'
description: Meer informatie over het maken van een aangepaste Linux-afbeelding die kan worden uitgevoerd op Azure App Service, deze implementeren in Azure Container Registries en deze uitvoeren in App Service.
keywords: azure-app-service, web-app, linux, docker, container
author: msangapu-msft
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: a6c9eb354bce09a5f652895f4af34df1f6750bec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80045745"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>Zelfstudie: Een aangepaste afbeelding maken en uitvoeren in App-service vanuit een privéregister

[App Service](app-service-linux-intro.md) biedt ingebouwde Docker-afbeeldingen op Linux met ondersteuning voor specifieke versies, zoals PHP 7.3 en Node.js 10.14. App Service maakt gebruik van de Docker container technologie om zowel ingebouwde afbeeldingen als aangepaste afbeeldingen te hosten als een platform als een service. In deze zelfstudie leert u hoe u een aangepaste afbeelding maken en deze uitvoeren in App-service. Dit patroon is handig als de taal van keuze niet is inbegrepen in de ingebouwde installatiekopieën, of wanneer uw toepassing een specifieke configuratie vereist die niet door de ingebouwde installatiekopieën wordt verschaft.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een aangepaste afbeelding implementeren in een privécontainerregister
> * De aangepaste afbeelding uitvoeren in App-service
> * Omgevingsvariabelen configureren
> * De afbeelding bijwerken en opnieuw implementeren
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

Bekijk in de Git-opslagplaats het _Docker-bestand_. Dit bestand beschrijft de Python-omgeving die vereist is om uw toepassing uit te voeren. De installatiekopie stelt bovendien een [SSH](https://www.ssh.com/ssh/protocol/)-server in voor veilige communicatie tussen de container en de host. De laatste regel in het _Dockerfile_, `ENTRYPOINT ["init.sh"]`roept `init.sh` op om de SSH-service en Python-server te starten.

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

Bouw de Docker-afbeelding met de `docker build` opdracht.

```bash
docker build --tag mydockerimage .
```

Test of de build werkt door de Docker-container uit te voeren. Geef [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) de opdracht uit en geef de naam en tag van de afbeelding aan de opdracht door. Zorg ervoor dat u de poort specificeert met behulp van het argument `-p`.

```bash
docker run -p 8000:8000 mydockerimage
```

Controleer of de web-app en de container goed werken door naar `http://localhost:8000` te bladeren.

![Web-app lokaal testen](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>App in Azure implementeren

To create an app that uses the image you just created, you run Azure CLI commands that create a resource group, pushes the image, and then creates the App Service plan web app to run it.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>Een Azure Container Registry maken

Gebruik de [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) opdracht om in de Cloud Shell een Azure Container Registry te maken.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>Aanmelden bij Azure Container Registry

Als u een afbeelding naar het register wilt pushen, moet u verifiëren bij het privéregister. Gebruik de opdracht om [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) in de Cloud Shell de referenties op te halen uit het register dat u hebt gemaakt.

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

De uitvoer onthult twee wachtwoorden samen met de gebruikersnaam.

```json
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
  "username": "<registry-username>"
}
```

Meld u vanuit uw lokale terminalvenster aan `docker login` bij het Azure Container Registry met de opdracht, zoals in het volgende voorbeeld wordt weergegeven. Vervang * \<de naam azure-container-registry>* en * \<registergebruikersnaam>* door waarden voor uw register. Wanneer u daarom wordt gevraagd, typt u een van de wachtwoorden uit de vorige stap in.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Controleer of de aanmelding slaagt.

### <a name="push-image-to-azure-container-registry"></a>Installatiekopie pushen naar Azure Container Registry

Tag uw lokale afbeelding voor het Azure Container Registry. Bijvoorbeeld:
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Push de installatiekopie met behulp van de opdracht `docker push`. Tag de installatiekopie met de naam van het register, gevolgd door de naam van de installatiekopie en de tag.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Terug in de Cloud Shell, controleren of de push succesvol is.

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

U moet de volgende uitvoer krijgen.

```json
[
  "mydockerimage"
]
```

### <a name="create-app-service-plan"></a>Een App Service-plan maken

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>Een web-app maken

Maak in de Cloud Shell een [web-app](app-service-linux-intro.md) in het `myAppServicePlan`App Service-plan met de opdracht [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Vervang _ \<app-naam>_ door een unieke _ \<app-naam en de naam azure-container-registry>_ door uw registernaam.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-container-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Wanneer de web-app is gemaakt, toont de Azure CLI soortgelijke uitvoer als in het volgende voorbeeld:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-registry-credentials-in-web-app"></a>Registerreferenties configureren in web-app

Als U de privéafbeelding wilt opvragen, heeft deze informatie nodig over uw register en afbeelding. Geef ze in de Cloud [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) Shell de opdracht. Vervang * \<>, * * \<de naam van Azure-container-registry>*, _ \<>_ en _ \<wachtwoord>_.

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> Wanneer u een ander register `--docker-registry-server-url` dan Docker `https://` Hub gebruikt, moet deze worden opgemaakt, gevolgd door de volledig gekwalificeerde domeinnaam van het register.
>

### <a name="configure-environment-variables"></a>Omgevingsvariabelen configureren

De meeste Docker-afbeeldingen gebruiken aangepaste omgevingsvariabelen, zoals een andere poort dan 80. U vertelt App Service over de poort `WEBSITES_PORT` die uw afbeelding gebruikt met behulp van de app-instelling. De GitHub-pagina voor het [Python-voorbeeld in deze zelfstudie](https://github.com/Azure-Samples/docker-django-webapp-linux) laat zien dat u `WEBSITES_PORT` in moet stellen op _8000_.

Als u app-instellingen [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) wilt instellen, gebruikt u de opdracht in de Cloud Shell. App-instellingen zijn hoofdlettergevoelig en door spaties gescheiden.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>De web-app testen

Controleer of de web-app werkt door erheen te bladeren (`http://<app-name>.azurewebsites.net`).

> [!NOTE]
> De eerste keer dat u de app opent, kan het enige tijd duren omdat App Service de hele afbeelding moet trekken. Als de browser een keer uitvalt, vernieuwt u de pagina.

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

Nadat u het Python-bestand hebt gewijzigd en dit hebt opgeslagen, moet u de nieuwe Docker-installatiekopie opnieuw opbouwen en pushen. Start de web-app opnieuw om de wijzigingen door te voeren. Gebruik dezelfde opdrachten die u eerder in deze zelfstudie hebt gebruikt. U verwijzen naar [De afbeelding bouwen vanuit het Docker-bestand](#build-the-image-from-the-docker-file) en De afbeelding [pushen naar Azure Container Registry.](#push-image-to-azure-container-registry) De web-app testen door de instructies te volgen in [De web-app testen](#test-the-web-app).

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>SSH-verbindingen inschakelen

SSH maakt veilige communicatie tussen een container en een client mogelijk. Als u de SSH-verbinding met uw container wilt inschakelen, moet uw aangepaste afbeelding hiervoor zijn geconfigureerd. Laten we eens kijken naar de voorbeeldrepository die al de benodigde configuratie heeft.

* In het [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile)installeert de volgende code de SSH-server en stelt u ook de aanmeldingsreferenties in.

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Deze configuratie staat geen externe verbindingen naar de container toe. SSH is alleen beschikbaar via de Kudu/SCM-Site. De Kudu/SCM-site is geverifieerd met uw Azure-account.

* Het [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) kopieert het [sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config) bestand in de repository naar de */etc/ssh/directory.*

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* Het [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) legt poort 2222 in de container bloot. Het is een interne poort die alleen toegankelijk is voor containers in het brugnetwerk van een particulier virtueel netwerk. 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* Het [invoerscript](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5) start de SSH-server.

    ```bash
    #!/bin/bash
    service ssh start
    ```

### <a name="open-ssh-connection-to-container"></a>SSH-verbinding met de container openen

SSH-verbinding is alleen beschikbaar via de Kudu-site, die toegankelijk is op `https://<app-name>.scm.azurewebsites.net`.

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
> * Een aangepaste afbeelding implementeren in een privécontainerregister
> * De aangepaste afbeelding uitvoeren in App-service
> * Omgevingsvariabelen configureren
> * De afbeelding bijwerken en opnieuw implementeren
> * Toegang tot diagnostische logboeken
> * Verbinding maken met de container via SSH

Ga door naar de volgende zelfstudie om te leren hoe u een aangepaste DNS-naam aan uw app kunt toewijzen.

> [!div class="nextstepaction"]
> [Zelfstudie: aangepaste DNS-naam toewijzen aan uw app](../app-service-web-tutorial-custom-domain.md)

Of bekijk andere bronnen:

> [!div class="nextstepaction"]
> [Een aangepaste container configureren](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Zelfstudie: WordPress-app voor meerdere containers](tutorial-multi-container-app.md)
