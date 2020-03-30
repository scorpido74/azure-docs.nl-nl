---
title: Een beheertool voor Windows Virtual Desktop implementeren met serviceprincipal - Azure
description: Het beheerprogramma voor Windows Virtual Desktop implementeren met PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0838edb03c4868548f3d09f14d71ec7016e670a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127792"
---
# <a name="deploy-a-management-tool-with-powershell"></a>Een beheertool implementeren met PowerShell

In dit artikel ziet u hoe u de beheertool implementeert met PowerShell.

## <a name="important-considerations"></a>Belangrijke overwegingen

Het abonnement van elke Azure Active Directory -tenant (Azure AD) heeft een eigen afzonderlijke implementatie van het beheerprogramma nodig. Deze tool biedt geen ondersteuning voor B2B-scenario's (Azure AD Business-to-Business). 

Deze beheertool is een voorbeeld. Microsoft zal belangrijke beveiligings- en kwaliteitsupdates bieden. [De broncode is beschikbaar in GitHub.](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy) Of u nu een klant of partner bent, we raden u aan om de tool aan te passen aan uw zakelijke behoeften.

De volgende browsers zijn compatibel met het beheerprogramma:

- Google Chrome 68 of hoger
- Microsoft Edge 40.15063 of hoger
- Mozilla Firefox 52.0 of hoger
- Safari 10 of hoger (alleen macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Wat u nodig hebt om de beheertool te implementeren

Voordat u het beheerprogramma implementeert, hebt u een Azure AD-gebruiker (Azure Directory) nodig om een app-registratie te maken en de beheer-gebruikersinterface te implementeren. Deze gebruiker moet:

- Hebt toestemming om resources te maken in uw Azure-abonnement
- Heb toestemming om een Azure AD-toepassing te maken. Volg deze stappen om te controleren of uw gebruiker over de vereiste machtigingen beschikt door de instructies in [Vereiste machtigingen te](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)volgen.

Als u het beheerprogramma met succes wilt implementeren en configureren, moet u eerst de volgende PowerShell-scripts downloaden van de [GitHub-repo van RDS-sjablonen](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts) en deze opslaan in dezelfde map op uw lokale machine.

  - createWvdMgmtUxAppRegistration.ps1
  - updateWvdMgmtUxApiUrl.ps1

Nadat u de beheertool hebt geïmplementeerd en geconfigureerd, raden we u aan een gebruiker te vragen de beheer-gebruikersinterface te starten om ervoor te zorgen dat alles werkt. De gebruiker die de beheer-gebruikersinterface start, moet een roltoewijzing hebben waarmee hij of zij de Windows Virtual Desktop-tenant kan bekijken of bewerken.

## <a name="set-up-powershell"></a>PowerShell instellen

Ga aan de slag door u aan te melden bij zowel de AZ- als azure AD PowerShell-modules. U zich als volgen:

1. Open PowerShell als beheerder en navigeer naar de map waar u de PowerShell-scripts hebt opgeslagen.
2. Meld u aan bij Azure met een account met machtigingen voor eigenaar of inzender op het Azure-abonnement dat u wilt gebruiken om het beheerprogramma te maken door de volgende cmdlet uit te voeren:

    ```powershell
    Login-AzAccount
    ```

3. Voer de volgende cmdlet uit om u aan te melden bij Azure AD met hetzelfde account dat u voor de Az PowerShell-module hebt gebruikt:

    ```powershell
    Connect-AzureAD
    ```

4. Ga daarna naar de map waar u de twee PowerShell-scripts hebt opgeslagen in de GitHub-repo van RDS-sjablonen.

Houd het PowerShell-venster dat u hebt gebruikt om u aan te melden open om extra PowerShell-cmdlets uit te voeren terwijl u bent aangemeld.

## <a name="create-an-azure-active-directory-app-registration"></a>Een Azure Active Directory-app-registratie maken

Voer de volgende opdrachten uit om de app-registratie te maken met vereiste API-machtigingen:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$subscriptionId = Read-Host -Prompt "Enter the Azure subscription ID where you will be deploying the management tool."

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Nu u de azure AD-appregistratie hebt voltooid, u het beheerprogramma implementeren.

## <a name="deploy-the-management-tool"></a>Hulpprogramma voor beheer implementeren

Voer de volgende PowerShell-opdrachten uit om het beheerhulpprogramma te implementeren en te koppelen aan de serviceprincipal die u zojuist hebt gemaakt:
     
```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

Nadat u de web-app hebt gemaakt, moet u een omleidingsuri toevoegen aan de Azure AD-toepassing om gebruikers met succes aan te melden.

## <a name="set-the-redirect-uri"></a>De URI omleiden instellen

Voer de volgende PowerShell-opdrachten uit om de URL van de web-app op te halen en deze in te stellen als de verificatieomleiding URI (ook wel een antwoord-URL genoemd):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri  
```

Nu u een omleidingsURI hebt toegevoegd, moet u vervolgens de API-URL bijwerken, zodat het beheerprogramma kan communiceren met de API-backend-service.

## <a name="update-the-api-url-for-the-web-application"></a>De API-URL voor de webtoepassing bijwerken

Voer het volgende script uit om de API-URL-configuratie in de front-end van de webtoepassing bij te werken:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Nu u de web-app voor beheertool volledig hebt geconfigureerd, is het tijd om de Azure AD-toepassing te verifiëren en toestemming te geven.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>De Azure AD-toepassing verifiëren en toestemming geven

Ga als volgt te werk om de configuratie van de Azure AD-toepassings te verifiëren en toestemming te geven:

1. Open uw internetbrowser en meld u aan bij de [Azure-portal](https://portal.azure.com/) met uw beheerdersaccount.
2. Zoek in de zoekbalk boven aan de Azure-portal naar **App-registraties** en selecteer het item onder **Services**.
3. Selecteer **Alle toepassingen** en zoek op de unieke app-naam die u hebt opgegeven voor het PowerShell-script in Een Azure Active [Directory-app-registratie maken](#create-an-azure-active-directory-app-registration).
4. Selecteer in het deelvenster aan de linkerkant van de browser **Verificatie** en controleer of de omleidings-URI hetzelfde is als de URL van de web-app voor het beheerprogramma, zoals in de volgende afbeelding wordt weergegeven.
   
   [![De verificatiepagina met de](media/management-ui-redirect-uri-inline.png) ingevoerde omleidings-URI](media/management-ui-redirect-uri-expanded.png#lightbox)

5. Selecteer **api-machtigingen** in het linkerdeelvenster om te bevestigen dat er machtigingen zijn toegevoegd. Als u een globale beheerder bent, selecteert u de **knop Toestemming `tenantname` voor beheerders verlenen** en volgt u de dialoogvensterprompts om beheerderstoestemming voor uw organisatie te geven.
    
    [![De pagina](media/management-ui-permissions-inline.png) API-machtigingen](media/management-ui-permissions-expanded.png#lightbox)

U nu beginnen met het gebruik van de beheertool.

## <a name="use-the-management-tool"></a>Het beheergereedschap gebruiken

Nu u de beheertool op elk gewenst moment hebt ingesteld, u deze altijd en overal starten. Zo start je de tool:

1. Open de URL van de web-app in een webbrowser. Als u de URL niet meer weet, u zich aanmelden bij Azure, de app-service zoeken die u voor het beheerprogramma hebt geïmplementeerd en vervolgens de URL selecteren.
2. Meld u aan met uw Windows Virtual Desktop-referenties.
   
   > [!NOTE]
   > Als u geen toestemming van de beheerder hebt gegeven tijdens het configureren van de beheertool, moet elke gebruiker die zich aanmeldt, zijn eigen gebruikerstoestemming geven om de tool te kunnen gebruiken.

3. Wanneer u wordt gevraagd een tenantgroep te kiezen, selecteert u **Standaardtenantgroep** in de vervolgkeuzelijst.
4. Wanneer u **Standaardtenantgroep**selecteert, moet er een menu aan de linkerkant van het venster worden weergegeven. Zoek in dit menu de naam van uw tenantgroep en selecteer deze.
   
   > [!NOTE]
   > Als u een aangepaste tenantgroep hebt, voert u de naam handmatig in in plaats van te kiezen uit de vervolgkeuzelijst.

## <a name="report-issues"></a>Problemen melden

Als u problemen tegenkomt met het beheerprogramma of andere Windows Virtual Desktop-hulpprogramma's, volgt u de aanwijzingen in [Azure Resource Manager-sjablonen voor Extern bureaublad-services](https://github.com/Azure/RDS-Templates/blob/master/README.md) om deze op GitHub te rapporteren.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u de beheertool implementeren en verbinden, u leren hoe u Azure Service Health gebruiken om serviceproblemen en gezondheidsadviezen te controleren. Zie de [zelfstudie Servicewaarschuwingen instellen](./set-up-service-alerts.md)voor meer informatie.
