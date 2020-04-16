---
title: Gids voor Azure Key Vault-ontwikkelaars
description: Ontwikkelaars kunnen Azure Key Vault gebruiken om cryptografische sleutels binnen de Microsoft Azure-omgeving te beheren.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mbaldwin
ms.openlocfilehash: 18e1ab1d01394d585cfb06ced8c5fbac04114177
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431954"
---
# <a name="azure-key-vault-developers-guide"></a>Gids voor Azure Key Vault-ontwikkelaars

Met Key Vault u veilig toegang krijgen tot gevoelige informatie vanuit uw toepassingen:

- Sleutels en geheimen worden beschermd zonder de code zelf te hoeven schrijven en u ze gemakkelijk gebruiken vanuit uw toepassingen.
- U bent in staat om uw klanten eigen en beheren van hun eigen sleutels, zodat u zich concentreren op het verstrekken van de belangrijkste software functies. Op deze manier zijn uw toepassingen niet de verantwoordelijkheid of potentiële aansprakelijkheid voor de tenantsleutels en -geheimen van uw klanten.
- Uw toepassing kan sleutels gebruiken voor ondertekening en versleuteling, maar houdt het sleutelbeheer extern van uw toepassing, waardoor uw oplossing geschikt is als een geografisch gedistribueerde app.
- Key Vault-certificaten beheren. Zie [Certificaten voor](../certificates/about-certificates.md) meer informatie

Zie [Wat is Key Vault](overview.md)voor meer algemene informatie over Azure Key Vault).

## <a name="public-previews"></a>Openbare previews

Periodiek geven we een openbare preview vrij van een nieuwe Key Vault-functie. Probeer deze en laat ons weten azurekeyvault@microsoft.comwat je denkt via , onze feedback e-mailadres.

## <a name="creating-and-managing-key-vaults"></a>Sleutelkluizen maken en beheren

Azure Key Vault biedt een manier voor het veilig opslaan van referenties en andere sleutels en geheimen, maar uw code moet worden geverifieerd voor Key Vault om ze op te halen. Beheerde identiteiten voor Azure-resources maken het oplossen van dit probleem eenvoudiger door Azure-services een automatisch beheerde identiteit te geven in Azure Active Directory (Azure AD). U kunt deze identiteit gebruiken voor verificatie bij alle services die ondersteuning bieden voor Azure AD-verificatie, inclusief Key Vault, zonder dat u referenties in uw code hoeft te hebben. 

