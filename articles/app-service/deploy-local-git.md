---
title: Implementeren vanuit lokale Git-repo
description: Meer informatie over het inschakelen van lokale Git-implementatie naar Azure App Service. Een van de eenvoudigste manieren om code van uw lokale machine te implementeren.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: efe4c07a6231e0b2c95b049db056a4e5d055db98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152989"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Lokale Git-implementatie naar Azure App Service

In deze handleiding ziet u hoe u uw app implementeert naar [Azure App Service](overview.md) vanuit een Git-opslagplaats op uw lokale computer.

## <a name="prerequisites"></a>Vereisten

Ga als volgt te werk om de stappen in deze handleiding te volgen:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Installeer Git](https://www.git-scm.com/downloads).
  
- Heb een lokale Git-opslagplaats met code die u wilt implementeren. Als u een voorbeeldopslagplaats wilt downloaden, voert u de volgende opdracht uit in het lokale terminalvenster:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Implementeren met Kudu-buildserver

De eenvoudigste manier om lokale Git-implementatie voor uw app in te schakelen met de Kudu App Service-buildserver, is door Azure Cloud Shell te gebruiken. 

### <a name="configure-a-deployment-user"></a>Een implementatiegebruiker configureren

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>De url van de implementatie ophalen

Voer de URL in om de lokale Git-implementatie voor een bestaande app in te [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) schakelen in de Cloud Shell. Vervang \<de app-naam> en \<groepsnaam> door de namen van uw app en de Azure-brongroep.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```
> [!NOTE]
> Als u een linux app-service-abonnement gebruikt, moet u deze parameter toevoegen: --runtime python|3.7


Of als u een nieuwe Git-app [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) wilt maken, `--deployment-local-git` wordt u in de Cloud Shell uitgevoerd met de parameter. Vervang \<>, \<groepsnaam> en \<> met de namen voor uw nieuwe Git-app, de Azure-brongroep en het Azure App Service-abonnement.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Een van de opdrachten `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`retourneert een URL als: . Gebruik deze URL om uw app in de volgende stap te implementeren.

In plaats van deze URL op accountniveau te gebruiken, u ook lokale Git inschakelen met behulp van referenties op app-niveau. Azure App Service genereert deze referenties automatisch voor elke app. 

Haal de app-referenties op door de volgende opdracht uit te voeren in de Cloud Shell. Vervang \<de> \<en groepsnaam van de app> door de naam van uw app en de naam van de Azure-brongroep.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Gebruik de URL die terugkeert om uw app in de volgende stap te implementeren.

### <a name="deploy-the-web-app"></a>De web-app implementeren

1. Open een lokaal terminalvenster voor uw lokale Git-opslagplaats en voeg een Azure-afstandsbediening toe. Vervang in de \<volgende opdracht url-> door de gebruikersspecifieke URL of app-specifieke URL die u van de vorige stap hebt gekregen.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Duw naar de `git push azure master`Azure-afstandsbediening met . 
   
1. Voer in het venster **Git Credential Manager** het wachtwoord van uw [implementatiegebruiker](#configure-a-deployment-user)in, niet het azure-aanmeldingswachtwoord.
   
1. Controleer de uitvoer. Mogelijk ziet u runtime-specifieke automatisering, zoals `npm install` MSBuild voor ASP.NET, `pip install` voor Node.js en voor Python. 
   
1. Blader naar uw app in de Azure-portal om te controleren of de inhoud is geïmplementeerd.

## <a name="deploy-with-azure-pipelines-builds"></a>Implementeren met Azure Pipelines-builds

Als uw account over de benodigde machtigingen beschikt, u Azure Pipelines (Preview) instellen om de lokale Git-implementatie voor uw app in te schakelen. 

- Uw Azure-account moet machtigingen hebben om naar Azure Active Directory te schrijven en een service te maken. 
  
- Uw Azure-account moet de **rol Eigenaar** hebben in uw Azure-abonnement.

- U moet een beheerder zijn in het Azure DevOps-project dat u wilt gebruiken.

Lokale Git-implementatie voor uw app inschakelen met Azure Pipelines (Voorbeeld:

1. Zoek in de [Azure-portal](https://portal.azure.com)naar en selecteer **App Services**. 

1. Selecteer uw Azure App Service-app en selecteer **Implementatiecentrum** in het linkermenu.
   
1. Selecteer op de pagina **Implementatiecentrum** de optie **Lokale Git**en selecteer **Doorgaan**. 
   
   ![Selecteer Lokale Git en selecteer Doorgaan](media/app-service-deploy-local-git/portal-enable.png)
   
1. Selecteer op de pagina Azure **Pipelines (Preview)** op **de** pagina Azure Pipelines en selecteer **Doorgaan**. 
   
   ![Selecteer Azure Pipelines (Voorbeeld) en selecteer Doorgaan.](media/app-service-deploy-local-git/pipeline-builds.png)

1. Configureer op de pagina **Configureren** een nieuwe Azure DevOps-organisatie of geef een bestaande organisatie op en selecteer **Doorgaan**.
   
   > [!NOTE]
   > Als uw bestaande Azure DevOps-organisatie niet wordt vermeld, moet u deze mogelijk koppelen aan uw Azure-abonnement. Zie [Uw cd-releasepijplijn definiëren](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)voor meer informatie .
   
1. Afhankelijk van de [prijscategorie app-service-abonnement](https://azure.microsoft.com/pricing/details/app-service/plans/)ziet u mogelijk een pagina **Implementeren naar fasering.** Kies of [u implementatieruimten wilt inschakelen](deploy-staging-slots.md)en selecteer **Doorgaan**.
   
1. Controleer **op** de pagina Overzicht de instellingen en selecteer **Voltooien**.
   
1. Wanneer de Azure Pipeline klaar is, kopieert u de URL van de Git-opslagplaats vanaf de pagina **Implementatiecentrum** om in de volgende stap te gebruiken. 
   
   ![De URL van de Git-opslagplaats kopiëren](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. Voeg in uw lokale terminalvenster een Azure-afstandsbediening toe aan uw lokale Git-opslagplaats. Vervang in de \<opdracht url> door de URL van de Git-repository die u van de vorige stap hebt gekregen.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Duw naar de `git push azure master`Azure-afstandsbediening met . 
   
1. Meld u op de pagina **Git Credential Manager** aan met uw gebruikersnaam visualstudio.com. Zie [verificatieoverzicht azure DevOps Services](/vsts/git/auth-overview?view=vsts)voor andere verificatiemethoden voor andere verificatiemethoden voor verificatiemethoden voor Azure DevOps Services.
   
1. Zodra de implementatie is voltooid, `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`bekijkt u de `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`voortgang van de opbouw op en de voortgang van de implementatie op .
   
1. Blader naar uw app in de Azure-portal om te controleren of de inhoud is geïmplementeerd.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Problemen met implementatie oplossen

Mogelijk ziet u de volgende veelvoorkomende foutmeldingen wanneer u Git gebruikt om te publiceren naar een App Service-app in Azure:

|Bericht|Oorzaak|Oplossing
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|De app is niet actief.|Start de app in de Azure-portal. Git-implementatie is niet beschikbaar wanneer de web-app is gestopt.|
|`Couldn't resolve host 'hostname'`|De adresgegevens voor de afstandsbediening 'azure' zijn onjuist.|Gebruik `git remote -v` de opdracht om alle afstandsbedieningen weer te geven, samen met de bijbehorende URL. Controleer of de URL voor de afstandsbediening 'azure' juist is. Verwijder en maak deze afstandsbediening indien nodig opnieuw met de juiste URL.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|U hebt geen vertakking opgegeven tijdens `git push`, `push.default` of `.gitconfig`u hebt de waarde niet ingesteld in .|Voer `git push` opnieuw uit en geef `git push azure master`de hoofdvertakking op: .|
|`src refspec [branchname] does not match any.`|U probeerde te duwen naar een andere tak dan master op de 'azure' afstandsbediening.|Voer `git push` opnieuw uit en geef `git push azure master`de hoofdvertakking op: .|
|`RPC failed; result=22, HTTP code = 5xx.`|Deze fout kan optreden als u een grote git-repository via HTTPS probeert te pushen.|Wijzig de git-configuratie op de `postBuffer` lokale machine om de grotere machine te maken. Bijvoorbeeld: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|U hebt een Node.js-app geïmplementeerd met een _package.json-bestand_ dat extra vereiste modules opgeeft.|Controleer `npm ERR!` de foutberichten vóór deze fout voor meer context over de fout. De volgende oorzaken van deze fout en `npm ERR!` de bijbehorende berichten zijn de volgende oorzaken:<br /><br />**Misvormd package.json bestand:**`npm ERR! Couldn't read dependencies.`<br /><br />**Native module heeft geen binaire distributie voor Windows:**<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />of <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Aanvullende bronnen

- [Project Kudu documentatie](https://github.com/projectkudu/kudu/wiki)
- [Continue implementatie naar Azure App Service](deploy-continuous-deployment.md)
- [Voorbeeld: Een web-app maken en code implementeren vanuit een lokale Git-repository (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Voorbeeld: Een web-app maken en code implementeren vanuit een lokale Git-opslagplaats (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
