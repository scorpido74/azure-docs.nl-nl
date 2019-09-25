---
title: Fortinet-gegevens koppelen aan Azure-Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Fortinet-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 20079fd0c95da3e3aec9518f194ea39561a5e662
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240703"
---
# <a name="connect-your-fortinet-appliance"></a>Verbinding maken met uw Fortinet-apparaat



U kunt Azure Sentinel verbinden met een Fortinet-apparaat door de logboek bestanden op te slaan als algemene gebeurtenis notatie syslog (CEF). Met de integratie met Azure Sentinel kunt u eenvoudig analyses en query's uitvoeren op de gegevens van het logboek bestand vanuit Fortinet. Zie [verbinding maken met CEF-apparaten](connect-common-event-format.md)voor meer informatie over de manier waarop Azure Sentinel CEF-gegevens opneemt.

> [!NOTE]
> Gegevens worden opgeslagen op de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="step-1-connect-your-fortinet-appliance-by-using-an-agent"></a>Stap 1: Verbinding maken met uw Fortinet-apparaat met behulp van een agent

Als u uw Fortinet-apparaat wilt verbinden met Azure Sentinel, implementeert u een agent op een specifieke virtuele machine of op een on-premises computer ter ondersteuning van de communicatie tussen het apparaat en de Azure-Sentinel. 

U kunt de agent ook hand matig implementeren op een bestaande Azure-VM, op een virtuele machine in een andere Cloud of op een on-premises machine.

> [!NOTE]
> Zorg ervoor dat u de beveiliging van de computer configureert op basis van het beveiligings beleid van uw organisatie. U kunt bijvoorbeeld uw netwerk zodanig configureren dat het wordt uitgelijnd met het beveiligings beleid van uw bedrijfs netwerk en de poorten en protocollen in de daemon wijzigen om af te stemmen op uw vereisten. 

