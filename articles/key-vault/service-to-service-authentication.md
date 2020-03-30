---
title: Service-to-service-verificatie naar Azure Key Vault met .NET
description: Gebruik de Microsoft.Azure.Services.AppAuthentication-bibliotheek om te verifiëren naar Azure Key Vault met behulp van .NET.
keywords: lokale referenties azure key-vault-verificatie
author: msmbaldwin
manager: rkarlin
services: key-vault
ms.author: mbaldwin
ms.date: 08/28/2019
ms.topic: conceptual
ms.service: key-vault
ms.subservice: general
ms.openlocfilehash: 6377edf72651c9cddf570d0c6db9d5ceb40409f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270886"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Service-to-service-verificatie naar Azure Key Vault met .NET

Als u wilt verifiëren voor Azure Key Vault, hebt u een Azure Active Directory-referentie (Azure AD) nodig, een gedeeld geheim of een certificaat.

Het beheren van dergelijke referenties kan moeilijk zijn. Het is verleidelijk om referenties te bundelen in een app door ze op te nemen in bron- of configuratiebestanden. De `Microsoft.Azure.Services.AppAuthentication` voor.NET-bibliotheek vereenvoudigt dit probleem. Het gebruikt de referenties van de ontwikkelaar om te verifiëren tijdens lokale ontwikkeling. Wanneer de oplossing later wordt geïmplementeerd in Azure, schakelt de bibliotheek automatisch over naar toepassingsreferenties. Het gebruik van ontwikkelaarsreferenties tijdens lokale ontwikkeling is veiliger omdat u geen Azure AD-referenties hoeft te maken of referenties tussen ontwikkelaars hoeft te delen.

De `Microsoft.Azure.Services.AppAuthentication` bibliotheek beheert de verificatie automatisch, waardoor u zich op zijn beurt concentreren op uw oplossing, in plaats van op uw referenties. Het ondersteunt lokale ontwikkeling met Microsoft Visual Studio, Azure CLI of Azure AD Integrated Authentication. Wanneer de bibliotheek wordt geïmplementeerd in een Azure-bron die een beheerde identiteit ondersteunt, gebruikt de bibliotheek automatisch [beheerde identiteiten voor Azure-resources.](../active-directory/msi-overview.md) Er zijn geen code- of configuratiewijzigingen vereist. De bibliotheek ondersteunt ook direct gebruik van Azure [AD-clientreferenties](../azure-resource-manager/resource-group-authenticate-service-principal.md) wanneer een beheerde identiteit niet beschikbaar is of wanneer de beveiligingscontext van de ontwikkelaar niet kan worden bepaald tijdens lokale ontwikkeling.

