---
title: Azure Active Directory gebruiken om batchbeheeroplossingen te verifiëren
description: Ontdek azure active directory gebruiken om te verifiëren van toepassingen die de Batch Management .NET-bibliotheek gebruiken.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/27/2017
ms.author: labrenne
ms.openlocfilehash: 5c217971bd213c97a2ee31a0a1f513b601d14df9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472976"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Batchbeheeroplossingen verifiëren met Active Directory

Toepassingen die de Azure Batch Management-service aanroepen, verifiëren met [Azure Active Directory][aad_about] (Azure AD). Azure AD is de multi-tenant cloudgebaseerde directory- en identiteitsbeheerservice van Microsoft. Azure zelf gebruikt Azure AD voor de verificatie van zijn klanten, servicebeheerders en organisatiegebruikers.

De Batch Management .NET-bibliotheek legt typen bloot voor het werken met Batch-accounts, accountsleutels, toepassingen en toepassingspakketten. De Batch Management .NET-bibliotheek is een Azure-bronproviderclient en wordt samen met [Azure Resource Manager][resman_overview] gebruikt om deze resources programmatisch te beheren. Azure AD is vereist om aanvragen te verifiëren die zijn gedaan via een Azure-client voor resourcesprovider, inclusief de Batch Management .NET-bibliotheek en via [Azure Resource Manager.][resman_overview]

In dit artikel verkennen we het gebruik van Azure AD om te verifiëren van toepassingen die de Batch Management .NET-bibliotheek gebruiken. We laten zien hoe u Azure AD gebruiken om een abonnementsbeheerder of medebeheerder te verifiëren met behulp van geïntegreerde verificatie. We gebruiken het [voorbeeldproject AccountManagement,][acct_mgmt_sample] beschikbaar op GitHub, om azure AD te gebruiken met de Batch Management .NET-bibliotheek.

Zie [Batchaccounts en quota beheren met de batchbeheerclientbibliotheek voor .NET voor](batch-management-dotnet.md)meer informatie over het gebruik van de batchbeheer .NET-bibliotheek en het voorbeeld AccountBeheer.

## <a name="register-your-application-with-azure-ad"></a>Uw toepassing registreren bij Azure AD

De Azure [Active Directory Authentication Library][aad_adal] (ADAL) biedt een programmatische interface met Azure AD voor gebruik binnen uw toepassingen. Als u ADAL vanuit uw toepassing wilt bellen, moet u uw toepassing registreren in een Azure AD-tenant. Wanneer u uw toepassing registreert, verstrekt u Azure AD informatie over uw toepassing, waaronder een naam daarvoor in de Azure AD-tenant. Azure AD biedt vervolgens een toepassings-id die u gebruikt om uw toepassing te koppelen aan Azure AD tijdens de runtime. Zie [Hoofdobjecten voor toepassingen en services in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md)voor meer informatie over de toepassings-id.

Als u de voorbeeldtoepassing AccountManagement wilt registreren, voert u de stappen uit in de sectie [Een toepassing toevoegen](../active-directory/develop/quickstart-register-app.md) in Het integreren van toepassingen met Azure Active [Directory][aad_integrate]. Geef **native clienttoepassing op** voor het type toepassing. De industriestandaard OAuth 2.0 URI `urn:ietf:wg:oauth:2.0:oob`voor de Redirect **URI** is . U echter alle geldige URI `http://myaccountmanagementsample`(zoals ) opgeven voor de **Omleidinguri,** omdat het geen echt eindpunt hoeft te zijn:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

Zodra u het registratieproces hebt voltooid, ziet u de toepassings-id en de object-id (serviceprincipal) die voor uw toepassing worden vermeld.  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>De Azure Resource Manager API toegang verlenen tot uw toepassing

Vervolgens moet u de toegang tot uw toepassing delegeren aan de Azure Resource Manager API. De Azure AD-id voor de Api voor Resource Beheer is **Windows Azure Service Management API**.

Volg deze stappen in Azure Portal:

1. Kies in het linkernavigatiedeelvenster van de Azure-portal **alle services,** klik op **App-registraties**en klik op **Toevoegen**.
2. Zoek naar de naam van uw toepassing in de lijst met app-registraties:

    ![Zoeken naar uw toepassingsnaam](./media/batch-aad-auth-management/search-app-registration.png)

