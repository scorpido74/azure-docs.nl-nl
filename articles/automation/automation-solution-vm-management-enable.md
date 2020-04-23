---
title: De oplossing voor het starten/stoppen van Vm's in Azure Automation
description: In dit artikel wordt beschreven hoe u de oplossing voor het Azure Automation starten/stoppen van VM'S voor uw virtuele Azure-machines inschakelt.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: d47daa29c65f847fdeb33b9e24a892ac1f31b52a
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096959"
---
# <a name="enable-azure-automation-startstop-vms-solution"></a>Vm's van Azure Automation starten/stoppen inschakelen

Voer de volgende stappen uit om de **VM's buiten bedrijfsuren starten/stoppen** -oplossing toe te voegen aan een nieuw of bestaand Automation-account en gekoppelde log Analytics werk ruimte. Nadat het voorbereidings proces is voltooid, configureert u de variabelen om de oplossing aan te passen.

>[!NOTE]
>Als u deze oplossing wilt gebruiken met klassieke Vm's, hebt u een klassiek uitvoeren als-account nodig, dat niet standaard wordt gemaakt. Zie [een klassiek uitvoeren als-account maken](automation-create-standalone-account.md#create-a-classic-run-as-account)voor instructies voor het maken van een klassiek uitvoeren als-account.
>

## <a name="enable-solution"></a>Oplossing inschakelen

