---
title: Azure Service Fabric Linux-cluster instellen in Windows
description: In dit artikel wordt beschreven hoe u Service Fabric Linux-clusters kunt instellen die worden uitgevoerd op Windows-ontwikkel machines. Dit is met name nuttig voor het ontwikkelen van meerdere platforms.
author: suhuruli
ms.topic: conceptual
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: 806e77a928d25e30aed24147525f74507bc32795
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462993"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Een Linux Service Fabric-cluster instellen op uw Windows-ontwikkelaars computer

In dit document wordt beschreven hoe u een lokale Linux-Service Fabric instelt op Windows-ontwikkel machines. Het instellen van een lokaal Linux-cluster is handig om snel toepassingen te testen die gericht zijn op Linux-clusters, maar die zijn ontwikkeld op een Windows-computer.

## <a name="prerequisites"></a>Vereisten
Service Fabric-clusters op basis van Linux kunnen niet systeem eigen worden uitgevoerd op Windows. Als u een lokaal Service Fabric cluster wilt uitvoeren, wordt er een vooraf geconfigureerde docker-container installatie kopie opgegeven. Voordat u aan de slag gaat, hebt u het volgende nodig:

* Ten minste 4 GB RAM-geheugen
* Nieuwste versie van [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker moet worden uitgevoerd in de Linux-modus

>[!TIP]
> * U kunt de stappen in de officiële docker- [documentatie](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) volgen om docker te installeren op uw Windows. 
> * Wanneer u klaar bent met de installatie, controleert u of de installatie goed is verlopen door [deze stappen](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine) uit te voeren


## <a name="create-a-local-container-and-setup-service-fabric"></a>Een lokale container maken en Service Fabric configureren
Als u een lokale docker-container wilt instellen en een service Fabric-cluster wilt uitvoeren, voert u de volgende stappen uit in Power shell:


1. Werk de configuratie van de Docker-daemon op uw host bij met het volgende en start de Docker-daemon opnieuw op: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    De aanbevolen manier om bij te werken is-Ga naar het pictogram docker > Instellingen > daemon > Advanced en werk het daar bij. Start vervolgens de docker-daemon opnieuw op om de wijzigingen van kracht te laten worden. 

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
    > Standaard wordt hierdoor de installatiekopie met de nieuwste versie van Service Fabric opgehaald. Ga naar de [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/)-pagina als u een bepaalde revisie wilt ophalen

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

5. Het duurt even voor het cluster is gestart. U kunt logboeken bekijken via de volgende opdracht of naar het dashboard gaan om de status van het cluster, [http://localhost:19080](http://localhost:19080), te bekijken:

    ```powershell 
    docker logs sftestcluster
    ```

6. Nadat stap 5 is voltooid, kunt u naar ``http://localhost:19080`` gaan vanuit uw Windows en kunt u de Service Fabric Explorer bekijken. Op dit moment kunt u verbinding maken met dit cluster met behulp van alle hulpprogram ma's van uw Windows-ontwikkelaars computer en toepassings doel voor Linux-Service Fabric clusters implementeren. 

    > [!NOTE]
    > De Eclipse-invoegtoepassing wordt momenteel niet ondersteund in Windows. 

7. Wanneer u klaar bent, kunt u de container stoppen en opschonen met de volgende opdracht:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Bekende beperkingen 
 
 Hier volgen bekende beperkingen van het uitvoeren van het lokale cluster in een container voor Mac-computers: 
 
 * DNS-service kan niet worden uitgevoerd en wordt niet ondersteund [Probleem #132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>Volgende stappen
* Aan de slag met [eclips](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Andere Java-voor [beelden](https://github.com/Azure-Samples/service-fabric-java-getting-started) bekijken


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
