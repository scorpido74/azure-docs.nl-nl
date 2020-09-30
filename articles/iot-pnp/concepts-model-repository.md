---
title: Begrippen begrijpen van de Azure IoT-model opslagplaats | Microsoft Docs
description: Als oplossings ontwikkelaar of IT-Professional leert u meer over de basis concepten van de Azure IoT-model opslagplaats.
author: prashmo
ms.author: prashmo
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: c82858294054b50d6edae42a3d41e9fcb89ca89d
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577795"
---
# <a name="azure-iot-model-repository"></a>Azure IoT-model opslagplaats

Met de opslag plaats Azure IoT model kunnen apparaten bouwen IoT Plug en Play-apparaten beheren en delen. De modellen van het apparaat zijn JSON-bestanden die zijn gedefinieerd met de [Digital Apparaatdubbels Modeling Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). De modellen die zijn opgeslagen in de model opslagplaats service kunnen met behulp van toegangs beheer of openbaar worden gedeeld met oplossings ontwikkelaars, zonder dat hiervoor een verificatie vereist is om de IoT Plug en Play Cloud-oplossing te integreren en te ontwikkelen.

> [!NOTE]
> Apparaat-Builders kunnen ervoor kiezen om IoT Plug en Play-apparaten rechtstreeks op een apparaat te implementeren, modules te gebruiken of in een IoT Edge-module.

U kunt de model opslagplaats openen met behulp van:

