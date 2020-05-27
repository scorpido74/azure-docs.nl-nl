---
title: Veilige toegang tot een sleutel kluis-Azure Key Vault | Microsoft Docs
description: Toegangs machtigingen voor Azure Key Vault, sleutels en geheimen beheren. Behandelt het verificatie-en autorisatie model voor Key Vault en hoe u uw sleutel kluis kunt beveiligen.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: sudbalas
ms.openlocfilehash: 348ddb0fa8bd973a7e8ebcf5ae14de1eee57d5a5
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83827511"
---
# <a name="secure-access-to-a-key-vault"></a>Veilige toegang tot een sleutel kluis

Azure Key Vault is een Cloud service die versleutelings sleutels en geheimen beveiligt, zoals certificaten, verbindings reeksen en wacht woorden. Omdat deze gegevens gevoelig en zakelijk kritiek zijn, moet u de toegang tot uw sleutel kluizen beveiligen door alleen geautoriseerde toepassingen en gebruikers toe te staan. In dit artikel vindt u een overzicht van het toegangs model van Key Vault. Hierin worden verificatie en autorisatie uitgelegd en wordt beschreven hoe u de toegang tot uw sleutel kluizen kunt beveiligen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Overzicht van toegangs modellen

Toegang tot een sleutel kluis wordt beheerd via twee interfaces: het **beheer vlak** en het **gegevens vlak**. Het beheer vlak is waar u Key Vault zichzelf beheert. Bewerkingen in dit vlak zijn onder andere het maken en verwijderen van sleutel kluizen, het ophalen van Key Vault eigenschappen en het bijwerken van het toegangs beleid. Op het gegevens vlak kunt u werken met de gegevens die zijn opgeslagen in een sleutel kluis. U kunt sleutels, geheimen en certificaten toevoegen, verwijderen en wijzigen.

Voor toegang tot een sleutel kluis in een van beide vlieg tuigen moeten alle bellers (gebruikers of toepassingen) de juiste verificatie en autorisatie hebben. Met verificatie wordt de identiteit van de aanroeper bepaald. Autorisatie bepaalt welke bewerkingen de aanroeper kan uitvoeren.

Beide plannen gebruiken Azure Active Directory (Azure AD) voor verificatie. Voor autorisatie gebruikt het beheer vlak op rollen gebaseerd toegangs beheer (RBAC) en gebruikt het gegevens vlak een Key Vault toegangs beleid.

## <a name="active-directory-authentication"></a>Active Directory-verificatie

Wanneer u een sleutel kluis maakt in een Azure-abonnement, wordt deze automatisch gekoppeld aan de Azure AD-Tenant van het abonnement. Alle bellers in beide abonnementen moeten zich registreren bij deze Tenant en verifiëren voor toegang tot de sleutel kluis. In beide gevallen kunnen toepassingen op twee manieren toegang krijgen tot Key Vault:

- **Gebruiker en toegang tot toepassingen**: de toepassing heeft Key Vault namens een aangemelde gebruiker toegang. Voor beelden van dit type toegang zijn Azure PowerShell en de Azure Portal. Gebruikers toegang wordt op twee manieren verleend. Gebruikers hebben toegang tot Key Vault vanuit elke toepassing, of ze moeten een specifieke toepassing (aangeduid als _samengestelde identiteit_) gebruiken.
- **Alleen-toepassing toegang**: de toepassing wordt uitgevoerd als een daemon-service of achtergrond taak. Aan de toepassings-id wordt toegang verleend tot de sleutel kluis.

