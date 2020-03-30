---
title: Azure DevTest Labs uitbreiden met Azure-functies | Microsoft Documenten
description: Meer informatie over het uitbreiden van Azure DevTest Labs met Azure-functies.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: dd1fc4c1076d89c12b25837db9fa6a0ac3e1f3a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70014358"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Azure Functions gebruiken om DevTest Labs uit te breiden
U Azure-functies gebruiken om extra scenario's te ondersteunen die verder gaan dan de scenario's die al worden ondersteund door DevTest Labs. Azure-functies kunnen worden gebruikt om de ingebouwde functionaliteit van de service uit te breiden om aan uw bedrijfsspecifieke behoeften te voldoen. De volgende lijst bevat enkele van de mogelijke scenario's. In dit artikel ziet u hoe u een van deze voorbeeldscenario's implementeert.

- Een overzicht op het hoogste niveau van virtuele machines (VM's) in het lab
- [Een lab configureren voor gebruik van een Extern bureaublad-gateway](configure-lab-remote-desktop-gateway.md)
- Nalevingsrapportage op de interne ondersteuningspagina
- Gebruikers inschakelen om bewerkingen uit te voeren waarvoor meer machtigingen in het abonnement nodig zijn
- [Werkstromen starten op basis van DevTest Labs-gebeurtenissen](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>Overzicht
[Azure Functions](../azure-functions/functions-overview.md) is een serverloos computerplatform in Azure. Het gebruik van Azure Functions in een oplossing met DevTest Labs stelt ons in staat om de bestaande functies uit te breiden met onze eigen aangepaste code. Zie [Azure-functiesdocumentatie](../azure-functions/functions-overview.md)voor meer informatie over Azure-functies. Om te illustreren hoe Azure-functies kunnen helpen om aan uw vereisten of volledige scenario's in DevTest Labs te voldoen, wordt in dit artikel een voorbeeld gebruikt om een overzicht op het hoogste niveau van VM's in het lab als volgt te geven:

**Voorbeeldvereisten/scenario:** gebruikers kunnen details zien over alle VM's in een lab, inclusief het besturingssysteem, de eigenaar en alle toegepaste artefacten.  Bovendien, als het artefact **Windows-updates** toepassen niet onlangs is toegepast, is er een eenvoudige manier om het toe te passen.

Als u het scenario wilt voltooien, gebruikt u twee functies zoals beschreven in het volgende diagram:  

![Totale stroom](./media/extend-devtest-labs-azure-functions/flow.png)

De broncode voor deze voorbeeldfuncties bevindt zich in de [GitHub-repository van DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) (zowel C#- als PowerShell-implementaties zijn beschikbaar).

- **UpdateInternalSupportPage**: Deze functie stelt DevTest Labs op en werkt de interne ondersteuningspagina direct bij met details over de virtuele machines.
- **WindowsUpdateArtefact toepassen:** voor een vm in een lab past deze functie het **Artefact voor Windows-updates** toe.

## <a name="how-it-works"></a>Hoe werkt het?
Wanneer gebruikers de **interne ondersteuningspagina** in DevTest Labs selecteren, hebben ze een vooraf ingevulde pagina met informatie over VM's, labeigenaren en ondersteuningscontactpersonen.  

Wanneer u de knop Selecteren hier selecteert **om te vernieuwen,** roept de pagina de eerste Azure-functie aan: **UpdateInternalSupportPage**. De functie vraagt DevTest Labs om informatie en herschrijft vervolgens de **interne ondersteuningspagina** met de nieuwe informatie.

Er is een extra actie die kan worden ondernomen, voor alle VM's waarop de Windows Update-artefacten onlangs niet zijn toegepast, is er een knop om Windows-updates toe te passen op de VM. Wanneer u de knop ***Windows-update uitvoeren** voor een vm selecteert, roept de pagina de tweede Azure-functie **aan: ApplyWindowsUpdateArtifact**. Deze functie controleert of de virtuele machine wordt uitgevoerd en past zo ja, het [Artefact van Windows Update](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates) rechtstreeks toe.

## <a name="step-by-step-walkthrough"></a>Stapsgewijze walkthrough
In deze sectie worden stapsgewijze instructies gegeven voor het instellen van Azure Resources die nodig zijn om de **pagina Interne ondersteuning** bij te werken. Deze walkthrough geeft een voorbeeld van de uitbreiding van DevTest Labs. U dit patroon gebruiken voor andere scenario's.

### <a name="step-1-create-a-service-principal"></a>Stap 1: Een serviceprincipal maken 
De eerste stap is om een service principal te krijgen met toestemming voor het abonnement dat het lab bevat. De serviceprincipal moet de verificatie op basis van een wachtwoord gebruiken. Dit kan met [Azure CLI,](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) [Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)of de [Azure-portal.](../active-directory/develop/howto-create-service-principal-portal.md) Als u al een serviceprincipal hebt om te gebruiken, u deze stap overslaan.

Noteer de **toepassings-id,** **sleutel**en **tenant-id** voor de serviceprincipal. Je zult ze later nodig hebben in deze walkthrough. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>Stap 2: Download het voorbeeld en open in Visual Studio 2019
Download lokaal een kopie van het voorbeeld van [De C# Azure-functies](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) (door de repository te klonen of de repository vanaf [hier](https://github.com/Azure/azure-devtestlab/archive/master.zip)te downloaden).  

1. Open de voorbeeldoplossing met Visual Studio 2019.  
1. Installeer de **Azure-ontwikkelworkload** voor Visual Studio als u deze nog niet hebt geïnstalleerd. Het kan worden geïnstalleerd via **Tools** -> **Get Tools en Functies** menu-item).

    ![Azure-ontwikkelingswerkbelasting](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. Bouw de oplossing. Selecteer **Menu-item Bouwen** en **vervolgens Oplossing bouwen.**

### <a name="step-3-deploy-the-sample-to-azure"></a>Stap 3: Het voorbeeld implementeren in Azure
Klik in Visual Studio in het venster **Solution Explorer** met de rechtermuisknop op het **AzureFunctions-project** en selecteer **Publiceren.** Volg de wizard om publicatie te voltooien naar een nieuwe of een bestaande Azure-functie-app. Zie [Azure-functies ontwikkelen met Behulp van Visual Studio](../azure-functions/functions-develop-vs.md)voor gedetailleerde informatie over het ontwikkelen en implementeren van Azure-functies met Visual Studio.

![Dialoogvenster Publiceren](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>Stap 4: Toepassingsinstellingen verzamelen
Zodra de functies zijn gepubliceerd, moet u URL's voor deze functies ophalen via de Azure-portal. 

1. Navigeer naar de [Azure-portal](https://portal.azure.com). 
1. Zoek de functie-app.
1. Selecteer op de pagina **Functie-apps** de functie. 
1. Selecteer **Functie-URL ophalen** zoals weergegeven in de volgende afbeelding. 

    ![URL's voor Azure-functies](./media/extend-devtest-labs-azure-functions/function-url.png)
4. Kopieer en sla de URL op. Herhaal deze stappen voor de andere Azure-functie. 

U hebt ook aanvullende informatie nodig over de serviceprincipal, zoals toepassings-ID, sleutel- en tenant-id.


### <a name="step-5--update-application-settings"></a>Stap 5: Toepassingsinstellingen bijwerken
Selecteer in Visual Studio, nadat u de Azure-functie hebt gepubliceerd, de **Instellingen voor Azure App-service bewerken** onder **Acties**. De volgende toepassingsinstellingen bijwerken (extern):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (standaard tot 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![Toepassingsinstellingen](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>Stap 6: De Azure-functie testen
De laatste stap in deze walkthrough is het testen van de Azure-functie.  

1. Navigeer naar de functie **UpdateInternalSupportPage** in de functie-app die in stap 3 is gemaakt. 
1. Selecteer **Testen** aan de rechterkant van de pagina. 
1. Voer de route-eigenschappen (LABNAME, RESOURCEGROUPNAME en SUBSCRIPTIONID) in.
1. Selecteer **Uitvoeren** om de functie uit te voeren.  

    Met deze functie wordt de interne ondersteuningspagina van het opgegeven lab bijgewerkt. Het bevat ook een knop voor gebruikers om de functie de volgende keer rechtstreeks te bellen

    ![Testfunctie](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>Volgende stappen
Azure Functions kan helpen de functionaliteit van DevTest Labs uit te breiden tot meer dan wat al is ingebouwd en klanten helpen om te voldoen aan hun unieke vereisten voor hun teams. Dit patroon kan worden uitgebreid & verder uitgebreid om nog meer te dekken.  Zie de volgende artikelen voor meer informatie over DevTest Labs: 

- [DevTest Labs Enterprise Reference Architecture](devtest-lab-reference-architecture.md)
- [Veelgestelde vragen](devtest-lab-faq.md)
- [DevTest Labs opschalen](devtest-lab-guidance-scale.md)
- [DevTest Labs automatiseren met PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








