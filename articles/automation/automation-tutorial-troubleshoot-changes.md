---
title: Problemen met wijzigingen in een virtuele machine van Azure oplossen | Microsoft Docs
description: Gebruik Wijzigingen bijhouden om problemen met wijzigingen in een virtuele machine van Azure op te lossen.
services: automation
ms.subservice: change-inventory-management
keywords: bijhouden, wijzigingen, automation
ms.date: 12/05/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 60ca1ef3d5c14a0f3dea5b662fc5c95184e6574d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75420641"
---
# <a name="troubleshoot-changes-in-your-environment"></a>Problemen met wijzigingen in uw omgeving oplossen

In deze zelfstudie leert u hoe u problemen met wijzigingen in een virtuele machine van Azure oplost. Als u Wijzigingen bijhouden inschakelt, kunt u wijzigingen in software, bestanden, Linux-daemons, Windows-services en Windows-registersleutels op uw computers bijhouden.
Door deze configuratiewijzigingen op te sporen, kunt operationele problemen in uw hele omgeving vaststellen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een VM onboarden voor Wijzigingen bijhouden en Inventaris
> * Zoeken in wijzigingenlogboeken voor gestopte services
> * Wijzigingen bijhouden configureren
> * Verbinding met activiteitenlogboek inschakelen
> * Een gebeurtenis activeren
> * Wijzigingen weergeven
> * Waarschuwingen configureren

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Een Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een [Automation-account](automation-offering-get-started.md) voor opslag van de Watcher- en actie-runbooks en de Watcher-taak.
* Een [virtuele machine](../virtual-machines/windows/quick-create-portal.md) voor de onboarding.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Wijzigingen bijhouden en Inventaris inschakelen

U moet voor deze zelfstudie eerst Wijzigingen bijhouden en Inventaris voor uw virtuele machine inschakelen. Als u eerder een andere automatiseringsoplossing voor een VM hebt ingeschakeld, is deze stap niet nodig.

1. Selecteer in het menu links **Virtuele machines** en selecteer een virtuele machine in de lijst.
1. Klik in het menu links in de sectie **Bewerkingen** op **Inventaris**. De pagina **Wijzigingen bijhouden** wordt geopend.

Als u ![Wijzigingen bijhouden inschakelt](./media/automation-tutorial-troubleshoot-changes/enableinventory.png), wordt het scherm **Wijzigingen bijhouden** weergegeven. Configureer de locatie, de Log Analytics-werkruimte en het Automation-account dat moet worden gebruikt, en klik op **Inschakelen**. Als de velden lichtgrijs zijn, betekent dit dat een andere automatiseringsoplossing is ingeschakeld voor de virtuele machine en dat dezelfde werkruimte en hetzelfde Automation-account moeten worden gebruikt.

Een [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)-werkruimte wordt gebruikt om gegevens te verzamelen die worden gegenereerd door functies en services zoals Inventaris.
De werkruimte biedt één locatie om gegevens uit meerdere bronnen te bekijken en te analyseren.

Tijdens het onboardingproces wordt de virtuele machine ingericht met Microsoft Monitoring Agent (MMA) en Hybrid Worker.
Deze agent wordt gebruikt om te communiceren met de virtuele machine en om informatie op te vragen over geïnstalleerde software.

Het inschakelen van de oplossing kan maximaal 15 minuten duren. Gedurende deze tijd mag u het browservenster niet sluiten.
Nadat de oplossing is ingeschakeld, wordt informatie over geïnstalleerde software en wijzigingen aan de VM-stromen naar Azure Monitor-logboeken verzonden.
Het duurt tussen 30 minuten en 6 uur voordat de gegevens beschikbaar zijn voor analyse.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-change-tracking-in-azure-monitor-logs"></a>Wijzigingen bijhouden gebruiken in Azure Monitor-logboeken

Wijzigingen bijhouden genereert logboekgegevens die naar Azure Monitor-logboeken worden verzonden.
Als u de logboeken wilt doorzoeken door query's uit te voeren, selecteert u **Log Analytics** boven in het venster **Wijzigingen bijhouden**.
Gegevens van Wijzigingen bijhouden worden opgeslagen onder het type **ConfigurationChange**.
De volgende voorbeeldquery voor Log Analytics retourneert alle Windows-services die zijn gestopt.

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Zie [Azure Monitor-logboeken](../azure-monitor/log-query/log-query-overview.md) voor meer informatie over het uitvoeren en doorzoeken van logboekbestanden in Azure Monitor-logboeken.

