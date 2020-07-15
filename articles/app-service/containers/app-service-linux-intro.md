---
title: Code uitvoeren op standaard Linux-containers
description: Met Azure App Service kunt u uw code uitvoeren op vooraf ontwikkelde Linux-containers. Ontdek hoe u uw Linux-webtoepassingen kunt uitvoeren op Azure.
keywords: azure app service, linux, oss
author: msangapu-msft
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 891e0c18b3f95dca905fbc14b957af773135eeec
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/30/2020
ms.locfileid: "85557892"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Inleiding tot App Service onder Linux

[Azure App Service](../overview.md) is een volledig beheerd rekenplatform dat is geoptimaliseerd voor het hosten van websites en web-apps. Klanten kunnen App Service onder Linux gebruiken voor het systeemeigen hosten van web-apps op Linux voor ondersteunde toepassingsstacks.

## <a name="languages"></a>Talen

App Service onder Linux ondersteunt een aantal ingebouwde installatiekopieën om zodoende de productiviteit van ontwikkelaars te verhogen. Ondersteunde talen zijn: Node.js, Java (JRE 8 & JRE 11), PHP, Python, .NET Core en Ruby. Voer [`az webapp list-runtimes --linux`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) uit om de nieuwste talen en ondersteunde versies te zien. Als de runtime die uw toepassing vereist, niet in de ingebouwde installatiekopieën wordt ondersteund, zijn er instructies voor het [bouwen van een Docker-installatiekopie](tutorial-custom-docker-image.md) voor implementatie voor Web App for Containers.

## <a name="deployments"></a>Implementaties

* FTP
* Lokale Git
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Faseringsomgevingen
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) en DockerHub-CI/CD

## <a name="console-publishing-and-debugging"></a>Console, publiceren en foutopsporing

* Omgevingen
* Implementaties
* Basisconsole
* SSH

## <a name="scaling"></a>Schalen

* Klanten kunnen web-apps omhoog of omlaag schalen door de laag van hun [App Service-plan](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json) te wijzigen

## <a name="locations"></a>Locaties

Controleer [Azure Status Dashboard](https://azure.microsoft.com/status).

## <a name="limitations"></a>Beperkingen

In Azure Portal worden alleen functies getoond die momenteel werken voor Web App for Containers. Naarmate er meer functies mogelijk worden, zullen ze op de portal worden getoond.

App Service onder Linux wordt alleen ondersteund met [Gratis, Basic, Standard, Premium en Isolated](https://azure.microsoft.com/pricing/details/app-service/plans/) app-serviceplannen, en heeft geen [Shared](https://azure.microsoft.com/pricing/details/app-service/plans/)-laag. U kunt geen Linux-web-app maken in een App Service-plan waar al niet-Linux-web-apps worden gehost.  

Vanwege een huidige beperking kunt u geen Windows- en Linux-apps voor dezelfde resourcegroep in dezelfde regio gebruiken.

## <a name="troubleshooting"></a>Problemen oplossen

> [!NOTE]
> Er zijn nieuwe geïntegreerde logboekregistratiemogelijkheden met [Azure Monitoring (preview)](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs#send-logs-to-azure-monitor-preview) . 
>
>

Wanneer uw toepassing niet kan worden gestart of als u de logboeken vanuit de app wilt controleren, controleert u de Docker-logboeken in de map LogFiles. U kunt deze map openen via uw SCM-site of via FTP. Als u de `stdout` en `stderr` van uw container wilt registreren, moet u **Toepassingslogboeken** inschakelen onder **App Service-logboeken**. De instelling wordt onmiddellijk van kracht. App Service detecteert de wijziging en start de container automatisch opnieuw op.

U hebt toegang tot de SCM-site via **Geavanceerde hulpmiddelen** in het menu **Ontwikkelprogramma's**.

![Kudu gebruiken om Docker-logboeken weer te geven][1]

## <a name="next-steps"></a>Volgende stappen

De volgende artikelen helpen u om aan de slag te gaan met App Service op Linux met web-apps die zijn geschreven in verschillende talen:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [App met meerdere containers](quickstart-multi-container.md)

Zie voor meer informatie over App Service op Linux:

* [Veelgestelde vragen over App Service voor Linux](app-service-linux-faq.md)
* [SSH-ondersteuning voor App Service op Linux](app-service-linux-ssh-support.md)
* [Faseringsomgevingen in App Service instellen](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Doorlopende implementatie met Docker Hub](app-service-linux-ci-cd.md)

Vragen kunt u posten op [ons forum](https://docs.microsoft.com/answers/topics/azure-webapps.html).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
