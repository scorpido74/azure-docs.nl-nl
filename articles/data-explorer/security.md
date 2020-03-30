---
title: Azure Data Explorer-clusters beveiligen in Azure
description: Meer informatie over het beveiligen van clusters in Azure Data Explorer.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 786950011f10e25d6bcb72061212c1878e79d45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77373353"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>Azure Data Explorer-clusters beveiligen in Azure

In dit artikel vindt u een inleiding tot beveiliging in Azure Data Explorer om u te helpen uw gegevens en bronnen in de cloud te beschermen en te voldoen aan de beveiligingsbehoeften van uw bedrijf. Het is belangrijk om uw clusters veilig te houden. Het beveiligen van uw clusters omvat een of meer Azure-functies, waaronder beveiligde toegang en opslag. In dit artikel vindt u informatie waarmee u uw cluster veilig houden.

## <a name="managed-identities-for-azure-resources"></a>Beheerde identiteiten voor Azure-resources

Een veelvoorkomende uitdaging bij het bouwen van cloudtoepassingen is het beheer van referenties in uw code voor het authenticeren naar cloudservices. Het is belangrijk dat de referenties veilig worden bewaard. De referenties mogen niet worden opgeslagen in werkstations van ontwikkelaars of worden ingecheckt in bronbeheer. Azure Key Vault biedt een manier voor het veilig opslaan van referenties, geheimen en andere sleutels, maar uw code moet worden geverifieerd bij Key Vault om ze op te halen.

De azure active directory (Azure AD) beheerde identiteiten voor Azure-resources- functie lost dit probleem op. De functie biedt Azure-services met een automatisch beheerde identiteit in Azure AD. U kunt de identiteit gebruiken voor verificatie bij alle services die ondersteuning bieden voor Azure AD-verificatie, inclusief Key Vault, zonder referenties in de code. Zie [overzichtspagina beheerde identiteiten voor Azure-bronnen voor](/azure/active-directory/managed-identities-azure-resources/overview) meer informatie over deze service.

## <a name="data-encryption"></a>Gegevensversleuteling

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) helpt uw gegevens te beschermen en te beschermen om te voldoen aan uw organisatorische beveiligings- en nalevingsverplichtingen. Het biedt volumeversleuteling voor het besturingssysteem en gegevensschijven van de virtuele machines van uw cluster. Azure Disk Encryption integreert ook met [Azure Key Vault](/azure/key-vault/), waarmee we de schijfversleutelingssleutels en -geheimen kunnen beheren en ervoor kunnen zorgen dat alle gegevens op de VM-schijven worden versleuteld. 

### <a name="customer-managed-keys-with-azure-key-vault"></a>Door de klant beheerde sleutels met Azure Key Vault

Standaard worden gegevens versleuteld met door Microsoft beheerde sleutels. Voor extra controle over versleutelingssleutels u door de klant beheerde sleutels leveren die u gebruiken voor gegevensversleuteling. U versleuteling van uw gegevens beheren op opslagniveau met uw eigen sleutels. Een door de klant beheerde sleutel wordt gebruikt om de toegang tot de hoofdversleutelingssleutel te beschermen en te beheren, die wordt gebruikt om alle gegevens te versleutelen en te decoderen. Door de klant beheerde sleutels bieden meer flexibiliteit om toegangsbesturingselementen te maken, te roteren, uit te schakelen en in te trekken. U ook de versleutelingssleutels controleren die worden gebruikt om uw gegevens te beschermen.

