---
title: Syslog-gegevens verbinden met Azure-Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van syslog-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 73fd55fc24fd94dc88bba2f591c32480f77c7d5d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588073"
---
# <a name="connect-your-external-solution-using-syslog"></a>Verbinding maken met uw externe oplossing met behulp van syslog

U kunt elk on-premises apparaat dat syslog ondersteunt, verbinden met Azure Sentinel. Dit wordt gedaan met behulp van een agent die is gebaseerd op een Linux-machine tussen het apparaat en de Azure-Sentinel. Als uw Linux-machine zich in azure bevindt, kunt u de logboeken van uw apparaat of toepassing streamen naar een toegewezen werk ruimte die u in azure maakt en er verbinding mee maken. Als uw Linux-machine zich niet in azure bevindt, kunt u de logboeken van uw apparaat streamen naar een specifieke on-premises VM of computer waarop u de agent voor Linux installeert. 

> [!NOTE]
> Als uw apparaat syslog CEF ondersteunt, is de verbinding meer voltooid en moet u deze optie kiezen en de instructies volgen bij het [verbinden van gegevens van CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Hoe werkt het?

Syslog is een protocol voor gebeurtenis registratie dat algemeen is voor Linux. Toepassingen zullen berichten verzenden die kunnen worden opgeslagen op de lokale computer of worden geleverd aan een syslog-Collector. Wanneer de Log Analytics-agent voor Linux is geïnstalleerd, wordt de lokale syslog-daemon geconfigureerd voor het door sturen van berichten naar de agent. De agent verzendt het bericht vervolgens naar Azure Monitor waar een corresponderende record wordt gemaakt.

Zie [syslog-gegevens bronnen in azure monitor](../azure-monitor/platform/data-sources-syslog.md)voor meer informatie.

> [!NOTE]
> - De agent kan Logboeken van meerdere bronnen verzamelen, maar moet op een specifieke proxy computer worden geïnstalleerd.
> - Als u connectors wilt ondersteunen voor zowel CEF als syslog op dezelfde VM, voert u de volgende stappen uit om te voor komen dat gegevens worden gedupliceerd:
>    1. Volg de instructies om [verbinding te maken met uw CEF](connect-common-event-format.md).
>    2. Als u de syslog-gegevens wilt verbinden, gaat u naar **instellingen** > **werk ruimte** > instellingen**Geavanceerde instellingen** > **gegevens** > **syslog** en stelt u de voorzieningen en de bijbehorende prioriteiten in, zodat ze niet dezelfde faciliteiten en eigenschappen hebben als die u in uw CEF-configuratie hebt gebruikt. <br></br>Als u **de onderstaande configuratie Toep assen op mijn machines**selecteert, worden deze instellingen toegepast op alle vm's die zijn verbonden met deze werk ruimte.


## <a name="connect-your-syslog-appliance"></a>Uw syslog-apparaat aansluiten

1. Selecteer in azure Sentinel **Data connectors** en selecteer vervolgens de **syslog** -connector.

2. Selecteer op de Blade **syslog** de optie **connector pagina openen**.

3. De Linux-agent installeren:
    
    - Als uw virtuele Linux-machine zich in azure bevindt, selecteert u de **agent downloaden en installeren op de virtuele machine van Azure Linux**. Selecteer op de Blade **virtuele machines** de virtuele machines waarop u de agent wilt installeren en klik vervolgens op **verbinding maken**.
    - Als uw Linux-machine zich niet in azure bevindt, selecteert u **agent op Linux niet-Azure-machine downloaden en installeren**. Kopieer op de Blade **direct-agent** de opdracht voor het **downloaden en onboarden van de agent voor Linux** en voer deze uit op uw computer. 
    
   > [!NOTE]
   > Zorg ervoor dat u de beveiligings instellingen voor deze computers configureert op basis van het beveiligings beleid van uw organisatie. U kunt bijvoorbeeld de netwerk instellingen zodanig configureren dat deze worden uitgelijnd met het netwerk beveiligings beleid van uw organisatie en de poorten en protocollen in de daemon wijzigen, zodat deze overeenkomen met de beveiligings vereisten.

4. Selecteer **de configuratie geavanceerde instellingen voor de werk ruimte openen**.

5. Selecteer op de Blade **Geavanceerde instellingen** de optie **gegevens** > **syslog**. Voeg vervolgens de voorzieningen toe die door de connector moeten worden verzameld.
    
    Voeg de faciliteiten toe die uw syslog-apparaat in de logboek headers heeft opgenomen. U kunt deze configuratie bekijken in uw syslog-apparaat in **syslog-d** in `/etc/rsyslog.d/security-config-omsagent.conf` de map en in **r-syslog** van `/etc/syslog-ng/security-config-omsagent.conf`.
    
    Als u afwijkende SSH-aanmeldings detectie wilt gebruiken met de gegevens die u verzamelt, voegt u **auth** en **authpriv**toe. Raadpleeg de [volgende sectie](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) voor meer informatie.

6. Wanneer u alle faciliteiten hebt toegevoegd die u wilt bewaken en de ernst opties voor elke functie hebt aangepast, schakelt u het selectie vakje **op de onderstaande configuratie Toep assen op mijn computers in**.

7. Selecteer **Opslaan**. 

8. Controleer op uw syslog-apparaat of u de door u opgegeven faciliteiten wilt verzenden.

9. Als u het relevante schema in Azure Monitor voor de syslog-logboeken wilt gebruiken, zoekt u naar **syslog**.

10. U kunt de functie Kusto die wordt beschreven in [functies gebruiken in azure monitor logboek query's](../azure-monitor/log-query/functions.md) gebruiken om uw syslog-berichten te parseren. U kunt deze vervolgens opslaan als een nieuwe Log Analytics functie om te gebruiken als een nieuw gegevens type.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>De syslog-connector configureren voor de detectie van afwijkende SSH-aanmeldingen

> [!IMPORTANT]
> De detectie van afwijkende SSH-aanmeldingen is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen.
> Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel kan machine learning (ML) Toep assen op de syslog-gegevens om de aanmeldings activiteit voor afwijkende Secure Shell (SSH) te identificeren. Scenario's zijn onder andere:

- Onmogelijk traject: wanneer twee geslaagde aanmeldings gebeurtenissen plaatsvinden vanaf twee locaties die niet kunnen bereiken binnen het tijds bestek van de twee aanmeldings gebeurtenissen.
- Onverwachte locatie: de locatie van waaruit een geslaagde aanmeldings gebeurtenis is opgetreden verdacht. De locatie is bijvoorbeeld niet recent gezien.
 
Voor deze detectie is een specifieke configuratie van de syslog-gegevens connector vereist: 

1. Zorg ervoor dat voor stap 5 in de vorige procedure zowel **auth** als **authpriv** zijn geselecteerd als te bewaken faciliteiten. Behoud de standaard instellingen voor de ernst opties, zodat deze allemaal zijn geselecteerd. Bijvoorbeeld:
    
    > [!div class="mx-imgBorder"]
    > ![Voorzieningen die zijn vereist voor afwijkende SSH-aanmeldings detectie](./media/connect-syslog/facilities-ssh-detection.png)

2. Zorg dat er voldoende tijd is om syslog-gegevens te verzamelen. Ga vervolgens naar **Azure Sentinel-logs**en kopieer en plak de volgende query:
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    Wijzig het **tijds bereik** indien nodig en selecteer **uitvoeren**.
    
    Als het resulterende aantal nul is, bevestigt u de configuratie van de connector en controleert u of de bewaakte computers geslaagde aanmeldings activiteiten hebben voor de periode die u voor uw query hebt opgegeven.
    
    Als het resulterende aantal groter is dan nul, zijn uw syslog-gegevens geschikt voor afwijkende SSH-aanmeldings detectie. U schakelt deze detectie in op basis van **analyse** >  **regel sjablonen** > **(preview) afwijkende SSH-aanmeldings detectie**.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u op on-premises syslog-apparaten verbindt met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.

