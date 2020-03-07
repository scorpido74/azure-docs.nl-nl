---
title: 'Snelstartgids: een Windows-Data Science Virtual Machine maken'
titleSuffix: Azure Data Science Virtual Machine
description: Configureren en een Data Science Virtual Machine in Azure maken voor analyse en machine learning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: 1fdf8eae5d19a2d43499d1984f4dea834d8a61d8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78397294"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Snelstartgids: de Data Science Virtual Machine instellen voor Windows

Ga aan de slag met een Windows Server 2019-Data Science Virtual Machine.

## <a name="prerequisite"></a>Vereiste

Als u een Windows-Data Science Virtual Machine wilt maken, moet u een Azure-abonnement hebben. [Probeer Azure gratis](https://azure.com/free)uit.
Houd er rekening mee dat gratis Azure-accounts voor GPU ingeschakelde Sku's voor virtuele machines niet ondersteunen.

## <a name="create-your-dsvm"></a>Uw DSVM maken

Een DSVM-exemplaar maken:

1. Ga naar de [Azure Portal](https://portal.azure.com) u wordt mogelijk gevraagd om u aan te melden bij uw Azure-account als u nog niet bent aangemeld.
1. Zoek de vermelding van de virtuele machine door in ' data Science virtual machine ' te typen en Data Science Virtual Machine-Win 2019 (preview) te selecteren.

1. Selecteer de knop **maken** onderaan.

1. U moet worden omgeleid naar de Blade een virtuele machine maken.

1. Vul het tabblad **basis principes** in:
      * **Abonnement**: als u meer dan één abonnement hebt, selecteert u de computer waarop de machine wordt gemaakt en gefactureerd. U hebt privileges voor het maken van resources nodig voor dit abonnement.
      * **Resource groep**: Maak een nieuwe groep of gebruik een bestaande.
      * **Naam van de virtuele machine**: Voer de naam van de virtuele machine in. Dit wordt weer gegeven in uw Azure Portal.
      * **Locatie**: Selecteer het Data Center dat het meest geschikt is. Voor de snelste netwerk toegang is het het Data Center dat het meren deel van uw gegevens heeft of zich het dichtst bij uw fysieke locatie bevindt. Meer informatie over [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Afbeelding**: behoud de standaard waarde.
      * **Grootte**: dit moet automatisch worden ingevuld met een grootte die geschikt is voor algemene werk belastingen. Lees meer over [Windows VM-grootten in azure](../../virtual-machines/windows/sizes.md).
      * **Gebruikers naam**: Voer de gebruikers naam van de beheerder in. Dit is de gebruikers naam die u gaat gebruiken om u aan te melden bij uw virtuele machine en hoeft niet hetzelfde te zijn als uw Azure-gebruikers naam.
      * **Wacht woord**: Voer het wacht woord in dat u wilt gebruiken om u aan te melden bij de virtuele machine.    
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
* Meer informatie over de Azure Machine Learning door te lezen [Wat is Azure machine learning?](../overview-what-is-azure-ml.md) en [zelf studies](../index.yml)te proberen.
* Lees het artikel [tien dingen die u op het data Science virtual machine kunt doen](https://aka.ms/dsvmtenthings).
* Ga naar de [Azure AI Gallery](https://gallery.cortanaintelligence.com) voor voor beelden van machine learning en gegevens analyse die gebruikmaken van Azure machine learning en gerelateerde gegevens Services in Azure. Daarnaast hebt u in het menu **Start** en op het bureau blad van de virtuele machine een pictogram voor deze galerie gegeven.

