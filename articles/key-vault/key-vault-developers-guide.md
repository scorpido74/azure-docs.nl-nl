---
title: Ontwikkelaars handleiding Azure Key Vault
description: Ontwikkel aars kunnen Azure Key Vault gebruiken voor het beheren van cryptografische sleutels binnen de Microsoft Azure omgeving.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 1bfd156f9fbdb69766e4588a342a695fbd79ca3a
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595301"
---
# <a name="azure-key-vault-developers-guide"></a>Ontwikkelaars handleiding Azure Key Vault

Met Key Vault kunt u veilig toegang krijgen tot gevoelige informatie in uw toepassingen:

- Sleutels en geheimen zijn beveiligd zonder dat u de code zelf hoeft te schrijven en u kunt ze eenvoudig gebruiken vanuit uw toepassingen.
- U kunt uw eigen eigen sleutels voor uw klanten beheren, zodat u zich kunt concentreren op het leveren van de basis software functies. Op deze manier zijn uw toepassingen niet verantwoordelijk voor de verantwoordelijkheid of de mogelijke aansprakelijkheid voor de Tenant sleutels en geheimen van uw klanten.
- Uw toepassing kan gebruikmaken van sleutels voor ondertekening en versleuteling, maar het sleutel beheer is ook extern vanuit uw toepassing, zodat uw oplossing geschikt is voor een geografisch gedistribueerde app.
- Vanaf de release van september 2016 van Key Vault kunnen uw toepassingen nu Key Vault certificaten beheren. Zie [informatie over sleutels, geheimen en certificaten](/rest/api/keyvault/about-keys--secrets-and-certificates)voor meer informatie.

Zie [Wat is Key Vault](key-vault-overview.md)voor meer algemene informatie over Azure Key Vault.

## <a name="public-previews"></a>Open bare voor beelden

Er wordt regel matig een open bare preview van een nieuwe Key Vault-functie uitgebracht. Probeer het eens en laat ons weten wat u van azurekeyvault@microsoft.com hebt, ons feedback e-mail adres.

## <a name="creating-and-managing-key-vaults"></a>Sleutel kluizen maken en beheren

Azure Key Vault biedt een manier voor het veilig opslaan van referenties en andere sleutels en geheimen, maar uw code moet worden geverifieerd voor Key Vault om ze op te halen. Beheerde identiteiten voor Azure-resources maken het oplossen van dit probleem eenvoudiger door Azure-Services een automatisch beheerde identiteit in Azure Active Directory (Azure AD) te geven. U kunt deze identiteit gebruiken voor verificatie bij alle services die ondersteuning bieden voor Azure AD-verificatie, inclusief Key Vault, zonder dat u referenties in uw code hoeft te hebben. 

