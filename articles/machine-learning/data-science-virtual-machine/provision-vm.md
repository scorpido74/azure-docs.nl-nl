---
title: 'Quickstart: Een Windows-DSVM maken'
description: Configureren en een Data Science Virtual Machine in Azure maken voor analyse en machine learning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/10/2019
ms.openlocfilehash: e08287fec79ec634d6e2353328854e19565533ca
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204071"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Quickstart: De Data Science Virtual Machine instellen voor Windows

Ga aan de slag met een Windows-Data Science Virtual Machine.

## <a name="prerequisite"></a>Vereiste

Als u een Windows-Data Science Virtual Machine wilt maken, moet u een Azure-abonnement hebben. [Probeer Azure gratis](https://azure.com/free)uit.

## <a name="create-your-dsvm"></a>Uw DSVM maken

Een DSVM-exemplaar maken:

1. Ga naar de [Azure Portal](https://portal.azure.com) u wordt mogelijk gevraagd om u aan te melden bij uw Azure-account als u nog niet bent aangemeld.
1. Zoek de vermelding van de virtuele machine door in ' data Science virtual machine ' te typen en Data Science Virtual Machine-Windows 2016 te selecteren.

    ![Windows VM-vermelding](./media/provision-vm/search-windows.png)

1. Selecteer de knop **maken** onderaan.

    [![](media/provision-vm/create-windows.png "Knop voor het maken van een Windows-computer")](media/provision-vm/create-windows-expanded.png#lightbox)

1. U moet worden omgeleid naar de Blade een virtuele machine maken.
   ![Tabblad basis informatie dat overeenkomt met Windows virtual machine](./media/provision-vm/review-create-windows.png)

1. Vul het tabblad **basis principes** in:
      * **Abonnement**: Als u meer dan één abonnement hebt, selecteert u de computer waarop de machine wordt gemaakt en gefactureerd. U hebt privileges voor het maken van resources nodig voor dit abonnement.
      * **Resourcegroep**: Een nieuwe groep maken of een bestaand item gebruiken.
      * **Naam van virtuele machine**: Voer de naam van de virtuele machine in. Dit wordt weer gegeven in uw Azure Portal.
      * **Locatie**: Selecteer het Data Center dat het meest geschikt is. Voor de snelste netwerk toegang is het het Data Center dat het meren deel van uw gegevens heeft of zich het dichtst bij uw fysieke locatie bevindt. Meer informatie over [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Installatie kopie**: Laat de standaardwaarde staan.
      * **Grootte**: Dit moet automatisch worden ingevuld met een grootte die geschikt is voor algemene werk belastingen. Lees meer over [Windows VM-grootten in azure](../../virtual-machines/windows/sizes.md).
      * **Gebruikersnaam**: Voer de gebruikers naam van de beheerder in. Dit is de gebruikers naam die u gaat gebruiken om u aan te melden bij uw virtuele machine en hoeft niet hetzelfde te zijn als uw Azure-gebruikers naam.
      * **Wachtwoord**: Voer het wacht woord in dat u wilt gebruiken om u aan te melden bij de virtuele machine.    
1. Selecteer **Controleren + maken**.
1. **Controleren en maken**
   * Controleer of alle informatie die u hebt ingevoerd juist is. 
   * Selecteer **Maken**.


> [!NOTE]
> * U betaalt geen licentie kosten voor de software die vooraf is geladen op de virtuele machine. U betaalt de reken kosten voor de server grootte die u in de stap **grootte** hebt gekozen.
> * Het inrichten duurt 10 tot 20 minuten. U kunt de status van uw virtuele machine weer geven op de Azure Portal.

## <a name="access-the-dsvm"></a>Toegang tot de DSVM

Nadat de VM is gemaakt en ingericht, volgt u de stappen die worden weer gegeven om [verbinding te maken met uw virtuele machine op basis van Azure](../../marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm.md). Gebruik de referenties van het beheerders account die u hebt geconfigureerd in de **basis** stappen voor het maken van een virtuele machine. 

U bent klaar om te beginnen met de hulpprogramma's die zijn geïnstalleerd en geconfigureerd op de virtuele machine. Veel van de hulpprogram ma's zijn toegankelijk via tegels en bureaublad pictogrammen in het **Start** menu.

U kunt ook een DSVM aan Azure Notebooks koppelen om Jupyter-notebooks op de virtuele machine uit te voeren en de beperkingen van de gratis servicelaag over te slaan. Zie [notebook-projecten beheren en configureren](../../notebooks/configure-manage-azure-notebooks-projects.md#manage-and-configure-projects)voor meer informatie.

<a name="tools"></a>


## <a name="next-steps"></a>Volgende stappen

* Bekijk de hulpprogram ma's op de DSVM door het menu **Start** te openen.
* Lees meer over de Azure Machine Learning-service door te lezen [Wat is Azure machine learning service?](../service/overview-what-is-azure-ml.md) en [zelf studies](../index.yml)te proberen.
* Ga in Verkenner naar C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts voor voor beelden die gebruikmaken van de RevoScaleR-bibliotheek in R die ondersteuning biedt voor gegevens analyse op ondernemings schaal. 
* Lees het artikel [tien dingen die u op het data Science virtual machine kunt doen](https://aka.ms/dsvmtenthings).
* Informatie over het bouwen van analytische oplossingen voor end-to-end systematisch met behulp van de [Team Data Science Process](../team-data-science-process/index.yml).
* Ga naar de [Azure AI Gallery](https://gallery.cortanaintelligence.com) services voor machine learning en data analytics-voorbeelden die gebruikmaken van Azure Machine Learning en gerelateerde gegevens in Azure. Daarnaast hebt u in het menu **Start** en op het bureau blad van de virtuele machine een pictogram voor deze galerie gegeven.
* Raadpleeg de juiste [referentie documentatie](./reference-windows-vm.md) voor deze virtuele machine.

