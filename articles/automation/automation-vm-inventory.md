---
title: Een virtuele Azure-machine beheren met inventarisverzameling | Microsoft Docs
description: Een virtuele machine beheren met inventarisverzameling
services: automation
ms.subservice: change-inventory-management
keywords: inventaris, automation, wijziging, bijhouden
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0627d2daa70c276535dc43b722e22e1d73b0c8d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617375"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Een virtuele Azure-machine met inventarisverzameling beheren

U kunt Inventaris bijhouden voor een virtuele Azure-machine inschakelen vanaf de resourcepagina van de computer. U kunt de volgende inventaris gegevens verzamelen en weer geven op uw computers:

- Windows-software (Windows-toepassingen en Windows-updates), services, bestanden en register sleutels
- Linux-software (pakketten) daemons en bestanden

Deze methode biedt een gebruikersinterface op basis van een browser voor het instellen en configureren van een inventarisverzameling.

## <a name="before-you-begin"></a>Voordat u begint

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/).

In dit artikel wordt ervan uitgegaan dat u een virtuele machine hebt waarmee u de oplossing kunt configureren. Als u nog geen virtuele Azure-machine hebt, [maak dan een virtuele machine](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Verzamelen van inventaris inschakelen op de resourcepagina van de virtuele machine

1. Selecteer in het linkerdeelvenster van de Azure-portal de optie **Virtuele machines**.
2. Selecteer een virtuele machine in de lijst met virtuele machine.
3. Selecteer in het menu **resource** onder **bewerkingen**de optie **inventaris**.
4. Selecteer een Log Analytics-werk ruimte om uw gegevens logboeken op te slaan.
    Als er geen werkruimte beschikbaar is voor deze regio, wordt u gevraagd om een standaardwerkruimte en een Automation-account te maken.
5. Selecteer **Inschakelen** om de onboarding voor uw computer te starten.

   ![Opties voor onboarding weergeven](./media/automation-vm-inventory/inventory-onboarding-options.png)

    In een statusbalk ziet u de melding dat de oplossing wordt ingeschakeld. Dit proces duurt maximaal 15 minuten. Gedurende deze tijd kunt u het venster sluiten, of u kunt het openen en u krijgt een melding wanneer de oplossing is ingeschakeld. U kunt de implementatiestatus controleren vanuit het deelvenster met meldingen.

   ![De inventarisoplossing onmiddellijk na de onboarding weergeven](./media/automation-vm-inventory/inventory-onboarded.png)

Wanneer de implementatie is voltooid, verdwijnt de statusbalk. Het systeem is nog bezig met het verzamelen van inventarisgegevens en de gegevens zijn daarom mogelijk nog niet zichtbaar. Het duurt maximaal 24 uur voordat een volledige verzameling van de gegevens beschikbaar is.

## <a name="configure-your-inventory-settings"></a>De inventarisinstellingen configureren

Standaard worden software-, Windows Services- en Linux-daemons geconfigureerd voor verzameling. Als u Windows-register- en bestandsinventarisgegevens wilt verzamelen, configureer dan de instellingen voor de inventarisverzameling.

1. Klik boven aan de pagina op **Instellingen bewerken** op de pagina inventarisatie.
2. Als u een nieuwe instelling voor de verzameling wilt toevoegen, gaat u naar de instellings categorie die u wilt toevoegen door het tabblad **Windows-REGI ster**, **Windows-bestanden**of Linux- **bestanden** te selecteren.
3. Selecteer de juiste categorie en klik boven aan de pagina op **toevoegen** .

De volgende tabellen bevatten informatie over elke eigenschap die kan worden geconfigureerd voor de verschillende categorieën.

### <a name="windows-registry"></a>Windows-register

|Eigenschap  |Beschrijving  |
|---------|---------|
|Ingeschakeld     | Bepaalt of de instelling wordt toegepast        |
|Itemnaam     | Beschrijvende naam van het bestand dat moet worden bijgehouden        |
|Groep     | De naam van een groep voor het logisch groeperen van bestanden        |
|Windows-registersleutel   | Het pad voor het controleren op het bestand, bijvoorbeeld: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="windows-files"></a>Windows-bestanden

|Eigenschap  |Beschrijving  |
|---------|---------|
|Ingeschakeld     | Waar als de instelling wordt toegepast en anders false.        |
|Itemnaam     | De beschrijvende naam van het bestand dat moet worden bijgehouden.        |
|Groep     | Een groeps naam voor het logisch groeperen van bestanden.       |
|Pad invoeren     | Het pad om het bestand te controleren, bijvoorbeeld **c:\temp\myfile.txt**.

### <a name="linux-files"></a>Linux-bestanden

|Eigenschap  |Beschrijving  |
|---------|---------|
|Ingeschakeld     | Waar als de instelling wordt toegepast en anders false.        |
|Itemnaam     | De beschrijvende naam van het bestand dat moet worden bijgehouden.        |
|Groep     | Een groeps naam voor het logisch groeperen van bestanden.        |
|Pad invoeren     | Het pad om het bestand te controleren, bijvoorbeeld **/etc/*. conf**.       |
|Padtype     | Het type item dat moet worden bijgehouden. Waarden zijn bestand en Directory.        |
|Recursie     | Waar als recursie wordt gebruikt bij het zoeken naar het item dat moet worden bijgehouden, en ONWAAR anders onwaar.        |
|Sudo gebruiken     | Waar als sudo wordt gebruikt bij het controleren op het item en ONWAAR, anders false.         |
|Koppelingen     | Waarde die aangeeft hoe symbolische koppelingen worden behandeld bij het door lopen van directory's. Mogelijke waarden zijn: <br> Negeren - Symbolische koppelingen worden genegeerd en de bestanden/mappen waarnaar wordt verwezen, worden niet opgenomen<br>Volgen - Symbolische koppelingen worden gevolgd tijdens recursie en de bestanden/mappen waarnaar wordt verwezen, worden opgenomen<br>Beheren - Symbolische koppelingen worden gevolgd en de afhandeling van de geretourneerde inhoud kan worden gewijzigd      |

## <a name="manage-machine-groups"></a>Computer groepen beheren

Met inventaris kunt u computer groepen maken en weer geven in Azure Monitor Logboeken. Computer groepen zijn verzamelingen machines die door een query in Azure Monitor logboeken worden gedefinieerd.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Als u uw computer groepen wilt weer geven, selecteert u het tabblad **machine groepen** op de pagina inventarisatie.

![Computer groepen weer geven op de pagina inventaris](./media/automation-vm-inventory/inventory-machine-groups.png)

Als u een computer groep selecteert in de lijst, wordt de pagina computer groepen geopend. Deze pagina bevat details over de computer groep. Deze details bevatten de log Analytics-query die wordt gebruikt voor het definiëren van de groep. Onder aan de pagina bevindt zich een lijst met pagina's van de computers die deel uitmaken van die groep.

![Pagina computer groep weer geven](./media/automation-vm-inventory/machine-group-page.png)

Klik op **+ kloon** om de computer groep te klonen. U moet de groep een nieuwe naam en alias geven voor de groep. De definitie kan op dit moment worden gewijzigd. Nadat u de query hebt gewijzigd, klikt u op **query valideren** om een voor beeld te bekijken van de computers die worden geselecteerd. Wanneer u tevreden bent met de groep, klikt u op **maken** om de computer groep te maken.

Als u een nieuwe machine groep wilt maken, klikt u op **+ een computer groep maken**. Met deze knop opent u de pagina **een machine groep maken** , waar u de nieuwe groep kunt definiëren. Klik op **Maken** om de groep te maken.

![Nieuwe computer groep maken](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>De virtuele machine loskoppelen van beheer

Ga als volgt te werk om uw virtuele machine te verwijderen uit inventarisbeheer:

1. Selecteer in het linkermenu van de Azure-portal de optie **Log Analytics** en selecteer de werkruimte die u hebt gebruikt tijdens de onboarding van de virtuele machine.
2. Open op de pagina Log Analytics het **resource** menu.
3. Selecteer **virtual machines** onder **gegevens bronnen voor de werk ruimte**.
4. Selecteer in de lijst de virtuele machine die u wilt loskoppelen. De virtuele machine heeft een groen vinkje naast de tekst **Deze werkruimte** in de kolom **OMS-verbinding**.

   >[!NOTE]
   >Operations Management Suite (OMS) wordt nu Azure Monitor-logboeken genoemd.
   
5. Klik boven aan de volgende pagina op **verbinding verbreken**.
6. Klik in het bevestigings venster op **Ja** om de computer los te koppelen van beheer.

## <a name="next-steps"></a>Volgende stappen

* Zie [Track software changes in your environment with the Change Tracking solution](../log-analytics/log-analytics-change-tracking.md) (Wijzigingen in uw omgeving bijhouden met de oplossing Wijzigingen bijhouden) voor meer informatie over het beheren van wijzigingen in bestands- en registerinstellingen.
* Zie [de updatebeheer-oplossing in azure](../operations-management-suite/oms-solution-update-management.md)voor meer informatie over het beheren van Windows en pakket updates op uw virtuele machines.