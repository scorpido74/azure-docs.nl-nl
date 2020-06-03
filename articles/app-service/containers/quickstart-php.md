---
title: 'Quickstart: Een PHP-app maken in Linux'
description: Ga aan de slag met Linux-apps op Azure App Service door uw eerste PHP-app te implementeren in een Linux-container in App Service.
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.topic: quickstart
ms.date: 05/25/2020
ms.custom: mvc, cli-validatem seodec18
ms.openlocfilehash: f0307088d274c9afee8fb8816b41a6357b4ce9f2
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848073"
---
# <a name="create-a-php-app-in-app-service-on-linux"></a>Een PHP-web-app maken in App Service op Linux

> [!NOTE]
> In dit artikel gaat u een app implementeren in App Service onder Linux. Zie [Een PHP-app maken in Azure](../app-service-web-get-started-php.md) om een app te implementeren in App Service op _Windows_.
>

[App Service onder Linux](app-service-linux-intro.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie onder het Linux-besturingssysteem. Deze quickstart laat zien hoe u een PHP-app in Azure App Service naar Linux implementeert met [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

![Voorbeeld-app die wordt uitgevoerd in Azure](media/quickstart-php/hello-world-in-browser.png)

U kunt de stappen in dit artikel volgen met behulp van een Mac-, Windows- of Linux-computer.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstart:

* <a href="https://git-scm.com/" target="_blank">Git installeren</a>
* <a href="https://php.net" target="_blank">PHP installeren</a>

## <a name="download-the-sample"></a>Het voorbeeld downloaden

Voer de volgende opdrachten uit in een terminalvenster. Hiermee wordt de voorbeeldtoepassing gekloond naar uw lokale machine en navigeert u naar de map met de voorbeeldcode.

```bash
git clone https://github.com/Azure-Samples/php-docs-hello-world
cd php-docs-hello-world
```

## <a name="run-the-app-locally"></a>De app lokaal uitvoeren

Voer de toepassing lokaal uit zodat u kunt zien hoe deze eruit ziet wanneer u de toepassing implementeert naar Azure. Open een terminalvenster en gebruik het script `php` om de ingebouwde PHP-webserver te starten.

```bash
php -S localhost:8080
```

Open een webbrowser en navigeer naar de voorbeeldapp op `http://localhost:8080`.

Het bericht **Hallo wereld** uit de voorbeeld-app wordt weergegeven op de pagina.

![Voorbeeld-app die lokaal wordt uitgevoerd](media/quickstart-php/localhost-hello-world-in-browser.png)

Druk in uw terminalvenster op **Ctrl + C** om de webserver af te sluiten.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Een webtoepassing maken

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-php-linux-no-h.md)] 

Blader naar de site om uw nieuwe app met de ingebouwde installatiekopie te bekijken. Vervang _&lt;app-naam>_ door de naam van uw app.

```bash
http://<app-name>.azurewebsites.net
```

Uw nieuwe app lijkt op het volgende:

![Lege app-pagina](media/quickstart-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

<pre>
Counting objects: 26, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (23/23), done.
Writing objects: 100% (26/26), 4.95 KiB | 0 bytes/s, done.
Total 26 (delta 9), reused 0 (delta 0)
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'df425ea6ef'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote: .
remote: .
remote: .
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/.../log'
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      master -> master
</pre>

## <a name="browse-to-the-app"></a>Bladeren naar de app

Blader naar de geïmplementeerde toepassing via uw webbrowser.

```bash
http://<app-name>.azurewebsites.net
```

De PHP-voorbeeldcode wordt uitgevoerd in een App Service op Linux met een ingebouwde installatiekopie.

![Voorbeeld-app die wordt uitgevoerd in Azure](media/quickstart-php/hello-world-in-browser.png)

**Gefeliciteerd!** U hebt uw eerste PHP-app geïmplementeerd in App Service on Linux.

## <a name="update-locally-and-redeploy-the-code"></a>De code lokaal bijwerken en opnieuw implementeren

Open in de lokale map het bestand `index.php` binnen de PHP-app en breng een kleine wijziging aan in de tekst in de tekenreeks naast `echo`:

```php
echo "Hello Azure!";
```

Leg uw wijzigingen vast in Git en push de codewijzigingen vervolgens naar Azure.

```bash
git commit -am "updated output"
git push azure master
```

Wanneer de implementatie is voltooid, gaat u terug naar het browservenster dat is geopend in de stap **Bladeren naar de app** en vernieuwt u de pagina.

![Bijgewerkte voorbeeld-app die wordt uitgevoerd in Azure](media/quickstart-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Uw nieuwe Azure-app beheren

Ga naar <a href="https://portal.azure.com" target="_blank">Azure Portal</a> om de app te beheren die u hebt gemaakt.

Klik in het linkermenu op **App Services** en klik op de naam van uw Azure-app.

![Navigatie naar Azure-app in de portal](./media/quickstart-php/php-docs-hello-world-app-service-list.png)

De pagina Overzicht van uw app wordt weergegeven. Hier kunt u algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw opstarten en verwijderen.

![App Service-pagina in Azure Portal](media/quickstart-php/php-docs-hello-world-app-service-detail.png)

Het linkermenu bevat een aantal pagina's voor het configureren van uw app. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: PHP-app met MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [PHP-app configureren](configure-language-php.md)
