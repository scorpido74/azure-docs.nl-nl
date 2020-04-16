---
title: Beveiligde toegang tot een sleutelkluis - Azure Key Vault | Microsoft Documenten
description: Beheer toegangsmachtigingen voor Azure Key Vault, sleutels en geheimen. Dekt het verificatie- en autorisatiemodel voor Key Vault en hoe u uw sleutelkluis beveiligen.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 0ae1b26bb2e01d388f3f91d94134bb9723a5a305
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432019"
---
# <a name="secure-access-to-a-key-vault"></a>Beveiligde toegang tot een sleutelkluis

Azure Key Vault is een cloudservice die versleutelingssleutels en -geheimen zoals certificaten, verbindingstekenreeksen en wachtwoorden beschermt. Omdat deze gegevens gevoelig en bedrijfskritisch zijn, moet u de toegang tot uw sleutelkluizen beveiligen door alleen geautoriseerde toepassingen en gebruikers toe te staan. In dit artikel vindt u een overzicht van het Key Vault-toegangsmodel. Het verklaart verificatie en autorisatie en beschrijft hoe u de toegang tot uw sleutelkluizen beveiligen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Overzicht van toegangsmodel

De toegang tot een sleutelkluis wordt geregeld via twee interfaces: het **beheervlak** en het **gegevensvlak.** Het managementvliegtuig is waar u Key Vault zelf beheert. Bewerkingen in dit vlak omvatten het maken en verwijderen van sleutelkluizen, het ophalen van Key Vault-eigenschappen en het bijwerken van toegangsbeleid. Het gegevensvlak is waar u werkt met de gegevens die zijn opgeslagen in een sleutelkluis. U sleutels, geheimen en certificaten toevoegen, verwijderen en wijzigen.

Om toegang te krijgen tot een sleutelkluis in beide vliegtuigen, moeten alle bellers (gebruikers of toepassingen) over de juiste verificatie en autorisatie beschikken. Verificatie bepaalt de identiteit van de beller. Autorisatie bepaalt welke bewerkingen de beller kan uitvoeren.

Beide vlakken gebruiken Azure Active Directory (Azure AD) voor verificatie. Voor autorisatie maakt het beheervlak gebruik van rbac (role-based access control) en het gegevensvlak maakt gebruik van een key vault-toegangsbeleid.

## <a name="active-directory-authentication"></a>Active Directory-verificatie

Wanneer u een sleutelkluis maakt in een Azure-abonnement, wordt deze automatisch gekoppeld aan de Azure AD-tenant van het abonnement. Alle bellers in beide vliegtuigen moeten zich registreren in deze tenant en zich verifiëren om toegang te krijgen tot de sleutelkluis. In beide gevallen hebben toepassingen op twee manieren toegang tot Key Vault:

- **Toegang tot de gebruiker plus toepassingen:** de toepassing heeft toegang tot Key Vault namens een aangemelde gebruiker. Voorbeelden van dit type toegang zijn Azure PowerShell en de Azure-portal. Gebruikerstoegang wordt op twee manieren verleend. Gebruikers hebben toegang tot Key Vault vanuit elke toepassing, of ze moeten een specifieke toepassing gebruiken (aangeduid als _samengestelde identiteit)._
- **Alleen-in-toepassing toegang:** De toepassing wordt uitgevoerd als een daemon service of achtergrond baan. De identiteit van de toepassing krijgt toegang tot de sleutelkluis.

Voor beide typen toegang wordt de toepassing geverifieerd met Azure AD. De toepassing maakt gebruik van elke [ondersteunde verificatiemethode](../../active-directory/develop/authentication-scenarios.md) op basis van het toepassingstype. De toepassing verwerft een token voor een resource in het vliegtuig om toegang te verlenen. De bron is een eindpunt in het beheer- of gegevensvlak, gebaseerd op de Azure-omgeving. De toepassing gebruikt het token en stuurt een REST API-verzoek naar Key Vault. Bekijk de hele [verificatiestroom](../../active-directory/develop/v2-oauth2-auth-code-flow.md)voor meer informatie.

