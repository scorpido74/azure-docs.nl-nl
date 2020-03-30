---
title: Verzamel aangepaste statistieken voor Linux VM met de InfluxData Telegraf-agent
description: Instructies voor het implementeren van de InfluxData Telegraf-agent op een Linux-vm in Azure en configureren van de agent om statistieken te publiceren naar Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 0ed9144116c1d716124025ef0aae39e7783c5934
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655460"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Verzamel aangepaste statistieken voor een Linux-VM met de InfluxData Telegraf-agent

Met Azure Monitor u aangepaste statistieken verzamelen via uw toepassingstelemetrie, een agent die wordt uitgevoerd op uw Azure-resources of zelfs externe bewakingssystemen. Vervolgens u ze rechtstreeks indienen bij Azure Monitor. In dit artikel vindt u instructies over het implementeren van de [InfluxData](https://www.influxdata.com/) Telegraf-agent op een Linux-vm in Azure en configureert u de agent om statistieken te publiceren naar Azure Monitor. 

## <a name="influxdata-telegraf-agent"></a>InfluxData Telegraf agent 

[Telegraf](https://docs.influxdata.com/telegraf/) is een plug-in-gedreven agent die het verzamelen van statistieken uit meer dan 150 verschillende bronnen mogelijk maakt. Afhankelijk van welke workloads op uw VM worden uitgevoerd, u de agent configureren om gebruik te maken van gespecialiseerde invoerplug-ins om statistieken te verzamelen. Voorbeelden zijn MySQL, NGINX en Apache. Door uitvoerplug-ins te gebruiken, kan de agent vervolgens schrijven naar bestemmingen die u kiest. De Telegraf-agent is rechtstreeks geïntegreerd met de Azure Monitor custom metrics REST API. Het ondersteunt een Azure Monitor-uitvoerplug-in. Door deze plug-in te gebruiken, kan de agent workload-specifieke statistieken verzamelen op uw Linux-vm en deze als aangepaste statistieken indienen bij Azure Monitor. 

 ![Overzicht van telegraafagenten](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Aangepaste statistieken verzenden 

Voor deze zelfstudie implementeren we een Linux VM die het Ubuntu 16.04 LTS-besturingssysteem draait. De Telegraf-agent wordt ondersteund voor de meeste Linux-besturingssystemen. Zowel Debian en RPM pakketten zijn beschikbaar samen met onverpakte Linux binaries op de [InfluxData download portal](https://portal.influxdata.com/downloads). Zie deze [Telegraf installatiegids](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) voor extra installatie-instructies en opties. 

Meld u aan bij [Azure Portal](https://portal.azure.com).

Maak een nieuwe Linux VM: 

1. Selecteer de optie **Een resource maken** in het navigatiedeelvenster aan de linkerkant. 
1. Zoeken naar **virtuele machine**.  
1. Selecteer **Ubuntu 16.04 LTS** en selecteer **Maken**. 
1. Geef een VM-naam op zoals **MyTelegrafVM**.  
1. Laat het schijftype als **SSD .** Geef vervolgens een **gebruikersnaam**op, zoals **azureuser.** 
1. Selecteer **Wachtwoord**voor **verificatietype**. Voer vervolgens een wachtwoord in dat u later gebruikt om SSH in deze VM te gebruiken. 
1. Kies om **nieuwe resourcegroep te maken**. Geef vervolgens een naam op, zoals **myResourceGroup**. Kies uw **locatie.** Selecteer vervolgens **OK**. 

    ![Maken van een Ubuntu-VM](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. Selecteer een grootte voor de VM. U kunt bijvoorbeeld filteren op **Rekentype** of **Schijftype**. 

    ![Virtuele machine grootte Telegraph agent overzicht](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. Selecteer **HTTP** en **SSH (22)** op de pagina **Instellingen** in**netwerkbeveiligingsgroep** >  **Network** > **Selecteer openbare binnenkomende poorten.** Laat de overige standaardwaarden staan en selecteer **OK**. 

1. Selecteer **Maken** op de overzichtspagina om de implementatie van de VM te starten. 

1. De VM wordt aan het dashboard van de Azure Portal vastgemaakt. Nadat de implementatie is voltooid, wordt het VM-overzicht automatisch geopend. 

1. Navigeer in het vm-deelvenster naar het tabblad **Identiteit.** Controleer of uw vm een door het systeem toegewezen identiteit heeft ingesteld op **Aan**. 
 
    ![Telegraf VM-identiteitsvoorbeeld](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine 

Maak een SSH-verbinding met de VM. Selecteer de knop **Verbinden** op de overzichtspagina van uw VM. 

![Overzichtspagina telegraf VM](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

Laat op de pagina **Verbinding maken met virtuele machine** de standaardopties staan om verbinding te maken met de DNS-naam via poort 22. In **Login met het lokale VM-account**wordt een verbindingsopdracht weergegeven. Selecteer de knop om de opdracht te kopiëren. Het volgende voorbeeld laat zien hoe de SSH-verbindingsopdracht eruitziet: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Plak de opdracht SSH-verbinding in een shell, zoals Azure Cloud Shell of Bash op Ubuntu op Windows, of gebruik een SSH-client van uw keuze om de verbinding te maken. 

## <a name="install-and-configure-telegraf"></a>Telegraf installeren en configureren 

Als u het Telegraf Debian-pakket op de VM wilt installeren, voert u de volgende opdrachten uit van uw SSH-sessie: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Het configuratiebestand van Telegraf definieert de activiteiten van Telegraf. Standaard wordt een voorbeeldconfiguratiebestand geïnstalleerd op het pad **/etc/telegraf/telegraf.conf.** Het voorbeeldconfiguratiebestand bevat alle mogelijke invoer- en uitvoerplug-ins. We maken echter een aangepast configuratiebestand en laten de agent het gebruiken door de volgende opdrachten uit te voeren: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> De voorgaande code maakt slechts twee invoerplug-ins mogelijk: **cpu** en **mem**. U meer invoerplug-ins toevoegen, afhankelijk van de werkbelasting die op uw machine wordt uitgevoerd. Voorbeelden zijn Docker, MySQL en NGINX. Zie de sectie **Extra configuratie** voor een volledige lijst met invoerplug-ins. 

Ten slotte, om de agent te laten beginnen met het gebruik van de nieuwe configuratie, dwingen we de agent te stoppen en te beginnen met het uitvoeren van de volgende opdrachten: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Nu verzamelt de agent statistieken van elk van de opgegeven invoerplug-ins en zendt deze uit naar Azure Monitor. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Uw Telegraf-statistieken uitzetten in de Azure-portal 

1. Open de [Azure Portal](https://portal.azure.com). 

1. Navigeer naar het nieuwe **tabblad Monitor.** Selecteer vervolgens **Statistieken**.  

     ![Monitor - Statistieken (voorbeeld)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. Selecteer uw VM in de bronkiezer.

     ![Metrische grafiek](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. Selecteer de **naamruimte Telegraf/CPU** en selecteer de **usage_system** statistiek. U ervoor kiezen om te filteren op de afmetingen op deze statistiek of splitsen op hen.  

     ![Naamruimte en statistiek selecteren](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Aanvullende configuratie 

De voorafgaande walkthrough geeft informatie over het configureren van de Telegraf-agent om statistieken te verzamelen van een paar basisinvoerplug-ins. De Telegraf-agent heeft ondersteuning voor meer dan 150 invoerplug-ins, met een aantal ondersteunende extra configuratieopties. InfluxData heeft een lijst met [ondersteunde plug-ins](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) en instructies gepubliceerd over [het configureren ervan.](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/)  

Bovendien hebt u in deze walkthrough de Telegraf-agent gebruikt om statistieken uit te zenden over de VM waarop de agent is geïmplementeerd. De Telegraf-agent kan ook worden gebruikt als verzamelaar en expediteur van statistieken voor andere bronnen. Zie Azure Monitor Custom Metric Output voor Telegraf voor meer informatie over het configureren van de agent om statistieken voor andere [Azure-bronnen](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md)uit te zenden.  

## <a name="clean-up-resources"></a>Resources opschonen 

Als ze niet meer nodig zijn, u de brongroep, virtuele machine en alle gerelateerde resources verwijderen. Selecteer hiervoor de brongroep voor de virtuele machine en selecteer **Verwijderen**. Bevestig vervolgens de naam van de resourcegroep die u wilt verwijderen. 

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [aangepaste statistieken](metrics-custom-overview.md).



