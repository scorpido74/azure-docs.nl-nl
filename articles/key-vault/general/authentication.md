---
title: Verifiëren bij Azure Key Vault
description: Informatie over verifiëren bij Azure Key Vault
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 08/27/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 1ef5b2229aadc4be46361a7319351a1f27b28b63
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378968"
---
# <a name="authenticate-to-azure-key-vault"></a>Verifiëren bij Azure Key Vault

Met Azure Key Vault kunt u geheimen opslaan en de distributie ervan beheren in een gecentraliseerde, beveiligde cloudopslagplaats. Hierdoor hoeft u geen referenties meer op te slaan in toepassingen. Toepassingen moeten alleen tijdens de uitvoering worden geverifieerd bij Key Vault om toegang tot deze geheimen te krijgen.

## <a name="app-identity-and-service-principals"></a>App-id’s en service-principals

Verificatie met Key Vault werkt in combinatie met [Azure AD (Active Directory)](/azure/active-directory/fundamentals/active-directory-whatis), dat verantwoordelijk is voor het verifiëren van de identiteit van elke opgegeven **beveiligingsprincipal**.

Een beveiligingsprincipal is een object dat een gebruiker, groep, service of toepassing vertegenwoordigt die toegang tot Azure-resources aanvraagt. Azure wijst een unieke **object-id** toe aan elke beveiligingsprincipal.

* Een beveiligingsprincipal voor een **gebruiker** identificeert een persoon met een profiel in Azure Active Directory.

* Een beveiligingsprincipal voor een **groep** identificeert een set gebruikers die zijn gemaakt in Azure Active Directory. Elke rol of machtiging die wordt toegewezen aan de groep, geldt voor alle gebruikers in de groep.

* Een **service-principal** is een type beveiligingsprincipal waarmee een toepassing of service wordt geïdentificeerd, dat wil zeggen: een stukje code in plaats van een gebruiker of groep. De object-id van een service-principal wordt aangeduid als de **client-id** en fungeert als de bijbehorende gebruikersnaam. Het **clientgeheim** van een service-principal fungeert als het bijbehorende wachtwoord.

Er zijn twee manieren om voor toepassingen een service-principal te verkrijgen:

* Aanbevolen: een met het systeem toegewezen **beheerde identiteit** inschakelen voor de toepassing.

    Met een beheerde identiteit wordt in Azure de service-principal van de toepassing intern beheerd en wordt de toepassing automatisch geverifieerd bij andere Azure-services. Beheerde identiteiten zijn beschikbaar voor toepassingen die zijn geïmplementeerd in diverse services.

    Zie [Overzicht van beheerde identiteiten](/azure/active-directory/managed-identities-azure-resources/overview) voor meer informatie. Zie ook [Azure-services die ondersteuning bieden voor beheerde identiteiten](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities), met hierin koppelingen naar artikelen waarin wordt beschreven hoe u een beheerde identiteit kunt inschakelen voor specifieke services (zoals App Service, Azure Functions, Virtual Machines, enzovoort).

* Als u geen beheerde identiteit kunt gebruiken, kunt u in plaats hiervan de toepassing **registreren** bij uw Azure AD-tenant, zoals beschreven in [Quickstart: Een toepassing registreren bij het Azure-identiteitsplatform](/azure/active-directory/develop/quickstart-register-app). Bij de registratie wordt ook een tweede toepassingsobject gemaakt waarmee de app wordt geïdentificeerd bij alle tenants.

## <a name="authorize-a-service-principal-to-access-key-vault"></a>Een service-principal machtigen voor toegang tot Key Vault

Key Vault werkt met twee afzonderlijke machtigingsniveaus:

- **Toegangsbeleid** bepaalt of een gebruiker, groep of service-principal is gemachtigd voor toegang tot geheimen, sleutels en certificaten *binnen* een bestaande Key Vault-resource (ook wel bewerkingen op het gegevensvlak genoemd). Toegangsbeleid wordt doorgaans verleend aan gebruikers, groepen en toepassingen.

    Raadpleeg de volgende artikelen als u toegangsbeleid wilt toewijzen:

    - [Azure Portal](assign-access-policy-portal.md)
    - [Azure-CLI](assign-access-policy-cli.md)
    - [Azure PowerShell](assign-access-policy-portal.md)

