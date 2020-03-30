---
title: Azure Batch-services verifiëren met Azure Active Directory
description: Batch ondersteunt Azure AD voor verificatie vanuit de Batch-service. Meer informatie over hoe u op twee manieren verifiëren.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 01/28/2020
ms.author: labrenne
ms.openlocfilehash: f56c05f64086ac2e98e69d6b21fae7a0a63b5006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019516"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Batch-serviceoplossingen verifiëren met Active Directory

Azure Batch ondersteunt verificatie met [Azure Active Directory][aad_about] (Azure AD). Azure AD is de multi-tenant cloudgebaseerde directory- en identiteitsbeheerservice van Microsoft. Azure zelf gebruikt Azure AD om zijn klanten, servicebeheerders en organisatiegebruikers te verifiëren.

Wanneer u Azure AD-verificatie gebruikt met Azure Batch, u op twee manieren verifiëren:

- Door **geïntegreerde verificatie** te gebruiken om een gebruiker te verifiëren die interactie heeft met de toepassing. Een toepassing met geïntegreerde verificatie verzamelt de referenties van een gebruiker en gebruikt deze referenties om toegang tot Batch-bronnen te verifiëren.
- Door een **serviceprincipal** te gebruiken om een onbeheerde toepassing te verifiëren. Een serviceprincipal definieert het beleid en de machtigingen voor een toepassing om de toepassing weer te geven wanneer u toegang krijgt tot resources tijdens uitvoering.