- [Bibliotheek Portal van Azure IOT-model](https://aka.ms/iotmodelrepo)
- [REST API van Azure IoT-model opslagplaats](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync)
- [Opslag opdrachten voor Azure CLI IoT-model](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest&preserve-view=true)

## <a name="public-models"></a>Open bare modellen

De open bare digitale twee modellen die zijn opgeslagen in de model opslagplaats, zijn beschikbaar voor iedereen om zonder verificatie te worden gebruikt en te integreren in hun toepassing. Daarnaast kunnen de open bare modellen een open eco-systeem voor apparaten bouwen en ontwikkel aars van oplossingen om hun IoT Plug en Play-apparaten te delen en opnieuw te gebruiken.

Raadpleeg de sectie [een model publiceren](#publish-a-model) onder de **bedrijfs modellen** voor instructies over het publiceren van een model in de model opslagplaats om het openbaar te maken.

Een openbaar model weer geven met behulp van de portal voor model opslagplaats:

1. Ga naar de [Portal voor Azure IOT model-opslag](https://aka.ms/iotmodelrepo).

1. Selecteer op **open bare modellen weer geven**.

    ![Open bare modellen weer geven](./media/concepts-model-repository/public-models.png)

Als u een openbaar model programmatisch wilt weer geven met behulp van de REST API, raadpleegt u de documentatie [model rest API ophalen](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) .

```csharp
var httpClient = new HttpClient();
httpClient.BaseAddress = new Uri("https://repo.azureiotrepository.com");

var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-09-30").ConfigureAwait(false);
```

Als u een openbaar model wilt weer geven met de CLI, raadpleegt u de Azure CLI-opdracht voor het [ophalen van modellen](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show&preserve-view=true) .

## <a name="company-models"></a>Bedrijfs modellen

De bedrijfs model opslagplaats is een Tenant in de Azure IoT-model opslagplaats voor uw organisatie voor het maken en beheren van digitale twee modellen die zijn gemaakt door gebruikers binnen uw bedrijf of organisatie. Bedrijfs modellen zijn alleen beschikbaar voor geverifieerde gebruikers van uw bedrijf of organisatie. Een Tenant beheerder van een model opslagplaats kan machtigingen toewijzen en de toegang tot de modellen van andere gebruikers in het bedrijf of de organisatie beheren in de model opslagplaats van het bedrijf.

### <a name="set-up-your-company-model-repository"></a>Uw bedrijfs model opslagplaats instellen

Gebruik uw *werk-of school Azure Active Directory-account (Azure AD)* voor toegang tot de model opslagplaats. Als uw organisatie al een Azure AD-Tenant heeft, kunt u gebruikers account (s) en service-principals van deze Azure AD-Tenant gebruiken.

Voor informatie over het instellen van een Azure AD-Tenant en het maken van een gebruiker of Service-Principal in een Azure AD-Tenant raadpleegt u de sectie [aanvullende informatie](#additional-information) .

- Als u de eerste gebruiker van uw organisatie bent om toegang te krijgen tot de model opslagplaats of om u aan te melden bij de portal, krijgt u de **Tenant Administrator** rol. Met deze rol kunt u rollen toewijzen aan andere gebruikers in de opslag plaats-Tenant van uw organisatie.

- U kunt andere rollen toewijzen door een **Tenant Administrator** zoals **modellen lezen** of **modellen maken**.

### <a name="understand-access-management"></a>Toegangs beheer begrijpen

De volgende tabel bevat een overzicht van de ondersteunde mogelijkheden in de bedrijfs model opslagplaats en de bijbehorende machtigingen:

| Mogelijkheid  | Machtiging| Beschrijving|
|-------------|-----------|------------|
|Modellen lezen|Modellen lezen|Standaard kunnen alle gebruikers in de Tenant van het bedrijf hun bedrijfs modellen weer geven. Daarnaast kan de gebruiker ook de persoonlijke model (len) weer geven die door andere bedrijven worden gedeeld.|
|Toegang beheren|Toegang beheren|De gebruikersrol toewijzing (toevoegen of verwijderen) voor andere gebruikers in de organisatie beheren.|
|Modellen maken|Modellen maken|Modellen maken in de bedrijfs model opslagplaats.|
|Modellen publiceren|Modellen publiceren|Publiceer modellen om ze openbaar te maken voor iedereen om het model weer te geven.|

De volgende tabel bevat een overzicht van de ondersteunde rollen en hun mogelijkheden in de model opslagplaats die kunnen worden gebruikt voor toegangs beheer.

|Rol|Mogelijkheid|
|----|----------|
|TenantAdministrator|Toegang beheren, modellen lezen|
|Creator|Modellen maken, modellen lezen|
|Publisher|Modellen publiceren, modellen lezen|

#### <a name="passing-a-security-token-when-accessing-company-models-with-a-rest-api"></a>Een beveiligings token door geven bij het openen van bedrijfs modellen met een REST API

Wanneer u de REST-Api's aanroept voor het beheren van bedrijfs modellen die privé zijn of worden gedeeld, moet u een autorisatie token opgeven voor de gebruiker of Service-Principal in de JWT-indeling. Zie de sectie [aanvullende informatie](#additional-information) voor meer informatie over het ophalen van een JWT-token voor een gebruiker of Service-Principal.

De JWT-token moet worden door gegeven in de autorisatie-HTTP-header in de API bij het richten op bedrijfs modellen of gedeelde modellen. De JWT-token is niet nodig bij het richten op open bare modellen.

```csharp
// sample token
var authorizationToken = "eyJhbGciOiJIUzI1NiIsInR5cCTI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c";
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authorizationToken);
```

### <a name="view-company-or-shared-models"></a>Bedrijfs-of gedeelde modellen weer geven

U moet lid zijn van de rol *lezer* van de opslagplaats Tenant of het model moet met u worden gedeeld om een model te lezen. U kunt een lijst weer geven met niet-gepubliceerde modellen die met u zijn gedeeld en een lijst met gepubliceerde modellen die met u zijn gedeeld. Standaard kunnen gebruikers hun bedrijfs modellen lezen, modellen die met hen zijn gedeeld door andere bedrijven en alle open bare modellen.

Een bedrijfs-of gedeeld model weer geven met behulp van de portal:

1. Meld u aan bij de bibliotheek van het [Azure IOT-model](https://aka.ms/iotmodelrepo).

1. **Bedrijfs modellen** uitvouwen: **publicatie ongedaan** gemaakt in het linkerdeel venster

    ![Bedrijfs modellen weer geven](./media/concepts-model-repository/view-company-models.png)

1. Gedeelde modellen uitvouwen **: publicatie ongedaan** gemaakt in het linkerdeel venster

    ![Gedeelde modellen weer geven](./media/concepts-model-repository/view-shared-models.png)

Als u een bedrijfs-of gedeeld model wilt weer geven met behulp van de REST API, raadpleegt u de documentatie [model rest API ophalen](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) . Zie [een beveiligings token door geven bij het openen van bedrijfs modellen met een rest API](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) voor informatie over het door geven van een JWT-autorisatie-header in de HTTP-aanvraag.

```csharp
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-09-30").ConfigureAwait(false);
```

Als u een bedrijfs model of een gedeeld model wilt weer geven met behulp van de CLI, raadpleegt u de Azure CLI-opdracht voor het [ophalen van modellen](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show&preserve-view=true) .

### <a name="manage-roles"></a>Rollen beheren

De Tenant beheerder kan rollen toewijzen aan gebruikers in de opslagplaats Tenant, zodat ze modellen kunnen maken die persoonlijk zijn voor het bedrijf of de organisatie, het publiceren van modellen of het beheren van rollen voor andere gebruikers.

Een gebruiker toevoegen aan een model opslagplaats Tenant rol met behulp van de portal:

1. Meld u aan bij de bibliotheek van het [Azure IOT-model](https://aka.ms/iotmodelrepo).

1. Selecteer **toegangs beheer** in het linkerdeel venster en selecteer **+ toevoegen**. Typ in het deel venster **machtiging toevoegen** het werk adres van de gebruiker die u wilt toevoegen aan de rol:

    ![Werk adres toevoegen](./media/concepts-model-repository/add-user.png)

1. Kies de rol waaraan u de gebruiker wilt toevoegen in de vervolg keuzelijst **rol** . Selecteer vervolgens **Opslaan**:

    ![Rol kiezen](./media/concepts-model-repository/choose-role.png)

### <a name="upload-a-model"></a>Een model uploaden

U moet lid zijn van de **Maker** -rol van de opslagplaats om een model te uploaden naar de opslag plaats van het bedrijfs model.

Deze modellen zijn niet gepubliceerd en zijn standaard alleen toegankelijk voor gebruikers binnen uw organisatie. U kunt ook een of meer niet-gepubliceerde modellen delen met externe gebruikers.

Geüploade modellen zijn onveranderbaar.

De model-Id's voor deze modellen moeten wereld wijd uniek zijn in alle opslagplaats tenants voor alle geüploade modellen.

Een model uploaden met behulp van de portal:

1. Meld u aan bij de bibliotheek van het [Azure IOT-model](https://aka.ms/iotmodelrepo).

1. Vouw **bedrijfs modellen** uit in het linkerdeel venster en selecteer **model maken**. Selecteer vervolgens **JSON importeren**.

    ![Model maken](./media/concepts-model-repository/create-model.png)

1. Selecteer het bestand dat u wilt uploaden. Als het model door de portal is gevalideerd, selecteert u **Opslaan**.

Als u een model wilt uploaden met behulp van de REST API, raadpleegt u de API [Create a model](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) . Zie [een beveiligings token door geven bij het openen van bedrijfs modellen met een rest API](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) voor informatie over het door geven van een JWT-autorisatie-header in de HTTP-aanvraag.

```csharp
var httpContent = new StringContent(jsonLdModel, Encoding.UTF8, "application/json");
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.PutAsync($"/models/{modelId}?api-version=2020-09-30", httpContent).ConfigureAwait(false);
```

Als u een model wilt uploaden met behulp van de CLI, raadpleegt u de Azure CLI-opdracht voor het [maken van een model](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-create&preserve-view=true) .

### <a name="publish-a-model"></a>Een model publiceren

Voor het publiceren van een model moeten aan de volgende vereisten worden voldaan:

1. Uw organisatie moet lid zijn van de [Microsoft Partner Network](https://docs.microsoft.com/partner-center/) om een model te publiceren. Zie [een partner centrum-account maken](https://docs.microsoft.com/partner-center/mpn-create-a-partner-center-account)voor het maken van een partner centrum-account. Nadat uw account is goedgekeurd, kunt u uw modellen publiceren. Zie de [Veelgestelde vragen over Partner Center](https://support.microsoft.com/help/4340639/partner-center-account-faqs)voor meer informatie.

2. De gebruiker moet lid zijn van de *Publisher* -rol van de opslagplaats Tenant.

Modellen die door gebruikers in uw organisatie zijn gemaakt en gepubliceerd, zijn zichtbaar als *gepubliceerde modellen*. Deze modellen zijn openbaar en kunnen door iedereen worden gevonden onder **open bare modellen**.

Een model publiceren met behulp van de portal:

1. Meld u aan bij de bibliotheek van het [Azure IOT-model](https://aka.ms/iotmodelrepo).

2. Vouw **bedrijfs modellen** in het linkerdeel venster uit en selecteer het model dat u wilt publiceren. Selecteer vervolgens **Publiceren**.

    ![Model publiceren](./media/concepts-model-repository/publish-model.png)

> [!NOTE]
> Als er een melding wordt weer gegeven dat u geen ID van een micro soft-partner (MPN) hebt, volgt u de registratie stappen in de melding. Zie de vereisten aan het begin van deze sectie voor meer informatie.

Als u een model wilt publiceren met behulp van de REST API, raadpleegt u de documentatie [een model publiceren](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) rest API. Geef de query teken reeks parameter `update-metadata=true` op om een model te publiceren met behulp van de rest API. Zie [een beveiligings token door geven bij het openen van bedrijfs modellen met een rest API](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) voor informatie over het door geven van een JWT-autorisatie-header in de HTTP-aanvraag.

Als u een model wilt publiceren met behulp van de CLI, raadpleegt u de Azure CLI-opdracht voor het [publiceren van een model](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-publish&preserve-view=true) .

> [!NOTE]
> Modellen moeten worden gepubliceerd in de model opslagplaats voordat u de certificerings tests uitvoert. Zie voor meer informatie [hoe u IoT Plug en Play-apparaten kunt certificeren](howto-certify-device.md).

### <a name="share-a-model"></a>Een model delen

U kunt bedrijfs modellen delen die u hebt gemaakt met gebruikers van externe organisaties. Op deze manier kunt u samen werkers toestaan oplossingen te bekijken en te ontwikkelen met uw persoonlijke bedrijfs modellen.

Zo wilt u bijvoorbeeld dat de fabrikant van het apparaat persoonlijke modellen kan onderhouden voor het bedrijf of de organisatie. Ze kunnen klanten hebben die vereisen dat hun mogelijkheden voor apparaten vertrouwelijk blijven.

Het delen van modellen in bedrijven of organisaties maakt veilige toegang mogelijk tot modellen die niet openbaar zijn.

Een bedrijfs model delen met behulp van de portal:

- Als u de maker van een model bent, worden de **deel** -en **gedeelde** knoppen actief wanneer u het model bekijkt in de sectie **bedrijfs modellen** .

    ![Model delen](./media/concepts-model-repository/share-model.png)

- Als u het model met een externe gebruiker wilt delen, selecteert u **delen**. In het deel venster **model delen** voert u het e-mail adres van de externe gebruiker in en selecteert u **Opslaan**.

- Selecteer **gedeeld met**om de gebruikers weer te geven met wie u het model hebt gedeeld.

- Als u wilt stoppen met het delen van het model met een specifieke gebruiker, selecteert u de gebruiker in de lijst met gebruikers in het deel venster **gedeeld met** . Selecteer vervolgens **verwijderen** en bevestig uw keuze wanneer u hierom wordt gevraagd.

    ![Delen beëindigen](./media/concepts-model-repository/stop-sharing.png)

## <a name="additional-information"></a>Aanvullende informatie

Mogelijk vindt u de volgende onderwerpen nuttig bij het werken met Azure AD:

- Zie [een nieuwe Tenant maken in azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)voor meer informatie over het maken van een nieuwe Azure AD-Tenant. De meeste organisaties beschikken al over Azure AD-tenants.

- Zie [gebruikers toevoegen of verwijderen met Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory)om gebruikers of gast gebruikers toe te voegen aan een Azure AD-Tenant.

- Als u een Service-Principal wilt toevoegen aan een Azure AD-Tenant, raadpleegt u [hoe u de portal gebruikt om een Azure AD-toepassing en Service-Principal te maken die toegang hebben tot resources](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

- Zie [een Token ophalen uit Azure AD voor het machtigen van aanvragen van een client toepassing voor](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app)meer informatie over het ophalen van een JWT-token van Azure AD om te gebruiken bij het aanroepen van rest-api's.

## <a name="next-steps"></a>Volgende stappen

De voorgestelde volgende stap is het controleren van de [IoT Plug en Play-architectuur](concepts-architecture.md).