1. Meld u aan bij Azure [Portal](https://portal.azure.com).

2. Zoek en selecteer **Automation-accounts**.

3. Selecteer op de pagina Automation-accounts uw Automation-account in de lijst.

4. Selecteer in het Automation-account de optie **VM starten/stoppen** onder **gerelateerde resources**. Hier kunt u op meer **informatie klikken en de oplossing inschakelen**. Als u al een VM-oplossing voor starten/stoppen hebt geïmplementeerd, kunt u deze selecteren door te klikken op **de oplossing beheren** en deze te zoeken in de lijst.

   ![Inschakelen vanuit Automation-account](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > U kunt de app ook maken op een wille keurige locatie in de Azure Portal door te klikken op **een resource maken**. Typ op de pagina Marketplace een tref woord zoals **starten** of **starten/stoppen**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. U kunt ook een of meer tref woorden in de volledige naam van de oplossing typen en vervolgens op ENTER drukken. Selecteer **VM's buiten bedrijfsuren starten/stoppen** in de zoek resultaten.

5. Controleer op de pagina **VM's buiten bedrijfsuren starten/stoppen** voor de geselecteerde oplossing de samenvattings informatie en klik vervolgens op **maken**.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

6. De pagina oplossing toevoegen wordt weer gegeven. U wordt gevraagd om de oplossing te configureren voordat u deze kunt importeren in uw Automation-abonnement.

   ![Pagina oplossing toevoegen van VM-beheer](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

7. Selecteer op de pagina oplossing toevoegen de optie **werk ruimte**. Selecteer een Log Analytics-werk ruimte die is gekoppeld aan hetzelfde Azure-abonnement waarin het Automation-account zich bevindt. Als u geen werk ruimte hebt, selecteert u **nieuwe werk ruimte maken**. Voer de volgende stappen uit op de pagina werk ruimte Log Analytics:

   - Geef een naam op voor de nieuwe Log Analytics-werk ruimte, zoals **ContosoLAWorkspace**.
   - Selecteer een **abonnement** om te koppelen door te selecteren in de vervolg keuzelijst, als de standaard optie is geselecteerd, niet geschikt is.
   - Voor **resource groep**kunt u een nieuwe resource groep maken of een bestaande selecteren.
   - Selecteer een **locatie**.
   - Selecteer een **prijs categorie**. Kies de optie **per GB (zelfstandig)** . Azure Monitor-logboeken hebben bijgewerkte [prijzen](https://azure.microsoft.com/pricing/details/log-analytics/) en de laag per GB is de enige optie.

   > [!NOTE]
   > Bij het inschakelen van oplossingen worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account.
   >
   > Zie [regio toewijzing voor Automation-account en log Analytics-werk ruimte](how-to/region-mappings.md)voor een lijst met de ondersteunde toewijzings paren.

8. Nadat u de vereiste gegevens op de pagina Log Analytics werk ruimte hebt opgegeven, klikt u op **maken**. U kunt de voortgang bijhouden onder **meldingen** in het menu, waarmee u wordt teruggebracht naar de pagina oplossing toevoegen wanneer u klaar bent.

9. Selecteer op de pagina oplossing toevoegen de optie **Automation-account**. Als u een nieuwe Log Analytics-werk ruimte maakt, kunt u een nieuw Automation-account maken dat u eraan wilt koppelen of een bestaand Automation-account selecteren dat nog niet aan een Log Analytics-werk ruimte is gekoppeld. Selecteer een bestaand Automation-account of klik op **een Automation-account maken**en geef op de pagina Automation-account toevoegen de volgende informatie op:
 
   - Voer in het veld **Naam** de naam van het Automation-account in.

     Alle andere opties worden automatisch ingevuld op basis van de geselecteerde Log Analytics werk ruimte. Deze opties kunnen niet worden gewijzigd. Een Uitvoeren als-account voor Azure is de standaardmethode voor verificatie voor de runbooks die zijn opgenomen in deze oplossing. Nadat u op **OK**hebt geklikt, worden de configuratie opties gevalideerd en wordt het Automation-account gemaakt. U kunt de voortgang bijhouden onder **Meldingen** in het menu.

10. Selecteer ten slotte op de pagina oplossing toevoegen de optie **configuratie**. De pagina para meters wordt weer gegeven.

    ![De pagina para meters voor de oplossing](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Hier wordt u gevraagd het volgende te doen:
  
   - Geef de **namen**van de doel-ResourceGroup op. Deze waarden zijn namen van resource groepen die virtuele machines bevatten die door deze oplossing moeten worden beheerd. U kunt meer dan één naam invoeren en deze scheiden door een komma te gebruiken (waarden zijn niet hoofdletter gevoelig). Jokertekens worden ondersteund als de bewerking moet worden gericht op VM's in alle resourcegroepen in het abonnement. Deze waarde wordt opgeslagen in de variabelen **External_Start_ResourceGroupNames** en **External_Stop_ResourceGroupNames** .
  
   - Geef de **lijst met uitsluitingen van de virtuele machine op (teken reeks)**. Deze waarde is de naam van een of meer virtuele machines uit de doel resource groep. U kunt meer dan één naam invoeren en deze scheiden door een komma te gebruiken (waarden zijn niet hoofdletter gevoelig). Het gebruik van een Joker teken wordt ondersteund. Deze waarde wordt opgeslagen in de variabele **External_ExcludeVMNames** .
  
   - Selecteer een **planning**. Selecteer een datum en tijd voor de planning. Een opnieuw uitgevoerd dagelijks schema wordt gemaakt op basis van de tijd die u hebt geselecteerd. Het selecteren van een andere regio is niet beschikbaar. Zie [het schema voor opstarten en afsluiten aanpassen](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules)als u de planning wilt configureren voor uw specifieke tijd zone nadat u de oplossing hebt geconfigureerd.
  
   - Als u **e-mail meldingen** van een actie groep wilt ontvangen, accepteert u de standaard waarde **Ja** en geeft u een geldig e-mail adres op. Als u **Nee** selecteert maar besluit op een later tijdstip dat u e-mail meldingen wilt ontvangen, kunt u de [actie groep](../azure-monitor/platform/action-groups.md) bijwerken die is gemaakt met geldige e-mail adressen gescheiden door een komma. U moet ook de volgende waarschuwings regels inschakelen:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > De standaard waarde voor de **namen van doel** - **&ast;** ResourceGroup is een. Hiermee worden alle virtuele machines in een abonnement gericht. Als u niet wilt dat de oplossing alle virtuele machines in uw abonnement heeft, moet deze waarde worden bijgewerkt naar een lijst met namen van resource groepen voordat de planningen worden ingeschakeld.

11. Nadat u de aanvankelijk vereiste instellingen voor de oplossing hebt geconfigureerd, klikt u op **OK** om de pagina para meters te sluiten en selecteert u **maken**. 

Nadat alle instellingen zijn gevalideerd, wordt de oplossing geïmplementeerd voor uw abonnement. Het kan enkele seconden duren voordat dit proces is voltooid en u kunt de voortgang bijhouden onder **meldingen** in het menu.

> [!NOTE]
> Als u een abonnement voor Azure Cloud Solution Provider (Azure CSP) hebt nadat de implementatie is voltooid, gaat u in uw Automation-account naar **variabelen** onder **gedeelde resources** en stelt u de [External_EnableClassicVMs](automation-solution-vm-management.md#variables) variabele in op **Onwaar**. Zo stopt de oplossing met het zoeken naar klassieke VM-resources.

## <a name="next-steps"></a>Volgende stappen

Nu u de oplossing hebt ingeschakeld, kunt u deze [configureren](automation-solution-vm-management-config.md) ter ondersteuning van uw vereisten voor VM-beheer.