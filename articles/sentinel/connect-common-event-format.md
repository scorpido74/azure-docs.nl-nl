---
title: CEF-gegevens verbinden met Azure Sentinel preview | Microsoft Docs
description: Meer informatie over het verbinden van CEF-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: e74dd54403ed599aa95e8fc8a94c2bd7a3ca41d8
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719111"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Verbind uw externe oplossing met de algemene gebeurtenis indeling

U kunt Azure Sentinel verbinden met een externe oplossing waarmee u logboek bestanden kunt opslaan in syslog. Als uw apparaat u in staat stelt om logboeken op te slaan als de algemene gebeurtenis indeling syslog (CEF), kunt u met de integratie met Azure Sentinel eenvoudig analyses en query's uitvoeren op de gegevens.

> [!NOTE] 
> Gegevens worden opgeslagen op de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="how-it-works"></a>Hoe werkt het?

De verbinding tussen Azure Sentinel en uw CEF-apparaat vindt plaats in drie stappen:

1. Op het apparaat moet u deze waarden instellen zodat het apparaat de benodigde logboeken naar de vereiste indeling verzendt naar de Azure Sentinel syslog-agent, op basis van de micro soft monitoring agent. U kunt deze para meters in uw apparaat wijzigen, op voor waarde dat u ze ook wijzigt in de syslog-daemon op de onderverklikker agent van Azure.
    - Protocol = UDP
    - Poort = 514
    - Faciliteit = Local4
    - Indeling = CEF
2. De syslog-agent verzamelt de gegevens en verzendt deze veilig naar Log Analytics, waar ze worden geparseerd en verrijkt.
3. De agent slaat de gegevens op in een Log Analytics-werk ruimte, zodat deze kan worden opgevraagd als nodig, met behulp van analyses, correlatie regels en dash boards.

> [!NOTE]
> De agent kan Logboeken van meerdere bronnen verzamelen, maar moet op de toegewezen computer worden geïnstalleerd.


 ![CEF in azure](./media/connect-cef/cef-syslog-azure.png)

