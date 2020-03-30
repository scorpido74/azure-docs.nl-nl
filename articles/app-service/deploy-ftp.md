---
title: Inhoud implementeren met FTP/S
description: Meer informatie over het implementeren van uw app naar Azure App Service met FTP of FTPS. Verbeter de beveiliging van de website door onversleutelde FTP uit te schakelen.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 09/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 7bc637b5719da3c5f5e5607436aa7da0721f5a9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266011"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Uw app implementeren voor Azure App Service met FTP/S

In dit artikel ziet u hoe u FTP of FTPS gebruiken om uw web-app, back-end voor mobiele apps of API-app te implementeren in [Azure App Service.](https://go.microsoft.com/fwlink/?LinkId=529714)

Het FTP/S-eindpunt voor uw app is al actief. Er is geen configuratie nodig om FTP/S-implementatie in te schakelen.

## <a name="open-ftp-dashboard"></a>FTP-dashboard openen

1. Zoek in de [Azure-portal](https://portal.azure.com)naar en selecteer **App Services**.

    ![Zoeken naar App-services.](media/app-service-continuous-deployment/search-for-app-services.png)

2. Selecteer de web-app die u wilt implementeren.

    ![Selecteer uw app.](media/app-service-continuous-deployment/select-your-app.png)

3. Selecteer > **FTP-dashboard** > **Dashboard** **van implementatiecentrum**.

    ![FTP-dashboard openen](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>FTP-verbindingsgegevens opvragen

Selecteer **kopiëren in** het FTP-dashboard om de FTPS-eindpunt- en app-referenties te kopiëren.

![FTP-informatie kopiëren](./media/app-service-deploy-ftp/ftp-dashboard.png)

Het wordt aanbevolen dat u **app-referenties** gebruikt om te implementeren in uw app, omdat deze uniek is voor elke app. Als u echter op **Gebruikersreferenties**klikt, u referenties op gebruikersniveau instellen die u gebruiken voor FTP/S-aanmelding bij alle App Service-apps in uw abonnement.

> [!NOTE]
> Als u een FTP/FTPS-eindpunt gebruikt met referenties op gebruikersniveau, moet u een gebruikersnaam in de volgende indeling gebruiken: 
>
>`<app-name>\<user-name>`
>
> Aangezien referenties op gebruikersniveau zijn gekoppeld aan de gebruiker en niet aan een specifieke bron, moet de gebruikersnaam in deze indeling staan om de aanmeldingsactie naar het juiste eindpunt van de app te leiden.
>

## <a name="deploy-files-to-azure"></a>Bestanden implementeren naar Azure

1. Gebruik vanuit uw FTP-client (bijvoorbeeld [Visual Studio,](https://www.visualstudio.com/vs/community/) [Cyberduck](https://cyberduck.io/)of [WinSCP)](https://winscp.net/index.php)de verbindingsgegevens die u hebt verzameld om verbinding te maken met uw app.
2. Kopieer uw bestanden en hun respectieve directorystructuur naar de [ **map /site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) in Azure (of de **/site/wwwroot/App_Data/Jobs/directory** voor WebJobs).
3. Blader naar de URL van uw app om te controleren of de app goed wordt uitgevoerd. 

> [!NOTE] 
> In tegenstelling tot [implementaties op basis van Git, biedt FTP-implementatie](deploy-local-git.md)geen ondersteuning voor de volgende implementatieautomatiseringen: 
>
> - afhankelijkheidsherstel (zoals NuGet, NPM, PIP en Composer-automatiseringen)
> - compilatie van .NET-binaries
> - generatie van web.config (hier is een [Node.js voorbeeld)](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps)
> 
> Genereer deze benodigde bestanden handmatig op uw lokale machine en implementeer ze vervolgens samen met uw app.
>

## <a name="enforce-ftps"></a>FTPS afdwingen

Voor verbeterde beveiliging moet u alleen FTP via SSL toestaan. U zowel FTP als FTPS ook uitschakelen als u geen FTP-implementatie gebruikt.

Selecteer **Configuratiealgemene** > **instellingen** van de linkernavigatie op de resourcepagina van uw app in [Azure-portal.](https://portal.azure.com)

Als u niet-versleutelde FTP wilt uitschakelen, selecteert u **ALLEEN FTP's** in **FTP-status**. Als u zowel FTP als FTPS volledig wilt uitschakelen, selecteert **u Uitgeschakeld**. Klik op **Opslaan** als u klaar bent. Als u **alleen FTPS gebruikt,** moet u TLS 1.2 of hoger afdwingen door naar het **tls/SSL-instellingenblad** van uw web-app te navigeren. TLS 1.0 en 1.1 worden niet ondersteund met **alleen FTPS.**

![FTP/S uitschakelen](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatiseren met scripts

Zie [Een web-app maken en bestanden implementeren met FTP (Azure CLI)](./scripts/cli-deploy-ftp.md)voor FTP-implementatie met [Azure CLI.](/cli/azure)

Zie [Bestanden uploaden naar een web-app met FTP (PowerShell)](./scripts/powershell-deploy-ftp.md)voor FTP-implementatie met [Azure PowerShell.](/cli/azure)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>FTP-implementatie oplossen

- [Hoe kan ik ftp-implementatie oplossen?](#how-can-i-troubleshoot-ftp-deployment)
- [Ik kan mijn code niet FTP en publiceren. Hoe kan ik het probleem oplossen?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Hoe kan ik verbinding maken met FTP in Azure App Service via de passieve modus?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Hoe kan ik ftp-implementatie oplossen?

De eerste stap voor het oplossen van FTP-implementatie is het isoleren van een implementatieprobleem van een runtime-toepassingsprobleem.

Een implementatieprobleem resulteert meestal in geen bestanden of verkeerde bestanden die naar uw app worden geïmplementeerd. U problemen oplossen door uw FTP-implementatie te onderzoeken of een alternatief implementatiepad te selecteren (zoals bronbeheer).

Een runtime-toepassingsprobleem resulteert meestal in de juiste set bestanden die naar uw app worden geïmplementeerd, maar onjuist app-gedrag. U problemen oplossen door u te concentreren op codegedrag tijdens runtime en specifieke foutpaden te onderzoeken.

Zie [Implementatie versus runtime-problemen](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)voor het bepalen van een probleem met de implementatie of runtime.

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Ik kan FTP niet gebruiken en mijn code niet publiceren. Hoe kan ik het probleem oplossen?
Controleer of u de juiste hostnaam en [referenties](#open-ftp-dashboard)hebt ingevoerd. Controleer ook of de volgende FTP-poorten op uw machine niet worden geblokkeerd door een firewall:

- Verbindingspoort voor FTP-besturingselement: 21
- FTP-gegevensverbindingspoort: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Hoe kan ik verbinding maken met FTP in Azure App Service via de passieve modus?
Azure App Service ondersteunt verbinding maken via zowel de actieve als de passieve modus. Passieve modus heeft de voorkeur omdat uw implementatiemachines zich meestal achter een firewall bevinden (in het besturingssysteem of als onderdeel van een thuis- of bedrijfsnetwerk). Zie een [voorbeeld uit de WinSCP-documentatie](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Volgende stappen

Probeer voor meer geavanceerde implementatiescenario's [implementeren in Azure met Git](deploy-local-git.md). Git-gebaseerde implementatie naar Azure maakt versiebeheer, pakketherstel, MSBuild en meer mogelijk.

## <a name="more-resources"></a>Meer bronnen

* [Referenties voor Azure App-service-implementatie](deploy-configure-credentials.md)
