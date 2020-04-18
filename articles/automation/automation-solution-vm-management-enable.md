---
title: Azure Automation Start/Stop VM's tijdens off hours-oplossing inschakelen
description: In dit artikel wordt beschreven hoe u de VM-oplossing Voor Azure Automation Start/Stop inschakelt voor uw virtuele Azure-machines.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 2414567b74232d634fa0a34202691a8e43ae6135
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604750"
---
# <a name="enable-azure-automation-startstop-vms-solution"></a>Azure Automation Start/Stop VM's-oplossing inschakelen

Voer de volgende stappen uit om de **VM's starten/stoppen toe** te voegen tijdens off-hours oplossing aan een nieuw of bestaand Automatiseringsaccount en gekoppelde Log Analytics-werkruimte. Configureer na het voltooien van het onboardingproces de variabelen om de oplossing aan te passen.

>[!NOTE]
>Als u deze oplossing wilt gebruiken met klassieke VM's, hebt u een Classic Run As-account nodig, dat niet standaard is gemaakt. Zie [Een Klassiek run as-account maken](automation-create-standalone-account.md#create-a-classic-run-as-account)voor instructies voor het maken van een Classic Run As-account .
>

## <a name="enable-solution"></a>Oplossing inschakelen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Zoeken naar en selecteer **Automatiseringsaccounts**.

3. Selecteer op de pagina Automatiseringsaccounts uw automatiseringsaccount in de lijst.

