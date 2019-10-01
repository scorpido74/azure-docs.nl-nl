---
title: Gebruikers migratie benaderingen in Azure Active Directory B2C
description: In dit artikel worden de basis-en geavanceerde concepten van de gebruikers migratie besproken met de Azure AD-Graph API en optioneel met behulp van Azure AD B2C aangepast beleid.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 20c8fbd1626adaf67b790d500d8bfdeff6f27841
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71702179"
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: Gebruikers migratie

Wanneer u uw ID-provider migreert naar Azure Active Directory B2C (Azure AD B2C), moet u mogelijk ook de gebruikers accounts migreren. In dit artikel wordt uitgelegd hoe u bestaande gebruikers accounts van een id-provider migreert naar Azure AD B2C. Het artikel is niet bedoeld om te worden gescripteerd, maar in plaats daarvan worden enkele scenario's beschreven. De ontwikkelaar is verantwoordelijk voor de geschiktheid van elke benadering.

## <a name="user-migration-flows"></a>Gebruikers migratie stromen

Met Azure AD B2C kunt u gebruikers migreren via de [Azure AD-Graph API][B2C-GraphQuickStart]. Het proces voor gebruikers migratie valt in twee stromen:

- **Pre-migratie**: Deze stroom is van toepassing wanneer u een duidelijke toegang hebt tot de referenties van een gebruiker (gebruikers naam en wacht woord) of de referenties zijn versleuteld, maar u kunt ze ontsleutelen. Het proces vóór de migratie omvat het lezen van de gebruikers van de oude ID-provider en het maken van nieuwe accounts in de Azure AD B2C Directory.

- **Vóór de migratie en het opnieuw instellen van het wacht woord**: Deze stroom is van toepassing wanneer het wacht woord van een gebruiker niet toegankelijk is. Bijvoorbeeld:
  - Het wacht woord wordt opgeslagen in HASH-indeling.
  - Het wacht woord wordt opgeslagen in een id-provider waartoe u geen toegang hebt. Uw oude ID-provider valideert de gebruikers referenties door een webservice aan te roepen.

In beide stromen voert u eerst het proces voorafgaand aan de migratie uit, leest u de gebruikers van uw oude ID-provider en maakt u nieuwe accounts in de Azure AD B2C Directory. Als u het wacht woord niet hebt, maakt u het account met behulp van een wacht woord dat wille keurig wordt gegenereerd. Vervolgens vraagt u de gebruiker om het wacht woord te wijzigen of wanneer de gebruiker zich voor de eerste keer aanmeldt, Azure AD B2C de gebruiker wordt gevraagd om deze opnieuw in te stellen.

## <a name="password-policy"></a>Wachtwoordbeleid

Het Azure AD B2C wachtwoord beleid (voor lokale accounts) is gebaseerd op het Azure AD-beleid. In de beleids regels voor aanmelden of aanmelden en het beleid voor Azure AD B2C het opnieuw instellen van het wacht woord wordt gebruikgemaakt van de sterke wachtwoord sterkte en verlopen wacht woorden niet. Zie voor meer informatie [Azure AD-wachtwoord beleid][AD-PasswordPolicies].

Als de accounts die u wilt migreren een zwakkere wachtwoord sterkte hebben dan de [sterke wachtwoord sterkte die wordt afgedwongen door Azure AD B2C][AD-PasswordPolicies], kunt u de vereiste voor sterke wacht woorden uitschakelen. Als u het standaard wachtwoord beleid wilt wijzigen, stelt u de eigenschap `passwordPolicies` in op `DisableStrongPassword`. U kunt de aanvraag voor het maken van een gebruiker bijvoorbeeld als volgt wijzigen:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>Stap 1: Azure AD Graph API gebruiken om gebruikers te migreren

U maakt het Azure AD B2C gebruikers account via Graph API (met het wacht woord of met een wille keurig wacht woord). In deze sectie wordt het proces voor het maken van gebruikers accounts in de Azure AD B2C Directory beschreven met behulp van Graph API.

### <a name="step-11-register-your-application-in-your-tenant"></a>Stap 1,1: Uw toepassing registreren in uw Tenant

Als u met de Graph API wilt communiceren, moet u eerst een service account met beheerders bevoegdheden hebben. In azure AD registreert u een toepassing en schakelt u schrijf toegang in voor de map. De referenties van de toepassing zijn de **toepassings-id** en het **toepassings geheim**. De toepassing fungeert als een gebruiker om de Graph API aan te roepen.

