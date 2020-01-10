---
title: Azure Data Explorer-clusters beveiligen in azure
description: Meer informatie over het beveiligen van clusters in azure Data Explorer.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: cc16a40b0ea53d433a5a6c592f3b9ea364ef9089
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75725830"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>Azure Data Explorer-clusters beveiligen in azure

Dit artikel bevat een inleiding tot beveiliging in azure Data Explorer om u te helpen uw gegevens en resources in de cloud te beschermen en te voldoen aan de beveiligings behoeften van uw bedrijf. Het is belang rijk om uw clusters beveiligd te laten zijn. Het beveiligen van uw clusters omvat een of meer Azure-functies die beveiligde toegang en opslag omvatten. Dit artikel bevat informatie waarmee u uw cluster veilig kunt houden.

## <a name="managed-identities-for-azure-resources"></a>Beheerde identiteiten voor Azure-resources

Een veelvoorkomende uitdaging bij het bouwen van Cloud toepassingen is referentie beheer in uw code voor verificatie bij Cloud Services. Het is belangrijk dat de referenties veilig worden bewaard. De referenties mogen niet worden opgeslagen in ontwikkel werkstations of worden ingecheckt bij broncode beheer. Azure Key Vault biedt een manier voor het veilig opslaan van referenties, geheimen en andere sleutels, maar uw code moet worden geverifieerd bij Key Vault om ze op te halen.

Met de functie beheerde identiteiten voor Azure-resources voor Azure Active Directory (Azure AD) kunt u dit probleem oplossen. De functie biedt Azure-services met een automatisch beheerde identiteit in Azure AD. U kunt de identiteit gebruiken voor verificatie bij alle services die ondersteuning bieden voor Azure AD-verificatie, inclusief Key Vault, zonder referenties in de code. Zie [Managed Identities voor Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) Overview (pagina) voor meer informatie over deze service.

## <a name="data-encryption"></a>Gegevensversleuteling

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) helpt uw gegevens te beschermen en beschermen om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. Het biedt volume versleuteling voor het besturings systeem en de gegevens schijven van de virtuele machines van uw cluster. Azure Disk Encryption integreert ook met [Azure Key Vault](/azure/key-vault/), waarmee we de versleutelings sleutels en geheimen van de schijf kunnen controleren en beheren en ervoor zorgen dat alle gegevens op de VM-schijven worden versleuteld. 

### <a name="customer-managed-keys-with-azure-key-vault"></a>Door de klant beheerde sleutels met Azure Key Vault

Standaard worden gegevens versleuteld met door micro soft beheerde sleutels. Voor extra controle over versleutelings sleutels kunt u door de klant beheerde sleutels leveren voor het gebruik van gegevens versleuteling. U kunt versleuteling van uw gegevens op het opslag niveau beheren met uw eigen sleutels. Een door de klant beheerde sleutel wordt gebruikt voor het beveiligen en beheren van de toegang tot de hoofd versleutelings sleutel, die wordt gebruikt voor het versleutelen en ontsleutelen van alle gegevens. Door de klant beheerde sleutels bieden meer flexibiliteit voor het maken, draaien, uitschakelen en intrekken van toegangs beheer. U kunt ook de versleutelings sleutels controleren die worden gebruikt voor het beveiligen van uw gegevens.

Gebruik Azure Key Vault om uw door de klant beheerde sleutels op te slaan. U kunt uw eigen sleutels maken en deze opslaan in een sleutel kluis, maar u kunt ook een Azure Key Vault-API gebruiken om sleutels te genereren. Het Azure Data Explorer-cluster en de Azure Key Vault moeten zich in dezelfde regio bevinden, maar ze kunnen zich in verschillende abonnementen bevinden. Zie [Wat is Azure Key Vault?](/azure/key-vault/key-vault-overview)voor meer informatie over Azure Key Vault. Voor een gedetailleerde uitleg over door de klant beheerde sleutels, Zie [door de klant beheerde sleutels met Azure Key Vault](/azure/storage/common/storage-service-encryption)

> [!Note]
> Door de klant beheerde sleutels zijn gebaseerd op beheerde identiteiten voor Azure-resources, een functie van Azure Active Directory (Azure AD). Als u door de klant beheerde sleutels wilt configureren in de Azure Portal, moet u een door **SystemAssigned** beheerde identiteit configureren voor uw cluster.

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Door de klant beheerde sleutels opslaan in Azure Key Vault

Als u door de klant beheerde sleutels wilt inschakelen op een cluster, gebruikt u een Azure Key Vault om uw sleutels op te slaan. U moet de instellingen **voorlopig verwijderen** en **niet wissen** in de sleutel kluis inschakelen. De sleutel kluis moet zich in hetzelfde abonnement bevinden als het cluster. Azure Data Explorer maakt gebruik van beheerde identiteiten voor Azure-resources om te verifiëren bij de sleutel kluis voor het versleutelen en ontsleutelen van bewerkingen. Beheerde identiteiten bieden geen ondersteuning voor scenario's met meerdere mappen.

#### <a name="rotate-customer-managed-keys"></a>Door de klant beheerde sleutels draaien

U kunt een door de klant beheerde sleutel in Azure Key Vault draaien volgens uw nalevings beleid. Wanneer de sleutel wordt geroteerd, moet u het cluster bijwerken om de nieuwe sleutel-URI te gebruiken. Als u de sleutel draait, wordt het opnieuw versleutelen van gegevens in het cluster niet geactiveerd. 

#### <a name="revoke-access-to-customer-managed-keys"></a>Toegang tot door de klant beheerde sleutels intrekken

Als u de toegang tot door de klant beheerde sleutels wilt intrekken, gebruikt u Power shell of Azure CLI. Zie [Azure Key Vault Power shell](/powershell/module/az.keyvault/) of [Azure Key Vault cli](/cli/azure/keyvault)(Engelstalig) voor meer informatie. Als u toegang intrekt, wordt de toegang tot alle gegevens in het opslag niveau van het cluster geblokkeerd omdat de versleutelings sleutel daarom niet toegankelijk is voor Azure Data Explorer.

> [!Note]
> Wanneer Azure Data Explorer identificeert dat toegang tot een door de klant beheerde sleutel wordt ingetrokken, wordt het cluster automatisch opgeschort om alle gegevens in de cache te verwijderen. Wanneer de toegang tot de sleutel wordt geretourneerd, moet het cluster hand matig worden hervat.

## <a name="role-based-access-control"></a>Toegangsbeheer op basis van rollen

Met [op rollen gebaseerd toegangs beheer (RBAC)](/azure/role-based-access-control/overview)kunt u taken in uw team scheiden en alleen de vereiste toegang verlenen aan cluster gebruikers. In plaats van iedereen onbeperkte machtigingen op het cluster te geven, kunt u alleen bepaalde acties toestaan. U kunt [toegangs beheer configureren voor de data bases](/azure/data-explorer/manage-database-permissions) in de [Azure Portal](/azure/role-based-access-control/role-assignments-portal), met behulp van de [Azure cli](/azure/role-based-access-control/role-assignments-cli)of [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell).

## <a name="next-steps"></a>Volgende stappen

* [Beheerde identiteiten voor uw Azure Data Explorer-cluster configureren](managed-identities.md)
* [Beveilig uw cluster in Azure Data Explorer-portal](manage-cluster-security.md) door versleuteling op rest in te scha kelen.
* [Door de klant beheerde sleutels configureren met behulp van de Azure Resource Manager sjabloon](customer-managed-keys-resource-manager.md)
* [Door de klant beheerde sleutels configureren metC#](customer-managed-keys-csharp.md)