## <a name="configure-change-tracking"></a>Wijzigingen bijhouden configureren

Met Wijzigingen bijhouden kunt u wijzigingen in de configuratie van de virtuele machine bijhouden. In de volgende stappen wordt uitgelegd hoe u het bijhouden van registersleutels en bestanden configureert.

Kies welke bestanden en registersleutels u wilt verzamelen en volgen door **Instellingen bewerken** te selecteren bovenaan de pagina **Wijzigingen bijhouden**.

> [!NOTE]
> Inventaris en Wijzigingen bijhouden gebruiken dezelfde verzamelingsinstellingen en instellingen worden geconfigureerd op het niveau van de werkruimte.

In het venster **Werkruimteconfiguratie** voegt u Windows-registersleutels, Windows-bestanden of Linux-bestanden toe die moeten worden bijgehouden, zoals wordt beschreven in de volgende drie secties.

### <a name="add-a-windows-registry-key"></a>Een Windows-registersleutel toevoegen

1. Selecteer **Toevoegen** op het tabblad **Windows-register**.
    Het venster **Windows-register voor het bijhouden van wijzigingen toevoegen** wordt geopend.

1. In **Windows-register voor het bijhouden van wijzigingen toevoegen** voert u de gegevens in die u wilt bijhouden voor de sleutel en klikt u op **Opslaan**

|Eigenschap  |Beschrijving  |
|---------|---------|
|Ingeschakeld     | Bepaalt of de instelling wordt toegepast        |
|Itemnaam     | Beschrijvende naam van het bestand dat moet worden bijgehouden        |
|Groep     | De naam van een groep voor het logisch groeperen van bestanden        |
|Windows-registersleutel   | Het pad voor het controleren op het bestand, bijvoorbeeld: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="add-a-windows-file"></a>Een Windows-bestand toevoegen

1. Selecteer **Toevoegen** op het tabblad **Windows-bestanden**. Het venster **Windows-bestand voor het bijhouden van wijzigingen toevoegen** wordt geopend.

1. In **Windows-bestand voor het bijhouden van wijzigingen toevoegen** voert u de gegevens van het bestand of de map voor het bijhouden van wijzigingen in en klikt u op **Opslaan**

|Eigenschap  |Beschrijving  |
|---------|---------|
|Ingeschakeld     | Bepaalt of de instelling wordt toegepast        |
|Itemnaam     | Beschrijvende naam van het bestand dat moet worden bijgehouden        |
|Groep     | De naam van een groep voor het logisch groeperen van bestanden        |
|Pad invoeren     | Het pad voor het controleren op het bestand, bijvoorbeeld: "C:\temp\\\*.txt"<br>U kunt ook omgevingsvariabelen gebruiken zoals ' %winDir%\System32\\\*. * "         |
|Recursie     | Bepaalt of recursie wordt gebruikt bij het zoeken naar het item dat moet worden bijgehouden.        |
|Bestandsinhoud uploaden voor alle instellingen| Schakelt uploaden van bestandsinhoud bij bijgehouden wijzigingen in of uit. Beschikbare opties: **Waar** of **Onwaar**.|

### <a name="add-a-linux-file"></a>Een Linux-bestand toevoegen

1. Selecteer **Toevoegen** op het tabblad **Linux-bestanden**. Het venster **Linux-bestand voor het bijhouden van wijzigingen toevoegen** wordt geopend.

1. In **Linux-bestand voor het bijhouden van wijzigingen toevoegen** voert u de gegevens van het bestand of de map voor het bijhouden van wijzigingen in en klikt u op **Opslaan**