U kunt de agent ook hand matig implementeren op een bestaande Azure-VM, op een virtuele machine in een andere Cloud of op een on-premises machine. 

 ![CEF on-premises](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>Beveiligingsoverwegingen

Zorg ervoor dat u de beveiliging van de computer configureert op basis van het beveiligings beleid van uw organisatie. U kunt bijvoorbeeld uw netwerk zodanig configureren dat het wordt uitgelijnd met het beveiligings beleid van uw bedrijfs netwerk en de poorten en protocollen in de daemon wijzigen om af te stemmen op uw vereisten. U kunt de volgende instructies gebruiken om de beveiligings configuratie van uw computer te verbeteren:  [Beveilig de virtuele machine in azure](../virtual-machines/linux/security-policy.md), [Aanbevolen procedures voor netwerk beveiliging](../security/fundamentals/network-best-practices.md).

Als u TLS-communicatie tussen de beveiligings oplossing en de syslog-computer wilt gebruiken, moet u de syslog-daemon (rsyslog of syslog-ng) configureren om te communiceren in TLS: [Syslog-verkeer versleutelen met TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [logboek berichten versleutelen met TLS-syslog-aardgas](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).


## <a name="step-1-configure-your-syslog-vm"></a>Stap 1: Uw syslog VM configureren

U moet een agent implementeren op een toegewezen Linux-machine (VM of on-premises) ter ondersteuning van de communicatie tussen het apparaat en de Azure-Sentinel. 

> [!NOTE]
> Zorg ervoor dat u de beveiliging van de computer configureert op basis van het beveiligings beleid van uw organisatie. U kunt bijvoorbeeld uw netwerk zodanig configureren dat het wordt uitgelijnd met het beveiligings beleid van uw bedrijfs netwerk en de poorten en protocollen in de daemon wijzigen om af te stemmen op uw vereisten. 


1. Klik in de Azure-Sentinel-Portal op **gegevens connectors** en selecteer **algemene gebeurtenis indeling (CEF)** en open vervolgens de **pagina connector**. 

1. Onder **de syslog-agent downloaden en installeren**, selecteert u het type computer, ofwel Azure ofwel on-premises. 
1. Selecteer in het scherm **virtuele machines** dat wordt geopend de computer die u wilt gebruiken en klik op **verbinden**.
1. Als u **agent voor virtuele Azure Linux-machines downloaden en installeren**kiest, selecteert u de computer en klikt u op **verbinding maken**. Als u ervoor kiest **om de agent te downloaden en te installeren voor niet-Azure Linux virtuele machines**, voert u in het scherm **direct agent** het script uit onder **down load en onboarding-agent voor Linux**.
1. Stel in het scherm CEF-connector onder **syslog configureren en door sturen**in of uw syslog-daemon **rsyslog. d** of **syslog-ng**is. 
1. Kopieer deze opdrachten en voer deze uit op uw apparaat:
    - Als u rsyslog hebt geselecteerd. d:
              
       1. Vertel de syslog-daemon om te Luis teren naar de faciliteit local_4 en om de syslog-berichten te verzenden naar de Azure Sentinel-agent met behulp van poort 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
       2. Down load en installeer het [security_events-configuratie bestand](https://aka.ms/asi-syslog-config-file-linux) dat de syslog-agent configureert om te Luis teren op poort 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Waar {0} moet worden vervangen door de GUID van uw werk ruimte.
            
       1. De syslog-daemon opnieuw starten`sudo service rsyslog restart`<br> Zie de [rsyslog-documentatie](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html) voor meer informatie.
           
    - Als u syslog-aardgas hebt geselecteerd:
       1. Vertel de syslog-daemon om te Luis teren naar de faciliteit local_4 en om de syslog-berichten te verzenden naar de Azure Sentinel-agent met behulp van poort 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
       2. Down load en installeer het [security_events-configuratie bestand](https://aka.ms/asi-syslog-config-file-linux) dat de syslog-agent configureert om te Luis teren op poort 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Waar {0} moet worden vervangen door de GUID van uw werk ruimte.

        3. De syslog-daemon opnieuw starten`sudo service syslog-ng restart` <br>Zie de [syslog-ng-documentatie](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/mutual-authentication-using-tls/2) voor meer informatie.
1. Start de syslog-agent opnieuw met behulp van deze opdracht:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. Controleer of er geen fouten zijn in het Agent logboek door de volgende opdracht uit te voeren:`tail /var/opt/microsoft/omsagent/log/omsagent.log`

Zoek naar om het relevante schema in Log Analytics te gebruiken voor de CEF- `CommonSecurityLog`gebeurtenissen.

## <a name="step-2-forward-common-event-format-cef-logs-to-syslog-agent"></a>Stap 2: CEF-Logboeken (common Event Format) door sturen naar syslog-agent

Stel uw beveiligings oplossing in voor het verzenden van syslog-berichten in de CEF-indeling naar uw syslog-agent. Zorg ervoor dat u dezelfde para meters gebruikt die worden weer gegeven in de configuratie van de agent. Dit zijn meestal:

- Poort 514
- Faciliteit local4

## <a name="step-3-validate-connectivity"></a>Stap 3: Connectiviteit valideren

Het kan 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 

1. Zorg ervoor dat u de juiste functie gebruikt. De faciliteit moet hetzelfde zijn in uw apparaat en in azure Sentinel. U kunt controleren welk faciliteit bestand u gebruikt in azure Sentinel en dit wijzigen in het bestand `security-config-omsagent.conf`. 

2. Zorg ervoor dat uw logboeken de juiste poort in de syslog-agent krijgen. Voer deze opdracht uit op de computer van de syslog-agent: `tcpdump -A -ni any  port 514 -vv`Met deze opdracht wordt de logboeken weer gegeven die van het apparaat naar de syslog-machine worden gestreamd. Zorg ervoor dat Logboeken worden ontvangen van het bron apparaat op de juiste poort en de juiste faciliteit.

3. Zorg ervoor dat de logboeken die u verzendt, voldoen aan [RFC 3164](https://tools.ietf.org/html/rfc3164).

4. Op de computer waarop de syslog-agent wordt uitgevoerd, moet u ervoor zorgen dat deze poorten 514, 25226 zijn geopend `netstat -a -n:`en Luis teren met behulp van de opdracht. Meer informatie over het gebruik van deze opdracht vindt u op de [pagina netstat (8)-Linux-man](https://linux.die.net/man/8/netstat). Als de service goed luistert, ziet u dit:

   ![Azure-Sentinel-poorten](./media/connect-cef/ports.png) 

5. Zorg ervoor dat de daemon is ingesteld om te Luis teren op poort 514, waarop u de logboeken verzendt.
    - Voor rsyslog:<br>Zorg ervoor dat het bestand `/etc/rsyslog.conf` de volgende configuratie bevat:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Zie [voor meer informatie imudp: Invoer module](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) voor UDP syslog [en imtcp: Invoer module voor TCP syslog](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)

   - Voor syslog-aardgas:<br>Zorg ervoor dat het bestand `/etc/syslog-ng/syslog-ng.conf` de volgende configuratie bevat:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Zie [syslog-ng Open Source Edition 3,16-Administration Guide (Engelstalig](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455)) voor meer informatie.

1. Controleer of er communicatie is tussen de syslog-daemon en de agent. Voer deze opdracht uit op de computer van de syslog-agent: `tcpdump -A -ni any  port 25226 -vv`Met deze opdracht wordt de logboeken weer gegeven die van het apparaat naar de syslog-machine worden gestreamd. Zorg ervoor dat de logboeken ook worden ontvangen op de agent.

6. Als bij beide opdrachten geslaagde resultaten worden weer gegeven, controleert u Log Analytics om te zien of uw logboeken arriveren. Alle gebeurtenissen die vanaf deze apparaten worden gestreamd, worden in onbewerkte vorm in log Analytics onder `CommonSecurityLog` type weer gegeven.

7. Als u wilt controleren of er fouten zijn of als de logboeken niet arriveren, kijkt u in `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Als wordt aangegeven dat er fouten in de logboek indeling niet overeenkomen, gaat u `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` naar het bestand `security_events.conf`en kijkt u of uw logboeken overeenkomen met de regex-indeling die u ziet in dit bestand.

8. Zorg ervoor dat de standaard grootte van het syslog-bericht is beperkt tot 2048 bytes (2 KB). Als de logboeken te lang zijn, werkt u het security_events. conf bij met behulp van deze opdracht: `message_length_limit 4096`


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u CEF-apparaten verbindt met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).