Zie [overzicht van beheerde identiteiten](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten voor Azure-resources. Zie [toepassingen integreren met Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md)voor meer informatie over het werken met Aad.

Voordat u aan de slag gaat met sleutels, geheimen of certificaten in uw sleutel kluis, maakt en beheert u uw sleutel kluis via CLI, Power shell, Resource Manager-sjablonen of REST, zoals beschreven in de volgende artikelen:

- [Sleutel kluizen maken en beheren met CLI](key-vault-manage-with-cli2.md)
- [Sleutel kluizen maken en beheren met Power shell](key-vault-overview.md)
- [Een sleutel kluis maken en een geheim toevoegen via een Azure Resource Manager sjabloon](../azure-resource-manager/resource-manager-template-keyvault.md)
- [Sleutel kluizen maken en beheren met REST](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Coderen met Key Vault

Het Key Vault-beheer systeem voor programmeurs bestaat uit verschillende interfaces. Deze sectie bevat koppelingen naar alle talen en enkele voor beelden van code. 

### <a name="supported-programming-and-scripting-languages"></a>Ondersteunde programmeer-en script talen

#### <a name="rest"></a>REST

Al uw Key Vault-resources zijn toegankelijk via de REST-interface. kluizen, sleutels, geheimen enz. 

[Key Vault rest API verwijzing](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[Naslag informatie voor .net API voor Key Vault](/dotnet/api/microsoft.azure.keyvault).

Zie de [release opmerkingen](key-vault-dotnet2api-release-notes.md)voor meer informatie over de 2. x-versie van de .NET SDK.

#### <a name="java"></a>Java

[Java SDK voor Key Vault](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

In node. js worden de API voor Key Vault beheer en de Key Vault object-API gescheiden. In het volgende overzichts artikel vindt u toegang tot beide. 

[Azure Key Vault-modules voor node. js](/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Azure Key Vault bibliotheken voor python](/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>Azure CLI 2

[Azure CLI voor Key Vault](/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell voor Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)

### <a name="quickstart-guides"></a>Quick start-gidsen

- [Key Vault maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Aan de slag met Key Vault in node. js](https://github.com/Azure-Samples/key-vault-node-getting-started)

### <a name="code-examples"></a>Code voorbeelden

Zie voor meer voor beelden van het gebruik van Key Vault met uw toepassingen:

- Code voorbeelden [Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) code voor Azure Key Vault. 
- [Gebruik Azure Key Vault uit een webtoepassing](quick-create-net.md) : zelf studie om u te leren hoe u Azure Key Vault kunt gebruiken vanuit een webtoepassing in Azure. 

## <a name="how-tos"></a>Procedures

De volgende artikelen en scenario's bevatten toepassingsspecifieke richt lijnen voor het werken met Azure Key Vault:

- De [Tenant-id van de sleutel kluis wijzigen nadat het abonnement is verplaatst](key-vault-subscription-move-fix.md) : wanneer u uw Azure-abonnement van Tenant A naar Tenant b verplaatst, zijn uw bestaande sleutel kluizen niet toegankelijk voor de principals (gebruikers en toepassingen) in Tenant b. Los dit probleem op met behulp van deze hand leiding.
- Toegang [tot Key Vault achter de firewall](key-vault-access-behind-firewall.md) : voor toegang tot een sleutel kluis moet de client toepassing voor de sleutel kluis toegang hebben tot meerdere eind punten voor verschillende functies.
- [Het genereren en overdragen van met HSM beveiligde sleutels voor Azure Key Vault](key-vault-hsm-protected-keys.md) : dit helpt u bij het plannen, genereren en vervolgens overdragen van uw eigen met HSM beveiligde sleutels voor gebruik met Azure Key Vault.
- [Veilige waarden (zoals wacht woorden) door geven tijdens de implementatie](../azure-resource-manager/resource-manager-keyvault-parameter.md) : wanneer u tijdens de implementatie een beveiligde waarde (zoals een wacht woord) moet door geven als een para meter, kunt u die waarde opslaan als een geheim in een Azure Key Vault en naar de waarde in een andere Resource Manager verwijzen sjablonen.
- Het [gebruik van Key Vault voor Extensible Key Management met SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) -de SQL Server-connector voor Azure Key Vault stelt SQL Server en SQL-in-a-vm in staat om gebruik te maken van de Azure Key Vault-service als een EKM-provider (Extensible Key Management) voor het beveiligen van de versleutelings sleutels voor de koppeling van toepassingen; Transparent Data Encryption, versleuteling van back-ups en versleuteling op kolom niveau.
- [Certificaten implementeren op vm's van Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) : een Cloud toepassing die wordt uitgevoerd in een VM in azure, heeft een certificaat nodig. Hoe krijgt u dit certificaat vandaag nog aan deze VM?
- [Key Vault met end-to-end-draaiing en controle instellen](key-vault-key-rotation-log-monitoring.md) : in dit artikel wordt beschreven hoe u de sleutel wisseling en controle kunt instellen met Azure Key Vault.
- Het implementeren van een [Azure web app-certificaat via Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) biedt stapsgewijze instructies voor het implementeren van certificaten die zijn opgeslagen in Key Vault als onderdeel van [app service Certificate](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) aanbieding.
- [Veel toepassingen toegang verlenen tot een sleutel kluis](key-vault-group-permissions-for-apps.md) Key Vault Access Control-beleid ondersteunt Maxi maal 1024 vermeldingen. U kunt echter een Azure Active Directory beveiligings groep maken. Voeg alle bijbehorende service-principals toe aan deze beveiligings groep en ken vervolgens toegang toe aan deze beveiligings groep om Key Vault.
- Zie voor meer informatie over de taak-specifieke richt lijnen voor het integreren en gebruiken van sleutel kluizen met Azure de [Azure Resource Manager sjabloon voorbeelden van Ryan Jones voor Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- Het [gebruik van Key Vault zacht verwijderen met CLI](key-vault-soft-delete-cli.md) begeleidt u bij het gebruik en de levens cyclus van een sleutel kluis en verschillende sleutel kluis objecten waarvoor het zacht verwijderen is ingeschakeld.
- Het [gebruik van Key Vault zacht verwijderen met Power shell](key-vault-soft-delete-powershell.md) begeleidt u bij het gebruik en de levens cyclus van een sleutel kluis en diverse belang rijke kluis objecten waarvoor zacht verwijderen is ingeschakeld.

## <a name="integrated-with-key-vault"></a>Geïntegreerd met Key Vault

Deze artikelen zijn over andere scenario's en services die gebruikmaken van of worden geïntegreerd met Key Vault.

- [Azure Disk Encryption](../security/fundamentals/encryption-overview.md) maakt gebruik van de industrie standaard [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) -functie van Windows en de [DM-cryptografie](https://en.wikipedia.org/wiki/Dm-crypt) functie van Linux om volume versleuteling voor het besturings systeem en de gegevens schijven te bieden. De oplossing is geïntegreerd met Azure Key Vault om u te helpen de schijf versleutelings sleutels en geheimen in uw sleutel kluis-abonnement te controleren en te beheren, terwijl u ervoor zorgt dat alle gegevens in de virtuele-machine schijven op rest worden versleuteld in uw Azure-opslag.
- [Azure data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) biedt optie voor het versleutelen van gegevens die in het account zijn opgeslagen. Data Lake Store biedt sleutel beheer twee modi voor het beheren van uw Mek's (Master Encryption Keys), die vereist zijn voor het ontsleutelen van gegevens die zijn opgeslagen in de Data Lake Store. U kunt Data Lake Store de Mek's voor u laten beheren of ervoor kiezen om het eigendom van de Mek's te behouden met uw Azure Key Vault-account. U geeft de modus van sleutel beheer op tijdens het maken van een Data Lake Store-account.
- Met [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) kunt u uw eigen Tenant sleutel beheren. In plaats van micro soft uw Tenant sleutel te laten beheren (standaard), kunt u bijvoorbeeld uw eigen Tenant sleutel beheren om te voldoen aan specifieke regels die van toepassing zijn op uw organisatie. Het beheren van uw eigen Tenant sleutel wordt ook wel uw eigen sleutel of BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Key Vault overzichten en concepten

- [Key Vault gedrag bij zacht verwijderen](key-vault-ovw-soft-delete.md) beschrijft een functie waarmee verwijderde objecten kunnen worden hersteld, of het verwijderen per ongeluk of opzettelijk is geslaagd.
- [Key Vault-client beperking](key-vault-ovw-throttling.md) gaat u naar de basis concepten van beperking en biedt een benadering voor uw app.
- In het [overzicht van Key Vault Storage-account sleutels](key-vault-ovw-storage-keys.md) worden de sleutels Key Vault Integration Azure Storage accounts beschreven.
- [Key Vault Security-wereld](key-vault-ovw-security-worlds.md) beschrijft de relaties tussen regio's en beveiligings gebieden.

## <a name="social"></a>Sociaal

- [Key Vault blog](https://aka.ms/kvblog)
- [Key Vault forum](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Ondersteunende bibliotheken

- [Microsoft Azure Key Vault core-bibliotheek](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) biedt **IKey** -en **IKeyResolver** -interfaces voor het zoeken van sleutels uit id's en het uitvoeren van bewerkingen met sleutels.
- [Microsoft Azure Key Vault-extensies](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) bieden uitgebreide mogelijkheden voor Azure Key Vault.