Zie [verbinding maken met gegevens bronnen](connect-data-sources.md#agent-options)om een netwerk diagram van beide opties weer te geven.

### <a name="deploy-the-agent"></a>De agent implementeren

1. Klik in de Azure-Sentinel-Portal op **gegevens connectors** en selecteer vervolgens **Fortinet** en open vervolgens de **pagina connector**. 

1. Onder **de syslog-agent downloaden en installeren**, selecteert u het type computer, ofwel Azure ofwel on-premises. 
1. Selecteer in het scherm **virtuele machines** dat wordt geopend de computer die u wilt gebruiken en klik op **verbinden**.
1. Als u **agent voor virtuele Azure Linux-machines downloaden en installeren**kiest, selecteert u de computer en klikt u op **verbinding maken**. Als u ervoor kiest **om de agent te downloaden en te installeren voor niet-Azure Linux virtuele machines**, voert u in het scherm **direct agent** het script uit onder **down load en onboarding-agent voor Linux**.
1. Stel op het scherm connector onder **syslog configureren en door sturen**in of uw syslog-daemon **rsyslog. d** of **syslog-ng**is. 
1. Kopieer deze opdrachten en voer deze uit op uw apparaat:
   - Als u rsyslog hebt geselecteerd. d:
            
     1. Vertel de syslog-daemon om te Luis teren naar de faciliteit local_4 en om de syslog-berichten te verzenden naar de Azure Sentinel-agent met behulp van poort 25226. Gebruik deze opdracht:`sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
     1. Down load en installeer het [security_events-configuratie bestand](https://aka.ms/asi-syslog-config-file-linux) dat de syslog-agent configureert om te Luis teren op poort 25226. Gebruik deze opdracht: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` waar {0} moet worden vervangen door de GUID van uw werk ruimte.
     1. Start de syslog-daemon opnieuw met behulp van deze opdracht:`sudo service rsyslog restart`
            
   - Als u syslog-aardgas hebt geselecteerd:

      1. Vertel de syslog-daemon om te Luis teren naar de faciliteit local_4 en om de syslog-berichten te verzenden naar de Azure Sentinel-agent met behulp van poort 25226. Gebruik deze opdracht:`sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      1. Down load en installeer het [security_events-configuratie bestand](https://aka.ms/asi-syslog-config-file-linux) dat de syslog-agent configureert om te Luis teren op poort 25226. Gebruik deze opdracht: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` waar {0} moet worden vervangen door de GUID van uw werk ruimte.
      1. Start de syslog-daemon opnieuw met behulp van deze opdracht:`sudo service syslog-ng restart`
1. Start de syslog-agent opnieuw met behulp van deze opdracht:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. Controleer of er geen fouten zijn in het Agent logboek door de volgende opdracht uit te voeren:`tail /var/opt/microsoft/omsagent/log/omsagent.log`

 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>Stap 2: De Fortinet-logboeken door sturen naar de syslog-agent

Configureer Fortinet voor het door sturen van syslog-berichten in de CEF-indeling naar uw Azure-werk ruimte via de syslog-agent.

1. Open de CLI op uw Fortinet-apparaat en voer de volgende opdrachten uit:

        config log syslogd setting
        set format cef
        set facility <facility_name>
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Vervang het IP **-adres** van de server door het IP-adres van de agent.
    - Stel **facility_name** in om de faciliteit te gebruiken die u in de agent hebt geconfigureerd. De agent stelt dit standaard in op local4.
    - Stel de **syslog-poort** in op **514** of de poort die is ingesteld op de agent.
    - Als u de CEF-indeling in vroege FortiOS-versies wilt inschakelen, moet u mogelijk de opdracht set **CSV Disable**uitvoeren.
 
   > [!NOTE] 
   > Ga naar de [document bibliotheek van Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)voor meer informatie. Selecteer uw versie en gebruik de naslag **handleiding** en het **logboek bericht**.

 Als u het relevante schema in Azure Monitor Log Analytics voor de Fortinet-gebeurtenissen wilt gebruiken `CommonSecurityLog`, zoekt u naar.


## <a name="step-3-validate-connectivity"></a>Stap 3: Connectiviteit valideren

Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 

1. Zorg ervoor dat u de juiste functie gebruikt. De faciliteit moet hetzelfde zijn in uw apparaat en in azure Sentinel. U kunt controleren welk faciliteit bestand u gebruikt in azure Sentinel en dit wijzigen in het bestand `security-config-omsagent.conf`. 

2. Zorg ervoor dat uw logboeken de juiste poort in de syslog-agent krijgen. Voer deze opdracht uit op de computer van de `tcpdump -A -ni any  port 514 -vv`syslog-agent:. Met deze opdracht wordt de logboeken die streamen van het apparaat naar de syslog-computer. Zorg ervoor dat Logboeken worden ontvangen van het bron apparaat op de juiste poort en de juiste faciliteit.

3. Zorg ervoor dat de logboeken die u verzendt, voldoen aan [RFC 3164](https://tools.ietf.org/html/rfc3164).

4. Zorg ervoor dat op de computer waarop de syslog-agent wordt uitgevoerd, de poorten 514 en 25226 zijn geopend en Luis `netstat -a -n:`teren met behulp van de opdracht. Meer informatie over het gebruik van deze opdracht vindt u op de [pagina netstat (8)-Linux-man](https://linux.die.net/man/8/netstat). Als het op de juiste manier luistert, ziet u het volgende:

   ![Azure-Sentinel-poorten](./media/connect-cef/ports.png) 

5. Zorg ervoor dat de daemon is ingesteld om te Luis teren op poort 514, waarop u de logboeken verzendt.
    - Voor rsyslog:<br>Zorg ervoor dat het bestand `/etc/rsyslog.conf` de volgende configuratie bevat:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Zie [voor meer informatie imudp: Invoer module](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) voor UDP syslog [en imtcp: Invoer module](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)voor TCP syslog.

   - Voor syslog-aardgas:<br>Zorg ervoor dat het bestand `/etc/syslog-ng/syslog-ng.conf` de volgende configuratie bevat:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Zie [voor meer informatie imudp: Invoer module](https://rsyslog.readthedocs.io/en/latest/configuration/modules/imudp.html) voor UDP syslog en [syslog-ng Open Source Edition 3,16-beheer handleiding](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Controleer of er communicatie is tussen de syslog-daemon en de agent. Voer deze opdracht uit op de computer van de `tcpdump -A -ni any  port 25226 -vv`syslog-agent:. Met deze opdracht wordt de logboeken die streamen van het apparaat naar de syslog-computer. Zorg ervoor dat de logboeken ook worden ontvangen op de agent.

6. Als bij beide opdrachten geslaagde resultaten worden weer gegeven, controleert u Log Analytics om te zien of uw logboeken arriveren. Alle gebeurtenissen die vanaf deze apparaten worden gestreamd, worden in onbewerkte vorm in log Analytics onder `CommonSecurityLog` type weer gegeven.

7. Als u wilt controleren of er fouten zijn of als de logboeken niet arriveren, kijkt u in `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Als wordt aangegeven dat er fouten in de logboek indeling niet overeenkomen, gaat u naar `security_events.conf`het bestand en bekijkt u `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` het. Zorg ervoor dat uw logboeken overeenkomen met de regex-indeling die u ziet in dit bestand.

8. Zorg ervoor dat de standaard grootte van het syslog-bericht is beperkt tot 2048 bytes (2 KB). Als de logboeken te lang zijn, moet u security_events. conf bijwerken met behulp van de volgende opdracht:`message_length_limit 4096`

10. Als uw Fortinet-logboeken niet door de agent worden ontvangen, voert u deze opdracht uit, afhankelijk van het type syslog-daemon dat u gebruikt, om de faciliteit in te stellen en de logboeken in te stellen op zoeken naar het woord Fortinet in de logboeken:
       - rsyslog. d:`sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`

     Start de syslog-daemon opnieuw met behulp van deze opdracht:`sudo service rsyslog restart`
       - syslog-aardgas:`sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      
     Start de syslog-daemon opnieuw met behulp van deze opdracht:`sudo service syslog-ng restart`

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u Fortinet-apparaten verbindt met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).

