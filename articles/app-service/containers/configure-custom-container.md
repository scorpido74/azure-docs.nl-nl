---
title: Een aangepaste Linux-container configureren
description: Meer informatie over het configureren van een aangepaste Linux-container in Azure App Service. In dit artikel worden de meest voorkomende configuratietaken weergegeven.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6baa1fbd4932aa83a54081ff166dcae7f258fff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280142"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Een aangepaste Linux-container configureren voor Azure App Service

In dit artikel ziet u hoe u een aangepaste Linux-container configureert om op Azure App Service te worden uitgevoerd.

Deze handleiding bevat belangrijke concepten en instructies voor containerisatie van Linux-apps in App Service. Als u Azure App Service nog nooit hebt gebruikt, volgt u eerst de [aangepaste containersnelstart](quickstart-docker-go.md) en [-zelfstudie.](tutorial-custom-docker-image.md) Er is ook een [multi-container app quickstart](quickstart-multi-container.md) en [tutorial](tutorial-multi-container-app.md).

## <a name="configure-port-number"></a>Poortnummer configureren

De webserver in uw aangepaste afbeelding kan een andere poort dan 80 gebruiken. U vertelt Azure over de poort die `WEBSITES_PORT` uw aangepaste container gebruikt met behulp van de app-instelling. De GitHub-pagina voor het [Python-voorbeeld in deze zelfstudie](https://github.com/Azure-Samples/docker-django-webapp-linux) laat zien dat u `WEBSITES_PORT` in moet stellen op _8000_. U deze [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) instellen door de opdracht uit te voeren in de Cloud Shell. Bijvoorbeeld:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Omgevingsvariabelen configureren

Uw aangepaste container kan omgevingsvariabelen gebruiken die extern moeten worden geleverd. Je ze doorgeven [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) door de opdracht uit te voeren in de Cloud Shell. Bijvoorbeeld:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Deze methode werkt zowel voor apps met één container als voor apps met meerdere containers, waarbij de omgevingsvariabelen zijn opgegeven in het *docker-compose.yml-bestand.*

## <a name="use-persistent-shared-storage"></a>Permanente gedeelde opslag gebruiken

U de */home-map* in het bestandssysteem van uw app gebruiken om bestanden voor alle opnieuw opstarten te blijven beheren en deze te delen in verschillende instanties. De `/home` in uw app is beschikbaar om uw container-app toegang te geven tot permanente opslag.

Wanneer permanente opslag is uitgeschakeld, `/home` worden de schrijfbewerkingen naar de map niet blijven bestaan in het opnieuw opstarten van apps of in meerdere instanties. De enige uitzondering `/home/LogFiles` is de map, die wordt gebruikt om de Docker- en containerlogboeken op te slaan. Wanneer permanente opslag is ingeschakeld, `/home` blijven alle schrijfbewerkingen naar de map bestaan en kunnen ze worden geopend door alle exemplaren van een uitschaalbare app.

Standaard is permanente opslag *ingeschakeld* en wordt de instelling niet weergegeven in de toepassingsinstellingen. Als u deze `WEBSITES_ENABLE_APP_SERVICE_STORAGE` wilt uitschakelen, [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) stelt u de app-instelling in door de opdracht uit te voeren in de Cloud Shell. Bijvoorbeeld:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> U ook [uw eigen permanente opslag configureren.](how-to-serve-content-from-azure-storage.md)

## <a name="enable-ssh"></a>SSH inschakelen

SSH maakt veilige communicatie tussen een container en een client mogelijk. Als u een aangepaste container om SSH te ondersteunen, moet u deze toevoegen aan het Dockerfile zelf.

> [!TIP]
> Alle ingebouwde Linux containers hebben de SSH instructies toegevoegd in hun image repositories. U de volgende instructies doorlopen met de [Node.js 10.14-opslagplaats](https://github.com/Azure-App-Service/node/blob/master/10.14) om te zien hoe deze daar is ingeschakeld.

- Gebruik de [RUN-instructie](https://docs.docker.com/engine/reference/builder/#run) om de SSH-server te `"Docker!"`installeren en stel het wachtwoord voor het hoofdaccount in op . Voor een afbeelding op basis [van Alpine Linux](https://hub.docker.com/_/alpine)heb je bijvoorbeeld de volgende commando's nodig:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Deze configuratie staat geen externe verbindingen met de container toe. SSH is alleen `https://<app-name>.scm.azurewebsites.net` beschikbaar via en geverifieerd met de publicatiereferenties.

- Voeg [dit sshd_config bestand](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) toe aan uw afbeeldingsopslagplaats en gebruik de instructie [COPY](https://docs.docker.com/engine/reference/builder/#copy) om het bestand te kopiëren naar de */etc/ssh/directory.* Zie [OpenBSD-documentatie](https://man.openbsd.org/sshd_config)voor meer informatie over *sshd_config* bestanden.

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Het *sshd_config*-bestand moet de volgende items bevatten:
    > - `Ciphers`moet ten minste één item in deze lijst bevatten: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs`moet ten minste één item in deze lijst bevatten: `hmac-sha1,hmac-sha1-96`.

- Gebruik de [INSTRUCTIE EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) om poort 2222 in de container te openen. Hoewel het hoofdwachtwoord bekend is, is poort 2222 niet toegankelijk vanaf het internet. Het is alleen toegankelijk via containers binnen het brugnetwerk van een particulier virtueel netwerk.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- Start de SSH-server in het startscript voor uw container.

    ```bash
    /usr/sbin/sshd
    ```

    Zie bijvoorbeeld hoe de standaard [Node.js 10.14-container](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) de SSH-server start.

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Apps voor meerdere containers configureren

- [Permanente opslag gebruiken in Docker Compose](#use-persistent-storage-in-docker-compose)
- [Preview-beperkingen](#preview-limitations)
- [Opties voor Docker Compose](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Permanente opslag gebruiken in Docker Compose

Multi-container apps zoals WordPress hebben permanente opslag nodig om goed te kunnen functioneren. Om dit in te schakelen, moet uw Docker Compose-configuratie wijzen op een opslaglocatie *buiten* uw container. Opslaglocaties in uw container blijven niet bestaan in wijzigingen die verder gaan dan het opnieuw opstarten van de app.

Permanente opslag inschakelen `WEBSITES_ENABLE_APP_SERVICE_STORAGE` door de app-instelling in te stellen met de opdracht [az webapp config-appsettings](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Breng in uw *docker-compose.yml-bestand* de `volumes` optie in kaart aan `${WEBAPP_STORAGE_HOME}`. 

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

Multi-container is momenteel in preview. De volgende functies van het App Service-platform worden niet ondersteund:

- Verificatie / autorisatie
- Beheerde identiteiten

### <a name="docker-compose-options"></a>Opties voor Docker Compose

In de volgende lijsten worden ondersteunde en niet-ondersteunde docker-configuratieopties weergegeven:

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
> Alle andere opties die niet expliciet worden opgeroepen, worden genegeerd in Public Preview.

## <a name="configure-vnet-integration"></a>VNet-integratie configureren

Het gebruik van een aangepaste container met VNet-integratie kan extra containerconfiguratie vereisen. Zie [Uw app integreren met een Azure Virtual Network](../web-sites-integrate-with-vnet.md).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Implementeren vanuit de opslagplaats voor privécontainers](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Zelfstudie: WordPress-app voor meerdere containers](tutorial-multi-container-app.md)
