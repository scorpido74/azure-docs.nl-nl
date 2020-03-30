---
title: Azure Service Fabric Linux-cluster instellen op Windows
description: In dit artikel wordt ingaan op het instellen van Linux-clusters van Service Fabric die worden uitgevoerd op Windows-ontwikkelmachines. Dit is vooral handig voor cross-platform ontwikkeling.
author: suhuruli
ms.topic: conceptual
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: 806e77a928d25e30aed24147525f74507bc32795
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75462993"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Een Linux Service Fabric-cluster instellen op uw Windows-ontwikkelaarsmachine

Dit document gaat over het instellen van een lokale Linux Service Fabric op Windows-ontwikkelmachines. Het opzetten van een lokaal Linux-cluster is handig om snel toepassingen te testen die zijn gericht op Linux-clusters, maar worden ontwikkeld op een Windows-machine.

## <a name="prerequisites"></a>Vereisten
Linux-gebaseerde Service Fabric-clusters draaien niet native op Windows. Als u een lokaal cluster van ServiceFabric wilt uitvoeren, wordt een vooraf geconfigureerde Docker-containerafbeelding verstrekt. Voordat u aan de slag gaat, hebt u het volgende nodig:

* Ten minste 4 GB RAM-geheugen
* Nieuwste versie van [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker moet draaien op Linux-modus

>[!TIP]
> * U de stappen volgen die in de officiële [Docker-documentatie](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) worden vermeld om Docker op uw Windows te installeren. 
> * Wanneer u klaar bent met de installatie, controleert u of de installatie goed is verlopen door [deze stappen](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine) uit te voeren


## <a name="create-a-local-container-and-setup-service-fabric"></a>Een lokale container maken en Service Fabric configureren
Voer de volgende stappen uit in PowerShell om een lokale Docker-container in te stellen en een cluster van servicefabric's te laten uitvoeren:


1. Werk de configuratie van de Docker-daemon op uw host bij met het volgende en start de Docker-daemon opnieuw op: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    De geadviseerde manier om te updaten is - ga naar Docker Icon > Instellingen > Daemon > Advanced en werk het daar bij. Start vervolgens de Docker daemon opnieuw om de wijzigingen van kracht te laten worden. 

2. Maak in een nieuwe map een bestand met de naam `Dockerfile` om uw Service Fabric Image te maken:

    ```Dockerfile
    FROM microsoft/service-fabric-onebox
    WORKDIR /home/ClusterDeployer
    RUN ./setup.sh
    #Generate the local
    RUN locale-gen en_US.UTF-8
    #Set environment variables
    ENV LANG=en_US.UTF-8
    ENV LANGUAGE=en_US:en
    ENV LC_ALL=en_US.UTF-8
    EXPOSE 19080 19000 80 443
    #Start SSH before running the cluster
    CMD /etc/init.d/ssh start && ./run.sh
    ```

    >[!NOTE]
    >U kunt het bestand aanpassen zodat aanvullende programma's of afhankelijkheden aan uw container kunnen worden toegevoegd.
    >Bijvoorbeeld: het toevoegen van `RUN apt-get install nodejs -y` biedt ondersteuning voor `nodejs`-toepassingen als uitvoerbare gastbestanden.
    
    >[!TIP]
    > Standaard wordt hierdoor de installatiekopie met de nieuwste versie van Service Fabric opgehaald. Ga voor bepaalde revisies naar de [dockerhubpagina](https://hub.docker.com/r/microsoft/service-fabric-onebox/)

3. Als u uw herbruikbare installatiekopie wilt bouwen vanaf `Dockerfile`, opent u een terminal en gaat u met de opdracht `cd` naar de map met uw `Dockerfile`. Voer vervolgende de volgende opdracht uit:

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Deze bewerking kan enige tijd duren, maar hoeft slechts eenmaal te worden uitgevoerd.

4. Nu kunt u snel een lokale kopie van Service Fabric starten zodra u deze nodig hebt. Voer hiertoe de volgende opdracht uit:

    ```powershell 
    docker run --name sftestcluster -d -v //var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Door een naam op te geven voor uw instantie van de container, kunt u deze op een beter leesbare manier verwerken. 
    >
    >Als uw toepassing op bepaalde poorten luistert, moeten de poorten worden opgegeven met behulp van aanvullende `-p` labels. Bijvoorbeeld, als uw toepassing op poort 8080 luistert, voeg dan de volgende `-p` tag toe:
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

5. Het cluster neemt een korte tijd in beslag om te starten, u logboeken [http://localhost:19080](http://localhost:19080)bekijken met de volgende opdracht of naar het dashboard springen om de status van de clusters weer te geven:

    ```powershell 
    docker logs sftestcluster
    ```

6. Nadat stap 5 is voltooid, ``http://localhost:19080`` kunt u vanuit uw Windows gaan en u de Verkenner van Service Fabric zien. Op dit moment u verbinding maken met dit cluster met behulp van alle tools van uw Windows-ontwikkelaarsmachine en toepassingen implementeren die zijn gericht op Linux Service Fabric-clusters. 

    > [!NOTE]
    > De Eclipse-invoegtoepassing wordt momenteel niet ondersteund in Windows. 

7. Wanneer u klaar bent, stopt u en opruimt u de container met deze opdracht:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Bekende beperkingen 
 
 Hier volgen bekende beperkingen van het uitvoeren van het lokale cluster in een container voor Mac-computers: 
 
 * DNS-service kan niet worden uitgevoerd en wordt niet ondersteund [Probleem #132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>Volgende stappen
* Aan de slag met [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Bekijk andere [Java-voorbeelden](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
