---
title: Een aangepaste Linux-container configureren
description: Meer informatie over het configureren van een aangepaste Linux-container in Azure App Service. In dit artikel vindt u de meest voorkomende configuratie taken.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6baa1fbd4932aa83a54081ff166dcae7f258fff9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79280142"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Een aangepaste Linux-container voor Azure App Service configureren

Dit artikel laat u zien hoe u een aangepaste Linux-container kunt configureren om te worden uitgevoerd op Azure App Service.

Deze hand leiding bevat belang rijke concepten en instructies voor container opslag Linux-apps in App Service. Als u Azure App Service nog nooit hebt gebruikt, volgt u eerst de Snelstartgids en [zelf studie](tutorial-custom-docker-image.md) van de [aangepaste container](quickstart-docker-go.md) . Er is ook een Snelstartgids en [zelf studie](tutorial-multi-container-app.md)voor [apps met meerdere containers](quickstart-multi-container.md) .

## <a name="configure-port-number"></a>Poort nummer configureren

De webserver in uw aangepaste installatie kopie mag een andere poort dan 80 gebruiken. U vertelt Azure over de poort die uw aangepaste container gebruikt met behulp `WEBSITES_PORT` van de app-instelling. De GitHub-pagina voor het [Python-voorbeeld in deze zelfstudie](https://github.com/Azure-Samples/docker-django-webapp-linux) laat zien dat u `WEBSITES_PORT` in moet stellen op _8000_. U kunt deze instellen door de [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) opdracht uit te voeren in de Cloud shell. Bijvoorbeeld:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Omgevingsvariabelen configureren

Uw aangepaste container kan gebruikmaken van omgevings variabelen die extern moeten worden opgegeven. U kunt deze door geven in door [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) de opdracht uit te voeren in de Cloud shell. Bijvoorbeeld:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Deze methode werkt zowel voor apps met één container als apps met meerdere containers, waarbij de omgevings variabelen worden opgegeven in het bestand *docker-Compose. yml* .

## <a name="use-persistent-shared-storage"></a>Permanente gedeelde opslag gebruiken

U kunt de */Home* -map in het bestands systeem van uw app gebruiken om bestanden op te slaan tijdens het opnieuw opstarten en ze te delen met alle instanties. De `/home` in uw app is beschikbaar om uw container-app toegang te geven tot permanente opslag.

Wanneer permanente opslag is uitgeschakeld, worden de schrijf bewerkingen `/home` naar de Directory niet opgeslagen in een app die opnieuw wordt gestart of over meerdere exemplaren. De enige uitzonde ring `/home/LogFiles` hierop is de map die wordt gebruikt voor het opslaan van de docker-en container Logboeken. Wanneer permanente opslag is ingeschakeld, worden alle schrijf bewerkingen `/home` naar de Directory persistent gemaakt en kunnen alle exemplaren van een uitgeschaalde app worden geopend.

Permanente opslag is standaard *ingeschakeld* en de instelling wordt niet weer gegeven in de toepassings instellingen. Als u dit wilt uitschakelen, `WEBSITES_ENABLE_APP_SERVICE_STORAGE` stelt u de app [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) -instelling in door de opdracht uit te voeren in de Cloud shell. Bijvoorbeeld:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> U kunt ook [uw eigen permanente opslag configureren](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>SSH inschakelen

SSH maakt veilige communicatie tussen een container en een client mogelijk. Als u een aangepaste container SSH wilt ondersteunen, moet u deze toevoegen aan de Dockerfile zelf.

> [!TIP]
> Alle ingebouwde Linux-containers hebben de SSH-instructies toegevoegd aan hun afbeeldings opslagplaatsen. U kunt de volgende instructies door lopen met de [node. js 10,14-opslag plaats](https://github.com/Azure-App-Service/node/blob/master/10.14) om te zien hoe deze er wordt ingeschakeld.

- Gebruik de instructie [Run](https://docs.docker.com/engine/reference/builder/#run) om de SSH-server te installeren en stel het wacht woord voor het `"Docker!"`hoofd account in op. Voor een installatie kopie op basis van [Alpine Linux](https://hub.docker.com/_/alpine)hebt u bijvoorbeeld de volgende opdrachten nodig:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Deze configuratie staat geen externe verbindingen naar de container toe. SSH is alleen beschikbaar via `https://<app-name>.scm.azurewebsites.net` en geverifieerd met de publicatie referenties.

- Voeg [dit sshd_config bestand](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) toe aan uw opslag plaats voor installatie kopieën en gebruik de [Kopieer](https://docs.docker.com/engine/reference/builder/#copy) instructie om het bestand te kopiëren naar de */etc/ssh/* -map. Zie [OpenBSD-documentatie](https://man.openbsd.org/sshd_config)voor meer informatie over *sshd_config* -bestanden.

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Het *sshd_config*-bestand moet de volgende items bevatten:
    > - `Ciphers`moet ten minste één item in deze lijst bevatten: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs`moet ten minste één item in deze lijst bevatten: `hmac-sha1,hmac-sha1-96`.

- Gebruik de instructie [beschikbaar](https://docs.docker.com/engine/reference/builder/#expose) maken om poort 2222 in de container te openen. Hoewel het Hoofdwacht woord bekend is, is poort 2222 niet toegankelijk via internet. Het is alleen toegankelijk voor containers in het brug netwerk van een particulier virtueel netwerk.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- Start de SSH-server in het opstart script voor uw container.

    ```bash
    /usr/sbin/sshd
    ```

    Zie hoe de standaard [node. js 10,14-container](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) de SSH-server start, voor een voor beeld.

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Apps voor meerdere containers configureren

- [Permanente opslag gebruiken in docker opstellen](#use-persistent-storage-in-docker-compose)
- [Preview-beperkingen](#preview-limitations)
- [Opties voor docker opstellen](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Permanente opslag gebruiken in docker opstellen

Multi-container-apps zoals WordPress hebben permanente opslag nodig om goed te kunnen functioneren. Als u deze functie wilt inschakelen, moet de configuratie van de docker-samen stellen naar een opslag locatie *buiten* uw container verwijzen. Opslag locaties in uw container behouden geen wijzigingen na het opnieuw opstarten van de app.

Schakel permanente opslag in door de `WEBSITES_ENABLE_APP_SERVICE_STORAGE` app-instelling in te stellen met behulp van de opdracht [AZ webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in Cloud shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Wijs in uw *docker-Compose. yml* -bestand de `volumes` optie toe `${WEBAPP_STORAGE_HOME}`aan. 

`WEBAPP_STORAGE_HOME` is een omgevingsvariabele in App Service die is toegewezen aan de permanente opslag voor uw app. Bijvoorbeeld:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Preview-beperkingen

Meerdere containers zijn momenteel beschikbaar als preview-versie. De volgende App Service platform functies worden niet ondersteund:

- Verificatie / autorisatie
- Beheerde identiteiten

### <a name="docker-compose-options"></a>Opties voor docker opstellen

In de volgende lijsten worden ondersteunde en niet-ondersteunde docker-configuratie opties weer gegeven:

#### <a name="supported-options"></a>Ondersteunde opties

- command
- entrypoint
- omgeving
- installatiekopie
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Niet-ondersteunde opties

- build (niet toegestaan)
- depends_on (genegeerd)
- networks (genegeerd)
- secrets (genegeerd)
- andere poorten dan 80 en 8080 (genegeerd)

> [!NOTE]
> Alle andere opties die niet expliciet worden aangeroepen, worden genegeerd in de open bare preview-versie.

## <a name="configure-vnet-integration"></a>VNet-integratie configureren

Voor het gebruik van een aangepaste container met VNet-integratie is mogelijk extra container configuratie vereist. Zie [uw app integreren met een Azure-Virtual Network](../web-sites-integrate-with-vnet.md).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: implementeren vanuit een persoonlijke container opslagplaats](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Zelf studie: WordPress-app met meerdere containers](tutorial-multi-container-app.md)
