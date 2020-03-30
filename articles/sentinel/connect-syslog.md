---
title: Syslog-gegevens verbinden met Azure Sentinel | Microsoft Documenten
description: Meer informatie over het verbinden van Syslog-gegevens met Azure Sentinel.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588073"
---
# <a name="connect-your-external-solution-using-syslog"></a>Sluit uw externe oplossing aan met Syslog

U elk on-premises toestel dat Syslog ondersteunt, verbinden met Azure Sentinel. Dit wordt gedaan met behulp van een agent op basis van een Linux-machine tussen het toestel en Azure Sentinel. Als uw Linux-machine zich in Azure bevindt, u de logboeken van uw toestel of toepassing streamen naar een speciale werkruimte die u in Azure maakt en deze aansluit. Als uw Linux-machine zich niet in Azure bevindt, u de logboeken van uw toestel streamen naar een dedicated on-premises VM of machine waarop u de Agent voor Linux installeert. 

> [!NOTE]
> Als uw toestel Syslog CEF ondersteunt, is de verbinding completer en moet u deze optie kiezen en de instructies volgen in [Gegevens van CEF verbinden.](connect-common-event-format.md)

## <a name="how-it-works"></a>Hoe werkt het?

Syslog is een event logging protocol dat gemeenschappelijk is voor Linux. Toepassingen sturen berichten die op de lokale machine kunnen worden opgeslagen of aan een Syslog-verzamelaar kunnen worden geleverd. Wanneer de Log Analytics-agent voor Linux is geïnstalleerd, configureert deze de lokale Syslog-daemon om berichten naar de agent door te sturen. De agent stuurt het bericht vervolgens naar Azure Monitor waar een bijbehorende record wordt gemaakt.

Zie [Syslog-gegevensbronnen in Azure Monitor](../azure-monitor/platform/data-sources-syslog.md)voor meer informatie.

> [!NOTE]
> - De agent kan logboeken verzamelen van meerdere bronnen, maar moet worden geïnstalleerd op speciale proxy machine.
> - Als u connectors voor zowel CEF als Syslog op dezelfde VM wilt ondersteunen, voert u de volgende stappen uit om te voorkomen dat gegevens worden gedubbeld:
>    1. Volg de instructies om [uw CEF aan](connect-common-event-format.md)te sluiten.
>    2. Als u de Syslog-gegevens wilt verbinden, gaat u naar **Instellingen** > **werkruimte-instellingen** > **Geavanceerde instellingen** > **Data** > **Syslog** en stelt u de faciliteiten en hun prioriteiten zo in dat ze niet dezelfde faciliteiten en eigenschappen zijn die u in uw CEF-configuratie hebt gebruikt. <br></br>Als u **Onderstaande configuratie op mijn machines**toepast, worden deze ingesteldop alle VM's die op deze werkruimte zijn aangesloten.


## <a name="connect-your-syslog-appliance"></a>Sluit uw Syslog-toestel aan

1. Selecteer in Azure Sentinel **gegevensconnectors** en selecteer vervolgens de **Syslog-connector.**

2. Selecteer op het **Syslog-blad** de **optie Connectorpagina openen**.

3. Installeer de Linux-agent:
    
    - Als uw virtuele Linux-machine zich in Azure bevindt, selecteert u **Agent downloaden en installeren op de virtuele machine van Azure Linux.** Selecteer in het **blad Virtuele machines** de virtuele machines waarop de agent moet worden geïnstalleerd en klik op Verbinding **maken**.
    - Als uw Linux-machine zich niet in Azure bevindt, selecteert u **Agent downloaden en installeren op de linux-niet-Azure-machine.** Kopieer in het **direct agent-blad** de opdracht voor **DOWNLOAD EN ONBOARD AGENT FOR LINUX** en voer deze uit op uw computer. 
    
   > [!NOTE]
   > Zorg ervoor dat u beveiligingsinstellingen voor deze computers configureert volgens het beveiligingsbeleid van uw organisatie. U bijvoorbeeld de netwerkinstellingen configureren om af te stemmen op het netwerkbeveiligingsbeleid van uw organisatie en de poorten en protocollen in de daemon wijzigen om aan te sluiten bij de beveiligingsvereisten.

