---
title: Een Microsoft Azure Marketplace-installatiekopie gebruiken om een virtuele Terraform Linux-machine te maken met een beheerde identiteit
description: Gebruik de Marketplace-installatiekopie om een virtuele Terraform Linux-machine met een beheerde identiteit en Remote State Management te maken om eenvoudig resources in Azure te implementeren.
services: terraform
ms.service: azure
keywords: terraform, devops, MSI, virtuele machine, remote state, azure
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 835170448db8709812ca62ac67f8659b3b6a2807
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173059"
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-identities-for-azure-resources"></a>Een Microsoft Azure Marketplace-installatiekopie gebruiken om een virtuele Terraform Linux-machine te maken met een beheerde identiteit voor Azure-resources

In dit artikel leest u hoe u een [Terraform Marketplace-installatiekopie](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) gebruikt om een Ubuntu Linux VM (16.04 LTS) te maken met de nieuwste [Terraform](https://www.terraform.io/intro/index.html)-versie geïnstalleerd en geconfigureerd met behulp van [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Met deze installatiekopie wordt tevens een externe back-end geconfigureerd om beheer van de [remote state](https://www.terraform.io/docs/state/remote.html) met behulp van Terraform in te schakelen. 

Met de Terraform Marketplace-installatiekopie kunt u eenvoudig Terraform in Azure gaan gebruiken, zonder Terraform handmatig te hoeven installeren en te configureren. 

Voor deze Terraform VM-installatiekopie worden geen softwarekosten in rekening gebracht. U betaalt uitsluitend de gebruikskosten voor Azure-hardware die worden bepaald op basis van de grootte van de virtuele machine die wordt ingericht. Raadpleeg de [pagina met prijzen voor virtuele Linux-machines](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) voor meer informatie over de computerkosten.

## <a name="prerequisites"></a>Vereisten
Voordat u een virtuele Linux Terraform-machine kunt maken, moet u over een Azure-abonnement beschikken. Raadpleeg [Maak vandaag nog uw gratis Azure-account](https://azure.microsoft.com/free/) als u nog geen abonnement hebt.  

## <a name="create-your-terraform-virtual-machine"></a>Uw virtuele Terraform-machine maken 

Hier ziet u de stappen die u moet uitvoeren om een exemplaar van een virtuele Linux Terraform-machine te maken: 

1. Ga in Azure Portal naar de lijst [Resource maken](https://ms.portal.azure.com/#create/hub).

2. Ga naar de zoekbalk **Zoeken in Marketplace** en zoek **Terraform**. Selecteer de sjabloon **Terraform**. 

3. Ga naar het tabblad met Terraform-details (rechtsonder) en selecteer de knop **Maken**.

    ![Een virtuele Terraform-machine maken](media/terraformmsi.png)

4. De volgende gedeelten geven informatie voor alle stappen in de wizard om de virtuele Terraform Linux-machine te maken. In het volgende gedeelte staat de informatie die u nodig hebt om alle stappen te configureren.

## <a name="details-on-the-create-terraform-tab"></a>Details op het tabblad Terraform maken

Voer de volgende details in op het tabblad **Terraform maken**:

1. **Basisinstellingen**
    
   * **Naam**: de naam van uw virtuele Terraform-machine.
   * **Gebruikersnaam**: de aanmeldings-id van het eerste account.
   * **Wachtwoord**: het wachtwoord van het eerste account. (U kunt een openbare SSH-sleutel gebruiken in plaats van een wachtwoord.)
   * **Abonnement**: het abonnement waarop de machine moet worden gemaakt en gefactureerd. U hebt privileges voor het maken van resources nodig voor dit abonnement.
   * **Resourcegroep**: een nieuwe of bestaande resourcegroep.
   * **Locatie**: het meest geschikte datacenter. Doorgaans is dit het datacenter waarop de meeste van uw gegevens zijn opgeslagen of het datacenter dat fysiek het dichtst bij u in de buurt staat voor de snelste netwerktoegang.

2. **Aanvullende instellingen**

   * **Grootte**: de grootte van de virtuele machine. 
   * **Type VM-schijf**: SSD of HDD.

3. **Samenvatting Terraform**

   * Controleer of alle informatie die u hebt ingevoerd, correct is. 

4. **Kopen**

   * Selecteer **Kopen** om het inrichtingsproces te starten. Er wordt een koppeling gegeven naar de voorwaarden van de transactie. Voor de virtuele machine worden geen andere kosten in rekening gebracht naast de rekenkracht voor de servergrootte die u bij de stap Grootte hebt ingevoerd.

De installatiekopie van de virtuele Terraform-machine voert de volgende stappen uit:

* Maakt een virtuele machine met een door het systeem toegewezen identiteit op basis van de Ubuntu 16.04 LTS-installatiekopie.
* Installeert de MSI-extensie op de virtuele machine zodat OAuth-tokens voor Azure-resources kunnen worden uitgegeven.
* Wijst RBAC-machtigingen toe aan de beheerde identiteit, waardoor de eigenaar rechten krijgt voor de resourcegroep.
* Maakt een map met Terraform-sjablonen (tfTemplate).
* Configureert vooraf een externe status van Terraform met de Azure-back-end.

## <a name="access-and-configure-a-linux-terraform-virtual-machine"></a>Toegang krijgen tot een virtuele Linux Terraform-machine en deze configureren

Nadat u de virtuele machine hebt gemaakt, kunt u zich met behulp van SSH aanmelden bij deze VM. Gebruik de accountreferenties die u in het gedeelte 'Basisbeginselen' van stap 3 voor de tekstshell-interface hebt gemaakt. In Windows kunt u een SSH-clienthulpprogramma zoals [Putty](https://www.putty.org/) downloaden.

Nadat u met SSH verbinding met de virtuele machine hebt gemaakt, moet u beheerde identiteiten voor Azure-resources op de virtuele machine inzendermachtigingen voor het hele abonnement geven. 

Met inzendermachtigingen kan MSI op de virtuele machine Terraform gebruiken om resources buiten de VM-resourcegroep te maken. U kunt deze actie eenvoudig bereiken door eenmalig een script uit te voeren. Gebruik de volgende opdracht:

`. ~/tfEnv.sh`

In het vorige script werd het mechanisme [AZ CLI v 2.0 interactive log-in](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#sign-in-interactively) gebruikt voor verificatie bij Azure en de toewijzing van de inzendermachtiging voor beheerde identiteiten van de virtuele machine op het hele abonnement. 

 De virtuele machine heeft een Terraform-back-end met externe status. U kunt deze in uw Terraform-inrichting inschakelen door het bestand remoteState.tf in de map tfTemplate naar de hoofdmap van de Terraform-scripts te kopiëren.  

 `cp  ~/tfTemplate/remoteState.tf .`

 Voor meer informatie over Remote State Management raadpleegt u [deze pagina over de externe status van Terraform](https://www.terraform.io/docs/state/remote.html). De toegangssleutel voor opslag wordt in dit bestand vermeld en deze moet worden uitgesloten voordat u Terraform-configuratiebestanden doorvoert in het broncodebeheer.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u een virtuele Terraform Linux-machine in Azure instelt. Hier volgen een aantal aanvullende resources voor meer informatie over Terraform in Azure: 

 [Terraform Hub in Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Terraform Azure-documentatie voor providers](https://aka.ms/terraform)  
 [Terraform Azure-gegevensbron voor providers](https://aka.ms/tfgit)  
 [Terraform Azure-modules](https://aka.ms/tfmodules)
 

















