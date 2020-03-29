---
title: VM's migreren naar ResourceBeheer met Azure CLI
description: In dit artikel wordt de door het platform ondersteunde migratie van resources van klassiek naar Azure Resource Manager doorlopen met Azure CLI
author: tanmaygore
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: c41292a05e5c857cd0b1c120784a400f2f5410ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945360"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>IaaS-resources migreren van klassiek naar Azure Resource Manager met behulp van Azure CLI

> [!IMPORTANT]
> Vandaag de dag gebruikt ongeveer 90% van de IaaS VM's [Azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) Vanaf 28 februari 2020 zijn klassieke VM's afgeschaft en op 1 maart 2023 volledig met pensioen gegaan. [Meer informatie]( https://aka.ms/classicvmretirement) over deze afschaffing en [hoe het u beïnvloedt.](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)

In deze stappen ziet u hoe u CLI-opdrachten (Command Line Interface) gebruiken om infrastructuur als serviceresources (IaaS) te migreren van het klassieke implementatiemodel naar het implementatiemodel azure resource manager. Het artikel vereist de [Azure-klassieke CLI](../../cli-install-nodejs.md). Aangezien Azure CLI alleen van toepassing is op Azure Resource Manager-resources, kan het niet worden gebruikt voor deze migratie.

> [!NOTE]
> Alle hier beschreven operaties zijn idempotent. Als u een ander probleem hebt dan een niet-ondersteunde functie of een configuratiefout, raden we u aan de bewerking voor te bereiden, af te breken of te plegen opnieuw te proberen. Het platform zal de actie dan opnieuw proberen.
> 
> 

<br>
Hier volgt een stroomdiagram om de volgorde te bepalen waarin stappen moeten worden uitgevoerd tijdens een migratieproces

![Schermafbeelding van de migratiestappen](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Stap 1: Voorbereiden op migratie
Hier volgen een paar aanbevolen procedures die u aanbeveelt bij het evalueren van het migreren van IaaS-bronnen van klassiek naar Resource Manager:

* Lees de [lijst met niet-ondersteunde configuraties of functies](../windows/migration-classic-resource-manager-overview.md)door. Als u virtuele machines hebt die niet-ondersteunde configuraties of functies gebruiken, raden we u aan te wachten tot de functie-/configuratieondersteuning wordt aangekondigd. U deze functie ook verwijderen of uit die configuratie stappen om migratie in te schakelen als deze aan uw behoeften voldoet.
* Als u geautomatiseerde scripts hebt die uw infrastructuur en toepassingen vandaag implementeren, probeert u een vergelijkbare testinstelling te maken met behulp van deze scripts voor migratie. U ook voorbeeldomgevingen instellen met behulp van de Azure-portal.

> [!IMPORTANT]
> Toepassingsgateways worden momenteel niet ondersteund voor migratie van klassiek naar Resourcebeheer. Als u een klassiek virtueel netwerk wilt migreren met een toepassingsgateway, verwijdert u de gateway voordat u een bewerking Voorbereiden uitvoert om het netwerk te verplaatsen. Nadat u de migratie hebt voltooid, sluit u de gateway opnieuw aan in Azure Resource Manager. 
>
>ExpressRoute-gateways die verbinding maken met ExpressRoute-circuits in een ander abonnement, kunnen niet automatisch worden gemigreerd. Verwijder in dergelijke gevallen de ExpressRoute-gateway, migreerhet virtuele netwerk en maak de gateway opnieuw. Zie [ExpressRoute-circuits migreren en bijbehorende virtuele netwerken van de klassieker naar het implementatiemodel voor Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md) voor meer informatie.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Stap 2: Stel uw abonnement in en registreer de provider
Voor migratiescenario's moet u uw omgeving instellen voor zowel klassiek als Resource Manager. [Installeer Azure CLI](../../cli-install-nodejs.md) en [selecteer uw abonnement](/cli/azure/authenticate-azure-cli).

Log in of uit om te reageren op uw account.

    azure login

Selecteer het Azure-abonnement met de volgende opdracht.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> Registratie is een eenmalige stap, maar het moet eenmaal worden gedaan voordat u probeert migratie. Zonder registratie ziet u het volgende foutbericht 
> 
> *BadRequest : Abonnement is niet geregistreerd voor migratie.* 
> 
> 

Registreer u bij de migratieresourceprovider met behulp van de volgende opdracht. Houd er rekening mee dat in sommige gevallen deze opdracht een uit-een-op-leuks uitvalt. De registratie zal echter succesvol zijn.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Wacht vijf minuten tot de inschrijving is voltooid. U de status van de goedkeuring controleren met behulp van de volgende opdracht. Zorg ervoor dat `Registered` RegistrationState is voordat u verder gaat.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Schakel CLI nu `asm` over naar de modus.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Stap 3: Zorg ervoor dat u voldoende Azure Resource Manager Virtual Machine vCPU's hebt in het Azure-gebied van uw huidige implementatie of VNET
Voor deze stap moet je `arm` overschakelen naar de modus. Doe dit met de volgende opdracht.

```
azure config mode arm
```

U de volgende opdracht CLI gebruiken om het huidige aantal vCPU's in Azure Resource Manager te controleren. Zie [Limieten en Azure Resource Manager](../../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits)voor meer informatie over vCPU-quota.

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Zodra u klaar bent met het verifiëren van `asm` deze stap, u terug schakelen naar de modus.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Stap 4: Optie 1 - Virtuele machines migreren in een cloudservice
Download de lijst met cloudservices met behulp van de volgende opdracht en kies vervolgens de cloudservice die u wilt migreren. Houd er rekening mee dat als de VM's in de cloudservice zich in een virtueel netwerk bevinden of als ze web-/werknemersrollen hebben, u een foutmelding krijgt.

    azure service list

Voer de volgende opdracht uit om de implementatienaam voor de cloudservice uit de verbose-uitvoer te halen. In de meeste gevallen is de naam van de implementatie hetzelfde als de naam van de cloudservice.

    azure service show <serviceName> -vv

Controleer eerst of u de cloudservice migreren met de volgende opdrachten:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Bereid de virtuele machines in de cloudservice voor op migratie. Je hebt twee opties om uit te kiezen.

Als u de VM's wilt migreren naar een virtueel netwerk dat door een platform is gemaakt, gebruikt u de volgende opdracht.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Als u wilt migreren naar een bestaand virtueel netwerk in het implementatiemodel resourcebeheer, gebruikt u de volgende opdracht.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

Nadat de voorbereidingsbewerking is geslaagd, u de uitgebreide uitvoer bekijken om de migratiestatus `Prepared` van de VM's te krijgen en ervoor te zorgen dat ze zich in de status bevinden.

    azure vm show <vmName> -vv

Controleer de configuratie voor de voorbereide resources met CLI of de Azure-portal. Als u niet klaar bent voor migratie en u terug wilt naar de oude status, gebruikt u de volgende opdracht.

    azure service deployment abort-migration <serviceName> <deploymentName>

Als de voorbereide configuratie er goed uitziet, u verder gaan en de resources vastleggen met behulp van de volgende opdracht.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Stap 4: Optie 2 - Virtuele machines migreren in een virtueel netwerk
Kies het virtuele netwerk dat u wilt migreren. Houd er rekening mee dat als het virtuele netwerk web-/werknemersrollen of VM's met niet-ondersteunde configuraties bevat, u een validatiefoutbericht ontvangt.

Download alle virtuele netwerken in het abonnement met behulp van de volgende opdracht.

    azure network vnet list

De output ziet er ongeveer als volgt uit:

![Schermafbeelding van de opdrachtregel met de volledige virtuele netwerknaam gemarkeerd.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

In het bovenstaande voorbeeld is de **virtualNetworkName** de volledige naam **"Group classicubuntu16 classicubuntu16".**

Controleer eerst of u het virtuele netwerk migreren met de volgende opdracht:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Bereid het virtuele netwerk van uw keuze voor op migratie met behulp van de volgende opdracht.

    azure network vnet prepare-migration <virtualNetworkName>

Controleer de configuratie voor de voorbereide virtuele machines met behulp van CLI of de Azure-portal. Als u niet klaar bent voor migratie en u terug wilt naar de oude status, gebruikt u de volgende opdracht.

    azure network vnet abort-migration <virtualNetworkName>

Als de voorbereide configuratie er goed uitziet, u verder gaan en de resources vastleggen met behulp van de volgende opdracht.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Stap 5: Een opslagaccount migreren
Zodra u klaar bent met het migreren van de virtuele machines, raden we u aan het opslagaccount te migreren.

Het opslagaccount voorbereiden op migratie met de volgende opdracht

    azure storage account prepare-migration <storageAccountName>

Controleer de configuratie voor het voorbereide opslagaccount met CLI of de Azure-portal. Als u niet klaar bent voor migratie en u terug wilt naar de oude status, gebruikt u de volgende opdracht.

    azure storage account abort-migration <storageAccountName>

Als de voorbereide configuratie er goed uitziet, u verder gaan en de resources vastleggen met behulp van de volgende opdracht.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van platformondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planning voor de migratie van IaaS-resources van het klassieke implementatiemodel naar Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [PowerShell gebruiken om IaaS-resources te migreren van klassiek naar Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Communitytools voor het helpen bij de migratie van IaaS-resources van klassiek naar Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Bekijk de meest voorkomende migratiefouten](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Bekijk de meest gestelde vragen over het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