Het model van één mechanisme voor verificatie voor beide vlakken heeft verschillende voordelen:

- Organisaties kunnen de toegang centraal beheren tot alle belangrijke kluizen in hun organisatie.
- Als een gebruiker vertrekt, verliest hij direct de toegang tot alle belangrijke kluizen in de organisatie.
- Organisaties kunnen verificatie aanpassen met behulp van de opties in Azure AD, bijvoorbeeld om meervoudige verificatie in te schakelen voor extra beveiliging.

## <a name="resource-endpoints"></a>Resourceeindpunten

Toepassingen hebben toegang tot de vlakken via eindpunten. De toegangscontroles voor de twee vliegtuigen werken onafhankelijk van elkaar. Als u een toepassing toegang wilt verlenen tot het gebruik van sleutels in een sleutelkluis, verleent u toegang tot gegevensvlak met behulp van een toegangsbeleid voor sleutelkluizen. Als u een gebruiker leestoegang wilt verlenen tot eigenschappen en tags van Key Vault, maar geen toegang tot gegevens (sleutels, geheimen of certificaten), verleent u beheervlaktoegang met RBAC.

In de volgende tabel worden de eindpunten voor de beheer- en gegevensvlakken weergegeven.

| Toegangsvlak&nbsp; | Eindpunten voor toegang | Bewerkingen | Toegangscontrolemechanisme&nbsp; |
| --- | --- | --- | --- |
| Beheerlaag | **Globale:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure van de Amerikaanse overheid:**<br> management.usgovcloudapi.net:443<br><br> **Azure Duitsland:**<br> management.microsoftazure.de:443 | Belangrijke kluizen maken, lezen, bijwerken en verwijderen<br><br>Toegangsbeleid voor key vault instellen<br><br>Key Vault-tags instellen | Azure Resource Manager RBAC |
| Gegevenslaag | **Globale:**<br> &lt;kluisnaam&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;kluisnaam&gt;.vault.azure.cn:443<br><br> **Azure van de Amerikaanse overheid:**<br> &lt;kluisnaam&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Duitsland:**<br> &lt;kluisnaam&gt;.vault.microsoftazure.de:443 | Sleutels: decoderen, versleutelen,<br> uitpakken, omwikkelen, verifiëren, ondertekenen,<br> krijgen, aanbieden, bijwerken, maken,<br> importeren, verwijderen, back-ups maken, herstellen<br><br> Geheimen: krijgen, lijst, instellen, verwijderen | Toegangsbeleid voor belangrijke vault |

## <a name="management-plane-and-rbac"></a>Managementvliegtuig en RBAC

