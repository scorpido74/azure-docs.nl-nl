---
title: Gids voor Azure Key Vault-ontwikkelaars
description: Ontwikkel aars kunnen Azure Key Vault gebruiken voor het beheren van cryptografische sleutels binnen de Microsoft Azure omgeving.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 662c23a29e383800a4591c900e02133c16fa2090
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743315"
---
# <a name="azure-key-vault-developers-guide"></a>Gids voor Azure Key Vault-ontwikkelaars

Met Key Vault kunt u veilig toegang krijgen tot gevoelige informatie in uw toepassingen:

- Sleutels, geheimen en certificaten zijn beveiligd zonder dat u zelf de code hoeft te schrijven en u kunt deze eenvoudig gebruiken vanuit uw toepassingen.
- U kunt ervoor zorgen dat klanten hun eigen sleutels, geheimen en certificaten kunnen beheren, zodat u zich op de kern functies van de software kan concentreren. Op deze manier zijn uw toepassingen niet verantwoordelijk voor de verantwoordelijkheid of de mogelijke aansprakelijkheid voor de Tenant sleutels, geheimen en certificaten van uw klanten.
- Uw toepassing kan gebruikmaken van sleutels voor ondertekening en versleuteling, maar blijft het sleutel beheer extern vanuit uw toepassing. Zie [about Keys](../keys/about-keys.md) (Engelstalig) voor meer informatie over sleutels
- U kunt referenties, zoals wacht woorden, toegangs sleutels en SAS-tokens die deze opslaan in Key Vault als geheimen beheren, Zie [over geheimen](../secrets/about-secrets.md)
- Certificaten beheren. Zie [over certificaten](../certificates/about-certificates.md) voor meer informatie.

Zie [Wat is Key Vault](overview.md)voor meer algemene informatie over Azure Key Vault.

## <a name="public-previews"></a>Open bare voor beelden

Er wordt regel matig een open bare preview van een nieuwe Key Vault-functie uitgebracht. Probeer de open bare preview-functies uit en laat ons weten wat u ervan vindt via het azurekeyvault@microsoft.com e-mail adres van feedback.

## <a name="creating-and-managing-key-vaults"></a>Sleutel kluizen maken en beheren

Key Vault beheer, vergelijkbaar met andere Azure-Services, wordt uitgevoerd via Azure Resource Manager service. Azure Resource Manager is de implementatie- en beheersservice voor Azure. Het biedt een beheerlaag waarmee u resources in uw Azure-account kunt maken, bijwerken en verwijderen. Zie [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) voor meer informatie.