4. Selecteer **VM starten/stoppen** in het automatiseringsaccount onder **Gerelateerde resources**. Klik hier op **Meer informatie over en schakel de oplossing in.** Als u al een Start/Stop VM-oplossing hebt geïmplementeerd, u deze selecteren door op **De oplossing beheren** en deze in de lijst te vinden.

   ![Inschakelen vanuit automatiseringsaccount](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > U deze ook overal in de Azure-portal maken door op **Een resource maken**te klikken. Typ op de marketplace-pagina een trefwoord zoals **Start** of **Start/Stop**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. U ook een of meer zoekwoorden intypen vanuit de volledige naam van de oplossing en vervolgens op Enter drukken. Selecteer **VM's starten/stoppen tijdens off-uren** in de zoekresultaten.

5. Bekijk in de pagina **VM's starten/stoppen tijdens off-hours** voor de geselecteerde oplossing de overzichtsgegevens en klik vervolgens op **Maken**.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

6. De pagina Oplossing toevoegen wordt weergegeven. U wordt gevraagd de oplossing te configureren voordat u deze importeren in uw automatiseringsabonnement.

   ![Pagina Oplossing toevoegen van VM-beheer](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

7. Selecteer **werkruimte**op de pagina Oplossing toevoegen . Selecteer een Log Analytics-werkruimte die is gekoppeld aan hetzelfde Azure-abonnement waarin het Automatiseringsaccount zich bevindt. Als u geen werkruimte hebt, selecteert u **Nieuwe werkruimte maken**. Voer op de werkruimtepagina Log Analytics de volgende stappen uit:

   - Geef een naam op voor de nieuwe Werkruimte Log Analytics, zoals **ContosoLAWorkspace**.
   - Selecteer een **abonnement** waarnaar u wilt linken door in de vervolgkeuzelijst te selecteren of de standaardoptie niet geschikt is.
   - Voor **resourcegroep**u een nieuwe resourcegroep maken of een bestaande groep selecteren.
   - Selecteer een **locatie**.
   - Selecteer een **prijslaag**. Kies de optie **Per GB (Standalone).** Azure Monitor-logboeken hebben [de prijzen](https://azure.microsoft.com/pricing/details/log-analytics/) bijgewerkt en de laag Per GB is de enige optie.

   > [!NOTE]
   > Bij het inschakelen van oplossingen worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werkruimte en een Automatiseringsaccount.
   >
   > Zie [Regiotoewijzing voor Automatiseringsaccount en Log Analytics-werkruimte voor](how-to/region-mappings.md)een lijst met ondersteunde toewijzingsparen.

8. Nadat u de vereiste informatie op de werkruimtepagina Log Analytics hebt verstrekt, klikt u op **Maken**. U de voortgang bijhouden onder **Meldingen** in het menu, waarmee u wordt geretourneerd naar de pagina Oplossing toevoegen wanneer u klaar bent.

9. Selecteer op de pagina Oplossing toevoegen de optie **Automatiseringsaccount**. Als u een nieuwe Log Analytics-werkruimte maakt, u een nieuw automatiseringsaccount maken waaraan u moet worden gekoppeld of een bestaand automatiseringsaccount selecteren dat nog niet is gekoppeld aan een Log Analytics-werkruimte. Selecteer een bestaand Automatiseringsaccount of klik op **Een automatiseringsaccount maken**en geef op de pagina Automatiseringsaccount toevoegen de volgende gegevens op:
 
   - Voer in het veld **Naam** de naam van het Automation-account in.

     Alle andere opties worden automatisch ingevuld op basis van de geselecteerde werkruimte Log Analytics. Deze opties kunnen niet worden gewijzigd. Een Uitvoeren als-account voor Azure is de standaardmethode voor verificatie voor de runbooks die zijn opgenomen in deze oplossing. Nadat u op **OK**hebt geklikt, worden de configuratieopties gevalideerd en wordt het automatiseringsaccount gemaakt. U kunt de voortgang bijhouden onder **Meldingen** in het menu.

10. Selecteer tot slot op de pagina Oplossing toevoegen de optie **Configuratie**. De pagina Parameters wordt weergegeven.

    ![Parameters pagina voor oplossing](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Hier wordt u gevraagd om:
  
   - Geef de **namen van doelbrongroepen op**. Deze waarden zijn namen van resourcegroepen die VM's bevatten die door deze oplossing moeten worden beheerd. U meer dan één naam invoeren en elk afzonderlijk maken met behulp van een komma (waarden zijn niet hoofdlettergevoelig). Jokertekens worden ondersteund als de bewerking moet worden gericht op VM's in alle resourcegroepen in het abonnement. Deze waarde wordt opgeslagen in de **variabelen External_Start_ResourceGroupNames** en **External_Stop_ResourceGroupNames.**
  
   - Geef de **lijst met vm-uitsluiting (tekenreeks) op**. Deze waarde is de naam van een of meer virtuele machines uit de doelgroep. U meer dan één naam invoeren en elk afzonderlijk maken met behulp van een komma (waarden zijn niet hoofdlettergevoelig). Het gebruik van een wildcard wordt ondersteund. Deze waarde wordt opgeslagen in de **variabele External_ExcludeVMNames.**
  
   - Selecteer een **planning**. Selecteer een datum en tijd voor uw planning. Er wordt een terugkerende dagelijkse planning gemaakt vanaf het tijdstip dat u hebt geselecteerd. Het selecteren van een andere regio is niet beschikbaar. Zie [Het opstart- en afsluitschema wijzigen](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules)als u de planning wilt configureren voor uw specifieke tijdzone.
  
   - Als u **e-mailmeldingen** van een actiegroep wilt ontvangen, accepteert u de standaardwaarde **Ja** en geeft u een geldig e-mailadres op. Als u **Nee selecteert,** maar op een later tijdstip besluit dat u e-mailmeldingen wilt ontvangen, u de [actiegroep](../azure-monitor/platform/action-groups.md) bijwerken die is gemaakt met geldige e-mailadressen, gescheiden door een komma. U moet ook de volgende waarschuwingsregels inschakelen:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > De standaardwaarde voor **doelgroepgroepnamen** is een **&ast;**. Dit richt zich op alle VM's in een abonnement. Als u niet wilt dat de oplossing om alle VM's in uw abonnement te targeten, deze waarde moet worden bijgewerkt naar een lijst met namen van resourcegroepen voordat de planningen worden ingeschakeld.

11. Nadat u de eerste instellingen hebt geconfigureerd die nodig zijn voor de oplossing, klikt u op **OK** om de pagina Parameters te sluiten en **selecteer Maken**. 

Nadat alle instellingen zijn gevalideerd, wordt de oplossing geïmplementeerd in uw abonnement. Dit proces kan enkele seconden duren en u de voortgang ervan bijhouden onder **Meldingen** in het menu.

> [!NOTE]
> Als u een Azure Cloud Solution Provider-abonnement (Azure CSP) hebt, gaat u na implementatie voltooid in uw Automatiseringsaccount naar **Variabelen** onder **Gedeelde resources** en stelt u de [**External_EnableClassicVMs**](automation-solution-vm-management.md#variables) variabele in op **False**. Dit voorkomt dat de oplossing op zoek gaat naar klassieke VM-bronnen.

## <a name="next-steps"></a>Volgende stappen

Nu u de oplossing hebt ingeschakeld, u [deze configureren](automation-solution-vm-management-config.md) om uw VM-beheervereisten te ondersteunen.