---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 7f5c02c6c009e8916ed063454e0ae6049892e95c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297900"
---
Azure Data Explorer versleutelt alle gegevens in een opslagaccount in rust. Standaard worden gegevens versleuteld met door Microsoft beheerde sleutels. Voor extra controle over versleutelingssleutels u door de klant beheerde sleutels leveren die u gebruiken voor gegevensversleuteling. 

Door de klant beheerde sleutels moeten worden opgeslagen in een [Azure Key Vault.](/azure/key-vault/key-vault-overview) U uw eigen sleutels maken en opslaan in een sleutelkluis, of u een Azure Key Vault API gebruiken om sleutels te genereren. Het Azure Data Explorer-cluster en de sleutelkluis moeten zich in dezelfde regio bevinden, maar ze kunnen zich in verschillende abonnementen bevinden. Zie door de [klant beheerde sleutels met Azure Key Vault](/azure/storage/common/storage-service-encryption)voor een gedetailleerde uitleg over door de klant beheerde sleutels. 

In dit artikel ziet u hoe u door de klant beheerde sleutels configureert.

## <a name="configure-azure-key-vault"></a>Azure Key Vault configureren

Als u door de klant beheerde sleutels wilt configureren met Azure Data Explorer, moet u [twee eigenschappen instellen op de sleutelkluis:](/azure/key-vault/key-vault-ovw-soft-delete)Soft **Delete** en Do **Not Purge**. Deze eigenschappen zijn standaard niet ingeschakeld. Als u deze eigenschappen wilt inschakelen, voert **u Beveiliging voor het verwijderen van soft delete** en **Zuiveringsbeveiliging** inschakelen uit in [PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) of [Azure CLI](/azure/key-vault/key-vault-soft-delete-cli) op een nieuwe of bestaande sleutelkluis. Alleen RSA-toetsen van formaat 2048 worden ondersteund. Zie [Key Vault-toetsen voor](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)meer informatie over sleutels.

> [!NOTE]
> Gegevensversleuteling met behulp van door de klant beheerde sleutels wordt niet ondersteund op [leader- en volgerclusters.](/azure/data-explorer/follower) 

## <a name="assign-an-identity-to-the-cluster"></a>Een identiteit toewijzen aan het cluster

Als u door de klant beheerde sleutels voor uw cluster wilt inschakelen, wijst u eerst een door het systeem toegewezen beheerde identiteit toe aan het cluster. U gebruikt deze beheerde identiteit om de clustermachtigingen te verlenen voor toegang tot de sleutelkluis. Zie Beheerde identiteiten als u beheerde identiteiten met systeemtoegewezen [identiteiten wilt configureren.](/azure/data-explorer/managed-identities)