|Eigenschap  |Beschrijving  |
|---------|---------|
|Ingeschakeld     | Bepaalt of de instelling wordt toegepast        |
|Itemnaam     | Beschrijvende naam van het bestand dat moet worden bijgehouden        |
|Groep     | De naam van een groep voor het logisch groeperen van bestanden        |
|Pad invoeren     | Het pad voor het controleren op het bestand, bijvoorbeeld: "/etc/*.conf"       |
|Padtype     | Type item voor het bijhouden van wijzigingen. Mogelijke waarden zijn Bestand en Map        |
|Recursie     | Bepaalt of recursie wordt gebruikt bij het zoeken naar het item dat moet worden bijgehouden.        |
|Sudo gebruiken     | Deze instelling bepaalt of sudo wordt gebruikt bij het controleren op het item.         |
|Koppelingen     | Deze instelling bepaalt hoe symbolische koppelingen worden afgehandeld bij het doorlopen van mappen.<br> **Negeren** - Symbolische koppelingen worden genegeerd en de bestanden/mappen waarnaar wordt verwezen, worden niet opgenomen<br>**Volgen** - Symbolische koppelingen worden gevolgd tijdens recursie en de bestanden/mappen waarnaar wordt verwezen, worden opgenomen<br>**Beheren** - Symbolische koppelingen worden gevolgd en de afhandeling van de geretourneerde inhoud kan worden gewijzigd      |
|Bestandsinhoud uploaden voor alle instellingen| Schakelt uploaden van bestandsinhoud bij bijgehouden wijzigingen in of uit. Beschikbare opties: **Waar** of **Onwaar**.|

   > [!NOTE]
   > Het gebruik van de optie 'Beheren' voor koppelingen wordt niet aanbevolen. Het ophalen van bestandsinhoud wordt niet ondersteund.

## <a name="enable-activity-log-connection"></a>Verbinding met activiteitenlogboek inschakelen

Selecteer **Verbinding met het activiteitenlogboek beheren** op de pagina **Wijzigingen bijhouden** op de virtuele machine. Hiermee opent u de pagina **Azure-activiteitenlogboek**. Selecteer **Verbinden** om Wijzigingen bijhouden te verbinden met het Azure-activiteitenlogboek voor uw virtuele machine.

Terwijl deze instelling is ingeschakeld, gaat u naar de pagina **Overzicht** voor de virtuele machine en selecteert u **Stoppen** om de virtuele machine te stoppen. Wanneer u daarom wordt gevraagd, selecteert u **Ja** om de virtuele machine te stoppen. Wanneer deze toewijzing ongedaan is gemaakt, selecteert u **Starten** om de virtuele machine opnieuw op te starten.

Wanneer een virtuele machine wordt gestart en gestopt, wordt een gebeurtenis geregistreerd in het activiteitenlogboek. Ga terug naar de pagina **Wijzigingen bijhouden**. Selecteer het tabblad **Gebeurtenissen** onderaan op de pagina. Na een tijdje worden de gebeurtenissen weergegeven in de grafiek en in de tabel. Net als in de vorige stap kan elke gebeurtenis worden geselecteerd om gedetailleerde informatie over die gebeurtenis weer te geven.