Zie de [Azure Active Directory Documentation](https://docs.microsoft.com/azure/active-directory/)voor meer informatie over Azure AD.

## <a name="endpoints-for-authentication"></a>Eindpunten voor verificatie

Als u Batch-toepassingen wilt verifiëren met Azure AD, moet u enkele bekende eindpunten in uw code opnemen.

### <a name="azure-ad-endpoint"></a>Azure AD-eindpunt

Het eindpunt van azure AD-autoriteit is:

`https://login.microsoftonline.com/`

Als u wilt verifiëren met Azure AD, gebruikt u dit eindpunt samen met de tenant-id (directory-id). De tenant-id identificeert de Azure AD-tenant die moet worden gebruikt voor verificatie. Als u de tenant-id wilt ophalen, voert u de stappen uit die zijn beschreven in [Ophalen van de tenant-id voor uw Azure Active Directory:](#get-the-tenant-id-for-your-active-directory)

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> Het tenantspecifieke eindpunt is vereist wanneer u verifieert met behulp van een serviceprincipal. 
> 
> Het tenantspecifieke eindpunt is optioneel wanneer u verifieert met geïntegreerde verificatie, maar wordt aanbevolen. U echter ook het algemene eindpunt van Azure AD gebruiken. Het algemene eindpunt biedt een algemene interface voor het verzamelen van referenties wanneer een specifieke tenant niet wordt verstrekt. Het gemeenschappelijke `https://login.microsoftonline.com/common`eindpunt is .
>
>

Zie [Verificatiescenario's voor Azure AD voor][aad_auth_scenarios]meer informatie over AD-eindpunten van Azure.

### <a name="batch-resource-endpoint"></a>Eindpunt batchbron

Gebruik het eindpunt van de **Azure Batch-bron** om een token te verkrijgen voor het verifiëren van aanvragen voor de Batch-service:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Uw aanvraag registreren bij een huurder

De eerste stap in het gebruik van Azure AD om uw toepassing te verifiëren is het registreren van uw toepassing in een Azure AD-tenant. Als u uw toepassing registreert, u de Azure [Active Directory Authentication Library][aad_adal] (ADAL) bellen vanuit uw code. De ADAL biedt een API voor het verifiëren met Azure AD vanuit uw toepassing. Het registreren van uw toepassing is vereist, ongeacht of u van plan bent om geïntegreerde verificatie of een serviceprincipal te gebruiken.

Wanneer u uw toepassing registreert, geeft u informatie over uw toepassing aan Azure AD. Azure AD biedt vervolgens een toepassings-id (ook wel *client-id*genoemd) die u gebruikt om uw toepassing te koppelen aan Azure AD tijdens de runtime. Zie [Hoofdobjecten voor toepassingen en services in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md)voor meer informatie over de toepassings-id.

Als u uw Batch-toepassing wilt registreren, voert u de stappen uit in de sectie [Een toepassing toevoegen](../active-directory/develop/quickstart-register-app.md) in Het integreren van toepassingen met Azure Active [Directory][aad_integrate]. Als u uw aanvraag registreert als native toepassing, u een geldige URI opgeven voor de **Omleiding URI.** Het hoeft geen echt eindpunt te zijn.

Nadat u uw aanvraag hebt geregistreerd, ziet u de toepassings-id:

![Uw Batch-toepassing registreren bij Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Zie [Verificatiescenario's voor Azure AD voor](../active-directory/develop/authentication-scenarios.md)meer informatie over het registreren van een toepassing bij Azure AD.

## <a name="get-the-tenant-id-for-your-active-directory"></a>De tenant-id voor uw Active Directory opvragen

De tenant-id identificeert de Azure AD-tenant die verificatieservices aan uw toepassing biedt. Voer de volgende stappen uit om de tenant-id te krijgen:

1. Selecteer in de Azure-portal uw Active Directory.
1. Selecteer **Eigenschappen**.
1. Kopieer de GUID-waarde die is opgegeven voor de **map-id**. Deze waarde wordt ook wel de tenant-ID genoemd.

![De adreslijst-id kopiëren](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>Geïntegreerde verificatie gebruiken

Als u wilt verifiëren met geïntegreerde verificatie, moet u uw toepassingsmachtigingen verlenen om verbinding te maken met de Batch-service-API. Met deze stap kan uw toepassing oproepen naar de Batch service API verifiëren met Azure AD.

Nadat u uw toepassing hebt geregistreerd, voert u de volgende stappen uit in de Azure-portal om deze toegang te verlenen tot de Batch-service:

1. Kies Alle **services**in het linkernavigatiedeelvenster van de Azure-portal . Selecteer **App-registraties**.
1. Zoek naar de naam van uw toepassing in de lijst met app-registraties:

    ![Zoeken naar uw toepassingsnaam](./media/batch-aad-auth/search-app-registration.png)

1. Selecteer de toepassing en selecteer **API-machtigingen**.
1. Selecteer in de sectie **API-machtigingen** de optie **Een machtiging toevoegen**.
1. Zoek **in Een API**selecteren naar de Batch-API. Zoek deze tekenreeksen totdat u de API hebt gevonden:
    1. **Microsoft Azure-batch**
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** is de id voor de Batch-API.
1. Zodra u de Batch-API hebt gevonden, selecteert u deze en selecteert **u Selecteren**.
1. Schakel in **Machtigingen invoegen**het selectievakje naast **Access Azure Batch Service** in en selecteer vervolgens Machtigingen **toevoegen**.

De sectie **API-machtigingen** laat nu zien dat uw Azure AD-toepassing toegang heeft tot zowel Microsoft Graph als de Batch service API. Machtigingen worden automatisch verleend aan Microsoft Graph wanneer u uw app voor het eerst registreert bij Azure AD.

![API-machtigingen verlenen](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Een serviceprincipal gebruiken

Als u een toepassing wilt verifiëren die zonder toezicht wordt uitgevoerd, gebruikt u een serviceprincipal. Nadat u uw toepassing hebt geregistreerd, voert u de volgende stappen uit in de Azure-portal om een serviceprincipal te configureren:

1. Vraag een geheim aan voor uw aanvraag.
1. Wijs op rollen gebaseerd toegangscontrolebeheer (RBAC) toe aan uw toepassing.

### <a name="request-a-secret-for-your-application"></a>Vraag een geheim aan voor uw aanvraag

Wanneer uw toepassing zich verifieert met een serviceprincipal, wordt zowel de toepassings-id als een geheim naar Azure AD verstuurt. U moet de geheime sleutel maken en kopiëren om te gebruiken vanuit uw code.

Volg deze stappen in Azure Portal:

1. Kies Alle **services**in het linkernavigatiedeelvenster van de Azure-portal . Selecteer **App-registraties**.
1. Selecteer uw toepassing in de lijst met app-registraties.
1. Selecteer de toepassing en selecteer **certificaten & geheimen**. Selecteer in de sectie **Klantgeheimen** de optie **Nieuw klantgeheim**.
1. Als u een geheim wilt maken, voert u een beschrijving in voor het geheim. Selecteer vervolgens een expiratie voor het geheim van een jaar, twee jaar of geen vervaldatum..
1. Selecteer **Toevoegen** om het geheim te maken en weer te geven. Kopieer de geheime waarde naar een veilige plaats, omdat u deze niet meer openen nadat u de pagina hebt verlaten.

    ![Een geheime sleutel maken](./media/batch-aad-auth/secret-key.png)

### <a name="assign-rbac-to-your-application"></a>RBAC toewijzen aan uw toepassing

Als u wilt verifiëren met een serviceprincipal, moet u RBAC aan uw toepassing toewijzen. Volg deze stappen:

1. Navigeer in de Azure-portal naar het Batch-account dat door uw toepassing wordt gebruikt.
1. Selecteer **Access Control (IAM) in**het gedeelte **Instellingen** van het batchaccount .
1. Selecteer het tabblad **Roltoewijzingen.**
1. Selecteer **Roltoewijzing toevoegen**.
1. Kies **in** de vervolgkeuzelijst Rol de rol *Inzender* of *Lezer* voor uw toepassing. Zie Aan de slag [met op rollen gebaseerd toegangsbeheer in de Azure-portal](../role-based-access-control/overview.md)voor meer informatie over deze rollen.  
1. Voer **in** het veld Selecteren de naam van uw toepassing in. Selecteer uw toepassing in de lijst en selecteer **Opslaan**.

Uw toepassing moet nu worden weergegeven in uw instellingen voor toegangscontrole met een RBAC-rol toegewezen.

![Een RBAC-rol toewijzen aan uw toepassing](./media/batch-aad-auth/app-rbac-role.png)

### <a name="assign-a-custom-role"></a>Een aangepaste rol toewijzen

Met een aangepaste rol wordt een gebruiker gedetailleerde toestemming gegeven voor het indienen van taken, taken en meer. Dit biedt de mogelijkheid om te voorkomen dat gebruikers bewerkingen uitvoeren die van invloed zijn op de kosten, zoals het maken van groepen of het wijzigen van knooppunten.

U een aangepaste rol gebruiken om machtigingen toe te kennen aan een Azure AD-gebruiker, -groep of serviceprincipal voor de volgende RBAC-bewerkingen:

- Microsoft.Batch/batchAccounts/pools/schrijven
- Microsoft.Batch/batchAccounts/pools/verwijderen
- Microsoft.Batch/batchAccounts/pools/lezen
- Microsoft.Batch/batchAccounts/taakschema's/schrijven
- Microsoft.Batch/batchAccounts/taakschema's/verwijderen
- Microsoft.Batch/batchAccounts/taakschema's/gelezen
- Microsoft.Batch/batchAccounts/jobs/write
- Microsoft.Batch/batchAccounts/taken/verwijderen
- Microsoft.Batch/batchAccounts/vacatures/gelezen
- Microsoft.Batch/batchAccounts/certificaten/schrijven
- Microsoft.Batch/batchAccounts/certificaten/verwijderen
- Microsoft.Batch/batchAccounts/certificaten/gelezen
- Microsoft.Batch/batchAccounts/read (voor elke leesbewerking)
- Microsoft.Batch/batchAccounts/listKeys/action (voor elke bewerking)

Aangepaste rollen zijn voor gebruikers die zijn geverifieerd door Azure AD, niet voor de batch-accountreferenties (gedeelde sleutel). Houd er rekening mee dat de batch-accountreferenties volledige toestemming geven aan het Batch-account. Houd er ook rekening mee dat voor taken met behulp van autopool machtigingen op poolniveau vereist zijn.

Hier is een voorbeeld van een aangepaste roldefinitie:

```json
{
 "properties":{
    "roleName":"Azure Batch Custom Job Submitter",
    "type":"CustomRole",
    "description":"Allows a user to submit jobs to Azure Batch but not manage pools",
    "assignableScopes":[
      "/subscriptions/88888888-8888-8888-8888-888888888888"
    ],
    "permissions":[
      {
        "actions":[
          "Microsoft.Batch/*/read",
          "Microsoft.Authorization/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*",
          "Microsoft.Insights/alertRules/*"
        ],
        "notActions":[

        ],
        "dataActions":[
          "Microsoft.Batch/batchAccounts/jobs/*",
          "Microsoft.Batch/batchAccounts/jobSchedules/*"
        ],
        "notDataActions":[

        ]
      }
    ]
  }
}
```

Zie [Aangepaste rollen voor Azure-resources voor](../role-based-access-control/custom-roles.md)meer algemene informatie over het maken van een aangepaste rol.

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>De tenant-id voor uw Azure Active Directory opvragen

De tenant-id identificeert de Azure AD-tenant die verificatieservices aan uw toepassing biedt. Voer de volgende stappen uit om de tenant-id te krijgen:

1. Selecteer in de Azure-portal uw Active Directory.
1. Selecteer **Eigenschappen**.
1. Kopieer de GUID-waarde die is opgegeven voor de **map-id**. Deze waarde wordt ook wel de tenant-ID genoemd.

![De adreslijst-id kopiëren](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>Codevoorbeelden

De codevoorbeelden in deze sectie laten zien hoe u met Azure AD verifiëren met behulp van geïntegreerde verificatie en met een serviceprincipal. De meeste van deze codevoorbeelden gebruiken .NET, maar de concepten zijn vergelijkbaar voor andere talen.

> [!NOTE]
> Een Azure AD-verificatietoken vervalt na een uur. Wanneer u een **batchclientobject** met een lange levensduur gebruikt, raden we u aan om bij elk verzoek een token van ADAL op te halen om ervoor te zorgen dat u altijd een geldig token hebt. 
>
>
> Als u dit wilt bereiken in .NET, schrijft u een methode die het token uit Azure AD ophaalt en geeft u die methode als gemachtigde door aan een object **BatchTokenCredentials.** De gemachtigdemethode wordt op elk verzoek aan de Batch-service ingeschakeld om ervoor te zorgen dat een geldig token wordt verstrekt. Standaard slaat ADAL tokens in de cache, zodat een nieuw token alleen uit Azure AD wordt opgehaald als dat nodig is. Zie [Verificatiescenario's voor Azure AD voor][aad_auth_scenarios]meer informatie over tokens in Azure AD.
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Codevoorbeeld: Geïntegreerde verificatie van Azure AD gebruiken met Batch .NET

Als u wilt verifiëren met geïntegreerde verificatie van Batch .NET, verwijst u naar het [Azure Batch .NET-pakket](https://www.nuget.org/packages/Microsoft.Azure.Batch/) en het [ADAL-pakket.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

Neem de `using` volgende instructies op in uw code:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Verwijs naar het Azure AD-eindpunt in uw code, inclusief de tenant-id. Als u de tenant-id wilt ophalen, voert u de stappen uit die zijn beschreven in [Ophalen van de tenant-id voor uw Azure Active Directory:](#get-the-tenant-id-for-your-active-directory)

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Verwijs naar het eindpunt van de batchservicebron:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Verwijs naar uw Batch-account:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Geef de toepassings-id (client-id) voor uw toepassing op. De toepassings-id is beschikbaar via uw app-registratie in de Azure-portal:

```csharp
private const string ClientId = "<application-id>";
```

Kopieer ook de door u opgegeven omleiding URI als u uw aanvraag hebt geregistreerd als native toepassing. De omleiding uri opgegeven in uw code moet overeenkomen met de omleiding URI die u verstrekt toen u de aanvraag geregistreerd:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Schrijf een terugbelmethode om het verificatietoken van Azure AD te verkrijgen. De hier getoonde **GetAuthenticationTokenTokenAsync-terugroepmethode** roept ADAL op om een gebruiker te verifiëren die interactie heeft met de toepassing. De **AcquireTokenAsync-methode** die door ADAL wordt geleverd, vraagt de gebruiker om zijn referenties en de toepassing gaat door zodra de gebruiker deze heeft opgegeven (tenzij deze referenties al in de cache heeft opgeslagen):

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Maak een **object BatchTokenCredentials** dat de gemachtigde als parameter neemt. Gebruik deze referenties om een **BatchClient-object** te openen. U dat **BatchClient-object** gebruiken voor volgende bewerkingen tegen de Batch-service:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Voorbeeld van code: een Azure AD-serviceprincipal gebruiken met Batch .NET

Als u wilt verifiëren met een serviceprincipal van Batch .NET, verwijst u naar het [Azure Batch .NET-pakket](https://www.nuget.org/packages/Azure.Batch/) en het [ADAL-pakket.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

Neem de `using` volgende instructies op in uw code:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Verwijs naar het Azure AD-eindpunt in uw code, inclusief de tenant-id. Wanneer u een serviceprincipal gebruikt, moet u een tenantspecifiek eindpunt opgeven. Als u de tenant-id wilt ophalen, voert u de stappen uit die zijn beschreven in [Ophalen van de tenant-id voor uw Azure Active Directory:](#get-the-tenant-id-for-your-active-directory)

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Verwijs naar het eindpunt van de batchservicebron:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Verwijs naar uw Batch-account:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Geef de toepassings-id (client-id) voor uw toepassing op. De toepassings-id is beschikbaar via uw app-registratie in de Azure-portal:

```csharp
private const string ClientId = "<application-id>";
```

Geef de geheime sleutel op die u hebt gekopieerd van de Azure-portal:

```csharp
private const string ClientKey = "<secret-key>";
```

Schrijf een terugbelmethode om het verificatietoken van Azure AD te verkrijgen. De hier getoonde **GetAuthenticationTokenTokenAsync-terugroepmethode** roept ADAL op voor onbeheerde verificatie:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Maak een **object BatchTokenCredentials** dat de gemachtigde als parameter neemt. Gebruik deze referenties om een **BatchClient-object** te openen. Gebruik dat **object BatchClient** vervolgens voor volgende bewerkingen tegen de batchservice:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Voorbeeld van code: een Azure AD-serviceprincipal gebruiken met Batch Python

Als u wilt verifiëren met een serviceprincipal van Batch Python, installeert en verwijst u naar de [azure-batch-](https://pypi.org/project/azure-batch/) en [azure-common-modules.](https://pypi.org/project/azure-common/)

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Wanneer u een serviceprincipal gebruikt, moet u de tenant-id opgeven. Als u de tenant-id wilt ophalen, voert u de stappen uit die zijn beschreven in [Ophalen van de tenant-id voor uw Azure Active Directory:](#get-the-tenant-id-for-your-active-directory)

```python
TENANT_ID = "<tenant-id>"
```

Verwijs naar het eindpunt van de batchservicebron:  

```python
RESOURCE = "https://batch.core.windows.net/"
```

Verwijs naar uw Batch-account:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

Geef de toepassings-id (client-id) voor uw toepassing op. De toepassings-id is beschikbaar via uw app-registratie in de Azure-portal:

```python
CLIENT_ID = "<application-id>"
```

Geef de geheime sleutel op die u hebt gekopieerd van de Azure-portal:

```python
SECRET = "<secret-key>"
```

Een object **ServicePrincipalCredentials** maken:

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Gebruik de hoofdreferenties van de service om een **BatchServiceClient-object** te openen. Gebruik dat **Object BatchServiceClient** vervolgens voor latere bewerkingen tegen de Batch-service.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Active Directory Documentation](https://docs.microsoft.com/azure/active-directory/)voor meer informatie over Azure AD. Diepgaande voorbeelden die laten zien hoe ADAL te gebruiken, zijn beschikbaar in de [Azure Code Samples-bibliotheek.](https://azure.microsoft.com/resources/samples/?service=active-directory)

- Zie [Hoofdobjecten voor toepassingen en services in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md)voor meer informatie over serviceprincipals. Zie [Portal gebruiken om Active Directory-toepassing en serviceprincipal te maken die toegang hebben tot bronnen](../active-directory/develop/howto-create-service-principal-portal.md)als u een serviceprincipal wilt maken met de Azure-portal. U ook een serviceprincipal maken met PowerShell of Azure CLI.

- Zie [Batchbeheeroplossingen verifiëren met Active Directory](batch-aad-auth-management.md)om Batch Management-toepassingen te verifiëren met Azure AD.

- Zie het aangepaste voorbeeld van Azure Batch met een Python Script-voorbeeld voor een Voorbeeld van [Python- batch](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) met een Voorbeeld van Een Batch die is geverifieerd met een Azure AD-token.

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Wat is Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Verificatiescenario's voor Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Toepassingen integreren met Azure Active Directory"
[azure_portal]: https://portal.azure.com
