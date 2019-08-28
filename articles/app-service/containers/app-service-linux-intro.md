---
title: Inleiding tot App Service in Linux - Azure | Microsoft Docs
description: Meer informatie over Azure App Service onder Linux.
keywords: azure app service, linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 7c00946ed9de88df43a4435c23ebef27b5c64e5c
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071326"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Inleiding tot App Service onder Linux

[Azure App Service](../overview.md) is een volledig beheerd rekenplatform dat is geoptimaliseerd voor het hosten van websites en web-apps. Klanten kunnen App Service onder Linux gebruiken voor het systeemeigen hosten van web-apps op Linux voor ondersteunde toepassingsstacks. In de sectie [Talen](#languages) worden de toepassingsstacks vermeld die momenteel worden ondersteund.

## <a name="languages"></a>Languages

App Service onder Linux ondersteunt een aantal ingebouwde installatiekopieën om zodoende de productiviteit van ontwikkelaars te verhogen. Als de runtime die uw toepassing vereist, niet in de ingebouwde installatiekopieën wordt ondersteund, zijn er instructies voor het [bouwen van een Docker-installatiekopie](tutorial-custom-docker-image.md) voor implementatie voor Web App for Containers.

| Taal | Ondersteunde versies |
|---|---|
| Node.js | 4.4, 4.5, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0, 8.1, 8.2, 8.8, 8.9, 8.11, 8.12, 9.4, 10.1, 10.10, 10.14 |
| Java * | Tomcat 8.5, 9.0, Java SE, WildFly 14 (voeren alle JRE 8 uit) |
| PHP | 5,6, 7,0, 7,2, 7,3 |
| Python | 2.7, 3.6, 3.7 |
| .NET Core | 1.0, 1.1, 2.0, 2.1, 2.2 |
| Ruby | 2,3, 2,4, 2,5, 2,6 |

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

App Service op Linux wordt alleen ondersteund met de app service [-abonnementen gratis, Basic, Standard en Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) en heeft geen [gedeelde](https://azure.microsoft.com/pricing/details/app-service/plans/) laag. U kunt geen Linux-web-app maken in een App Service abonnement dat al wordt gehost op niet-Linux Web Apps.  

Op basis van een huidige beperking geldt dat voor dezelfde resource groep geen Windows-en Linux-apps kunnen worden gecombineerd in dezelfde regio.

## <a name="troubleshooting"></a>Problemen oplossen

Wanneer uw toepassing niet kan worden gestart of als u de logboeken vanuit de app wilt controleren, controleert u de Docker-logboeken in de map LogFiles. U kunt deze map openen via uw SCM-site of via FTP. Als u de `stdout` en `stderr` vanuit uw container wilt registreren, moet u **logboek registratie** voor docker-container inschakelen onder **app service logboeken**. De instelling wordt onmiddellijk van kracht. De wijziging wordt door App Service gedetecteerd en de container wordt automatisch opnieuw gestart.

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

Vragen kunt u posten op [ons forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