## <a name="prerequisites"></a>Vereisten

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) of [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- De app-verificatie-extensie voor Visual Studio, beschikbaar als aparte extensie voor Visual Studio 2017 Update 5 en gebundeld met het product in Update 6 en hoger. Met Update 6 of hoger u de installatie van de app-verificatieextensie verifiëren door Azure Development-hulpprogramma's te selecteren vanuit het installatieprogramma Visual Studio.

## <a name="using-the-library"></a>De bibliotheek gebruiken

Voor .NET-toepassingen is de eenvoudigste manier om `Microsoft.Azure.Services.AppAuthentication` met een beheerde identiteit te werken via het pakket. Stappen om aan de slag te gaan:

1. Selecteer **NuGet** > Package Manager > **Beheer NuGet-pakketten voor oplossing** om verwijzingen toe te voegen naar de [NuGet-pakketten microsoft.azure.services.appauthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) en [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet-pakketten aan uw project.**NuGet Package Manager**

1. Voeg de volgende code toe:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

De `AzureServiceTokenProvider` klasse slaat het token in het geheugen in de cache en haalt het vlak voor het verstrijken op uit Azure AD. U hoeft dus niet langer de vervaldatum `GetAccessTokenAsync` te controleren voordat u de methode aanroept. Bel de methode wanneer u het token wilt gebruiken.

De `GetAccessTokenAsync` methode vereist een resource-id. Zie [Wat wordt beheerd identiteiten voor Azure-bronnen](../active-directory/msi-overview.md)voor meer informatie over Microsoft Azure-services.

## <a name="local-development-authentication"></a>Verificatie voor lokale ontwikkeling

Voor lokale ontwikkeling zijn er twee primaire verificatiescenario's: [authenticeren naar Azure-services](#authenticating-to-azure-services)en [authenticeren naar aangepaste services.](#authenticating-to-custom-services)

### <a name="authenticating-to-azure-services"></a>Authenticeren naar Azure Services

Lokale machines ondersteunen geen beheerde identiteiten voor Azure-bronnen. Als gevolg hiervan `Microsoft.Azure.Services.AppAuthentication` gebruikt de bibliotheek uw ontwikkelaarsreferenties om te worden uitgevoerd in uw lokale ontwikkelomgeving. Wanneer de oplossing wordt geïmplementeerd in Azure, gebruikt de bibliotheek een beheerde identiteit om over te schakelen naar een oauth 2.0-clientreferentiesubsidiestroom. Deze aanpak betekent dat u dezelfde code lokaal en op afstand testen zonder zorgen.

Haal voor `AzureServiceTokenProvider` lokale ontwikkeling tokens op met **Visual Studio,** **Azure command-line interface** (CLI) of Azure AD Integrated **Authentication**. Elke optie wordt achtereenvolgens geprobeerd en de bibliotheek gebruikt de eerste optie die slaagt. Als er geen `AzureServiceTokenProviderException` optie werkt, wordt een uitzondering gegooid met gedetailleerde informatie.

#### <a name="authenticating-with-visual-studio"></a>Authenticeren met Visual Studio

Verifiëren met Visual Studio:

1. Meld u aan bij Visual Studio en gebruik **Extra**&nbsp;>&nbsp;**opties** om Opties te **openen.**

1. Selecteer **Azure Service Authentication,** kies een account voor lokale ontwikkeling en selecteer **OK**.

Als u problemen ondervindt bij het gebruik van Visual Studio, zoals fouten waarbij het tokenproviderbestand betrokken is, controleert u zorgvuldig de voorgaande stappen.

Mogelijk moet u uw ontwikkelaarstoken opnieuw verifiëren. Selecteer hiervoor&nbsp;>&nbsp;**Extraopties**en selecteer **vervolgens&nbsp;&nbsp;Azure Service Authentication**. **Tools** Zoek naar **een koppeling opnieuw verifiëren** onder het geselecteerde account. Selecteer deze optie om te verifiëren.

#### <a name="authenticating-with-azure-cli"></a>Authenticeren met Azure CLI

Als u Azure CLI wilt gebruiken voor lokale ontwikkeling, moet u ervoor zorgen dat u versie [Azure CLI v2.0.12](/cli/azure/install-azure-cli) of hoger hebt.

Ga als een gebruik van Azure CLI:

1. Zoek naar Azure CLI op de Windows-taakbalk om de **Microsoft Azure-opdrachtprompt**te openen.

1. Meld u aan bij de Azure-portal: *az-aanmelding* om u aan te melden bij Azure.

1. Controleer de toegang door het invoeren *van https://vault.azure.netaz-account get-access-token --resource *. Als u een fout ontvangt, controleert u of de juiste versie van Azure CLI correct is geïnstalleerd.

   Als Azure CLI niet is geïnstalleerd in de standaardmap, `AzureServiceTokenProvider` ontvangt u mogelijk een foutrapportage die het pad voor Azure CLI niet kan vinden. Gebruik de **azureclipath-omgevingsvariabele** om de azure CLI-installatiemap te definiëren. `AzureServiceTokenProvider`hiermee wordt de map toegevoegd die is opgegeven in de **azureclipath-omgevingsvariabele** aan de variabele **Pad-omgeving** wanneer dat nodig is.

1. Als u bent aangemeld bij Azure CLI met meerdere accounts of als uw account toegang heeft tot meerdere abonnementen, moet u het te gebruiken abonnement opgeven. Voer het *opdrachtaz-accountset -abonnement in <abonnement-id>*.

Deze opdracht genereert alleen uitvoer bij uitval. Als u de instellingen voor `az account list`de huidige account wilt verifiëren, voert u de opdracht in .

#### <a name="authenticating-with-azure-ad-authentication"></a>Authenticeren met Azure AD-verificatie

Als u Azure AD-verificatie wilt gebruiken, controleert u of:

- Uw on-premises Active Directory synchroniseert met Azure AD. Zie Wat is hybride identiteit met Azure Active Directory voor meer [informatie.](../active-directory/connect/active-directory-aadconnect.md)

- Uw code wordt uitgevoerd op een computer die is verbonden met een domein.

### <a name="authenticating-to-custom-services"></a>Authenticeren naar aangepaste services

Wanneer een service Azure-services aanroept, werken de vorige stappen omdat Azure-services toegang bieden tot zowel gebruikers als toepassingen.

Wanneer u een service maakt die een aangepaste service aanroept, gebruikt u Azure AD-clientreferenties voor verificatie van lokale ontwikkeling. Er zijn twee opties:

- Gebruik een serviceprincipal om u aan te melden bij Azure:

    1. Een service-principal maken. Zie [Een Azure-serviceprincipal maken met Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli)voor meer informatie.

    1. Gebruik Azure CLI om u aan te melden met de volgende opdracht:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Omdat de serviceprincipal mogelijk geen toegang heeft `--allow-no-subscriptions` tot een abonnement, gebruikt u het argument.

- Gebruik omgevingsvariabelen om serviceprincipal details op te geven. Zie [De toepassing uitvoeren met een serviceprincipal](#running-the-application-using-a-service-principal)voor meer informatie.

Nadat u zich hebt aangemeld `AzureServiceTokenProvider` bij Azure, gebruikt u de serviceprincipal om een token voor lokale ontwikkeling op te halen.

Deze aanpak geldt alleen voor lokale ontwikkeling. Wanneer uw oplossing wordt geïmplementeerd in Azure, schakelt de bibliotheek over naar een beheerde identiteit voor verificatie.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>De toepassing uitvoeren met een beheerde identiteit of door de gebruiker toegewezen identiteit

Wanneer u uw code uitvoert op een Azure App Service of een Azure VM met een beheerde identiteit ingeschakeld, gebruikt de bibliotheek automatisch de beheerde identiteit. Er zijn geen codewijzigingen vereist, maar de beheerde identiteit moet machtigingen *hebben voor* de sleutelkluis. U de beheerde *identiteitsverkrijgingsmachtigingen* geven via het *toegangsbeleid van*de sleutelkluis.

U ook verifiëren met een door de gebruiker toegewezen identiteit. Zie [Over beheerde identiteiten voor Azure-resources voor](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)meer informatie over door gebruikers toegewezen identiteiten. Als u wilt verifiëren met een door de gebruiker toegewezen identiteit, moet u de client-id van de door de gebruiker toegewezen identiteit opgeven in de verbindingstekenreeks. De verbindingstekenreeks is opgegeven in [ondersteuning voor verbindingstekenreeks](#connection-string-support).

## <a name="running-the-application-using-a-service-principal"></a>De toepassing uitvoeren met een serviceprincipal

Het kan nodig zijn om een Azure AD-clientreferentie te maken om te verifiëren. Deze situatie kan zich voordoen in de volgende voorbeelden:

- Uw code wordt uitgevoerd op een lokale ontwikkelomgeving, maar niet onder de identiteit van de ontwikkelaar. Service Fabric maakt bijvoorbeeld gebruik van het [NetworkService-account](../service-fabric/service-fabric-application-secret-management.md) voor lokale ontwikkeling.

- Uw code wordt uitgevoerd op een lokale ontwikkelomgeving en u verifieert een aangepaste service, zodat u uw ontwikkelaarsidentiteit niet gebruiken.

- Uw code wordt uitgevoerd op een Azure-compute resource die nog geen ondersteuning biedt voor beheerde identiteiten voor Azure-bronnen, zoals Azure Batch.

Er zijn drie primaire methoden voor het gebruik van een Service Principal om uw toepassing uit te voeren. Als u een van deze producten wilt gebruiken, moet u eerst een serviceprincipal maken. Zie [Een Azure-serviceprincipal maken met Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli)voor meer informatie.

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Een certificaat gebruiken in de lokale keystore om u aan te melden bij Azure AD

1. Maak een serviceprincipal certificaat met de opdracht Azure CLI [az ad sp create-for-rbac.](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Met deze opdracht wordt een .pem-bestand (privésleutel) gemaakt dat is opgeslagen in uw thuismap. Implementeer dit certificaat in het *LocalMachine-* of *CurrentUser-archief.*

    > [!Important]
    > De opdracht CLI genereert een .pem-bestand, maar Windows biedt alleen native ondersteuning voor PFX-certificaten. Als u in plaats daarvan een PFX-certificaat wilt genereren, gebruikt u de PowerShell-opdrachten die hier worden weergegeven: [Serviceprincipal maken met zelfondertekend certificaat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Deze opdrachten implementeren het certificaat ook automatisch.

1. Stel een omgevingsvariabele met de naam **AzureServicesAuthConnectionString in** op de volgende waarde:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    Vervang *{AppId}*, *{TenantId}* en *{Thumbprint}* door waarden die zijn gegenereerd in stap 1. Vervang *{CertificateStore}* door *LocalMachine*' of *CurrentUser*, op basis van uw implementatieplan.

1. Voer de toepassing uit.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Een gedeelde geheime referentie gebruiken om u aan te melden bij Azure AD

1. Maak een serviceprincipal certificaat met een wachtwoord met de Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) opdracht met de parameter --sdk-auth.

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. Stel een omgevingsvariabele met de naam **AzureServicesAuthConnectionString in** op de volgende waarde:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    Vervang _{AppId}_, _{TenantId}_ en _{ClientSecret}_ door waarden die zijn gegenereerd in stap 1.

1. Voer de toepassing uit.

Zodra alles correct is ingesteld, zijn er geen verdere codewijzigingen nodig. `AzureServiceTokenProvider`gebruikt de omgevingsvariabele en het certificaat om te verifiëren aan Azure AD.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Een certificaat in Key Vault gebruiken om u aan te melden bij Azure AD

Met deze optie u het clientcertificaat van een serviceprincipal opslaan in Key Vault en deze gebruiken voor serviceprincipal-verificatie. U deze optie gebruiken voor de volgende scenario's:

- Lokale verificatie, waarbij u wilt verifiëren met behulp van een expliciete serviceprincipal en de hoofdreferenties van de service veilig in een sleutelkluis wilt bewaren. Ontwikkelaarsaccount moet toegang hebben tot de sleutelkluis.

- Verificatie vanuit Azure waarbij u expliciete referenties wilt gebruiken en de hoofdreferenties van de service veilig in een sleutelkluis wilt bewaren. U deze optie gebruiken voor een scenario voor meerdere huurders. Beheerde identiteit moet toegang hebben tot de sleutelkluis.

De beheerde identiteit of uw ontwikkelaarsidentiteit moet toestemming hebben om het clientcertificaat uit de Key Vault op te halen. De AppAuthentication-bibliotheek gebruikt het opgehaalde certificaat als clientreferentie van de serviceprincipal.

Ga als lid van het clientcertificaat voor servicehoofdverificatie:

1. Maak een serviceprincipalcertificaat en sla het automatisch op in uw Key Vault. Gebruik de azure CLI [az ad sp create-for-rbac --keyvault \<keyvaultname> --cert \<certificatename> --create-cert --skip-assignment](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) command:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    De certificaat-id is een URL in de indeling`https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Vervang `{KeyVaultCertificateSecretIdentifier}` in deze verbindingstekenreeks door de certificaat-id:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Als uw sleutelkluis bijvoorbeeld *myKeyVault* werd genoemd en u een certificaat hebt gemaakt met de naam *myCert,* zou de certificaat-id zijn:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>Ondersteuning voor verbindingstekenreeks

Gebruikt standaard `AzureServiceTokenProvider` meerdere methoden om een token op te halen.

Als u het proces wilt beheren, `AzureServiceTokenProvider` gebruikt u een verbindingstekenreeks die is doorgegeven aan de constructor of is opgegeven in de omgevingvariabele *AzureServicesAuthConnectionString.*

De volgende opties worden ondersteund:

| Optie Verbindingstekenreeks | Scenario | Opmerkingen|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Lokale ontwikkeling | `AzureServiceTokenProvider`gebruikt AzureCli om token te krijgen. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Lokale ontwikkeling | `AzureServiceTokenProvider`gebruikt Visual Studio om token te krijgen. |
| `RunAs=CurrentUser` | Lokale ontwikkeling | `AzureServiceTokenProvider`maakt gebruik van Azure AD Integrated Authentication om token te krijgen. |
| `RunAs=App` | [Beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider`gebruikt een beheerde identiteit om token te krijgen. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Door de gebruiker toegewezen identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | `AzureServiceTokenProvider`gebruikt een door de gebruiker toegewezen identiteit om token te krijgen. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Verificatie van aangepaste services | `KeyVaultCertificateSecretIdentifier`is de geheime id van het certificaat. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Service-principal | `AzureServiceTokenProvider`gebruikt certificaat om token te krijgen van Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Service-principal | `AzureServiceTokenProvider`gebruikt certificaat om token te krijgen van Azure AD|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Service-principal |`AzureServiceTokenProvider`gebruikt geheim om token van Azure AD te krijgen. |

## <a name="samples"></a>Voorbeelden

Als u `Microsoft.Azure.Services.AppAuthentication` de bibliotheek in actie wilt zien, raadpleegt u de volgende codevoorbeelden.

- [Een beheerde identiteit gebruiken om een geheim op te halen uit Azure Key Vault tijdens runtime](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [Een Azure Resource Manager-sjabloon programmatisch implementeren vanuit een Azure VM met een beheerde identiteit](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

- [Gebruik .NET Core-voorbeeld en een beheerde identiteit om Azure-services aan te roepen vanaf een Azure Linux VM](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="appauthentication-troubleshooting"></a>Problemen met app-verificatie

### <a name="common-issues-during-local-development"></a>Veelvoorkomende problemen tijdens lokale ontwikkeling

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>Azure CLI is niet geïnstalleerd, u bent niet ingelogd of u hebt niet de nieuwste versie

Voer *het AZ-account get-access-token* uit om te zien of Azure CLI een token voor u toont. Als er **staat geen dergelijk programma gevonden,** installeer de nieuwste versie van de Azure [CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). U wordt mogelijk gevraagd u aan te melden.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider kan het pad voor Azure CLI niet vinden

AzureServiceTokenProvider zoekt Azure CLI op de standaardinstallatielocaties. Als Azure CLI niet kan worden gevonden, stelt u **azureCLIPath** in op de installatiemap Azure CLI. AzureServiceTokenProvider voegt de omgevingsvariabele toe aan de variabele Pad-omgeving.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>U bent aangemeld bij Azure CLI met meerdere accounts, hetzelfde account heeft toegang tot abonnementen in meerdere tenants of u krijgt een fout toegang geweigerd wanneer u probeert te bellen tijdens lokale ontwikkeling

Stel met Azure CLI het standaardabonnement in op een abonnement met het account dat u wilt gebruiken. Het abonnement moet zich in dezelfde tenant begeven als de resource die u wilt openen: **az-accountset --abonnement [subscription-id]**. Als er geen output wordt gezien, is het gelukt. Controleer of het juiste account nu de standaard is met de lijst met **AZ-accounts**.

### <a name="common-issues-across-environments"></a>Veelvoorkomende problemen in verschillende omgevingen

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>Ongeautoriseerde toegang, geweigerde toegang, verboden of soortgelijke fout

De gebruikte principal heeft geen toegang tot de resource waartoe deze toegang probeert te krijgen. Geef uw gebruikersaccount of de MSI-bijdrager van de App-service toegang tot een bron. Welke afhankelijk is van of u het voorbeeld uitvoert op uw lokale computer of wordt geïmplementeerd in Azure naar uw App Service. Sommige bronnen, zoals sleutelkluizen, hebben ook hun eigen [toegangsbeleid](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) dat u gebruikt, verleent toegang tot principals, zoals gebruikers, apps en groepen.

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Veelvoorkomende problemen bij implementatie in Azure App Service

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>Beheerde identiteit is niet ingesteld op de App-service

Controleer de omgevingsvariabelen MSI_ENDPOINT en MSI_SECRET bestaan met behulp van [Kudu debug console](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/). Als deze omgevingsvariabelen niet bestaan, is Managed Identity niet ingeschakeld in de App-service.

### <a name="common-issues-when-deployed-locally-with-iis"></a>Veelvoorkomende problemen wanneer deze lokaal worden geïmplementeerd met IIS

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Kan geen tokens ophalen bij het debuggen van de app in IIS

AppAuth wordt standaard uitgevoerd in een andere gebruikerscontext in IIS. Daarom heeft het geen toegang om je ontwikkelaarsidentiteit te gebruiken om toegangstokens op te halen. U IIS configureren om met uw gebruikerscontext uit te voeren met de volgende twee stappen:
- Configureer de groep toepassingen voor de web-app om te worden uitgevoerd als uw huidige gebruikersaccount. Zie meer informatie [hier](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)
- Configureer 'setProfileEnvironment' op 'True'. Zie meer informatie [hier](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration). 

    - Ga naar %windir%\System32\inetsrv\config\applicationHost.config
    - Zoek naar "setProfileEnvironment". Als het is ingesteld op "False", verander het in "True". Als het niet aanwezig is, voegt u het toe/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironmentals een kenmerk aan het element processModel (), en stelt u het in op 'Waar'.

- Meer informatie over [beheerde identiteiten voor Azure-bronnen](../active-directory/managed-identities-azure-resources/index.yml).
- Meer informatie over [Azure AD-verificatiescenario's](../active-directory/develop/active-directory-authentication-scenarios.md).