In het beheervlak gebruikt u RBAC(Role Based Access Control) om de bewerkingen te autoriseren die een beller kan uitvoeren. In het RBAC-model heeft elk Azure-abonnement een exemplaar van Azure AD. U verleent toegang tot gebruikers, groepen en toepassingen vanuit deze map. Toegang wordt verleend voor het beheren van resources in het Azure-abonnement die gebruikmaken van het Azure Resource Manager-implementatiemodel. Als u toegang wilt verlenen, gebruikt u de [Azure-portal](https://portal.azure.com/), de [Azure CLI,](/cli/azure/install-azure-cli?view=azure-cli-latest) [Azure PowerShell](/powershell/azureps-cmdlets-docs)of de [API's](https://msdn.microsoft.com/library/azure/dn906885.aspx)azure resource manager .

U maakt een sleutelkluis in een brongroep en beheert toegang met Azure AD. U verleent gebruikers of groepen de mogelijkheid om de sleutelkluizen in een resourcegroep te beheren. U verleent de toegang op een specifiek scopeniveau door de juiste RBAC-rollen toe te wijzen. Als u toegang wilt verlenen aan een gebruiker om `key vault Contributor` sleutelkluizen te beheren, wijst u een vooraf gedefinieerde rol toe aan de gebruiker op een specifiek bereik. De volgende scopeniveaus kunnen worden toegewezen aan een RBAC-rol:

- **Abonnement**: Een RBAC-rol die op abonnementsniveau is toegewezen, is van toepassing op alle resourcegroepen en resources binnen dat abonnement.
- **Resourcegroep**: Een RBAC-rol die is toegewezen op het niveau van de resourcegroep, is van toepassing op alle resources in die resourcegroep.
- **Specifieke resource:** een RBAC-rol die is toegewezen voor een specifieke resource, is van toepassing op die resource. In dit geval is de resource een specifieke sleutelkluis.

Er zijn verschillende vooraf gedefinieerde rollen. Als een vooraf gedefinieerde rol niet aan uw behoeften voldoet, u uw eigen rol definiëren. Zie [RBAC: Ingebouwde rollen](../../role-based-access-control/built-in-roles.md)voor meer informatie.

> [!IMPORTANT]
> Als een `Contributor` gebruiker machtigingen heeft voor een belangrijk vault management vliegtuig, kan de gebruiker zichzelf toegang verlenen tot het gegevensvlak door een Key Vault toegangsbeleid in te stellen. U moet goed controleren `Contributor` wie roltoegang heeft tot uw sleutelkluizen. Zorg ervoor dat alleen geautoriseerde personen toegang hebben tot uw sleutelkluizen, sleutels, geheimen en certificaten en deze beheren.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Beleid voor gegevensvlak en toegang

U verleent toegang tot gegevensvlak door key vault-toegangsbeleid in te stellen voor een sleutelkluis. Als u dit toegangsbeleid wilt instellen, moet `Contributor` een gebruiker, groep of toepassing machtigingen hebben voor het beheervlak voor die sleutelkluis.

U verleent een gebruiker, groep of toepassing toegang om specifieke bewerkingen uit te voeren voor sleutels of geheimen in een sleutelkluis. Key Vault ondersteunt maximaal 1.024 toegangsbeleidsvermeldingen voor een sleutelkluis. Als u gegevensvlaktoegang wilt verlenen aan verschillende gebruikers, maakt u een Azure AD-beveiligingsgroep en voegt u gebruikers toe aan die groep.

<a id="key-vault-access-policies"></a>Key Vault-toegangsbeleid verleent machtigingen afzonderlijk voor sleutels, geheimen en certificaat. U een gebruiker alleen toegang verlenen tot sleutels en niet tot geheimen. Toegangsmachtigingen voor sleutels, geheimen en certificaten bevinden zich op het niveau van de kluis. Key Vault-toegangsbeleid biedt geen ondersteuning voor gedetailleerde machtigingen op objectniveau, zoals een specifieke sleutel, geheim of certificaat. Als u toegangsbeleidsregels voor een sleutelkluis wilt instellen, gebruikt u de [Azure-portal,](https://portal.azure.com/)de [Azure CLI,](/cli/azure/install-azure-cli?view=azure-cli-latest) [Azure PowerShell](/powershell/azureps-cmdlets-docs)of de [API's voor het sleutelkluisbeheer](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> Het toegangsbeleid voor belangrijke vaults is van toepassing op het niveau van de kluis. Wanneer een gebruiker toestemming krijgt om sleutels te maken en te verwijderen, kunnen deze bewerkingen worden uitgevoerd op alle sleutels in die sleutelkluis.
>

U de toegang tot gegevensvlak beperken door [eindpunten van virtuele netwerkservices voor Azure Key Vault](overview-vnet-service-endpoints.md)te gebruiken). U [firewalls en virtuele netwerkregels](network-security.md) configureren voor een extra beveiligingslaag.

## <a name="example"></a>Voorbeeld

In dit voorbeeld ontwikkelen we een toepassing die een certificaat voor TLS/SSL, Azure Storage gebruikt om gegevens op te slaan en een RSA 2048-bits sleutel voor tekenbewerkingen. Onze toepassing wordt uitgevoerd in een Virtuele Azure-machine (VM) (of een virtuele machineschaalset). We kunnen een sleutelkluis gebruiken om de geheimen van de toepassing op te slaan. We kunnen het bootstrap-certificaat opslaan dat door de toepassing wordt gebruikt om te verifiëren met Azure AD.

We hebben toegang nodig tot de volgende opgeslagen sleutels en geheimen:
- **TLS/SSL-certificaat**: Gebruikt voor TLS/SSL.
- **Opslagsleutel**: wordt gebruikt om toegang te krijgen tot het opslagaccount.
- **RSA 2,048-bit toets**: Wordt gebruikt voor tekenbewerkingen.
- **Bootstrap-certificaat:** wordt gebruikt om te verifiëren met Azure AD. Nadat toegang is verleend, kunnen we de opslagsleutel ophalen en de RSA-sleutel gebruiken om te ondertekenen.

We moeten de volgende rollen definiëren om aan te geven wie onze toepassing kan beheren, implementeren en controleren:
- **Beveiligingsteam**: IT-personeel van het kantoor van de CSO (Chief Security Officer) of soortgelijke medewerkers. Het beveiligingsteam is verantwoordelijk voor de juiste bewaring van geheimen. De geheimen kunnen TLS/SSL-certificaten, RSA-sleutels voor ondertekening, verbindingstekenreeksen en opslagaccountsleutels bevatten.
- **Ontwikkelaars en operators**: de medewerkers die de toepassing ontwikkelen en implementeren in Azure. De leden van dit team maken geen deel uit van het beveiligingspersoneel. Ze mogen geen toegang hebben tot gevoelige gegevens zoals TLS/SSL-certificaten en RSA-sleutels. Alleen de toepassing die ze implementeren, moet toegang hebben tot gevoelige gegevens.
- **Auditors**: Deze rol is voor bijdragers die geen lid zijn van de ontwikkeling of het algemene IT-personeel. Ze bekijken het gebruik en onderhoud van certificaten, sleutels en geheimen om ervoor te zorgen dat de beveiligingsnormen worden nageleefd.

Er is nog een andere rol die buiten het bereik van onze toepassing valt: de beheerder van het abonnement (of de brongroep). De abonnementsbeheerder stelt de eerste toegangsmachtigingen voor het beveiligingsteam in. Ze verlenen toegang tot het beveiligingsteam met behulp van een resourcegroep die over de resources beschikt die vereist zijn voor de toepassing.

We moeten de volgende bewerkingen voor onze rollen autoriseren:

**Beveiligingsteam**
- Maak sleutelkluizen.
- Schakel Key Vault-logboekregistratie in.
- Voeg sleutels en geheimen toe.
- Maak back-ups van sleutels voor noodherstel.
- Stel key vault-toegangsbeleid in om gebruikers en toepassingen machtigingen toe te kennen voor specifieke bewerkingen.
- Rol de sleutels en geheimen periodiek.

**Ontwikkelaars en operators**
- Ontvang referenties van het beveiligingsteam voor de bootstrap- en TLS/SSL-certificaten (duimafdrukken), opslagsleutel (geheime URI) en RSA-toets (sleutel URI) voor ondertekening.
- Ontwikkel en implementeer de toepassing om programmatisch toegang te krijgen tot sleutels en geheimen.

**Auditors**
- Bekijk de Key Vault-logboeken om het juiste gebruik van sleutels en geheimen en naleving van de normen voor gegevensbeveiliging te bevestigen.

In de volgende tabel worden de toegangsmachtigingen voor onze rollen en toepassingen samengevat.

| Rol | Machtigingen voor de beheerlaag | Machtigingen voor de gegevenslaag |
| --- | --- | --- |
| Beveiligingsteam | Belangrijke vault-bijdrager | Sleutels: back-ups maken, verwijderen, ophalen, importeren, sorteren, herstellen<br>Geheimen: alle operaties |
| Ontwikkelaars en&nbsp;operators | Key Vault-implementatiemachtigingen<br><br> **Opmerking:** met deze machtiging kunnen geïmplementeerde VM's geheimen uit een sleutelkluis ophalen. | Geen |
| Auditors | Geen | Sleutels: weergeven<br>Geheimen: weergeven<br><br> **Opmerking:** Met deze machtiging kunnen auditors kenmerken (tags, activeringsdatums, vervaldatums) inspecteren op sleutels en geheimen die niet in de logboeken worden uitgezonden. |
| Toepassing | Geen | Sleutels: ondertekenen<br>Geheimen: ophalen |

De drie teamrollen hebben toegang nodig tot andere bronnen, samen met Key Vault-machtigingen. Om VM's (of de Web Apps-functie van Azure `Contributor` App Service) te implementeren, hebben ontwikkelaars en operators toegang nodig tot die brontypen. Auditors hebben leestoegang nodig tot het Opslagaccount waar de Key Vault-logboeken worden opgeslagen.

Zie de volgende bronnen voor meer informatie over het programmatisch implementeren van certificaten, toegangssleutels en geheimen:
- Meer informatie over het [implementeren van certificaten naar VM's vanuit een door de klant beheerde sleutelkluis](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (blogbericht).
- Download de [clientvoorbeelden van Azure Key Vault.](https://www.microsoft.com/download/details.aspx?id=45343) Deze inhoud illustreert hoe u een bootstrap-certificaat gebruiken om te verifiëren aan Azure AD om toegang te krijgen tot een sleutelkluis.

U de meeste toegangsmachtigingen verlenen met behulp van de Azure-portal. Als u gedetailleerde machtigingen wilt verlenen, u Azure PowerShell of Azure CLI gebruiken.

De PowerShell-fragmenten in deze sectie zijn gebouwd met de volgende aannames:
- De Azure AD-beheerder heeft beveiligingsgroepen gemaakt om de drie rollen te vertegenwoordigen: Contoso Security Team, Contoso App DevOps en Contoso App Auditors. De beheerder heeft gebruikers toegevoegd aan hun respectievelijke groepen.
- Alle bronnen bevinden zich in de **ContosoAppRG-brongroep.**
- De Key Vault-logboeken worden opgeslagen in het **opslagaccount voor contosologopslag.**
- De **ContosoKeyVault-sleutelkluis** en het **contosologstorageopslagaccount** bevinden zich op dezelfde Azure-locatie.

De abonnementsbeheerder wijst `key vault Contributor` `User Access Administrator` de en rollen toe aan het beveiligingsteam. Met deze rollen kan het beveiligingsteam de toegang tot andere bronnen en belangrijke kluizen beheren, die beide in de **ContosoAppRG-brongroep** zijn.

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Het beveiligingsteam maakt een sleutelkluis en stelt registratie- en toegangsmachtigingen in.

```powershell
# Create a key vault and enable logging
$sa = Get-AzStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent

# Set up data plane permissions for the Contoso Security Team role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Set up management plane permissions for the Contoso App DevOps role
# Create the new role from an existing role
$devopsrole = Get-AzRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App DevOps"
$devopsrole.Description = "Can deploy VMs that need secrets from a key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permissions for the Contoso App DevOps role so members can deploy VMs with secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzRoleDefinition -Role $devopsrole

# Assign the new role to the Contoso App DevOps security group
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Set up data plane permissions for the Contoso App Auditors role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

Onze gedefinieerde aangepaste rollen zijn alleen toe te wijzen op het abonnement waarbij de **ContosoAppRG-resourcegroep** wordt gemaakt. Als u een aangepaste rol wilt gebruiken voor andere projecten in andere abonnementen, voegt u andere abonnementen toe aan de ruimte voor de rol.

Voor onze DevOps-medewerkers is de aangepaste `deploy/action` roltoewijzing voor de sleutelkluismachtiging aangepast aan de resourcegroep. Alleen VM's die zijn gemaakt in de **ContosoAppRG-brongroep** hebben toegang tot de geheimen (TLS/SSL- en bootstrap-certificaten). VM's die door een DevOps-lid in andere resourcegroepen zijn gemaakt, hebben geen toegang tot deze geheimen, zelfs niet als de VM de geheime URI's heeft.

Ons voorbeeld beschrijft een eenvoudig scenario. Real-life scenario's kunnen complexer zijn. U machtigingen aanpassen aan uw sleutelkluis op basis van uw behoeften. We gingen ervan uit dat het beveiligingsteam de belangrijkste en geheime referenties (URI's en duimafdrukken) levert, die door de DevOps-medewerkers worden gebruikt in hun toepassingen. Ontwikkelaars en operators hebben geen toegang tot gegevensvliegtuigen nodig. We hebben ons gericht op het beveiligen van uw sleutelkluis. Geef dezelfde overweging wanneer u [uw VM's,](https://azure.microsoft.com/services/virtual-machines/security/) [opslagaccounts](../../storage/blobs/security-recommendations.md)en andere Azure-bronnen beveiligt.

> [!NOTE]
> In dit voorbeeld ziet u hoe Key Vault-toegang is vergrendeld in productie. Ontwikkelaars moeten hun eigen abonnements- of resourcegroep hebben met volledige machtigingen voor het beheren van hun kluizen, VM's en het opslagaccount waar ze de toepassing ontwikkelen.

We raden u aan extra beveiligde toegang tot uw sleutelkluis in te stellen door [Key Vault-firewalls en virtuele netwerken te configureren.](network-security.md)

## <a name="resources"></a>Resources

* [Azure AD RBAC](../../role-based-access-control/role-assignments-portal.md)

* [RBAC: Ingebouwde rollen](../../role-based-access-control/built-in-roles.md)

* [Inzicht in de implementatie van Resource Manager en klassieke implementatie](../../azure-resource-manager/management/deployment-models.md)

* [RBAC beheren met Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

* [RBAC beheren met de REST API](../../role-based-access-control/role-assignments-rest.md)

* [RBAC voor Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Deze 2015 Microsoft Ignite-conferentievideo bespreekt toegangsbeheer- en rapportagemogelijkheden in Azure. Het onderzoekt ook best practices voor het beveiligen van toegang tot Azure-abonnementen met Behulp van Azure AD.

* [Toegang tot webtoepassingen autoriseren met OAuth 2.0 en Azure AD](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

* [Key Vault Management REST API's](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    De referentie voor de REST API's om uw key vault programmatisch te beheren, inclusief het instellen van Key Vault-toegangsbeleid.

* [Key Vault REST API's](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Toegangsbeheer voor sleutels](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

* [Toegangsbeheer voor geheimen](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

* [Stel](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) het toegangsbeleid voor Key Vault in en [verwijder](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) deze met PowerShell.

## <a name="next-steps"></a>Volgende stappen

[Key Vault-firewalls en virtuele netwerken configureren.](network-security.md)

Zie Wat is Azure Key Vault voor een zelfstudie voor een [beheerder?](overview.md)).

Zie [Azure Key Vault-logboekregistratie](logging.md)voor meer informatie over use logging voor Key Vault).

Zie [Over sleutels en geheimen](https://msdn.microsoft.com/library/azure/dn903623.aspx)voor meer informatie over het gebruik van sleutels en geheimen met Azure Key Vault.

Als je vragen hebt over Key Vault, bezoek dan de [forums.](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)