Zie [het overzicht beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten voor Azure-resources. Zie Toepassingen integreren met Azure [Active Directory](../../active-directory/develop/active-directory-integrating-applications.md)voor meer informatie over het werken met Azure AD.

Voordat u met sleutels, geheimen of certificaten in uw sleutelkluis werkt, maakt en beheert u uw sleutelkluis via CLI, PowerShell, Resource Manager Templates of REST, zoals beschreven in de volgende artikelen:

- [Sleutelkluizen maken en beheren met CLI](../secrets/quick-create-cli.md)
- [Sleutelkluizen maken en beheren met PowerShell](../secrets/quick-create-powershell.md)
- [Sleutelkluizen maken en beheren met de Azure-poort](../secrets/quick-create-portal.md)
- [Sleutelkluizen maken en beheren met Python](../secrets/quick-create-python.md)
- [Sleutelkluizen maken en beheren met Java](../secrets/quick-create-java.md)
- [Sleutelkluizen maken en beheren met Node.js](../secrets/quick-create-node.md)
- [Sleutelkluizen maken en beheren met .NET (v4 SDK)](../secrets/quick-create-net.md)
- [Een sleutelkluis maken en een geheim toevoegen via een Azure Resource Manager-sjabloon](../secrets/quick-create-template.md)
- [Sleutelkluizen maken en beheren met REST](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Codering met Sleutelkluis

Het Key Vault management systeem voor programmeurs bestaat uit verschillende interfaces. Deze sectie bevat links naar alle talen en enkele codevoorbeelden. 

### <a name="supported-programming-and-scripting-languages"></a>Ondersteunde programmeer- en scripttalen

#### <a name="rest"></a>REST

Al uw Key Vault-bronnen zijn toegankelijk via de REST-interface; kluizen, sleutels, geheimen, enz. 

[Key Vault REST API-referentie](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[.NET API-verwijzing voor Key Vault](/dotnet/api/overview/azure/key-vault?view=azure-dotnet).

Zie de [releasenotes](dotnet2api-release-notes.md)voor meer informatie over de 2.x-versie van de .NET SDK.

#### <a name="java"></a>Java

[Java SDK voor Key Vault](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

In Node.js zijn de Key Vault management API en de Key Vault object API gescheiden. Het volgende overzichtsartikel geeft u toegang tot beide. 

[Azure Key Vault-modules voor Node.js](/javascript/api/overview/azure/key-vault?view=azure-node-latest)

#### <a name="python"></a>Python

[Azure Key Vault-bibliotheken voor Python](/python/api/overview/azure/key-vault?view=azure-python)

#### <a name="azure-cli"></a>Azure CLI

[Azure CLI voor Key Vault](/cli/azure/keyvault?view=azure-cli-latest)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell voor Key Vault](/powershell/module/az.keyvault/?view=azps-3.6.1#key_vault)

### <a name="code-examples"></a>Codevoorbeelden

Zie voor volledige voorbeelden met Key Vault met uw toepassingen:

- [Azure Key Vault-codevoorbeelden](https://azure.microsoft.com/resources/samples/?service=key-vault) - Codevoorbeelden voor Azure Key Vault. 
- [Gebruik Azure Key Vault vanuit een webtoepassing](../secrets/quick-create-net.md) - zelfstudie om te leren hoe u Azure Key Vault gebruiken vanuit een webtoepassing in Azure. 

## <a name="how-tos"></a>Procedures

De volgende artikelen en scenario's bieden taakspecifieke richtlijnen voor het werken met Azure Key Vault:

- [Sleutelkluistenant-id wijzigen na abonnementsverplaatsing](subscription-move-fix.md) - Wanneer u uw Azure-abonnement verplaatst van tenant A naar tenant B, zijn uw bestaande sleutelkluizen niet toegankelijk voor de principals (gebruikers en toepassingen) in tenant B. Los dit op met deze handleiding.
- [Toegang tot Key Vault achter firewall](access-behind-firewall.md) - Om toegang te krijgen tot een sleutelkluis moet uw key vault client applicatie toegang hebben tot meerdere eindpunten voor verschillende functionaliteiten.
- [HSM-beveiligde sleutels voor Azure Key Vault genereren en overdragen](../keys/hsm-protected-keys.md) - Dit helpt u bij het plannen, genereren en vervolgens overdragen van uw eigen HSM-beveiligde sleutels voor gebruik met Azure Key Vault.
- [Hoe u beveiligde waarden (zoals wachtwoorden) doorgeeft tijdens de implementatie](../../azure-resource-manager/templates/key-vault-parameter.md) - Wanneer u een veilige waarde (zoals een wachtwoord) als parameter moet doorgeven tijdens de implementatie, u die waarde opslaan als een geheim in een Azure Key Vault en verwijzen naar de waarde in andere Resource Manager-sjablonen.
- [Key Vault gebruiken voor uitbreidbaar sleutelbeheer met SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) - De SQL Server Connector voor Azure Key Vault stelt SQL Server en SQL-in-a-VM in staat om de Azure Key Vault-service te gebruiken als een Extensible Key Management (EKM)-provider om de versleutelingssleutels voor toepassingenkoppeling te beschermen; Transparante gegevensversleuteling, back-upversleuteling en kolomniveauversleuteling.
- [Certificaten implementeren in VM's vanuit Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - Een cloudtoepassing die wordt uitgevoerd in een VM op Azure heeft een certificaat nodig. Hoe krijg je dit certificaat vandaag nog in deze VM?
- [Sleutelkluis instellen met end-to-end sleutelrotatie en -controle](../secrets/key-rotation-log-monitoring.md) - Hiermee u sleutelrotatie en -controle instellen met Azure Key Vault.
- [Het implementeren van Azure Web App Certificate through Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) biedt stapsgewijze instructies voor het implementeren van certificaten die zijn opgeslagen in Key Vault als onderdeel van het aanbieden van App [Service-certificaten.](https://azure.microsoft.com/blog/internals-of-app-service-certificate/)
- [Toestemming verlenen aan veel toepassingen om toegang te krijgen tot een sleutelkluis](group-permissions-for-apps.md) Key Vault-toegangscontrolebeleid ondersteunt maximaal 1024 vermeldingen. U echter wel een Azure Active Directory-beveiligingsgroep maken. Voeg alle bijbehorende serviceprincipals toe aan deze beveiligingsgroep en geef deze beveiligingsgroep toegang tot Key Vault.
- Zie [ryan Jones' Azure Resource Manager-sjabloonvoorbeelden voor Key Vault voor](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)meer taakspecifieke richtlijnen voor het integreren en gebruiken van Key Vaults met Azure.
- [Hoe u Key Vault soft-delete gebruiken met CLI,](soft-delete-cli.md) leidt u door het gebruik en de levenscyclus van een sleutelkluis en verschillende sleutelkluisobjecten met soft-delete ingeschakeld.
- [Hoe u Key Vault soft-delete gebruiken met PowerShell,](soft-delete-powershell.md) leidt u door het gebruik en de levenscyclus van een sleutelkluis en verschillende sleutelkluisobjecten met soft-delete ingeschakeld.

## <a name="integrated-with-key-vault"></a>Geïntegreerd met Key Vault

Deze artikelen gaan over andere scenario's en services die key vault gebruiken of integreren.

- [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md) maakt gebruik van de industriestandaard [BitLocker-functie](https://technet.microsoft.com/library/cc732774.aspx) van Windows en de [DM-Crypt-functie](https://en.wikipedia.org/wiki/Dm-crypt) van Linux om volumeversleuteling te bieden voor het besturingssysteem en de gegevensschijven. De oplossing is geïntegreerd met Azure Key Vault om u te helpen de schijfversleutelingssleutels en -geheimen in uw key vault-abonnement te beheren en te beheren, terwijl u ervoor zorgt dat alle gegevens in de schijven van de virtuele machine in uw Azure-opslag in rust worden versleuteld.
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md) biedt de mogelijkheid voor versleuteling van gegevens die in het account zijn opgeslagen. Voor sleutelbeheer biedt Data Lake Store twee modi voor het beheren van uw master-encryptiesleutels (MEK's), die nodig zijn voor het ontsleutelen van gegevens die zijn opgeslagen in de Data Lake Store. U Data Lake Store de MEK's voor u laten beheren of ervoor kiezen om eigenaar te blijven van de MEK's met uw Azure Key Vault-account. U geeft de wijze van sleutelbeheer op tijdens het maken van een Data Lake Store-account.
- [Met Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) u uw eigen tenantsleutel beheren. In plaats van dat Microsoft bijvoorbeeld uw tenantsleutel beheert (de standaardinstelling), u uw eigen tenantsleutel beheren om te voldoen aan specifieke regels die van toepassing zijn op uw organisatie. Uw eigen tenantsleutel beheren wordt ook wel aangeduid als BYOK (Bring Your Own Key).

## <a name="key-vault-overviews-and-concepts"></a>Key Vault overzichten en concepten

- [Gedrag voor softdelete van Key Vault](overview-soft-delete.md)) beschrijft een functie die het herstel van verwijderde objecten mogelijk maakt, ongeacht of de verwijdering per ongeluk of opzettelijk was.
- [Key Vault client throttling](overview-throttling.md) oriënteert u op de basisconcepten van throttling en biedt een aanpak voor uw app.
- [Overzicht van Key Vault-opslagaccountsleutels](../secrets/overview-storage-keys.md)) beschrijft de sleutels van Azure Storage Accounts voor Key Vault-integratie.
- [Key Vault-beveiligingswerelden](overview-security-worlds.md) beschrijven de relaties tussen regio's en beveiligingsgebieden.

## <a name="social"></a>Sociaal

- [Key Vault-blog](https://aka.ms/kvblog)
- [Key Vault-forum](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Ondersteunende bibliotheken

- [Microsoft Azure Key Vault Core Library](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) biedt **IKey-** en **IKeyResolver-interfaces** voor het lokaliseren van sleutels van id's en het uitvoeren van bewerkingen met sleutels.
- [Microsoft Azure Key Vault-extensies](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) bieden uitgebreide mogelijkheden voor Azure Key Vault.
