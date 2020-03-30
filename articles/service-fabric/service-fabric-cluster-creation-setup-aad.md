---
title: Azure Active Directory instellen voor clientverificatie
description: Meer informatie over het instellen van Azure Active Directory (Azure AD) om clients te verifiëren voor servicefabricclusters.
ms.topic: conceptual
ms.date: 6/28/2019
ms.openlocfilehash: 28c4c65cfcc77607dfe9a463a09ecd10389a6eca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78193374"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Azure Active Directory instellen voor clientverificatie

Voor clusters die op Azure worden uitgevoerd, wordt Azure Active Directory (Azure AD) aanbevolen om beveiligde toegang tot beheereindpunten te beveiligen. In dit artikel wordt beschreven hoe u Azure AD instelt om clients voor een Cluster Servicefabric te verifiëren.

In dit artikel wordt de term 'toepassing' gebruikt om te verwijzen naar [Azure Active Directory-toepassingen,](../active-directory/develop/developer-glossary.md#client-application)niet naar Service Fabric-toepassingen; het onderscheid zal zo nodig worden gemaakt. Azure Active Directory maakt het beheren van toegang tot toepassingen door organisaties (bekend als tenants) mogelijk.

Een Service Fabric-cluster biedt verschillende toegangspunten bij de management-functionaliteit, met inbegrip van de webconsoles [Service Fabric Explorer][service-fabric-visualizing-your-cluster] en [Visual Studio][service-fabric-manage-application-in-visual-studio]. Als gevolg hiervan maakt u twee Azure AD-toepassingen om de toegang tot het cluster te beheren: één webtoepassing en één native toepassing. Nadat de toepassingen zijn gemaakt, wijst u gebruikers toe aan alleen-lezen- en beheerdersrollen.

> [!NOTE]
> Op Linux moet u de volgende stappen uitvoeren voordat u het cluster maakt. In Windows hebt u ook de optie om [Azure AD-verificatie voor een bestaand cluster](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/Configure%20Azure%20Active%20Directory%20Authentication%20for%20Existing%20Cluster.md)te configureren.

> [!NOTE]
> Het is een [bekend probleem](https://github.com/microsoft/service-fabric/issues/399) dat toepassingen en knooppunten op Linux AAD-clusters niet kunnen worden bekeken in Azure Portal.



## <a name="prerequisites"></a>Vereisten
In dit artikel nemen we aan dat u al een tenant hebt gemaakt. Als u dit nog niet hebt gedaan, begint u door [Een Azure Active Directory-tenant verkrijgen][active-directory-howto-tenant] te lezen.

Ter vereenvoudiging van enkele van de stappen voor het configureren van Azure Active Directory met een Service Fabric-cluster, hebben we een set Windows PowerShell-scripts gemaakt.

1. [Kloon de repo](https://github.com/Azure-Samples/service-fabric-aad-helpers) naar uw computer.
2. [Zorg ervoor dat u alle vereisten](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) voor de scripts hebt geïnstalleerd.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Azure Active Directory-toepassingen maken en gebruikers toewijzen aan rollen

We gebruiken de scripts om twee Azure AD-toepassingen te maken om de toegang tot het cluster te beheren: één webtoepassing en één native toepassing. Nadat u toepassingen hebt gemaakt om uw cluster weer te geven, maakt u gebruikers voor de [rollen die worden ondersteund door Service Fabric:](service-fabric-cluster-security-roles.md)alleen-lezen en beheerder.

Voer `SetupApplications.ps1` uit, en geef de tenant-ID, de naam van het cluster en de antwoord-URL van de webtoepassing op als parameters.  Geef ook gebruikersnamen en wachtwoorden voor de gebruikers op. Bijvoorbeeld:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Voor nationale clouds (bijvoorbeeld Azure Government, Azure China, Azure Duitsland), moet u ook de parameter `-Location` opgeven.

U uw *TenantId* vinden door `Get-AzureSubscription`de opdracht PowerShell uit te voeren. Bij het uitvoeren van deze opdracht wordt de TenantId voor elk abonnement weergegeven.

De *Clusternaam* wordt gebruikt voor als prefix aan de Azure Active Directory-toepassingen die zijn gemaakt door het script. Deze hoeft niet precies overeen te komen met de naam van het daadwerkelijke cluster. De naam is uitsluitend bedoeld om het eenvoudiger te maken dat Azure Active Directory-artefacten worden toegewezen aan het Service Fabric-cluster waarmee ze worden gebruikt.

*WebApplicationReplyUrl* wordt het standaardeindpunt dat Azure Active Directory retourneert naar uw gebruikers nadat ze klaar zijn aanmelden. Stel dit eindpunt in als het eindpunt van Service Fabric Explorer voor uw cluster. Als u Azure AD-toepassingen maakt om een bestaand cluster weer te geven, controleert u of deze URL overeenkomt met het eindpunt van uw bestaande cluster. Als u toepassingen maakt voor een nieuw cluster, plant u het eindpunt van uw cluster en moet u het eindpunt van een bestaand cluster niet gebruiken. Standaard is het eindpunt van Service Fabric Explorer:

https://&lt;cluster_domain&gt;:19080/Explorer

U wordt gevraagd of u zich aanmeldt bij een account dat beheerdersrechten voor de Azure Active Directory-tenant heeft. Nadat u zich hebt aangemeld, maakt het script de web- en systeemeigen toepassingen voor uw Service Fabric-cluster. Als u kijkt naar de toepassingen van de tenant in de [Azure-portal][azure-portal], ziet u twee nieuwe vermeldingen:

   * *Clusternaamcluster*\_
   * *Clusternaamclient*\_

Het script drukt de JSON af die vereist is door de Azure Resource Manager-sjabloon wanneer u [uw AAD-cluster maakt,](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access)dus het is een goed idee om het PowerShell-venster open te houden.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Help bij het oplossen van problemen met het instellen van Azure Active Directory
Het instellen van Azure AD en het gebruik ervan kan een uitdaging zijn, dus hier zijn enkele tips over wat u doen om het probleem te debuggen.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer vraagt u een certificaat te selecteren
#### <a name="problem"></a>Probleem
Nadat u zich hebt aangemeld bij Azure AD in Service Fabric Explorer, keert de browser terug naar de startpagina, maar in een bericht wordt u gevraagd een certificaat te selecteren.

![Dialoogvenster SFX-certificaat][sfx-select-certificate-dialog]

#### <a name="reason"></a>Reden
De gebruiker krijgt geen rol toegewezen in de Azure AD-clustertoepassing. Azure AD-verificatie mislukt dus in het cluster Servicefabric. Service Fabric Explorer valt terug naar certificaatverificatie.

#### <a name="solution"></a>Oplossing
Volg de instructies voor het instellen van Azure AD en wijs gebruikersrollen toe. Ook raden we u aan 'Gebruikerstoewijzing die nodig `SetupApplications.ps1` is om toegang te krijgen tot de app inschakelt, net als.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Verbinding met PowerShell mislukt met een fout: "De opgegeven referenties zijn ongeldig"
#### <a name="problem"></a>Probleem
Wanneer u PowerShell gebruikt om verbinding te maken met het cluster met de beveiligingsmodus 'AzureActiveDirectory', mislukt de verbinding met een fout nadat u zich hebt aangemeld bij Azure AD: 'De opgegeven referenties zijn ongeldig'.

#### <a name="solution"></a>Oplossing
Deze oplossing is hetzelfde als de vorige.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer retourneert een storing wanneer u zich aanmeldt: "AADSTS50011"
#### <a name="problem"></a>Probleem
Wanneer u zich probeert aan te melden bij Azure AD in Service Fabric Explorer, geeft de pagina &lt;&gt; een fout als resultaat: "AADSTS50011: De url van het antwoordadres komt niet overeen met de antwoordadressen die voor de toepassing zijn geconfigureerd: &lt;guid&gt;."

![SFX-antwoordadres komt niet overeen][sfx-reply-address-not-match]

#### <a name="reason"></a>Reden
De clustertoepassing (web) die Service Fabric Explorer vertegenwoordigt, probeert te verifiëren tegen Azure AD en biedt als onderdeel van de aanvraag de url voor omleidingsretour. De URL wordt echter niet vermeld in de **URL-lijst met azure** AD-toepassingen.

#### <a name="solution"></a>Oplossing
Selecteer op de pagina Azure AD-app-app registratie voor uw cluster de optie **Verificatie**en voeg onder de sectie **URL's van De** Structuur van de Service aan de lijst de URL van De Verkenner toe. Sla je wisselgeld op.

![URL voor antwoord van webtoepassingen][web-application-reply-url]

### <a name="connecting-to-the-cluster-using-azure-ad-authentication-via-powershell-gives-an-error-when-you-sign-in-aadsts50011"></a>Verbinding maken met het cluster via Azure AD-verificatie via PowerShell geeft een fout bij het aanmelden: "AADSTS50011"
#### <a name="problem"></a>Probleem
Wanneer u via PowerShell verbinding probeert te maken met een cluster van servicefabric via Azure AD, geeft de aanmeldingspagina een fout weer: "AADSTS50011: De in de aanvraag opgegeven antwoord-url komt niet overeen met de antwoorden url's die zijn geconfigureerd voor de toepassing: &lt;guid."&gt;

#### <a name="reason"></a>Reden
Net als bij het vorige probleem probeert PowerShell te verifiëren tegen Azure AD, dat een omleidings-URL biedt die niet wordt vermeld in de lijst **met URL's voor antwoord van** Azure AD-toepassing.  

#### <a name="solution"></a>Oplossing
Gebruik hetzelfde proces als in het vorige nummer, `urn:ietf:wg:oauth:2.0:oob`maar de URL moet worden ingesteld op , een speciale omleiding voor command-line authenticatie.

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Het cluster verbinden met Azure AD-verificatie via PowerShell
Als u het cluster Servicefabric wilt aansluiten, gebruikt u het volgende powershell-opdrachtvoorbeeld:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Zie [Connect-ServiceFabricCluster cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster)voor meer informatie.

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Kan ik dezelfde Azure AD-tenant opnieuw gebruiken in meerdere clusters?
Ja. Maar vergeet niet om de URL van Service Fabric Explorer toe te voegen aan uw cluster (web) applicatie. Anders werkt Service Fabric Explorer niet.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Waarom heb ik nog steeds een servercertificaat nodig terwijl Azure AD is ingeschakeld?
FabricClient en FabricGateway voeren een wederzijdse verificatie uit. Tijdens Azure AD-verificatie biedt Azure AD-integratie een clientidentiteit aan de server en wordt het servercertificaat door de client gebruikt om de identiteit van de server te verifiëren. Zie [X.509-certificaten en Service Fabric voor][x509-certificates-and-service-fabric]meer informatie over Service Fabric-certificaten.

## <a name="next-steps"></a>Volgende stappen
Nadat u Azure Active Directory-toepassingen hebt ingesteld en rollen voor gebruikers hebt ingesteld, [configureert en implementeert u een cluster.](service-fabric-cluster-creation-via-arm.md)


<!-- Links -->
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