![Details van de wijziging weergeven in de portal](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

## <a name="view-changes"></a>Wijzigingen weergeven

Zodra de oplossing Wijzigingen bijhouden en Inventaris is ingeschakeld, kunt u de resultaten bekijken op de pagina **Wijzigingen bijhouden**.

Selecteer vanuit de virtuele machine de optie **Wijzigingen bijhouden** onder **BEWERKINGEN**.

![Schermafbeelding van de lijst met wijzigingen in de virtuele machine](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

De grafiek toont wijzigingen die in de loop der tijd hebben plaatsgevonden.
Nadat u een verbinding met een Azure-activiteitenlogboek hebt toegevoegd, toont de lijngrafiek bovenaan gebeurtenissen uit het Azure-actitviteitenlogboek.
Elke rij in het staafdiagram vertegenwoordigt een ander type wijziging dat kan worden gevolgd.
Deze typen zijn Linux-daemons, bestanden, Windows-registersleutels, software en Windows-services.
Het tabblad met wijzigingen bevat details over de wijzigingen in de visualisatie. Deze worden getoond in aflopende volgorde vanaf het moment waarop de wijziging optrad (meest recente eerst).
Op het tabblad **Gebeurtenissen** worden in de tabel de gebeurtenissen in het verbonden activiteitenlogboek en de bijbehorende gegevens weergegeven met de meest recente eerst.

U kunt in de resultaten zien dat er meerdere wijzigingen in het systeem waren, inclusief wijzigingen in services en software. U kunt de filters bovenaan de pagina gebruiken om de resultaten te filteren op **Type wijziging** of op tijdsbereik.

Selecteer een **WindowsServices**-wijziging waarna het venster **Details van wijziging** wordt geopend. Dit venster toont details over de wijziging en de waarden voor en na de wijziging. In dit geval is de service Software Protection gestopt.

![Details van de wijziging weergeven in de portal](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>Waarschuwingen configureren

Het kan handig zijn om wijzigingen te bekijken in Azure Portal, maar het is nóg handiger om waarschuwingen te ontvangen wanneer er iets verandert (bijvoorbeeld wanneer een service wordt gestopt).

Als u een waarschuwing voor het stoppen van services wilt toevoegen, gaat u in Azure Portal naar **Bewaken**. Klik dan bij **Gedeelde services** op **Waarschuwingen** en klik dan op **+ Nieuwe waarschuwingsregel**

Klik op **Selecteren** om een resource te kiezen. Selecteer op de pagina **Een resource selecteren** in de vervolgkeuzelijst **Resourcetype** de optie **Log Analytics**. Selecteer de Log Analytics-werkruimte en selecteer vervolgens **Gereed**.

![Een resource selecteren](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

Klik op **Voorwaarde toevoegen**, en selecteer op de pagina **Signaallogica configureren**, in de tabel, de optie **Aangepast zoeken in logboeken**. Voer de volgende query in het tekstvak Zoekquery in:

```loganalytics
ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
```

Met deze query worden de computers geretourneerd waarbij de W3SVC-service is gestopt tijdens de opgegeven periode.

Voer onder **Waarschuwingslogica** voor **Drempelwaarde** het volgende in: **0**. Wanneer u klaar bent, selecteert u **Gereed**.

![Signaallogica configureren](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

Selecteer onder **Actiegroepen** de optie **Nieuwe maken**. Een actiegroep is een groep acties die u op meerdere waarschuwingen kunt toepassen. Deze acties kunnen bestaan uit, maar zijn niet beperkt tot, e-mailmeldingen, runbooks, webhooks en nog veel meer. Raadpleeg [Actiegroepen maken en beheren](../azure-monitor/platform/action-groups.md) voor meer informatie over actiegroepen.

Voer onder **Waarschuwingsdetails definiëren** een naam en beschrijving voor de waarschuwing in. Stel **Ernst** in op **Informatie (Sev 2)** , **Waarschuwing (Sev 1)** of **Kritiek (Sev 0)** .

Voer in het vak **Naam van actiegroep** een naam in voor de waarschuwing en een korte naam. De korte naam wordt gebruikt in plaats van een volledige naam voor de actiegroep als er meldingen worden verzonden door deze groep te gebruiken.

Voer onder **Acties** een naam in voor de actie, bijvoorbeeld **Beheerders een e-mail verzenden**. Selecteer onder **ACTIETYPE** de optie **E-mail/sms/push/spraak**. Selecteer onder **DETAILS** de optie **Details bewerken**.

![Actiegroep toevoegen](./media/automation-tutorial-troubleshoot-changes/add-action-group.png)

Voer in het deelvenster **E-mail/sms/push/spraak** een naam in. Selecteer het selectievakje **E-mail** en voer vervolgens een geldig e-mailadres in. Klik op **OK** op de pagina **E-mail/sms/push/spraak** en klik dan op **OK** op de pagina **Actiegroep toevoegen**.

Selecteer onder **Regel maken** bij **Acties aanpassen** de optie **E-mailonderwerp** als u het onderwerp van het waarschuwingsbericht wilt aanpassen. Wanneer u klaar bent, selecteert u **Waarschuwingsregel maken**. De waarschuwing laat u weten wanneer een update-implementatie is voltooid en op welke machines deze update-implementatie is uitgevoerd.

In de volgende afbeelding ziet u een e-mail die u zou kunnen ontvangen wanneer de W3SVC-service stopt.

![e-mail](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een VM onboarden voor Wijzigingen bijhouden en Inventaris
> * Zoeken in wijzigingenlogboeken voor gestopte services
> * Wijzigingen bijhouden configureren
> * Verbinding met activiteitenlogboek inschakelen
> * Een gebeurtenis activeren
> * Wijzigingen weergeven
> * Waarschuwingen configureren

Ga verder naar het overzicht van de oplossing Wijzigingen bijhouden en Inventaris als u er meer over wilt weten.

> [!div class="nextstepaction"]
> [De oplossing Wijzigingen bijhouden en Inventaris](automation-change-tracking.md)

