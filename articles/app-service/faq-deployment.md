---
title: Veelgestelde vragen over implementatie - Azure App-service | Microsoft Documenten
description: Antwoorden op veelgestelde vragen over implementatie voor de Web Apps-functie van Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 25f43051f11d42c4f8565039d3168f2ae162eb37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671689"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Veelgestelde vragen over implementaties voor web-apps in azure

In dit artikel vindt u antwoorden op veelgestelde vragen (veelgestelde vragen) over implementatieproblemen voor de [Web Apps-functie van Azure App Service.](https://azure.microsoft.com/services/app-service/web/)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Ik ben net begonnen met App Service web apps. Hoe publiceer ik mijn code?

Hier volgen enkele opties voor het publiceren van uw web-app-code:

*   Implementeren met Visual Studio. Als u de Visual Studio-oplossing hebt, klikt u met de rechtermuisknop op het project voor webtoepassingen en selecteert u **Publiceren**.
*   Implementeren met behulp van een FTP-client. Download in de Azure-portal het publicatieprofiel voor de web-app waarop u uw code wilt implementeren. Upload de bestanden vervolgens naar \site\wwwroot met dezelfde FTP-referenties voor publicatieprofielen.

Zie [Uw app implementeren naar app-service voor](deploy-local-git.md)meer informatie .

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this-error"></a>Ik zie een foutmelding wanneer ik probeer te implementeren vanuit Visual Studio. Hoe los ik deze fout op?

Als u het volgende bericht ziet, gebruikt u mogelijk een oudere versie van de SDK: 'Fout tijdens implementatie voor resource 'YourResourceName' in resourcegroep 'YourResourceGroup': MissingRegistrationForLocation: Het abonnement is niet geregistreerd voor de resourcetype 'componenten' op de locatie 'Central US'. Schrijf je opnieuw in voor deze provider om toegang te hebben tot deze locatie." 

Als u deze fout wilt oplossen, u upgraden naar de [nieuwste SDK.](https://azure.microsoft.com/downloads/) Als u dit bericht ziet en u beschikt over de nieuwste SDK, dient u een ondersteuningsverzoek in.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Hoe implementeer ik een ASP.NET toepassing van Visual Studio naar App Service?
<a id="deployasp"></a>

In de zelfstudie [Maak je in vijf minuten je eerste ASP.NET web-app in Azure,](app-service-web-get-started-dotnet.md) zie je hoe je een ASP.NET-webtoepassing implementeert naar een web-app in App Service met Behulp van Visual Studio.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Wat zijn de verschillende typen implementatiereferenties?

App Service ondersteunt twee typen referenties voor lokale Git-implementatie en FTP/S-implementatie. Zie [Implementatiereferenties configureren voor App Service](deploy-configure-credentials.md)voor meer informatie over het configureren van implementatiereferenties.

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Wat is de bestands- of directorystructuur van mijn App Service-webapp?

Zie [Bestandsstructuur in Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)voor informatie over de bestandsstructuur van uw App Service-app.

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Hoe los ik 'FTP-fout 550 - Er is niet genoeg ruimte op de schijf' op als ik mijn bestanden ftp probeer te ftp?

Als u dit bericht ziet, loopt u waarschijnlijk tegen een schijfquotum aan in het serviceplan voor uw web-app. Mogelijk moet u opschalen naar een hogere servicelaag op basis van uw schijfruimtebehoeften. Zie [App Service-prijzen](https://azure.microsoft.com/pricing/details/app-service/)voor meer informatie over prijsplannen en resourcelimieten.

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Hoe stel ik continue implementatie in voor mijn App Service-webapp?

U continue implementatie instellen vanuit verschillende bronnen, waaronder Azure DevOps, OneDrive, GitHub, Bitbucket, Dropbox en andere Git-repositories. Deze opties zijn beschikbaar in de portal. [Continue implementatie naar App Service](deploy-continuous-deployment.md) is een handige zelfstudie die uitlegt hoe u continue implementatie instellen.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Hoe los ik problemen op met continue implementatie van GitHub en Bitbucket?

Zie [Continue implementatie](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)onderzoeken voor hulp bij het onderzoeken van problemen met continue implementatie vanuit GitHub of Bitbucket.

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this-issue"></a>Ik kan geen FTP naar mijn site en mijn code publiceren. Hoe los ik dit probleem op?

Ga als een besluit over FTP-problemen:

1. Controleer of u de juiste hostnaam en -referenties invoert. Zie [Implementatiereferenties](https://github.com/projectkudu/kudu/wiki/Deployment-credentials)voor gedetailleerde informatie over verschillende typen referenties en hoe deze te gebruiken.
2. Controleer of de FTP-poorten niet worden geblokkeerd door een firewall. De poorten moeten de volgende instellingen hebben:
    * Verbindingspoort voor FTP-besturingselement: 21
    * FTP-gegevensverbindingspoort: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Hoe publiceer ik mijn code naar App Service?

De Azure Quickstart is ontworpen om u te helpen uw app te implementeren met behulp van de implementatiestack en -methode van uw keuze. Als u de Quickstart wilt gebruiken, gaat u in de Azure-portal naar uw app-service onder **Implementatie**, selecteert u **Snelstart**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Waarom wordt mijn app soms opnieuw opgestart na implementatie naar App Service?

Zie Problemen met implementatie [versus runtime](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts")voor meer informatie over de omstandigheden waaronder een implementatie van een toepassing kan leiden tot een herstart. Zoals in het artikel wordt beschreven, implementeert App Service bestanden naar de map wwwroot. Het nooit direct opnieuw start uw app.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Hoe kan ik Azure DevOps-code integreren met App Service?

U hebt twee opties voor het gebruik van continue implementatie met Azure DevOps:

*   Gebruik een Git-project. Maak verbinding via App Service via het Implementatiecentrum.
*   Gebruik een Team Foundation Version Control (TFVC) project. Implementeren met behulp van de buildagent voor App Service.

Continue code-implementatie voor beide opties is afhankelijk van bestaande ontwikkelaarsworkflows en incheckprocedures. Raadpleeg voor meer informatie de volgende artikelen: 

*   [Continue implementatie van uw app implementeren op een Azure-website](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Een Azure DevOps-organisatie instellen zodat deze kan worden geïmplementeerd in een web-app](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Hoe gebruik ik FTP of FTPS om mijn app te implementeren in De App Service?

Zie [Uw app implementeren naar App-service implementeren met FTP/S](deploy-ftp.md)voor informatie over het gebruik van FTP of FTPS om uw web-app te implementeren in App-service.