- **Rolmachtigingen** bepalen of een gebruiker, groep of service-principal is gemachtigd om een Key Vault-resource te maken, verwijderen of anderszins te beheren (soms ook wel bewerkingen op het beheervlak genoemd). Dergelijke rollen worden meestal alleen verleend aan beheerders.
 
    Raadpleeg de volgende artikelen als u rollen wilt toewijzen en beheren:

    - [Azure Portal](/azure/role-based-access-control/role-assignments-portal)
    - [Azure-CLI](/azure/role-based-access-control/role-assignments-cli)
    - [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell)

    Key Vault biedt momenteel alleen ondersteuning voor de rol [Inzender](/azure/role-based-access-control/built-in-roles#key-vault-contributor), waarmee beheerbewerkingen in Key Vault-resources zijn toegestaan. Een aantal andere rollen is momenteel beschikbaar als preview-versie. U kunt ook aangepaste rollen maken, zoals beschreven in [Aangepaste Azure-rollen](/azure/role-based-access-control/custom-roles).

    Raadpleeg [Wat is Azure RBAC (op rollen gebaseerd toegangsbeheer)?](/azure/role-based-access-control/overview) voor algemene informatie over rollen.


> [!IMPORTANT]
> Voor de beste beveiliging moet u altijd het principe van minimale bevoegdheid volgen, en dus het meest specifieke toegangsbeleid toepassen en alleen de benodigde rollen toewijzen. 
    
## <a name="configure-the-key-vault-firewall"></a>Key Vault-firewall configureren

In Key Vault is toegang tot resources via openbare IP-adressen standaard toegestaan. Voor een betere beveiliging kunt u de toegang ook beperken tot specifieke IP-bereiken, service-eindpunten, virtuele netwerken of privé-eindpunten.

Zie [Toegang tot Azure Key Vault achter een firewall](/azure/key-vault/general/access-behind-firewall) voor meer informatie.


## <a name="the-key-vault-authentication-flow"></a>De Key Vault-verificatiestroom

1. Een service-principal doet een aanvraag voor verificatie bij Azure AD, bijvoorbeeld:
    * Een gebruiker meldt zich bij de Azure-portal aan met een gebruikersnaam en wachtwoord.
    * Een toepassing roept een Azure REST API aan, waarbij een client-id en -geheim, of een clientcertificaat worden opgegeven.
    * Een Azure-resource, zoals een virtuele machine met een beheerde identiteit, neemt contact op met het REST-eindpunt van de [Azure IMDS (Instance Metadata Service)](/azure/virtual-machines/windows/instance-metadata-service) om een toegangstoken op te halen.

1. Als verificatie bij Azure AD slaagt, wordt een OAuth-token verleend aan de service-principal.

1. De service-principal roept de Key Vault REST API aan via het eindpunt (URI) van Key Vault.

1. De Key Vault-firewall controleert de volgende criteria. Als aan een van de criteria wordt voldaan, is de aanroep toegestaan. Anders wordt de aanroep geblokkeerd, en wordt gemeld dat het antwoord verboden is.

    * De firewall wordt uitgeschakeld en het openbare eindpunt van Key Vault is bereikbaar via openbaar internet.
    * De aanroeper is een [in Key Vault vertrouwde service](/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services), zodat de firewall kan worden omzeild.
    * Het IP-adres, virtuele netwerk of service-eindpunt van de aanroeper wordt vermeld in de firewall.
    * De aanroeper kan Key Vault bereiken via een geconfigureerde Private Link-verbinding.    

1. Als de firewall de aanroep toestaat, wordt Azure AD aangeroepen in Key Vault om het toegangstoken van de service-principal te valideren.

1. In Key Vault wordt gecontroleerd of de service-principal beschikt over het benodigde toegangsbeleid voor de aangevraagde bewerking. Als dit niet het geval is, meldt Key Vault dat het antwoord verboden is.

1. De aangevraagde bewerking wordt uitgevoerd in Key Vault en het resultaat wordt geretourneerd.

In het volgende diagram ziet u het proces waarbij een toepassing een Key Vault API aanroept voor het ophalen van een geheim:

![De Azure Key Vault-verificatiestroom](../media/authentication/authentication-flow.png)

## <a name="code-examples"></a>Codevoorbeelden

De volgende tabel bevat koppelingen naar verschillende artikelen waarin wordt uitgelegd hoe u met Key Vault werkt in toepassingscode, met behulp van de Azure SDK-bibliotheken voor de taal in kwestie. Andere interfaces, zoals de Azure CLI en de Azure-portal, zijn gemakshalve opgenomen.

| Key Vault-geheimen | Key Vault-sleutels | Key Vault-certificaten |
|  --- | --- | --- |
| [Python](/azure/key-vault/secrets/quick-create-python) | [Python](/azure/key-vault/keys/quick-create-python) | [Python](/azure/key-vault/certificates/quick-create-python) | 
| [.NET (SDK v4)](/azure/key-vault/secrets/quick-create-net) | -- | -- |
| [.NET (SDK v3)](/azure/key-vault/secrets/quick-create-net-v3) | -- | -- |
| [Java](/azure/key-vault/secrets/quick-create-java) | -- | -- |
| [JavaScript](/azure/key-vault/secrets/quick-create-node) | -- | -- | 
| | | |
| [Azure-portal](/azure/key-vault/secrets/quick-create-portal) | [Azure-portal](/azure/key-vault/keys/quick-create-portal) | [Azure-portal](/azure/key-vault/certificates/quick-create-portal) |
| [Azure-CLI](/azure/key-vault/secrets/quick-create-cli) | [Azure-CLI](/azure/key-vault/keys/quick-create-cli) | [Azure-CLI](/azure/key-vault/certificates/quick-create-cli) |
| [Azure PowerShell](/azure/key-vault/secrets/quick-create-powershell) | [Azure PowerShell](/azure/key-vault/keys/quick-create-powershell) | [Azure PowerShell](/azure/key-vault/certificates/quick-create-powershell) |
| [ARM-sjabloon](/azure/key-vault/secrets/quick-create-net) | -- | -- |

## <a name="next-steps"></a>Volgende stappen

- [Problemen met Key Vault-toegangsbeleid oplossen](troubleshooting-access-issues.md)
- [REST API-foutcodes in Key Vault](rest-error-codes.md)
- [Gids voor Key Vault-ontwikkelaars](developers-guide.md)
- [Wat is Azure RBAC (toegangsbeheer op basis van rollen)?](/azure/role-based-access-control/overview)