Voor beide typen toegang verifieert de toepassing met Azure AD. De toepassing gebruikt een [ondersteunde verificatie methode](../../active-directory/develop/authentication-scenarios.md) op basis van het toepassings type. De toepassing verkrijgt een token voor een resource in het vlak om toegang te verlenen. De resource is een eind punt in het beheer-of gegevens vlak, gebaseerd op de Azure-omgeving. De toepassing gebruikt het token en verzendt een REST API aanvraag naar Key Vault. Bekijk voor meer informatie de [volledige verificatie stroom](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Het model van één mechanisme voor verificatie voor beide abonnementen heeft verschillende voor delen:

- Organisaties kunnen de toegang centraal beheren voor alle sleutel kluizen in hun organisatie.
- Als een gebruiker deze verlaat, gaan ze onmiddellijk toegang tot alle sleutel kluizen in de organisatie.
- Organisaties kunnen verificatie aanpassen met behulp van de opties in azure AD, zoals om multi-factor Authentication in te scha kelen voor extra beveiliging.

## <a name="resource-endpoints"></a>Resource-eind punten

Toepassingen hebben toegang tot de abonnementen via eind punten. De toegangs controle voor de twee abonnementen werkt afzonderlijk. Als u een toepassing toegang wilt verlenen voor het gebruik van sleutels in een sleutel kluis, geeft u toegang tot het gegevens vlak met behulp van een Key Vault toegangs beleid. Als u een gebruiker lees toegang wilt verlenen voor Key Vault eigenschappen en tags, maar geen toegang tot gegevens (sleutels, geheimen of certificaten), verleent u beheer vlak toegang met RBAC.

De volgende tabel bevat de eind punten voor de beheer-en gegevens abonnementen.

| Toegangs &nbsp; vlak | Eindpunten voor toegang | Bewerkingen | Mechanisme voor toegangs &nbsp; beheer |
| --- | --- | --- | --- |
| Beheerlaag | **Internationaal**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure-Amerikaanse overheid:**<br> management.usgovcloudapi.net:443<br><br> **Azure Duitsland:**<br> management.microsoftazure.de:443 | Sleutel kluizen maken, lezen, bijwerken en verwijderen<br><br>Key Vault toegangs beleid instellen<br><br>Key Vault Tags instellen | Azure Resource Manager RBAC |
| Gegevenslaag | **Internationaal**<br> &lt;kluisnaam&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;kluisnaam&gt;.vault.azure.cn:443<br><br> **Azure-Amerikaanse overheid:**<br> &lt;kluisnaam&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Duitsland:**<br> &lt;kluisnaam&gt;.vault.microsoftazure.de:443 | Sleutels: ontsleutelen, versleutelen,<br> uitpakken, terugloop, verifiëren, ondertekenen,<br> ophalen, weer geven, bijwerken, maken,<br> importeren, verwijderen, back-ups maken en herstellen<br><br> Geheimen: ophalen, lijst, instellen, verwijderen | Toegangs beleid Key Vault |

## <a name="management-plane-and-rbac"></a>Beheer vlak en RBAC

In het beheer vlak gebruikt u RBAC (op rollen gebaseerd Access Control) om de bewerkingen te autoriseren die een aanroeper kan uitvoeren. In het RBAC-model heeft elk Azure-abonnement een exemplaar van Azure AD. U verleent toegang aan gebruikers, groepen en toepassingen vanuit deze map. Toegang is verleend om resources te beheren in het Azure-abonnement dat gebruikmaakt van het Azure Resource Manager-implementatie model. Als u toegang wilt verlenen, gebruikt u de [Azure Portal](https://portal.azure.com/), de [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest), [Azure PowerShell](/powershell/azureps-cmdlets-docs)of de [Azure Resource Manager rest-api's](https://msdn.microsoft.com/library/azure/dn906885.aspx).

U maakt een sleutel kluis in een resource groep en beheert de toegang met behulp van Azure AD. U verleent gebruikers of groepen de mogelijkheid om de sleutel kluizen in een resource groep te beheren. U verleent de toegang op een specifiek Scope niveau door de juiste RBAC-rollen toe te wijzen. Als u toegang wilt verlenen aan een gebruiker om sleutel kluizen te beheren, wijst u een vooraf gedefinieerde `key vault Contributor` rol toe aan de gebruiker op een specifiek bereik. De volgende Scope niveaus kunnen worden toegewezen aan een RBAC-rol:

- **Abonnement**: een RBAC-rol die is toegewezen op abonnements niveau, is van toepassing op alle resource groepen en resources in dat abonnement.
- **Resource groep**: een RBAC-rol die is toegewezen op het niveau van de resource groep, is van toepassing op alle resources in die resource groep.
- **Specifieke resource**: een RBAC-rol die is toegewezen voor een specifieke resource, is van toepassing op die resource. In dit geval is de resource een specifieke sleutel kluis.

Er zijn verschillende vooraf gedefinieerde rollen. Als een vooraf gedefinieerde rol niet aan uw behoeften voldoet, kunt u uw eigen rol definiëren. Zie [RBAC: ingebouwde rollen](../../role-based-access-control/built-in-roles.md)voor meer informatie.

> [!IMPORTANT]
> Als een gebruiker `Contributor` machtigingen heeft voor een sleutel kluis beheer vlak, kan de gebruiker zichzelf toegang verlenen tot het gegevens vlak door een Key Vault toegangs beleid in te stellen. U moet nauw keurig bepalen wie `Contributor` rollen toegang heeft tot uw sleutel kluizen. Zorg ervoor dat alleen geautoriseerde personen uw sleutel kluizen, sleutels, geheimen en certificaten kunnen gebruiken en beheren.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Gegevens vlak en toegangs beleid

U verleent toegang tot het gegevens vlak door Key Vault toegangs beleid in te stellen voor een sleutel kluis. Als u dit toegangs beleid wilt instellen, moet een gebruiker, groep of toepassing `Contributor` machtigingen hebben voor het beheer vlak voor die sleutel kluis.

U verleent toegang aan een gebruiker, groep of toepassing om specifieke bewerkingen uit te voeren voor sleutels of geheimen in een sleutel kluis. Key Vault ondersteunt Maxi maal 1.024 toegangs beleidsregels voor een sleutel kluis. Een Azure AD-beveiligings groep maken en gebruikers toevoegen aan de groep om gegevenslaag toegang te geven tot meerdere gebruikers.

<a id="key-vault-access-policies"></a>Key Vault toegangs beleid worden machtigingen afzonderlijk verleend aan sleutels, geheimen en certificaten. U kunt een gebruiker alleen toegang geven tot sleutels en niet op geheimen. Toegangs machtigingen voor sleutels, geheimen en certificaten bevinden zich op het niveau van de kluis. Key Vault toegangs beleid biedt geen ondersteuning voor granulaire machtigingen op object niveau, zoals een specifieke sleutel, geheim of certificaat. Als u toegangs beleid wilt instellen voor een sleutel kluis, gebruikt u de [Azure Portal](https://portal.azure.com/), de [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest), [Azure PowerShell](/powershell/azureps-cmdlets-docs)of de [rest api's van Key Vault beheer](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> Key Vault toegangs beleid is van toepassing op het niveau van de kluis. Wanneer een gebruiker gemachtigd is om sleutels te maken en te verwijderen, kunnen ze deze bewerkingen uitvoeren op alle sleutels in die sleutel kluis.
>

U kunt de toegang tot het gegevens vlak beperken met behulp van de [service-eind punten voor virtuele netwerken voor Azure Key Vault](overview-vnet-service-endpoints.md)). U kunt [firewalls en regels voor virtuele netwerken](network-security.md) configureren voor een extra beveiligingslaag.

## <a name="example"></a>Voorbeeld

In dit voor beeld ontwikkelen we een toepassing die gebruikmaakt van een certificaat voor TLS/SSL, Azure Storage voor het opslaan van gegevens en een RSA 2.048-bits sleutel voor teken bewerkingen. Onze toepassing wordt uitgevoerd op een virtuele machine (VM) van Azure (of een schaalset voor virtuele machines). We kunnen een sleutel kluis gebruiken om de toepassings geheimen op te slaan. We kunnen het Boots trap-certificaat dat door de toepassing wordt gebruikt, opslaan voor verificatie met Azure AD.

We hebben toegang tot de volgende opgeslagen sleutels en geheimen nodig:
- **TLS/SSL-certificaat**: wordt gebruikt voor TLS/SSL.
- **Opslag sleutel**: wordt gebruikt voor toegang tot het opslag account.
- **RSA 2.048-bits sleutel**: gebruikt voor Onderteken bewerkingen.
- **Boots trap-certificaat**: wordt gebruikt voor verificatie met Azure AD. Nadat de toegang is verleend, kunnen we de opslag sleutel ophalen en de RSA-sleutel gebruiken voor ondertekening.

We moeten de volgende rollen definiëren om aan te geven wie onze toepassing kan beheren, implementeren en controleren:
- **Security team**: it-mede werkers van het kantoor van de BBF (Chief Security Officer) of soort gelijke inzenders. Het beveiligings team is verantwoordelijk voor het behoorlijk veilig bewaren van geheimen. De geheimen kunnen TLS/SSL-certificaten, RSA-sleutels voor ondertekening, verbindings reeksen en opslag account sleutels bevatten.
- **Ontwikkel aars en Opera tors**: de mede werkers die de toepassing ontwikkelen en implementeren in Azure. De leden van dit team maken geen deel uit van het beveiligings personeel. Ze mogen geen toegang hebben tot gevoelige gegevens zoals TLS/SSL-certificaten en RSA-sleutels. Alleen de toepassing die ze implementeren, moet toegang hebben tot gevoelige gegevens.
- **Audi tors**: deze rol is voor inzenders die geen deel uitmaken van de ontwikkeling of algemene IT-mede werkers. Ze controleren het gebruik en onderhoud van certificaten, sleutels en geheimen om te zorgen voor naleving van beveiligings standaarden.

Er is nog een rol die zich buiten het bereik van de toepassing bevindt: de beheerder van het abonnement (of de resource groep). De abonnements beheerder stelt initiële toegangs machtigingen in voor het beveiligings team. Ze verlenen toegang tot het beveiligings team door gebruik te maken van een resource groep die de resources heeft die de toepassing nodig heeft.

We moeten de volgende bewerkingen voor onze rollen autoriseren:

**Beveiligingsteam**
- Maak sleutel kluizen.
- Schakel Key Vault logboek registratie in.
- Voeg sleutels en geheimen toe.
- Maak back-ups van sleutels voor herstel na nood gevallen.
- Stel Key Vault toegangs beleid in om machtigingen te verlenen aan gebruikers en toepassingen voor specifieke bewerkingen.
- Stel de sleutels en geheimen regel matig samen.

**Ontwikkelaars en operators**
- Referenties ophalen van het beveiligings team voor de Boots trap-en TLS/SSL-certificaten (vinger afdrukken), de opslag sleutel (geheime URI) en de RSA-sleutel (sleutel-URI) voor ondertekening.
- Ontwikkel en implementeer de toepassing om via een programma toegang te krijgen tot sleutels en geheimen.

**Auditors**
- Bekijk de Key Vault-Logboeken om het juiste gebruik van sleutels en geheimen te bevestigen en te voldoen aan de normen voor gegevens beveiliging.

De volgende tabel bevat een overzicht van de toegangs machtigingen voor onze rollen en toepassingen.

| Rol | Machtigingen voor de beheerlaag | Machtigingen voor de gegevenslaag |
| --- | --- | --- |
| Beveiligingsteam | Inzender Key Vault | Sleutels: back-ups maken, verwijderen, ophalen, importeren, sorteren, herstellen<br>Geheimen: alle bewerkingen |
| Ontwikkel aars en &nbsp; Opera tors | Machtiging voor Key Vault implementeren<br><br> **Opmerking**: met deze machtiging kunnen geïmplementeerde vm's worden gebruikt voor het ophalen van geheimen uit een sleutel kluis. | Geen |
| Auditors | Geen | Sleutels: weergeven<br>Geheimen: weergeven<br><br> **Opmerking**: met deze machtiging kunnen Audi tors kenmerken (tags, activerings datums, verval datums) controleren op sleutels en geheimen die niet in de logboeken zijn verzonden. |
| Toepassing | Geen | Sleutels: ondertekenen<br>Geheimen: ophalen |

De drie team rollen hebben toegang tot andere resources, samen met Key Vault machtigingen. Ontwikkel aars en Opera tors hebben toegang tot de resource typen nodig om Vm's (of de Web Apps functie van Azure App Service) te implementeren `Contributor` . Audi tors hebben lees toegang nodig tot het opslag account waarin de Key Vault logboeken worden opgeslagen.

Zie de volgende bronnen voor meer informatie over het implementeren van certificaten, toegangs sleutels en geheimen via een programma:
- Meer informatie over het [implementeren van certificaten op vm's vanuit een door de klant beheerde sleutel kluis](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (blog bericht).
- Bekijk de [Azure Key Vault-client voorbeelden](https://docs.microsoft.com/samples/browse/?term=Key%20Vault). Deze inhoud laat zien hoe u een Boots trap-certificaat kunt gebruiken om te verifiëren bij Azure AD voor toegang tot een sleutel kluis.

U kunt de meeste toegangs machtigingen verlenen met behulp van de Azure Portal. Als u gedetailleerde machtigingen wilt verlenen, kunt u Azure PowerShell of de Azure CLI gebruiken.

De Power shell-fragmenten in deze sectie zijn gebouwd met de volgende veronderstellingen:
- De Azure AD-beheerder heeft beveiligings groepen gemaakt die de drie rollen vertegenwoordigen: contoso Security team, contoso app DevOps en contoso app Audi tors. De beheerder heeft gebruikers toegevoegd aan hun respectieve groepen.
- Alle resources bevinden zich in de resource groep **ContosoAppRG** .
- De Key Vault-logboeken worden opgeslagen in het opslag account **contosologstorage** .
- De **ContosoKeyVault** -sleutel kluis en het **contosologstorage** -opslag account bevinden zich in dezelfde Azure-locatie.

De abonnements beheerder wijst de `key vault Contributor` rollen en `User Access Administrator` toe aan het beveiligings team. Met deze rollen kan het beveiligings team de toegang tot andere resources en sleutel kluizen beheren, beide in de resource groep **ContosoAppRG** .

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Het beveiligings team maakt een sleutel kluis en stelt logboek registratie-en toegangs machtigingen in.

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

Onze gedefinieerde aangepaste rollen kunnen alleen worden toegewezen aan het abonnement waarin de resource groep **ContosoAppRG** wordt gemaakt. Als u een aangepaste rol wilt gebruiken voor andere projecten in andere abonnementen, voegt u andere abonnementen toe aan het bereik voor de rol.

Voor onze DevOps-mede werkers wordt de aangepaste roltoewijzing voor de sleutel kluis- `deploy/action` machtiging binnen het bereik van de resource groep ingedeeld. Alleen Vm's die zijn gemaakt in de resource groep **ContosoAppRG** , hebben toegang tot de geheimen (TLS/SSL-en Boots trap-certificaten). Vm's die zijn gemaakt in andere resource groepen door een DevOps-lid hebben geen toegang tot deze geheimen, zelfs niet als de virtuele machine de geheime Uri's heeft.

In ons voor beeld wordt een eenvoudig scenario beschreven. Scenario's met Real-Life kunnen complexer zijn. U kunt de machtigingen voor uw sleutel kluis aanpassen op basis van uw behoeften. We gaan ervan uit dat het beveiligings team de sleutel en geheime verwijzingen (Uri's en vinger afdrukken) bevat die worden gebruikt door het DevOps-personeel in hun toepassingen. Ontwikkel aars en Opera tors hebben geen toegang tot het gegevens vlak nodig. We richten ons op hoe u uw sleutel kluis kunt beveiligen. Geef vergelijk bare aandacht wanneer u [uw vm's](https://azure.microsoft.com/services/virtual-machines/security/), [opslag accounts](../../storage/blobs/security-recommendations.md)en andere Azure-resources beveiligt.

> [!NOTE]
> In dit voor beeld ziet u hoe Key Vault toegang wordt vergrendeld tijdens de productie. Ontwikkel aars moeten hun eigen abonnement of resource groep hebben met volledige machtigingen voor het beheren van hun kluizen, Vm's en het opslag account waar ze de toepassing ontwikkelen.

U wordt aangeraden extra beveiligde toegang tot uw sleutel kluis in te stellen door [Key Vault firewalls en virtuele netwerken te configureren](network-security.md).

## <a name="resources"></a>Resources

* [Azure AD RBAC](../../role-based-access-control/role-assignments-portal.md)

* [RBAC: ingebouwde rollen](../../role-based-access-control/built-in-roles.md)

* [Resource Manager-implementatie en klassieke implementatie begrijpen](../../azure-resource-manager/management/deployment-models.md)

* [RBAC beheren met Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

* [RBAC beheren met de REST API](../../role-based-access-control/role-assignments-rest.md)

* [RBAC voor Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    In deze 2015 micro soft Ignite Conference-video worden de mogelijkheden voor toegangs beheer en rapportage in azure besproken. Daarnaast worden aanbevolen procedures voor het beveiligen van de toegang tot Azure-abonnementen besproken met behulp van Azure AD.

* [Toegang tot webtoepassingen toestaan met behulp van OAuth 2,0 en Azure AD](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

* [REST-Api's voor Key Vault beheer](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    De referentie voor de REST-Api's voor het programmatisch beheren van uw sleutel kluis, inclusief het instellen van Key Vault toegangs beleid.

* [Key Vault REST-Api's](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Toegangsbeheer voor sleutels](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

* [Toegangsbeheer voor geheimen](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

* Key Vault toegangs beleid [instellen](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) en [verwijderen](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) met behulp van Power shell.

## <a name="next-steps"></a>Volgende stappen

[Key Vault firewalls en virtuele netwerken](network-security.md)configureren.

Zie [Wat is Azure Key Vault?](overview.md)) voor een aan de slag-zelf studie voor een beheerder.

Zie [Azure Key Vault logging](logging.md)) voor meer informatie over de logboek registratie van het gebruik van Key Vault.

Zie [over sleutels en geheimen](https://msdn.microsoft.com/library/azure/dn903623.aspx)voor meer informatie over het gebruik van sleutels en geheimen met Azure Key Vault.

Als u vragen hebt over Key Vault, gaat u naar de [pagina micro soft Q&een vraag](https://docs.microsoft.com/answers/topics/azure-key-vault.html).