Registreer eerst een toepassing die u kunt gebruiken voor beheer taken zoals gebruikers migratie.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="step-12-grant-administrative-permission-to-your-application"></a>Stap 1,2: Beheer machtigingen verlenen aan uw toepassing

Vervolgens verleent u de toepassing de Azure AD-Graph API machtigingen die zijn vereist voor het schrijven naar de map.

1. Selecteer in het menu **instellingen** de optie **vereiste machtigingen**.
1. Selecteer **Windows Azure Active Directory**.
1. Selecteer in het deel venster **toegang inschakelen** onder **toepassings machtigingen**de optie **Lees-en schrijf Directory gegevens**en selecteer vervolgens **Opslaan**.
1. Selecteer in het deel venster **vereiste machtigingen** de optie **verlenen machtigingen**en selecteer vervolgens **Ja**.

   ![Selectie vakje Directory lezen/schrijven, opslaan en machtigingen verlenen gemarkeerd](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)

### <a name="step-13-create-the-application-secret"></a>Stap 1,3: Het toepassings geheim maken

Maak een client geheim (sleutel) voor gebruik door de gebruikers migratie toepassing die u in een latere stap hebt geconfigureerd.

1. Selecteer op de pagina **geregistreerde app** de optie **instellingen**.
1. Selecteer **Sleutels**.
1. Voeg onder **wacht woorden**een nieuwe sleutel toe (ook wel een client geheim genoemd) met de naam *MyClientSecret* of een andere naam van uw keuze, selecteer een verloop venster, selecteer **Opslaan**en kopieer de sleutel waarde voor later gebruik.

    ![De waarde van de toepassings-ID en sleutels menu-item zijn gemarkeerd in Azure Portal](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)

U hebt nu een toepassing met machtigingen om gebruikers in uw Azure AD B2C-Tenant te maken, te lezen en bij te werken.

### <a name="step-14-optional-environment-cleanup"></a>Stap 1,4: Beschrijving Omgeving opschonen

De machtiging voor het *lezen en schrijven van Directory gegevens* bevat *niet* het recht om gebruikers te verwijderen. Als u uw toepassing de mogelijkheid wilt geven om gebruikers te verwijderen (om uw omgeving op te schonen), moet u een extra stap uitvoeren, waarbij Power shell wordt uitgevoerd om beheerders machtigingen voor gebruikers accounts in te stellen. Als dat niet het geval is, kunt u door gaan naar de volgende sectie.

> [!IMPORTANT]
> U moet een B2C-Tenant beheerders account gebruiken dat *lokaal* is voor de B2C-Tenant. De syntaxis van de account naam is *admin\@contosob2c.onmicrosoft.com*.

In dit Power shell-script, waarvoor de [Azure AD Power shell V2-module][AD-Powershell]vereist is, gaat u als volgt te werk:

1. Maak verbinding met uw online service. U doet dit door de `Connect-AzureAD`-cmdlet uit te voeren op de Windows Power shell-opdracht prompt en uw referenties op te geven.

1. Gebruik de **toepassings-id** om de toepassing de beheerdersrol van het gebruikers account toe te wijzen. Deze rollen hebben bekende id's, dus u hoeft alleen maar uw **toepassings-id** in het script in te voeren.

```powershell
# NOTE: This script REQUIRES the Azure AD PowerShell V2 module
#       https://docs.microsoft.com/powershell/azure/active-directory/install-adv2

Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

Wijzig de waarde voor @no__t 0 met de ID van uw Azure AD **-toepassing**.

## <a name="step-2-pre-migration-application-sample"></a>Stap 2: Voor beeld van toepassingen vóór de migratie

U vindt het voor beeld van de vooraf-migratie code in de door de Community bijgehouden `azure-ad-b2c/user-migration` GitHub-opslag plaats:

[Azure-AD-B2C/User-Migration/premigratie][UserMigrationSample-code] (github)

### <a name="step-21-edit-the-migration-data-file"></a>Stap 2.1: Het bestand met migratie gegevens bewerken

De voor beeld-app maakt gebruik van een JSON-bestand dat dummy gebruikers gegevens bevat. Nadat u het voor beeld hebt uitgevoerd, kunt u de code wijzigen om de gegevens uit uw eigen data base te gebruiken. Of u kunt het gebruikers profiel exporteren naar een JSON-bestand en vervolgens de app instellen op het gebruik van dat bestand.

Als u het JSON-bestand wilt bewerken, opent u de `AADB2C.UserMigration.sln` Visual Studio-oplossing. Open in het project `AADB2C.UserMigration` het bestand `UsersData.json`.

![Onderdeel van UsersData. JSON-bestand met JSON-blokken van twee gebruikers](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Zoals u kunt zien, bevat het bestand een lijst met gebruikers entiteiten. Elke gebruikers entiteit heeft de volgende eigenschappen:

- email
- displayName
- firstName
- lastName
- wacht woord (kan leeg zijn)

> [!NOTE]
> Tijdens de compilatie kopieert Visual Studio het bestand naar de map `bin`.

### <a name="step-22-configure-the-application-settings"></a>Stap 2.2: De toepassings instellingen configureren

Open het bestand *app. config* onder het project `AADB2C.UserMigration`. Vervang de volgende app-instellingen door uw eigen waarden:

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Client Secret Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
</appSettings>
```

