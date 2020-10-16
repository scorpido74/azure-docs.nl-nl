---
title: Automatische registratie bij de resource provider van de SQL-VM
description: Meer informatie over het inschakelen van de functie Automatische registratie voor het automatisch registreren van alle oude en toekomstige SQL Server Vm's met de resource provider van de SQL-VM met behulp van de Azure Portal.
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.openlocfilehash: b986832e5febbb2a0f88b65213f9acf0dd4c5ab5
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996885"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>Automatische registratie bij de resource provider van de SQL-VM
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

De functie voor automatische registratie inschakelen in de Azure Portal om alle huidige en toekomstige SQL Server op virtuele Azure-machines automatisch te registreren bij de resource provider van de SQL-VM in de Lightweight-modus.

In dit artikel leert u de functie Automatische registratie in te scha kelen. U kunt ook [één virtuele machine registreren](sql-vm-resource-provider-register.md)of [uw vm's in bulk registreren](sql-vm-resource-provider-bulk-register.md) met de resource provider van de SQL-VM. 

## <a name="overview"></a>Overzicht

Met de [resource provider](sql-vm-resource-provider-register.md#overview) van de SQL-VM kunt u uw SQL Server-VM beheren vanuit de Azure Portal. Daarnaast kunt u met de resource provider een robuuste functieset maken, waaronder [geautomatiseerde patching](automated-patching.md), [automatische back-ups](automated-backup.md), en mogelijkheden voor bewaking en beheer baarheid. Daarnaast worden de [licenties](licensing-model-azure-hybrid-benefit-ahb-change.md) en de flexibiliteit van de [editie](change-sql-server-edition.md) ontgrendeld. Voorheen waren deze functies alleen beschikbaar voor het SQL Server van VM-installatie kopieën die zijn geïmplementeerd vanuit Azure Marketplace. 

Met de functie voor automatische registratie kunnen klanten automatisch alle huidige en toekomstige SQL Server Vm's registreren in hun Azure-abonnement met de resource provider van de SQL-VM. Dit wijkt af van de hand matige registratie, die alleen gericht is op huidige SQL Server Vm's. 

Met automatische registratie worden uw SQL Server Vm's geregistreerd in de Lightweight-modus. U moet nog steeds [hand matig bijwerken naar de volledige beheer baarheids modus](sql-vm-resource-provider-register.md#upgrade-to-full) om te kunnen profiteren van de volledige functieset. 

## <a name="prerequisites"></a>Vereisten

Als u uw SQL Server-VM wilt registreren bij de resource provider, hebt u het volgende nodig: 

- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Een Azure-resource model [virtuele machine](../../../virtual-machines/windows/quick-create-portal.md) met [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) geïmplementeerd in de open bare of Azure Government Cloud. 


## <a name="enable"></a>Inschakelen

Volg de stappen om automatische registratie van uw SQL Server Vm's in het Azure Portal in te scha kelen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Navigeer naar de resource pagina [**virtuele SQL-machines**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Selecteer **automatische SQL Server VM-registratie** om de pagina **automatische registratie** te openen. 

   :::image type="content" source="media/sql-vm-resource-provider-automatic-registration/automatic-registration.png" alt-text="Selecteer Automatische SQL Server VM-registratie om de pagina automatische registratie te openen":::

1. Kies uw abonnement in de vervolg keuzelijst. 
1. Lees de voor waarden en klik op **Ik ga**akkoord als u akkoord gaat. 
1. Selecteer **registreren** om de functie in te scha kelen en alle huidige en toekomstige SQL Server vm's automatisch te registreren bij de resource provider van de SQL-VM. Hiermee wordt de SQL Server-service op geen van de virtuele machines opnieuw opgestart. 

## <a name="disable"></a>Uitschakelen

Gebruik de [Azure cli](/cli/azure/install-azure-cli) of [Azure PowerShell](/powershell/azure/install-az-ps) om de functie Automatische registratie uit te scha kelen. Wanneer de functie Automatische registratie is uitgeschakeld, moeten SQL Server Vm's die zijn toegevoegd aan het abonnement, hand matig worden geregistreerd bij de resource provider van de SQL-VM. Hiermee wordt de registratie van bestaande SQL Server Vm's die al zijn geregistreerd, niet ongedaan gemaakt.



# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u automatische registratie met Azure CLI wilt uitschakelen, voert u de volgende opdracht uit: 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u automatische registratie wilt uitschakelen met Azure PowerShell, voert u de volgende opdracht uit: 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>Inschakelen voor meerdere abonnementen

U kunt de functie voor automatische registratie voor meerdere Azure-abonnementen inschakelen met behulp van Power shell. 

Volg hiervoor de onderstaande stappen:

1. Sla [Dit script](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/RegisterSubscriptionsToSqlVmAutomaticRegistration.ps1) op in een `.ps1` bestand, zoals `EnableBySubscription.ps1` . 
1. Ga naar de locatie waar u het script hebt opgeslagen met behulp van een opdracht prompt of Power shell-venster. 
1. Verbinding maken met Azure ( `az login` ).
1. Voer het script uit, door gegeven in SubscriptionIds als para meters zoals   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   Bijvoorbeeld: 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

Mislukte registratie fouten worden opgeslagen in `RegistrationErrors.csv` dezelfde map als waar u het script hebt opgeslagen en uitgevoerd `.ps1` . 

## <a name="next-steps"></a>Volgende stappen

Voer een upgrade uit van uw beheer baarheids modus naar [volledig](sql-vm-resource-provider-register.md#upgrade-to-full) om te profiteren van de volledige functieset die u hebt gekregen van de resource provider van de SQL-VM. 