Toegang tot de Management-laag wordt beheerd door op [rollen gebaseerd toegangs beheer van Azure](https://docs.microsoft.com/azure/role-based-access-control/overview). In Key Vault kunt u met management Layer, ook wel beheer of beheergebied, sleutel kluizen en de kenmerken ervan maken en beheren, inclusief het toegangs beleid, maar niet de sleutels, geheimen en certificaten, die worden beheerd op gegevens vlak. U kunt vooraf gedefinieerde rol gebruiken `Key Vault Contributor` om beheer toegang toe te kennen aan Key Vault.     

**API'S en Sdk's voor sleutel kluis beheer:**

| Azure CLI | PowerShell | REST-API | Resource Manager | .NET | Python | Java | Javascript |  
|--|--|--|--|--|--|--|--|
|[Verwijzing](/cli/azure/keyvault)<br>[Snelstartgids](quick-create-cli.md)|[Verwijzing](/powershell/module/az.keyvault)<br>[Snelstartgids](quick-create-powershell.md)|[Verwijzing](/rest/api/keyvault/)|[Verwijzing](/azure/templates/microsoft.keyvault/vaults)|[Verwijzing](/dotnet/api/microsoft.azure.management.keyvault)|[Verwijzing](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Verwijzing](/java/api/com.microsoft.azure.management.keyvault)|[Verwijzing](/javascript/api/@azure/arm-keyvault)|

Zie [client bibliotheken](client-libraries.md) voor installatie pakketten en bron code.

Zie [Key Vault-beheer vlak](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#management-plane-and-azure-rbac) voor meer informatie over Key Vault beheer vlak

## <a name="authenticate-to-key-vault-in-code"></a>Verifiëren voor Key Vault in code

Key Vault maakt gebruik van Azure AD-verificatie waarvoor Azure AD security principal toegang moet verlenen. Een beveiligings-principal voor Azure AD kan een gebruiker, een service-principal van de toepassing, een [beheerde identiteit voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)of een groep van elk type beveiligings-principals zijn.

### <a name="authentication-best-practices"></a>Aanbevolen procedures voor verificatie
Het is raadzaam beheerde identiteit te gebruiken voor toepassingen die zijn geïmplementeerd in Azure. Als u Azure-Services gebruikt die geen beheerde identiteit ondersteunen of als toepassingen op locatie worden geïmplementeerd, is de [Service-Principal met een certificaat](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) een mogelijk alternatief. In dat scenario moet het certificaat worden opgeslagen in Key Vault en vaak worden gedraaid. Service-principals met geheim kunnen worden gebruikt voor ontwikkel-en test omgevingen, en lokaal of in Cloud Shell het gebruik van User Principal wordt aanbevolen.

De bovenstaande verificaties scenario's worden ondersteund door de Azure Identity client-bibliotheek en geïntegreerd met Key Vault Sdk's. Azure Identity Library kan worden gebruikt in verschillende omgevingen en platformen zonder uw code te wijzigen. Met Azure-identiteit wordt ook automatisch een verificatie token opgehaald van aangemeld bij Azure-gebruiker met Azure CLI, Visual Studio, Visual Studio code en anderen. 

Zie voor meer informatie: 

| .NET | Python | Java | Javascript |
|--|--|--|--|
|[Azure Identity SDK .NET](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)|[Azure Identity SDK python](https://docs.microsoft.com/python/api/overview/azure/identity-readme)|[Azure Identity SDK java](https://docs.microsoft.com/java/api/overview/azure/identity-readme)|[Azure Identity SDK java script](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)|     

## <a name="manage-keys-certificates-and-secrets"></a>Sleutels, certificaten en geheimen beheren

Toegang tot sleutels, geheimen en certificaten wordt bepaald door het gegevens vlak. Toegangs beheer voor gegevens vlak kan worden uitgevoerd met behulp van het lokale kluis toegangs beleid of RBAC (preview-versie).

**Sleutels Api's en Sdk's**


| Azure CLI | PowerShell | REST-API | Resource Manager | .NET | Python | Java | Javascript |  
|--|--|--|--|--|--|--|--|
|[Verwijzing](/cli/azure/keyvault/key)<br>[Snelstartgids](../keys/quick-create-cli.md)|[Verwijzing](/powershell/module/az.keyvault/)<br>[Snelstartgids](../keys/quick-create-powershell.md)|[Verwijzing](/rest/api/keyvault/#key-operations)|N.v.t.|[Verwijzing](/dotnet/api/azure.security.keyvault.keys)|[Verwijzing](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Snelstartgids](../keys/quick-create-python.md)|[Verwijzing](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)|[Verwijzing](/javascript/api/@azure/keyvault-keys/)|

**Certificaten Api's en Sdk's**


| Azure CLI | PowerShell | REST-API | Resource Manager | .NET | Python | Java | Javascript |  
|--|--|--|--|--|--|--|--|
|[Verwijzing](/cli/azure/keyvault/certificate)<br>[Snelstartgids](../certificates/quick-create-cli.md)|[Verwijzing](/powershell/module/az.keyvault)<br>[Snelstartgids](../certificates/quick-create-powershell.md)|[Verwijzing](/rest/api/keyvault/#certificate-operations)|N.v.t.|[Verwijzing](/dotnet/api/azure.security.keyvault.certificates)|[Verwijzing](/python/api/overview/azure/keyvault-certificates-readme)<br>[Snelstartgids](../certificates/quick-create-python.md)|[Verwijzing](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)|[Verwijzing](/javascript/api/@azure/keyvault-certificates/)|

**Secrets-Api's en Sdk's**


| Azure CLI | PowerShell | REST-API | Resource Manager | .NET | Python | Java | Javascript |  
|--|--|--|--|--|--|--|--|
|[Verwijzing](/cli/azure/keyvault/secret)<br>[Snelstartgids](../secrets/quick-create-cli.md)|[Verwijzing](/powershell/module/az.keyvault/)<br>[Snelstartgids](../secrets/quick-create-powershell.md)|[Verwijzing](/rest/api/keyvault/#secret-operations)|[Verwijzing](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Snelstartgids](../secrets/quick-create-template.md)|[Verwijzing](/dotnet/api/azure.security.keyvault.secrets)<br>[Snelstartgids](../secrets/quick-create-net.md)|[Verwijzing](/python/api/overview/azure/keyvault-secrets-readme)<br>[Snelstartgids](../secrets/quick-create-python.md)|[Verwijzing](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Snelstartgids](../secrets/quick-create-java.md)|[Verwijzing](/javascript/api/@azure/keyvault-secrets/)<br>[Snelstartgids](../secrets/quick-create-node.md)|

Zie [client bibliotheken](client-libraries.md) voor installatie pakketten en bron code.

Zie [Key Vault data-vlak en toegangs beleid](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-access-policies) en Key Vault gegevenslaag en [RBAC (preview-versie)](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-azure-rbac-preview) voor meer informatie over de beveiliging van Key Vault gegevens vlak.

### <a name="code-examples"></a>Codevoorbeelden

Zie voor meer voor beelden van het gebruik van Key Vault met uw toepassingen:

- Code voorbeelden [Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) code voor Azure Key Vault. 

## <a name="how-tos"></a>Procedures

De volgende artikelen en scenario's bevatten toepassingsspecifieke richt lijnen voor het werken met Azure Key Vault:

- Toegang [tot Key Vault achter de firewall](access-behind-firewall.md) : voor toegang tot een sleutel kluis moet de client toepassing voor de sleutel kluis toegang hebben tot meerdere eind punten voor verschillende functies.
- Certificaten implementeren op Vm's vanuit Key Vault- [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows), [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-linux) : een Cloud toepassing die wordt uitgevoerd in een VM in azure, heeft een certificaat nodig. Hoe krijgt u dit certificaat vandaag nog aan deze VM?
- [Azure web app-certificaat implementeren via Key Vault](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
- Wijs een toegangs beleid toe ([cli](assign-access-policy-cli.md)  |  [Power shell](assign-access-policy-powershell.md)  |  -[Portal](assign-access-policy-portal.md)). 
- Het [gebruik van Key Vault zacht verwijderen met CLI](soft-delete-cli.md) begeleidt u bij het gebruik en de levens cyclus van een sleutel kluis en verschillende sleutel kluis objecten waarvoor het zacht verwijderen is ingeschakeld.
- [Veilige waarden (zoals wacht woorden) door geven tijdens de implementatie](../../azure-resource-manager/templates/key-vault-parameter.md) : wanneer u tijdens de implementatie een beveiligde waarde (zoals een wacht woord) moet door geven als een para meter, kunt u die waarde opslaan als een geheim in een Azure Key Vault en naar de waarde in andere Resource Manager-sjablonen verwijzen.

## <a name="integrated-with-key-vault"></a>Geïntegreerd met Key Vault

Deze artikelen zijn over andere scenario's en services die gebruikmaken van of worden geïntegreerd met Key Vault.

- [Versleuteling bij REST met Key Vault](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- Met [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) kunt u uw eigen Tenant sleutel beheren. In plaats van micro soft uw Tenant sleutel te laten beheren (standaard), kunt u bijvoorbeeld uw eigen Tenant sleutel beheren om te voldoen aan specifieke regels die van toepassing zijn op uw organisatie. Uw eigen tenantsleutel beheren wordt ook wel aangeduid als BYOK (Bring Your Own Key).

## <a name="key-vault-overviews-and-concepts"></a>Key Vault overzichten en concepten

- [Key Vault gedrag bij zacht verwijderen](soft-delete-overview.md) beschrijft een functie waarmee verwijderde objecten kunnen worden hersteld, of het verwijderen per ongeluk of opzettelijk is geslaagd.
- [Key Vault-client beperking](overview-throttling.md) gaat u naar de basis concepten van beperking en biedt een benadering voor uw app.
- [Key Vault Security-wereld](overview-security-worlds.md) beschrijft de relaties tussen regio's en beveiligings gebieden.

## <a name="social"></a>Sociaal netwerken

- [Key Vault blog](https://aka.ms/kvblog)
- [Key Vault forum](https://aka.ms/kvforum)