Gebruik Azure Key Vault om uw door de klant beheerde sleutels op te slaan. U uw eigen sleutels maken en opslaan in een sleutelkluis, of u een Azure Key Vault API gebruiken om sleutels te genereren. Het Azure Data Explorer-cluster en de Azure Key Vault moeten zich in dezelfde regio bevinden, maar ze kunnen zich in verschillende abonnementen bevinden. Zie Wat is Azure Key Vault voor meer informatie over Azure Key [Vault?](/azure/key-vault/key-vault-overview) Zie Door de [klant beheerde sleutels met Azure Key Vault](/azure/storage/common/storage-service-encryption)voor een gedetailleerde uitleg over door de klant beheerde sleutels. Door de klant beheerde sleutels configureren in uw Azure Data Explorer-cluster met [C#](/azure/data-explorer/customer-managed-keys-csharp) of de [sjabloon Azure Resource Manager](/azure/data-explorer/customer-managed-keys-resource-manager)

> [!Note]
> Door de klant beheerde sleutels zijn afhankelijk van beheerde identiteiten voor Azure-resources, een functie van Azure Active Directory (Azure AD). Als u door de klant beheerde sleutels in de Azure-portal wilt configureren, moet u een **beheerde identiteit met SystemAssigned** configureren voor uw cluster zoals beschreven in [Beheerde identiteiten configureren voor uw Azure Data Explorer-cluster.](/azure/data-explorer/managed-identities)

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Door de klant beheerde sleutels opslaan in Azure Key Vault

Als u door de klant beheerde sleutels in een cluster wilt inschakelen, gebruikt u een Azure Key Vault om uw sleutels op te slaan. U moet zowel de eigenschappen **Soft Delete** als Do **Not Purge** inschakelen op de sleutelkluis. De sleutelkluis moet zich in hetzelfde abonnement bevinden als het cluster. Azure Data Explorer gebruikt beheerde identiteiten voor Azure-bronnen om te verifiëren naar de sleutelkluis voor versleutelings- en decryptiebewerkingen. Beheerde identiteiten ondersteunen geen cross-directory scenario's.

#### <a name="rotate-customer-managed-keys"></a>Door de klant beheerde sleutels roteren

U een door de klant beheerde sleutel roteren in Azure Key Vault volgens uw nalevingsbeleid. Wanneer de sleutel wordt gedraaid, moet u het cluster bijwerken om de nieuwe sleutel URI te gebruiken. Als u de sleutel draait, wordt de versleuteling van gegevens in het cluster niet opnieuw versleuteld. 

#### <a name="revoke-access-to-customer-managed-keys"></a>Toegang tot door de klant beheerde sleutels intrekken

Als u de toegang tot door de klant beheerde sleutels wilt intrekken, gebruikt u PowerShell of Azure CLI. Zie [Azure Key Vault PowerShell](/powershell/module/az.keyvault/) of Azure Key Vault [CLI](/cli/azure/keyvault)voor meer informatie. Het intrekken van toegangsblokken blokkeert de toegang tot alle gegevens in het opslagniveau van het cluster, omdat de versleutelingssleutel bijgevolg niet toegankelijk is voor Azure Data Explorer.

> [!Note]
> Wanneer Azure Data Explorer vaststelt dat de toegang tot een door de klant beheerde sleutel wordt ingetrokken, wordt het cluster automatisch opgeschort om gegevens in de cache te verwijderen. Zodra de toegang tot de sleutel is geretourneerd, moet het cluster handmatig worden hervat.

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Met behulp van [role-based access control (RBAC)](/azure/role-based-access-control/overview)u taken binnen uw team scheiden en alleen de vereiste toegang verlenen aan clustergebruikers. In plaats van iedereen onbeperkte machtigingen op het cluster te geven, u alleen bepaalde acties toestaan. U [toegangsbeheer configureren voor de databases](/azure/data-explorer/manage-database-permissions) in de [Azure-portal,](/azure/role-based-access-control/role-assignments-portal)met behulp van Azure [CLI](/azure/role-based-access-control/role-assignments-cli)of [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell).

## <a name="next-steps"></a>Volgende stappen

* [Beveilig uw cluster in Azure Data Explorer - Portal](manage-cluster-security.md) door versleuteling in rust in te schakelen.
* [Beheerde identiteiten configureren voor uw Azure Data Explorer-cluster](managed-identities.md)
* [Door de klant beheerde sleutels configureren met de sjabloon Azure Resource Manager](customer-managed-keys-resource-manager.md)
* [Door de klant beheerde sleutels configureren met C #](customer-managed-keys-csharp.md)

