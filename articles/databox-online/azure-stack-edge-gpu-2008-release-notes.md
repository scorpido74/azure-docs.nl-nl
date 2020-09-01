---
title: Opmerkingen bij de preview-versie van Azure Stack Edge | Microsoft Docs
description: Hierin worden essentiële openstaande problemen en oplossingen beschreven voor het Azure Data Box Gateway uitvoeren van algemene Beschik baarheid.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: 6c29240aa3267cd93ba0c3de1f0c797ce1a1483c
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084516"
---
# <a name="azure-stack-edge-with-gpu-preview-release-notes"></a>Release opmerkingen bij Azure Stack Edge met GPU preview

In de volgende release opmerkingen worden de kritieke openstaande problemen en de opgeloste problemen voor de preview-versie van 2008 voor uw Azure Stack edge-apparaten met GPU geïdentificeerd.

De release opmerkingen worden voortdurend bijgewerkt en als er kritieke problemen worden gedetecteerd die een tijdelijke oplossing vereisen, worden deze toegevoegd. Lees de informatie in de release opmerkingen aandachtig door voordat u uw Azure Stack edge-apparaat implementeert.

Deze versie van **Azure stack Edge 2008** komt overeen met de volgende software versie:

- **2.1.1328.1904**



## <a name="known-issues-in-ga-release"></a>Bekende problemen in GA release

De volgende tabel bevat een overzicht van bekende problemen voor het Azure Stack edge-apparaat.

