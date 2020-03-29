---
title: IBM DB2 pureScale implementeren op Azure
description: Meer informatie over het implementeren van een voorbeeldarchitectuur die onlangs is gebruikt om een onderneming te migreren van de IBM DB2-omgeving die op z/OS wordt uitgevoerd naar IBM DB2 pureScale op Azure.
author: njray
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 98e912894a4d93a057a2f6a2153d0690deaed250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968900"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale implementeren op Azure

In dit artikel wordt beschreven hoe u een [voorbeeldarchitectuur](ibm-db2-purescale-azure.md) implementeert die een zakelijke klant onlangs heeft gebruikt om te migreren van de IBM DB2-omgeving die op z/OS naar IBM DB2 pureScale op Azure wordt uitgevoerd.

Zie de installatiescripts in de [DB2onAzure-repository](https://aka.ms/db2onazure) op GitHub om de stappen te volgen die voor de migratie zijn gebruikt. Deze scripts zijn gebaseerd op de architectuur voor een typische, middelgrote online transactieverwerking (OLTP) workload.

## <a name="get-started"></a>Aan de slag

Als u deze architectuur wilt implementeren, downloadt en voert u het deploy.sh script uit dat is gevonden in de [DB2onAzure-opslagplaats](https://aka.ms/db2onazure) op GitHub.

De repository heeft ook scripts voor het opzetten van een Grafana dashboard. U het dashboard gebruiken om Prometheus te bevragen, het open-source monitoring- en waarschuwingssysteem dat bij DB2 is meegeleverd.

> [!NOTE]
> De deploy.sh-script op de client maakt privé SSH-sleutels en geeft deze door aan de implementatiesjabloon via HTTPS. Voor meer beveiliging raden we u aan [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) te gebruiken om geheimen, sleutels en wachtwoorden op te slaan.

## <a name="how-the-deployment-script-works"></a>Hoe het implementatiescript werkt

Het deploy.sh script maakt en configureert de Azure-resources voor deze architectuur. Het script vraagt u om het Azure-abonnement en virtuele machines die in de doelomgeving worden gebruikt en voert vervolgens de volgende bewerkingen uit:

-   Hiermee stelt u de brongroep, het virtuele netwerk en de subnetten op Azure in voor de installatie.

-   Hiermee stelt u de netwerkbeveiligingsgroepen en SSH in voor het milieu.

-   Hiermee worden meerdere NIC's ingesteld op zowel de gedeelde opslag als de DB2 pureScale virtuele machines.

-   Hiermee maakt u de virtuele machines voor gedeelde opslag. Zie [Direct-overzicht opslagruimten](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)als u Storage Spaces Direct of een andere opslagoplossing gebruikt.

-   Hiermee maakt u de virtuele machine van de jumpbox.

-   Hiermee maakt u de DB2 pureScale virtuele machines.

-   Hiermee maakt u de virtuele computer die DB2 pureScale pings. Sla dit deel van de implementatie over als uw versie van Db2 pureScale geen getuige vereist.

-   Hiermee maakt u een virtuele Windows-machine om te gebruiken voor het testen, maar er wordt niets op geïnstalleerd.

Vervolgens stellen de implementatiescripts een iSCSI-netwerk voor virtuele opslagruimte (vSAN) in voor gedeelde opslag op Azure. In dit voorbeeld maakt iSCSI verbinding met het gedeelde opslagcluster. In de oorspronkelijke klantoplossing werd GlusterFS gebruikt. IBM ondersteunt deze aanpak echter niet langer. Om uw ondersteuning van IBM te behouden, moet u een ondersteund iSCSI-compatibel bestandssysteem gebruiken. Microsoft biedt Storage Spaces Direct (S2D) als optie aan.

Deze oplossing geeft u ook de mogelijkheid om de iSCSI-doelen te installeren als één Windows-knooppunt. iSCSI biedt een gedeelde blokopslaginterface via TCP/IP waarmee de DB2 pureScale-installatieprocedure een apparaatinterface kan gebruiken om verbinding te maken met gedeelde opslag.

De implementatiescripts voeren de volgende algemene stappen uit:

1.  Stel een gedeeld opslagcluster in op Azure. Deze stap omvat ten minste twee Linux-knooppunten.

2.  Stel een iSCSI Direct-interface in op doel-Linux-servers voor het gedeelde opslagcluster.

3.  Stel de iSCSI-initiator in op de virtuele Linux-machines. De initiator heeft toegang tot het gedeelde opslagcluster met behulp van een iSCSI-doel. Zie [Een iSCSI-doel en initiator in Linux configureren](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) in de documentatie van RootUsers voor installatiegegevens.

4.  Installeer de gedeelde opslaglaag voor de iSCSI-interface.

Nadat de scripts het iSCSI-apparaat hebben gemaakt, is de laatste stap het installeren van DB2 pureScale. Als onderdeel van de DB2 pureScale setup wordt [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (voorheen GPFS) gecompileerd en geïnstalleerd op het GlusterFS cluster. Dit geclusterde bestandssysteem stelt DB2 pureScale in staat om gegevens te delen tussen de virtuele machines die de DB2 pureScale-engine draaien. Zie voor meer informatie de documentatie van ibm [Spectrum Scale](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) op de IBM-website.

## <a name="db2-purescale-response-file"></a>DB2 pureScale-antwoordbestand

De GitHub repository bevat DB2server.rsp, een response (.rsp) bestand waarmee u een geautomatiseerd script voor de DB2 pureScale installatie genereren. In de volgende tabel worden de DB2-pureScale-opties weergegeven die het antwoordbestand gebruikt voor het instellen. U het antwoordbestand aanpassen als dat nodig is voor uw omgeving.

> [!NOTE]
> Een voorbeeldreactiebestand, DB2server.rsp, is opgenomen in de [DB2onAzure-repository](https://aka.ms/db2onazure) op GitHub. Als u dit bestand gebruikt, moet u het bewerken voordat het in uw omgeving kan werken.

| Schermnaam               | Veld                                        | Waarde                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Welkom                   |                                              | Nieuwe installatie                                                                                           |
| Een product kiezen          |                                              | DB2 Versie 11.1.3.3. Server Editions met DB2 pureScale                                              |
| Configuratie             | Directory                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | Het installatietype selecteren                 | Typische                                                                                               |
|                           | Ik ga akkoord met de IBM-voorwaarden                     | Geselecteerd                                                                                               |
| Instantie-eigenaar            | Bestaande gebruiker bijvoorbeeld, gebruikersnaam        | DB2sdin1                                                                                              |
| Omheinde gebruiker               | Bestaande gebruiker, gebruikersnaam                     | DB2sdfe1                                                                                              |
| Clusterbestandssysteem       | Apparaatpad gedeelde schijfpartitie            | /dev/dm-2                                                                                             |
|                           | Bevestigingspunt                                  | /DB2sd\_1804a                                                                                         |
|                           | Gedeelde schijf voor gegevens                         | /dev/dm-1                                                                                             |
|                           | Bevestigingspunt (gegevens)                           | /DB2fs/datafs1                                                                                        |
|                           | Gedeelde schijf voor logboek                          | /dev/dm-0                                                                                             |
|                           | Bevestigingspunt (logboek)                            | /DB2fs/logfs1                                                                                         |
|                           | DB2 Cluster Services Tiebreaker. Apparaatpad | /dev/dm-3                                                                                             |
| Hostlijst                 | d1 [eth1], d2 [eth1], cf1 [eth1], cf2 [eth1] |                                                                                                       |
|                           | Primaire CF van voorkeur                         | cf1                                                                                                   |
|                           | Voorkeur secundaire CF                       | cf2                                                                                                   |
| Reactiebestand en samenvatting | eerste optie                                 | Db2 Server Edition installeren met de IBM DB2 pureScale-functie en mijn instellingen opslaan in een reactiebestand |
|                           | Bestandsnaam antwoord                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Opmerkingen over deze implementatie

- De waarden voor /dev-dm0, /dev-dm1, /dev-dm2 en /dev-dm3 kunnen veranderen na een herstart op de virtuele machine waar de installatie plaatsvindt (d0 in het geautomatiseerde script). Als u de juiste waarden wilt vinden, u de volgende opdracht uitvoeren voordat u het antwoordbestand op de server voltooit waar de installatie wordt uitgevoerd:

   ```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
   ```

- De setup scripts gebruiken aliassen voor de iSCSI-schijven, zodat de werkelijke namen gemakkelijk kunnen worden gevonden.

- Wanneer het installatiescript op d0 wordt uitgevoerd, kunnen de **/dev/dm-waarden\* ** verschillen op d1, cf0 en cf1. Het verschil in waarden heeft geen invloed op de DB2 pureScale setup.

## <a name="troubleshooting-and-known-issues"></a>Probleemoplossing en bekende problemen

De GitHub repo bevat een kennisbank die de auteurs onderhouden. Het bevat mogelijke problemen die u zou kunnen hebben en oplossingen die u proberen. Bekende problemen kunnen bijvoorbeeld optreden wanneer:

-   U probeert het IP-adres van de gateway te bereiken.

-   U bent het samenstellen van Algemene Openbare Licentie (GPL).

-   De beveiligingshanddruk tussen hosts mislukt.

-   De DB2-installer detecteert een bestaand bestandssysteem.

-   U installeert ibm Spectrum Scale handmatig.

-   U installeert DB2 pureScale wanneer IBM Spectrum Scale al is gemaakt.

-   U verwijdert DB2 pureScale en IBM Spectrum Scale.

Zie het kb.md-bestand in de [DB2onAzure](https://aka.ms/DB2onAzure) repo voor meer informatie over deze en andere bekende problemen.

## <a name="next-steps"></a>Volgende stappen

-   [Vereiste gebruikers maken voor een DB2 pureScale Feature-installatie](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt - Instantie maken, opdracht](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [DB2 pureScale Clusters Data Solution](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Azure Virtual Data Center Lift and Shift Guide](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
