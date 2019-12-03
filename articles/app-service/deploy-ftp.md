---
title: Inhoud implementeren met FTP/S
description: Meer informatie over het implementeren van uw app naar Azure App Service met FTP of FTPS. Verbeter de beveiliging van websites door onversleutelde FTP uit te scha kelen.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 09/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: bccf4fa0b17f261d41c0a80d9f75fe391f591bfb
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671728"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Uw app implementeren op Azure App Service met behulp van FTP/S

In dit artikel leest u hoe u FTP of FTPS gebruikt om uw web-app, back-end voor mobiele apps of API-apps te implementeren in [Azure app service](https://go.microsoft.com/fwlink/?LinkId=529714).

Het FTP/S-eind punt voor uw app is al actief. Er is geen configuratie nodig om de implementatie van FTP/S in te scha kelen.

## <a name="open-ftp-dashboard"></a>FTP-dash board openen

Open de [resource pagina](../azure-resource-manager/manage-resources-portal.md#manage-resources)van uw app In de [Azure Portal](https://portal.azure.com).

Om het FTP-dash board te openen, klikt u op **Deployment Center** > **FTP** > **dash board**.

![FTP-dash board openen](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>FTP-verbindings gegevens ophalen

Klik in het FTP-dash board op **kopiëren** om het FTPS-eind punt en de app-referenties te kopiëren.

![FTP-gegevens kopiëren](./media/app-service-deploy-ftp/ftp-dashboard.png)

Het is raadzaam om app- **referenties** te gebruiken voor het implementeren van uw app, omdat deze uniek is voor elke app. Als u echter op **gebruikers referenties**klikt, kunt u referenties op gebruikers niveau instellen die u voor FTP/S-aanmelding kunt gebruiken voor alle app service-apps in uw abonnement.

> [!NOTE]
> Verificatie bij een FTP-FTPS-eind punt met referenties op gebruikers niveau vereist een gebruikers naam in de volgende indeling: 
>
>`<app-name>\<user-name>`
>
> Omdat referenties op gebruikers niveau zijn gekoppeld aan de gebruiker en niet aan een specifieke resource, moet de gebruikers naam de volgende indeling hebben om de aanmeldings actie door te sturen naar het juiste app-eind punt.
>

## <a name="deploy-files-to-azure"></a>Bestanden implementeren in azure

1. Gebruik op uw FTP-client (bijvoorbeeld [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/)of [WinSCP](https://winscp.net/index.php)) de verbindings gegevens die u hebt verzameld om verbinding te maken met uw app.
2. Kopieer uw bestanden en hun bijbehorende mapstructuur naar de [ **/site/wwwroot** -map](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) in azure (of de **/site/wwwroot/App_Data** Directory voor webjobs).
3. Blader naar de URL van uw app om te controleren of de app correct wordt uitgevoerd. 

> [!NOTE] 
> In tegens telling tot [op Git gebaseerde implementaties](deploy-local-git.md)biedt FTP-implementatie geen ondersteuning voor de volgende implementatie Automatiseringen: 
>
> - afhankelijkheden herstellen (zoals NuGet, NPM, PIP en Composer automatisering)
> - compilatie van .NET binaire bestanden
> - het genereren van web. config (hier is een [node. js-voor beeld](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Genereer deze benodigde bestanden hand matig op uw lokale machine en implementeer ze vervolgens samen met uw app.
>

## <a name="enforce-ftps"></a>FTPS afdwingen

Voor een betere beveiliging moet u alleen FTP via SSL toestaan. U kunt ook FTP-en FTPS uitschakelen als u geen FTP-implementatie gebruikt.

Selecteer in de pagina resource van uw [](https://portal.azure.com)app in Azure Portal **configuratie** > **algemene instellingen** in het linkernavigatievenster.

Als u niet-versleutelde FTP wilt uitschakelen, selecteert u **FTPS alleen** in **FTP-status**. Als u zowel FTP als FTPS volledig wilt uitschakelen, selecteert u **uitgeschakeld**. Klik op **Opslaan** als u klaar bent. Als u **alleen FTPS**gebruikt, moet u TLS 1,2 of hoger afdwingen door te navigeren naar de Blade **TLS/SSL-instellingen** van uw web-app. TLS 1,0 en 1,1 worden alleen ondersteund met **FTPS**.

![FTP/S uitschakelen](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatiseren met scripts

Zie [een web-app maken en bestanden implementeren met FTP (Azure CLI) voor een](./scripts/cli-deploy-ftp.md)FTP-implementatie met behulp van [Azure cli](/cli/azure).

Zie [bestanden uploaden naar een web-app met FTP (Power shell) voor een](./scripts/powershell-deploy-ftp.md)FTP-implementatie met behulp van [Azure PowerShell](/cli/azure).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Problemen met FTP-implementatie oplossen

- [Hoe kan ik problemen met FTP-implementatie oplossen?](#how-can-i-troubleshoot-ftp-deployment)
- [Ik kan niet FTP en mijn code publiceren. Hoe kan ik het probleem oplossen?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Hoe kan ik verbinding maken met FTP in Azure App Service via de passieve modus?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Hoe kan ik problemen met FTP-implementatie oplossen?

De eerste stap voor het oplossen van problemen met FTP-implementatie is het isoleren van een implementatie probleem van een runtime-toepassings probleem.

Een implementatie probleem leidt doorgaans ertoe dat er geen bestanden of verkeerde bestanden in uw app worden geïmplementeerd. U kunt problemen oplossen door uw FTP-implementatie te onderzoeken of door een alternatief pad voor de implementatie te selecteren (zoals broncode beheer).

Een runtime-toepassings probleem resulteert doorgaans in de juiste set met bestanden die zijn geïmplementeerd in uw app, maar een onjuist gedrag van de app. U kunt problemen oplossen door te focussen op code gedrag tijdens runtime en het onderzoeken van specifieke fout paden.

Zie implementatie-en [runtime problemen](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)als u een probleem met de implementatie of de uitvoering wilt vaststellen.

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Ik kan niet FTP en mijn code publiceren. Hoe kan ik het probleem oplossen?
Controleer of u de juiste hostnaam en [referenties](#open-ftp-dashboard)hebt ingevoerd. Controleer ook of de volgende FTP-poorten op uw computer niet zijn geblokkeerd door een firewall:

- Verbindingspoort voor FTP-besturingselement: 21
- FTP-gegevens verbindings poort: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Hoe kan ik verbinding maken met FTP in Azure App Service via de passieve modus?
Azure App Service ondersteunt verbinding via de actieve en passieve modus. De passieve modus verdient de voor keur omdat uw implementatie computers zich doorgaans achter een firewall bevinden (in het besturings systeem of als onderdeel van een thuis-of bedrijfs netwerk). Bekijk een [voor beeld in de WinSCP-documentatie](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Volgende stappen

Probeer [te implementeren naar Azure met git](deploy-local-git.md)voor meer geavanceerde implementatie scenario's. Met op Git gebaseerde implementatie naar Azure kunt u versie beheer, pakket herstel, MSBuild en meer.

## <a name="more-resources"></a>Meer informatiebronnen

* [Implementatie referenties Azure App Service](deploy-configure-credentials.md)