3. Geef het **mes Instellingen weer.** Selecteer in de sectie **API-toegang** **de optie Vereiste machtigingen**.
4. Klik **op Toevoegen** om een nieuwe vereiste machtiging toe te voegen. 
5. Voer in stap 1 **de API voor Windows Azure Service Management**in, selecteer die API in de lijst met resultaten en klik op de knop **Selecteren.**
6. Schakel in stap 2 het selectievakje naast **het klassieke implementatiemodel van Access Azure in als organisatiegebruikers**en klik op de knop **Selecteren.**
7. Klik op de knop **Gereed.**

Het blade **Vereiste machtigingen** laat nu zien dat machtigingen voor uw toepassing worden verleend aan zowel de ADAL- als de API's voor resourcebeheer. Machtigingen worden standaard aan ADAL verleend wanneer u uw app voor het eerst registreert bij Azure AD.

![Machtigingen delegeren aan de API Azure Resource Manager](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Azure AD-eindpunten

Als u uw Batch Management-oplossingen wilt verifiëren met Azure AD, hebt u twee bekende eindpunten nodig.

- Het **gemeenschappelijke eindpunt azure AD** biedt een algemene interface voor het verzamelen van referenties wanneer een specifieke tenant niet wordt verstrekt, zoals in het geval van geïntegreerde verificatie:

    `https://login.microsoftonline.com/common`

- Het **eindpunt Azure Resource Manager** wordt gebruikt om een token te verkrijgen voor het verifiëren van aanvragen voor de batchbeheerservice:

    `https://management.core.windows.net/`

De voorbeeldtoepassing AccountManagement definieert constanten voor deze eindpunten. Laat deze constanten ongewijzigd:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Uw toepassings-id verwijzen 

Uw clienttoepassing gebruikt de toepassings-id (ook wel client-id genoemd) om tijdens runtime toegang te krijgen tot Azure AD. Zodra u uw toepassing hebt geregistreerd in de Azure-portal, werkt u uw code bij om de toepassings-id van Azure AD te gebruiken voor uw geregistreerde toepassing. Kopieer in de voorbeeldtoepassing AccountManagement uw toepassings-id van de Azure-portal naar de juiste constante:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Graph API. For information
// about registering an application in Azure Active Directory, please see "Register an application with the Microsoft identity platform" here:
// https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-register-app
private const string ClientId = "<application-id>";
```
Kopieer ook de omleiding URI die u hebt opgegeven tijdens het registratieproces. De omleiding uri opgegeven in uw code moet overeenkomen met de omleiding URI die u verstrekt toen u de aanvraag geregistreerd.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Een Azure AD-verificatietoken aanschaffen

Nadat u het voorbeeld van AccountManagement in de Azure AD-tenant hebt geregistreerd en de voorbeeldbroncode met uw waarden hebt bijgewerkt, is het voorbeeld klaar om te verifiëren met Azure AD. Wanneer u het voorbeeld uitvoert, probeert het ADAL een verificatietoken te verkrijgen. Bij deze stap wordt u gevraagd om uw Microsoft-referenties: 

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

Nadat u uw referenties hebt opgemaakt, kan de voorbeeldtoepassing doorgaan met het uitgeven van geverifieerde aanvragen aan de batchbeheerservice. 

## <a name="next-steps"></a>Volgende stappen

Zie [Batchaccounts en quota beheren met de batchbeheerclientbibliotheek voor .NET voor](batch-management-dotnet.md)meer informatie over het uitvoeren van de [voorbeeldtoepassing AccountManagement][acct_mgmt_sample].

Zie de [Azure Active Directory Documentation](https://docs.microsoft.com/azure/active-directory/)voor meer informatie over Azure AD. Diepgaande voorbeelden die laten zien hoe ADAL te gebruiken, zijn beschikbaar in de [Azure Code Samples-bibliotheek.](https://azure.microsoft.com/resources/samples/?service=active-directory)

Zie [Batch-serviceoplossingen verifiëren met Active Directory](batch-aad-auth.md)als u batchservicetoepassingen wilt verifiëren met Azure AD. 


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Wat is Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Verificatiescenario's voor Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Toepassingen integreren met Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: https://portal.azure.com
[resman_overview]: ../azure-resource-manager/management/overview.md