| Nee. | Functie | Probleem | Tijdelijke oplossing/opmerkingen |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge + Azure SQL | Voor het maken van SQL database is beheerders toegang vereist.   |Voer de volgende stappen uit in plaats van stap 1-2 in [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database) . <ul><li>Schakel in de lokale gebruikers interface van uw apparaat Compute interface in. Selecteer **reken > poort # > inschakelen voor compute > van toepassing.**</li><li>Voer uit in een Power shell-venster dat is verbonden met het apparaat `Add-HcsComputeNetwork` . </li><li>Down load `sqlcmd` op de client computer van https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Maak verbinding met het IP-adres van de compute-interface (de poort die is ingeschakeld), waarbij een ', 1401 ' aan het einde van het adres wordt toegevoegd.</li><li>De laatste opdracht ziet er als volgt uit: sqlcmd-S {Interface IP}, 1401-U SA-P "Strong! Passw0rd".</li>Hierna moeten stap 3-4 van de huidige documentatie identiek zijn. </li></ul> |
| **2.** |Vernieuwen| Incrementele wijzigingen in blobs die zijn hersteld via **vernieuwen** , worden niet ondersteund |Voor BLOB-eind punten kunnen gedeeltelijke updates van blobs na een vernieuwing ertoe leiden dat de updates niet worden geüpload naar de Cloud. Bijvoorbeeld volg orde van acties zoals:<ul><li>Maak een BLOB in de Cloud. Of verwijder een eerder geüploade blob van het apparaat.</li><li>Vernieuw de blob van de Cloud naar het apparaat met behulp van de functie voor vernieuwen.</li><li>Werk slechts een deel van de BLOB bij met Azure SDK REST Api's.</li></ul>Deze acties kunnen ertoe leiden dat de bijgewerkte secties van de BLOB niet worden bijgewerkt in de Cloud. <br>**Tijdelijke oplossing**: gebruik hulpprogram ma's zoals Robocopy of een gewone bestands kopie via Verkenner of opdracht regel om de hele blobs te vervangen.|
|**3.**|Beperking|Als er tijdens het beperken geen nieuwe schrijf bewerkingen zijn toegestaan in het apparaat, mislukt de schrijf bewerkingen die zijn uitgevoerd door de NFS-client met de fout ' permission denied '.| De volgende fout wordt weer gegeven:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: kan directory ' test ' niet maken: de machtiging is geweigerd|
|**4.**|Opname Blob Storage|Wanneer u AzCopy versie 10 gebruikt voor inslikken van Blob-opslag, voert u AzCopy uit met het volgende argument: `Azcopy <other arguments> --cap-mbps 2000`| Als deze limieten niet worden verstrekt voor AzCopy, kan het mogelijk zijn om een groot aantal aanvragen naar het apparaat te verzenden en om problemen met de service op te lossen.|
|**5,0.**|Gelaagde opslag accounts|Het volgende is van toepassing wanneer u gelaagde opslag accounts gebruikt:<ul><li> Alleen blok-blobs worden ondersteund. Pagina-blobs worden niet ondersteund.</li><li>Er is geen moment opname-of Copy API-ondersteuning.</li><li> De inslikken van een Hadoop-werk belasting `distcp` wordt niet ondersteund omdat de Kopieer bewerking intensief wordt gebruikt.</li></ul>||
|**6,5.**|NFS-share verbinding|Als er meerdere processen naar dezelfde share worden gekopieerd en het `nolock` kenmerk niet wordt gebruikt, worden er mogelijk fouten weer geven tijdens de kopie.|Het `nolock` kenmerk moet worden door gegeven aan de koppelings opdracht om bestanden te kopiëren naar de NFS-share. Bijvoorbeeld: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Kubernetes-cluster|Wanneer u een update toepast op uw apparaat waarop een kubernetes-cluster wordt uitgevoerd, worden de virtuele machines van kubernetes opnieuw opgestart en opnieuw opgestart. In dit geval worden alleen de peulen die zijn geïmplementeerd met de opgegeven replica's automatisch hersteld na een update.  |Als u afzonderlijke peulen buiten een replicatie controller hebt gemaakt zonder een replicaset op te geven, wordt deze peul niet automatisch hersteld na het bijwerken van het apparaat. U moet deze peul herstellen.<br>Een replicaset vervangt de peulen die worden verwijderd of beëindigd om een wille keurige reden, zoals een storing in het knoop punt of een onderbreking van de upgrade van knoop punten. Daarom wordt u aangeraden een replicaset te gebruiken, zelfs als uw toepassing slechts één Pod vereist.|
|**achtste.**|Kubernetes-cluster|Kubernetes op Azure Stack Edge wordt alleen ondersteund met helm v3 of hoger. Voor meer informatie gaat u naar [Veelgestelde vragen: verwijderen van Tiller](https://v3.helm.sh/docs/faq/).|
|**9,4.**|Azure Arc + Azure Stack Edge|Implementaties van Azure Arc worden niet ondersteund als webproxy is geconfigureerd op uw Azure Stack edge-apparaat.||
|**6.**|Kubernetes |Poort 31000 is gereserveerd voor Kubernetes-dash board. Op dezelfde manier worden in de standaard configuratie de IP-adressen 10.96.0.1 en 10.96.0.10 gereserveerd voor respectievelijk de Kubernetes-service en de basis-DNS-service.|Gebruik geen gereserveerde Ip's.|
|**9.**|Kubernetes |Kubernetes ondersteunt momenteel geen LoadBalancer-Services met meerdere protocollen. Bijvoorbeeld een DNS-service die luistert op TCP en UDP. |Om deze beperking van Kubernetes met MetalLB te omzeilen, kunnen twee services (een voor TCP, een voor UDP) worden gemaakt op dezelfde pod selector. Deze services gebruiken dezelfde uitwisselings sleutel en spec. loadBalancerIP om hetzelfde IP-adres te delen. Ip's kunnen ook worden gedeeld als u meer services hebt dan beschik bare IP-adressen. <br> Zie [IP-adres delen](https://metallb.universe.tf/usage/#ip-address-sharing)voor meer informatie.|
|**12.**|Kubernetes-cluster|Bestaande Azure IoT Edge Marketplace-modules kunnen niet worden uitgevoerd op het Kubernetes-cluster als het hosting platform voor IoT Edge op Azure Stack edge-apparaat.|De modules moeten worden gewijzigd voordat deze op het Azure Stack edge-apparaat worden geïmplementeerd. Zie voor meer informatie Azure IoT Edge modules van Marketplace aanpassen zodat ze op Azure Stack edge-apparaat kunnen worden uitgevoerd.<!-- insert link-->|
|**13.**|Kubernetes |Op bestanden gebaseerde BIND koppelingen worden niet ondersteund met Azure IoT Edge op Kubernetes op Azure Stack edge-apparaat.|IoT Edge maakt gebruik van een Vertaal laag om `ContainerCreate` Opties om te zetten in Kubernetes-constructies. Het maken `Binds` van toewijzingen naar de map hostpath of het maken en op bestanden gebaseerde BIND koppelingen kan niet worden gekoppeld aan paden in IOT Edge containers.|


## <a name="next-steps"></a>Volgende stappen

- [Implementatie van Azure Stack edge-apparaat voorbereiden met GPU](azure-stack-edge-gpu-deploy-prep.md)