> [!NOTE]
> - Het gebruik van een Azure-tabel connection string wordt beschreven in de volgende secties.
> - De naam van uw B2C-Tenant is het domein dat u hebt opgegeven tijdens het maken van de Tenant en wordt weer gegeven in de Azure Portal. De naam van de Tenant eindigt meestal met het achtervoegsel *. onmicrosoft.com* (bijvoorbeeld *contosob2c.onmicrosoft.com*).

### <a name="step-23-run-the-pre-migration-process"></a>Stap 2,3: Het proces voorafgaand aan migratie uitvoeren

Klik met de rechter muisknop op de oplossing @no__t 0 en bouw het voor beeld opnieuw. Als dat lukt, hebt u nu een `UserMigration.exe` uitvoerbaar bestand in. `AADB2C.UserMigration\bin\Debug\net461` Als u het migratie proces wilt uitvoeren, gebruikt u een van de volgende opdracht regel parameters:

- Als u **gebruikers met een wacht woord wilt migreren**, gebruikt u de opdracht `UserMigration.exe 1`.

- Als u **gebruikers met een wille keurig wacht woord wilt migreren**, gebruikt u de opdracht `UserMigration.exe 2`. Met deze bewerking wordt ook een Azure Table-entiteit gemaakt. Later configureert u het beleid om de REST API-service aan te roepen. De service gebruikt een Azure-tabel om het migratie proces bij te houden en te beheren.