4. Selecteer **Geavanceerde instellingenconfiguratie voor uw werkruimte openen**.

5. Selecteer > **gegevenssyslog**in **Data**het geavanceerde **instellingenblad** . Voeg vervolgens de faciliteiten voor de connector te verzamelen.
    
    Voeg de faciliteiten die uw syslog toestel bevat in zijn log headers. U deze configuratie zien in uw Syslog-toestel in **Syslog-d** `/etc/syslog-ng/security-config-omsagent.conf`in de `/etc/rsyslog.d/security-config-omsagent.conf` map en in **r-Syslog** van.
    
    Als u afwijkende SSH-inlogdetectie wilt gebruiken met de gegevens die u verzamelt, voegt u **auth** en **authpriv**toe. Zie de [volgende sectie](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) voor meer informatie.

6. Wanneer u alle faciliteiten hebt toegevoegd die u wilt controleren en alle ernstopties voor elke optie hebt aangepast, schakelt u het selectievakje **Onderstaande configuratie toepassen op mijn machines in.**

7. Selecteer **Opslaan**. 

8. Zorg ervoor dat u de door u opgegeven faciliteiten op uw syslog-apparaat verzendt.

9. Als u het relevante schema in Azure Monitor wilt gebruiken voor de syslog-logboeken, zoekt u naar **Syslog**.

10. U de kusto-functie gebruiken die is beschreven in [Het gebruik van functies in Azure Monitor-logboekquery's](../azure-monitor/log-query/functions.md) om uw Syslog-berichten te ontzien. U ze vervolgens opslaan als een nieuwe Log Analytics-functie om te gebruiken als een nieuw gegevenstype.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>De Syslog-connector configureren voor afwijkende SSH-inlogdetectie

> [!IMPORTANT]
> Afwijkende SSH-inlogdetectie is momenteel in openbare preview.
> Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

Azure Sentinel kan machine learning (ML) toepassen op de sysloggegevens om de inlogactiviteit (anomalous Secure Shell) te identificeren. Scenario's zijn onder andere:

- Onmogelijke reizen – wanneer twee succesvolle inloggebeurtenissen plaatsvinden vanaf twee locaties die onmogelijk te bereiken zijn binnen het tijdsbestek van de twee inloggebeurtenissen.
- Onverwachte locatie : de locatie van waar een geslaagde aanmeldingsgebeurtenis heeft plaatsgevonden, is verdacht. De locatie is bijvoorbeeld niet recent gezien.
 
Deze detectie vereist een specifieke configuratie van de Syslog-gegevensconnector: 

1. Voor stap 5 in de vorige procedure, zorg ervoor dat zowel **auth** en **authpriv** worden geselecteerd als faciliteiten om te controleren. Houd de standaardinstellingen voor de ernstopties, zodat ze allemaal zijn geselecteerd. Bijvoorbeeld:
    
    > [!div class="mx-imgBorder"]
    > ![Faciliteiten die nodig zijn voor afwijkende SSH login detectie](./media/connect-syslog/facilities-ssh-detection.png)

2. Geef voldoende tijd om syslog-informatie te verzamelen. Navigeer vervolgens naar **Azure Sentinel - Logboeken**en kopieer en plak de volgende query:
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    Wijzig indien nodig het **tijdsbereik** en selecteer **Uitvoeren**.
    
    Als het resulterende aantal nul is, bevestigt u de configuratie van de connector en bevestigt u dat de bewaakte computers wel een succesvolle inlogactiviteit hebben voor de periode die u voor uw query hebt opgegeven.
    
    Als het resulterende aantal groter is dan nul, zijn uw sysloggegevens geschikt voor afwijkende SSH-inlogdetectie. U schakelt deze detectie in van **Analytics** >  **Rule-sjablonen** > **(Preview) Afwijkende SSH-aanmeldingsdetectie**.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Syslog on-premises apparaten verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te controleren.

