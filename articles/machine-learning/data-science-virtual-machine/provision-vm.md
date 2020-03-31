---
title: 'Snelstart: een Virtuele Windows Data Science-machine maken'
titleSuffix: Azure Data Science Virtual Machine
description: Een Data Science Virtual Machine configureren en maken op Azure voor analyse en machine learning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: afcb676f68e7be9d3ebef11ea2c6876a86bbd062
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281779"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Snelstart: de virtuele machine van Data Science instellen voor Windows

Aan de slag met een Windows Server 2019 Data Science Virtual Machine.

## <a name="prerequisite"></a>Vereiste

Als u een Virtuele Machine van Windows Data Science wilt maken, moet u een Azure-abonnement hebben. [Probeer Azure gratis.](https://azure.com/free)
Houd er rekening mee dat gratis Azure-accounts geen ONDERSTEUNING bieden voor GPU-enabled virtual machine SKU's.

## <a name="create-your-dsvm"></a>Uw DSVM maken

Ga als lid van het nieuwe voorbeeld van DSVM:

1. Ga naar de [Azure-portal](https://portal.azure.com) U wordt mogelijk gevraagd om u aan te melden bij uw Azure-account als u nog niet bent aangemeld.
1. Zoek de lijst met virtuele machines door 'virtuele machine voor data science' in te typen en 'Data Science Virtual Machine - Windows 2019' te selecteren.

1. Selecteer de knop **Maken** onderin.

1. U moet worden doorgestuurd naar het blad 'Maak een virtuele machine'.

1. Vul het tabblad **Basisbeginselen** in:
      * **Abonnement:** Als u meer dan één abonnement hebt, selecteert u het abonnement waarop de machine wordt gemaakt en gefactureerd. U hebt privileges voor het maken van resources nodig voor dit abonnement.
      * **Resourcegroep:** maak een nieuwe groep of gebruik een bestaande groep.
      * **Naam virtuele machine**: Voer de naam van de virtuele machine in. Dit is hoe het wordt weergegeven in uw Azure-portal.
      * **Locatie:** Selecteer het datacenter dat het meest geschikt is. Voor snelste netwerktoegang is dit het datacenter dat de meeste gegevens heeft of het dichtst bij uw fysieke locatie staat. Meer informatie over [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Afbeelding**: Laat de standaardwaarde achter.
      * **Grootte:** dit moet automatisch worden ingevuld met een grootte die geschikt is voor algemene workloads. Lees meer over [Windows VM-formaten in Azure](../../virtual-machines/windows/sizes.md).
      * **Gebruikersnaam**: Voer de gebruikersnaam van de beheerder in. Dit is de gebruikersnaam die u gebruikt om u aan te melden bij uw virtuele machine en hoeft niet dezelfde te zijn als uw Azure-gebruikersnaam.
      * **Wachtwoord:** Voer het wachtwoord in dat u gebruikt om u aan te melden bij uw virtuele machine.    
1. Selecteer **Controleren + maken**.
1. **Controleren+maken**
   * Controleer of alle ingevoerde gegevens juist zijn. 
   * Selecteer **Maken**.


> [!NOTE]
> * U betaalt geen licentiekosten voor de software die vooraf op de virtuele machine wordt geladen. U betaalt wel de rekenkosten voor de servergrootte die u hebt gekozen in de stap **Grootte.**
> * De inrichting duurt 10 tot 20 minuten. U de status van uw VM bekijken op de Azure-portal.

## <a name="access-the-dsvm"></a>Toegang tot de DSVM

Nadat de VM is gemaakt en ingericht, volgt u de stappen die worden aangeboden om [verbinding te maken met uw virtuele machine op Basis van Azure.](../../marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm.md) Gebruik de beheerdersaccountreferenties die u hebt geconfigureerd in **de** basisstap van het maken van een virtuele machine. 

U bent klaar om te beginnen met de hulpprogramma's die op de VM zijn geïnstalleerd en geconfigureerd. Veel van de tools zijn toegankelijk via **menutegels** en bureaubladpictogrammen.

U een DSVM ook aan Azure Notebooks koppelen om Jupyter-notitieblokken op de VM uit te voeren en de beperkingen van de gratis servicelaag te omzeilen. Zie [Projecten voor notitieblokken beheren en configureren voor](../../notebooks/configure-manage-azure-notebooks-projects.md#manage-and-configure-projects)meer informatie .

<a name="tools"></a>


## <a name="next-steps"></a>Volgende stappen

* Ontdek de hulpprogramma's op de DSVM door het **menu Start te** openen.
* Meer informatie over de Azure Machine Learning door te lezen [Wat is Azure Machine Learning?](../overview-what-is-azure-ml.md) en [zelfstudie](../index.yml)uitproberen .
* Lees het artikel [Tien dingen die je doen op de Data Science Virtual Machine](https://aka.ms/dsvmtenthings).
* Ga naar de [Azure AI Gallery](https://gallery.cortanaintelligence.com) voor voorbeelden van machine learning en gegevensanalyse die azure machine learning en gerelateerde gegevensservices op Azure gebruiken. We hebben ook een pictogram voor deze galerij in het **startmenu** en op het bureaublad van de virtuele machine.