![Opdracht prompt venster met de uitvoer van de opdracht UserMigration. exe](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Stap 2,4: Het proces voorafgaand aan migratie controleren

Als u de migratie wilt valideren, gebruikt u een van de volgende twee methoden:

- Als u wilt zoeken naar een gebruiker op weergave naam, gebruikt u de Azure Portal:

   1. Open **Azure AD B2C**en selecteer vervolgens **gebruikers**.
   1. Typ in het zoekvak de weergave naam van de gebruiker en Bekijk het profiel van de gebruiker.

- Als u een gebruiker wilt ophalen op basis van het e-mail adres voor aanmelden, gebruikt u deze voorbeeld toepassing:

   1. Voer de volgende opdracht uit:

      ```Console
          UserMigration.exe 3 {email address}
      ```

      > [!TIP]
      > U kunt ook een gebruiker ophalen op basis van de weergave naam met behulp van de volgende opdracht: `UserMigration.exe 4 "<Display name>"`.

   1. Open het bestand UserProfile. json in een JSON-editor om de informatie van de gebruiker weer te geven.

      ![UserProfile. JSON-bestand geopend in Visual Studio code-editor](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>Stap 2,5: Beschrijving Omgeving opschonen

Als u uw Azure AD-Tenant wilt opschonen en gebruikers uit de Azure AD-adres lijst wilt verwijderen, voert u de `UserMigration.exe 5`-opdracht uit.

> [!NOTE]
> * Configureer de beheerders machtigingen voor gebruikers accounts voor uw toepassing om uw Tenant op te schonen.
> * Met de voor beeld-app voor migratie worden alle gebruikers opgeschoond die worden vermeld in het JSON-bestand.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Stap 2,6: Aanmelden met gemigreerde gebruikers (met een wacht woord)

Nadat u het proces vóór migratie hebt uitgevoerd met gebruikers wachtwoorden, zijn de accounts klaar voor gebruik en kunnen gebruikers zich aanmelden bij uw toepassing met behulp van Azure AD B2C. Als u geen toegang hebt tot wacht woorden van gebruikers, gaat u verder met de volgende sectie.

## <a name="step-3-help-users-reset-their-password"></a>Stap 3: Gebruikers helpen hun wacht woord opnieuw in te stellen

Als u gebruikers met een wille keurig wacht woord migreert, moeten ze hun wacht woord opnieuw instellen. Om hen te helpen het wacht woord opnieuw in te stellen, stuurt u een welkomst bericht met een koppeling om het wacht woord opnieuw in te stellen.

Voer de volgende stappen uit om de koppeling naar het beleid voor het opnieuw instellen van het wacht woord op te halen. Bij deze procedure wordt ervan uitgegaan dat u eerder een [aangepast beleid](active-directory-b2c-get-started-custom.md)voor het opnieuw instellen van een wacht woord hebt gemaakt.

1. Selecteer de map met uw Azure AD B2C-Tenant met behulp van het filter **Directory +-abonnement** in de rechter bovenhoek van de [Azure Portal](https://portal.azure.com).
1. Selecteer **Azure AD B2C** in het linkermenu (of vanuit **alle services**).
1. Onder **beleids regels**selecteert u **identiteits ervaring-Framework**.
1. Selecteer het beleid voor het opnieuw instellen van het wacht woord. Bijvoorbeeld *B2C_1A_PasswordReset*.
1. Selecteer uw toepassing in de vervolg keuzelijst **toepassing selecteren** .

    > [!NOTE]
    > Voor **nu uitvoeren** moet ten minste één toepassing worden geregistreerd in uw Tenant. Zie [Tutorial voor meer informatie over het registreren van toepassingen: Een toepassing registreren in Azure Active Directory B2C @ no__t-0.

1. Kopieer de URL die wordt weer gegeven in het tekstvak **nu uitvoeren eind punt** en verzend deze naar uw gebruikers.

    ![De pagina beleid voor het opnieuw instellen van wacht woorden met eind punt nu uitvoeren gemarkeerd](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Stap 4: Beschrijving Wijzig uw beleid om de status van de gebruikers migratie te controleren en in te stellen

> [!NOTE]
> Als u de gebruikers migratie status wilt controleren en wijzigen, moet u een aangepast beleid gebruiken. De instructies voor het instellen van [aan de slag met aangepast beleid][B2C-GetStartedCustom] moeten zijn voltooid.

Wanneer gebruikers zich proberen aan te melden zonder het wacht woord eerst opnieuw in te stellen, moet uw beleid een vriendelijk fout bericht retour neren. Bijvoorbeeld:

> *Your-wacht woord is verlopen. Als u het opnieuw wilt instellen, selecteert u de koppeling wacht woord opnieuw instellen.*

Deze optionele stap vereist het gebruik van Azure AD B2C aangepast beleid, zoals beschreven in het artikel aan de slag [met aangepaste beleids regels][B2C-GetStartedCustom] .

In deze sectie wijzigt u het beleid om de gebruikers migratie status bij het aanmelden te controleren. Als de gebruiker het wacht woord niet heeft gewijzigd, wordt een HTTP 409-fout bericht weer gegeven waarin de gebruiker wordt gevraagd om het **wacht woord verg eten** te selecteren.

U kunt een Azure-tabel gebruiken om de wijziging van het wacht woord bij te houden. Wanneer u het proces voorafgaand aan de migratie uitvoert met de opdracht regel parameter `2`, maakt u een gebruikers entiteit in een Azure-tabel. Uw service doet het volgende:

- Bij het aanmelden wordt de REST van de migratie service door het Azure AD B2C-beleid aangeroepen, waarbij een e-mail bericht wordt verzonden als invoer claim. De service zoekt naar het e-mail adres in de Azure-tabel. Als het adres bestaat, genereert de service een fout bericht: *U moet het wacht woord wijzigen*.

- Nadat de gebruiker het wacht woord heeft gewijzigd, verwijdert u de entiteit uit de Azure-tabel.

> [!NOTE]
> We gebruiken een Azure-tabel om het voor beeld te vereenvoudigen. U kunt de migratie status opslaan in een Data Base of als aangepaste eigenschap in het Azure AD B2C-account.

### <a name="41-update-your-application-setting"></a>4,1: Uw toepassings instelling bijwerken

1. Als u de REST-API-demo wilt testen, opent u `AADB2C.UserMigration.sln` in Visual Studio.
1. Open het bestand *Web. config* in het project `AADB2C.UserMigration.API`. Vervang de instelling door het account dat is geconfigureerd in [stap 2,2](#step-22-configure-the-application-settings):

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Stap 4,2: Uw webtoepassing implementeren naar Azure App Service

Klik in Solution Explorer met de rechter muisknop op de `AADB2C.UserMigration.API` en selecteer publiceren.... Volg de instructies om naar Azure App Service te publiceren. Zie [uw app implementeren voor Azure app service][AppService-Deploy]voor meer informatie.

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Stap 4,3: Een technische profiel en technische profiel validatie aan uw beleid toevoegen

1. Vouw in Solution Explorer ' Solution items ' uit en open het beleids bestand *TrustFrameworkExtensions. XML* .
1. Wijzig `TenantId`, `PublicPolicyUri`-en `<TenantId>`-velden van `yourtenant.onmicrosoft.com` naar de naam van uw Tenant.
1. Vervang onder het element `<TechnicalProfile Id="login-NonInteractive">` alle exemplaren van `ProxyIdentityExperienceFrameworkAppId` en `IdentityExperienceFrameworkAppId` door de toepassings-Id's die zijn geconfigureerd in [aan de slag met aangepaste beleids regels][B2C-GetStartedCustom].
1. Zoek het volgende XML-fragment onder het knoop punt `<ClaimsProviders>`. Wijzig de waarde van `ServiceUrl` zodat deze verwijst naar de Azure App Service URL.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="LocalAccountSignIn">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>

        <TechnicalProfile Id="LocalAccountPasswordReset">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

In het voor gaande technische profiel wordt één invoer claim gedefinieerd: `signInName` (verzenden als e-mail bericht). Bij het aanmelden wordt de claim naar uw REST-eind punt verzonden.

Nadat u het technische profiel voor uw REST API hebt gedefinieerd, geeft u uw Azure AD B2C-beleid door om het technische profiel aan te roepen. Het XML-fragment overschrijft `SelfAsserted-LocalAccountSignin-Email`, dat in het basis beleid is gedefinieerd. Het XML-fragment voegt ook `ValidationTechnicalProfile` toe, met ReferenceId die verwijst naar uw technische profiel `LocalAccountUserMigration`.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Stap 4,4: Het beleid uploaden naar uw Tenant

1. In de [Azure Portal][Portal]gaat u naar de [context van uw Azure AD B2C-Tenant][B2C-NavContext]en selecteert u **Azure AD B2C**.
1. Selecteer een **Framework voor identiteits ervaring**.
1. Selecteer **alle beleids regels**.
1. Selecteer **beleid uploaden**.
1. Selecteer het selectie vakje het **beleid overschrijven als dit bestaat** .
1. Upload het bestand *TrustFrameworkExtensions. XML* en zorg ervoor dat het validatie wordt door gegeven.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Stap 4,5: Het aangepaste beleid testen met behulp van nu uitvoeren

1. Selecteer **Azure AD B2C**en selecteer vervolgens **Framework voor identiteits ervaring**.
1. Open *B2C_1A_signup_signin*, het aangepaste beleid RELYING Party (RP) dat u hebt geüpload en selecteer **nu uitvoeren**.
1. Voer de referenties van een van de gemigreerde gebruikers in en selecteer **Aanmelden**. Het REST API moet het volgende fout bericht genereren:

    ![Aanmeldings pagina voor aanmelding met het fout bericht voor het wijzigen van het wacht woord](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Stap 4,6: Beschrijving Problemen met uw REST API oplossen

U kunt logboek gegevens in vrijwel real time bekijken en bewaken.

1. Klik in het menu instellingen van de REST-toepassing onder **bewaking**op **Diagnostische logboeken**.
1. Stel **toepassings Logboeken (bestands systeem)** in **op aan**.
1. Stel het **niveau** in op **uitgebreid**.
1. Selecteer **opslaan**

    ![Configuratie pagina diagnose Logboeken in Azure Portal](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

1. Selecteer in het menu **instellingen** de optie **logboek stroom**.
1. Controleer de uitvoer van de REST-API.

> [!IMPORTANT]
> Gebruik de diagnostische Logboeken alleen tijdens het ontwikkelen en testen. De REST API-uitvoer kan vertrouwelijke informatie bevatten die niet in de productie omgeving mag worden weer gegeven.

## <a name="optional-download-the-complete-policy-files"></a>Beschrijving De volledige beleids bestanden downloaden

Nadat u de hand leiding aan de [slag met aangepast beleid][B2C-GetStartedCustom] hebt voltooid, wordt u aangeraden om uw scenario te bouwen met behulp van uw eigen aangepaste beleids bestanden. Voor uw referentie hebben we [voorbeeld beleids bestanden][UserMigrationSample-policy]gegeven.

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample-code]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/source-code
[UserMigrationSample-policy]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/policy
