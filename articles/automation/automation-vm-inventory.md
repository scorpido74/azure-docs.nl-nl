---
title: Een virtuele Azure-machine beheren met inventarisverzameling | Microsoft Docs
description: Een virtuele machine beheren met inventarisverzameling
services: automation
ms.subservice: change-inventory-management
keywords: inventaris, automation, wijziging, bijhouden
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0627d2daa70c276535dc43b722e22e1d73b0c8d2
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617375"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Een virtuele Azure-machine met inventarisverzameling beheren

U kunt Inventaris bijhouden voor een virtuele Azure-machine inschakelen vanaf de resourcepagina van de computer. U de volgende voorraadgegevens op uw computers verzamelen en bekijken:

- Windows-software (Windows-toepassingen en Windows-updates), services, bestanden en registersleutels
- Linux software (pakketten) daemons, en bestanden

Deze methode biedt een gebruikersinterface op basis van een browser voor het instellen en configureren van een inventarisverzameling.

## <a name="before-you-begin"></a>Voordat u begint

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/)aan.

In dit artikel wordt ervan uitgegaan dat u een VM hebt om de oplossing op te configureren. Als u nog geen virtuele Azure-machine hebt, [maak dan een virtuele machine](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Verzamelen van inventaris inschakelen op de resourcepagina van de virtuele machine

1. Selecteer in het linkerdeelvenster van de Azure-portal de optie **Virtuele machines**.
2. Selecteer een virtuele machine in de lijst met virtuele machine.
3. Selecteer Voorraad in het menu **Resource** onder **Bewerkingen**. **Inventory**
4. Selecteer een Log Analytics-werkruimte voor het opslaan van uw gegevenslogboeken.
    Als er geen werkruimte beschikbaar is voor deze regio, wordt u gevraagd om een standaardwerkruimte en een Automation-account te maken.
5. Selecteer **Inschakelen** om de onboarding voor uw computer te starten.

   ![Opties voor onboarding weergeven](./media/automation-vm-inventory/inventory-onboarding-options.png)

    In een statusbalk ziet u de melding dat de oplossing wordt ingeschakeld. Dit proces duurt maximaal 15 minuten. Gedurende deze tijd u het venster sluiten, of u het open houden en u op de hoogte stellen wanneer de oplossing is ingeschakeld. U kunt de implementatiestatus controleren vanuit het deelvenster met meldingen.

   ![De inventarisoplossing onmiddellijk na de onboarding weergeven](./media/automation-vm-inventory/inventory-onboarded.png)

Wanneer de implementatie is voltooid, verdwijnt de statusbalk. Het systeem is nog bezig met het verzamelen van inventarisgegevens en de gegevens zijn daarom mogelijk nog niet zichtbaar. Het duurt maximaal 24 uur voordat een volledige verzameling van de gegevens beschikbaar is.

## <a name="configure-your-inventory-settings"></a>De inventarisinstellingen configureren

Standaard worden software-, Windows Services- en Linux-daemons geconfigureerd voor verzameling. Als u Windows-register- en bestandsinventarisgegevens wilt verzamelen, configureer dan de instellingen voor de inventarisverzameling.

1. Klik op de pagina Inventaris boven aan de pagina op **Instellingen bewerken.**
2. Als u een nieuwe verzamelingsinstelling wilt toevoegen, gaat u naar de instellingscategorie die u wilt toevoegen door het tabblad **Windows-register,** **Windows-bestanden**of **Linux-bestanden te** selecteren.
3. Selecteer de juiste categorie en klik boven aan de pagina op **Toevoegen.**

In de volgende tabellen vindt u informatie over elke eigenschap die voor de verschillende categorieën kan worden geconfigureerd.

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
|Ingeschakeld     | True als de instelling wordt toegepast en Onwaar anders.        |
|Itemnaam     | De vriendelijke naam van het bestand dat moet worden bijgehouden.        |
|Groep     | Een groepsnaam voor het logisch groeperen van bestanden.       |
|Pad invoeren     | Het pad om te controleren op het bestand, bijvoorbeeld **c:\temp\myfile.txt**.

### <a name="linux-files"></a>Linux-bestanden

|Eigenschap  |Beschrijving  |
|---------|---------|
|Ingeschakeld     | True als de instelling wordt toegepast en Onwaar anders.        |
|Itemnaam     | De vriendelijke naam van het bestand dat moet worden bijgehouden.        |
|Groep     | Een groepsnaam voor het logisch groeperen van bestanden.        |
|Pad invoeren     | Het pad om te controleren op het bestand, bijvoorbeeld **/etc/*.conf**.       |
|Padtype     | Het type item dat moet worden bijgehouden. Waarden zijn Bestand en Directory.        |
|Recursie     | True als recursie wordt gebruikt bij het zoeken naar het item dat moet worden bijgehouden en False anders.        |
|Sudo gebruiken     | True als sudo wordt gebruikt bij het controleren op het item, en Onwaar anders.         |
|Koppelingen     | Waarde die aangeeft hoe symbolische koppelingen worden behandeld bij het doorkruisen van mappen. Mogelijke waarden zijn: <br> Negeren - Symbolische koppelingen worden genegeerd en de bestanden/mappen waarnaar wordt verwezen, worden niet opgenomen<br>Volgen - Symbolische koppelingen worden gevolgd tijdens recursie en de bestanden/mappen waarnaar wordt verwezen, worden opgenomen<br>Beheren - Symbolische koppelingen worden gevolgd en de afhandeling van de geretourneerde inhoud kan worden gewijzigd      |

## <a name="manage-machine-groups"></a>Machinegroepen beheren

Met Voorraad u machinegroepen maken en bekijken in Azure Monitor-logboeken. Machinegroepen zijn verzamelingen van machines die zijn gedefinieerd door een query in Azure Monitor-logboeken.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Als u uw machinegroepen wilt weergeven, selecteert u het tabblad **Machinegroepen** op de pagina Inventaris.

![Machinegroepen weergeven op de voorraadpagina](./media/automation-vm-inventory/inventory-machine-groups.png)

Als u een machinegroep selecteert in de lijst, wordt de pagina Machinegroepen geopend. Op deze pagina vindt u details over de machinegroep. Deze details omvatten de query voor logboekanalyse die wordt gebruikt om de groep te definiëren. Onder aan de pagina staat een lijst met de machines die deel uitmaken van die groep.

![Pagina machinegroep weergeven](./media/automation-vm-inventory/machine-group-page.png)

Klik **op + Kloon** om de machinegroep te klonen. U moet de groep een nieuwe naam en alias voor de groep geven. De definitie kan op dit moment worden gewijzigd. Nadat u de query hebt gewijzigd, klikt u op **Query valideren** om een voorbeeld te geven van de machines die zijn geselecteerd. Als u tevreden bent met de groep, klikt u op **Maken** om de machinegroep te maken.

Als u een nieuwe machinegroep wilt maken, klikt u op **+ Een machinegroep maken**. Met deze knop opent u de pagina **Een machinegroep maken,** waar u uw nieuwe groep definiëren. Klik op **Maken** om de groep te maken.

![Nieuwe machinegroep maken](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>De virtuele machine loskoppelen van beheer

Ga als volgt te werk om uw virtuele machine te verwijderen uit inventarisbeheer:

1. Selecteer in het linkermenu van de Azure-portal de optie **Log Analytics** en selecteer de werkruimte die u hebt gebruikt tijdens de onboarding van de virtuele machine.
2. Open op de pagina Log Analytics het menu **Resource.**
3. Selecteer **Virtuele machines** onder **Werkruimtegegevensbronnen**.
4. Selecteer in de lijst de virtuele machine die u wilt loskoppelen. De virtuele machine heeft een groen vinkje naast de tekst **Deze werkruimte** in de kolom **OMS-verbinding**.

   >[!NOTE]
   >Operations Management Suite (OMS) wordt nu Azure Monitor-logboeken genoemd.
   
5. Klik boven aan de volgende pagina op **Verbreken**.
6. Klik in het bevestigingsvenster op **Ja** om de machine los te koppelen van het beheer.

## <a name="next-steps"></a>Volgende stappen

* Zie [Track software changes in your environment with the Change Tracking solution](../log-analytics/log-analytics-change-tracking.md) (Wijzigingen in uw omgeving bijhouden met de oplossing Wijzigingen bijhouden) voor meer informatie over het beheren van wijzigingen in bestands- en registerinstellingen.
* Zie [De oplossing voor updatebeheer in Azure](../operations-management-suite/oms-solution-update-management.md)voor meer informatie over het beheren van Windows en pakketupdates op uw virtuele machines.