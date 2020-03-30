---
title: 'Quickstart: Maak een Ubuntu Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Configureer en maak een Data Science Virtual Machine voor Linux (Ubuntu) om analytics en machine learning te doen.
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: quickstart
ms.date: 03/10/2020
ms.openlocfilehash: f7629b4724e85f93a8dfe3e37ac2b2155288d235
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241638"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-linux-ubuntu"></a>Quickstart: Stel de Data Science Virtual Machine in voor Linux (Ubuntu)

Ga aan de slag met de Ubuntu 18.04 Data Science Virtual Machine.

## <a name="prerequisites"></a>Vereisten

Als u een Ubuntu 18.04 Data Science Virtual Machine wilt maken, moet u een Azure-abonnement hebben. [Probeer Azure gratis.](https://azure.com/free)
Houd er rekening mee dat gratis Azure-accounts geen ONDERSTEUNING bieden voor GPU-enabled virtual machine SKU's.

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Maak uw Data Science Virtual Machine voor Linux

Dit zijn de stappen om een exemplaar van de Data Science Virtual Machine Ubuntu 18.04 te maken:

1. Ga naar de [Azure-portal](https://portal.azure.com) U wordt mogelijk gevraagd om u aan te melden bij uw Azure-account als u nog niet bent aangemeld.
1. Vind de virtuele machine vermelding door te typen in "data science virtuele machine" en het selecteren van "Data Science Virtual Machine- Ubuntu 18.04"

1. Selecteer in het volgende venster **Maken**.

1. U moet worden doorgestuurd naar het blad 'Maak een virtuele machine'.
   
1. Voer de volgende gegevens in om elke stap van de wizard te configureren:

    1. **Basics**:
    
       * **Abonnement:** Als u meer dan één abonnement hebt, selecteert u het abonnement waarop de machine wordt gemaakt en gefactureerd. U hebt privileges voor het maken van resources nodig voor dit abonnement.
       * **Resourcegroep:** maak een nieuwe groep of gebruik een bestaande groep.
       * **Naam virtuele machine**: Voer de naam van de virtuele machine in. Dit is hoe het wordt weergegeven in uw Azure-portal.
       * **Regio**: Selecteer het datacenter dat het meest geschikt is. Voor snelste netwerktoegang is dit het datacenter dat de meeste gegevens heeft of het dichtst bij uw fysieke locatie staat. Meer informatie over [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Afbeelding**: Laat de standaardwaarde achter.
       * **Grootte:** dit moet automatisch worden ingevuld met een grootte die geschikt is voor algemene workloads. Lees meer over [Linux VM-formaten in Azure](../../virtual-machines/linux/sizes.md).
       * **Verificatietype:** Selecteer 'Wachtwoord' voor een snellere installatie. 
         
         > [!NOTE]
         > Als u van plan bent JupyterHub te gebruiken, moet u 'Wachtwoord' selecteren, omdat JupyterHub *niet* is geconfigureerd om openbare SSH-sleutels te gebruiken.

       * **Gebruikersnaam**: Voer de gebruikersnaam van de beheerder in. Dit is de gebruikersnaam die u gebruikt om u aan te melden bij uw virtuele machine en hoeft niet dezelfde te zijn als uw Azure-gebruikersnaam. Gebruik *geen* hoofdletters.
         
         > [!NOTE]
         > Als u hoofdletters in uw gebruikersnaam gebruikt, werkt JupyterHub niet en krijgt u een fout van 500 interne servers.

       * **Wachtwoord:** Voer het wachtwoord in dat u gebruikt om u aan te melden bij uw virtuele machine.    
    
   1. Selecteer **Controleren + maken**.
   1. **Controleren+maken**
      * Controleer of alle ingevoerde gegevens juist zijn. 
      * Selecteer **Maken**.
    
    De inrichting duurt ongeveer 5 minuten. De status wordt weergegeven in de Azure-portal.

## <a name="how-to-access-the-ubuntu-data-science-virtual-machine"></a>Toegang krijgen tot de Virtuele Ubuntu Data Science Virtual Machine

U de Ubuntu DSVM op drie manieren openen:

  * SSH voor terminalsessies
  * X2Go voor grafische sessies
  * JupyterHub en Jupyterlab voor Jupyter Notebooks

U ook een Virtuele Data Science-machine aan Azure-notitieblokken koppelen om Jupyter-notitieblokken op de VM uit te voeren en de beperkingen van de gratis servicelaag te omzeilen. Zie [Azure Notebooks-projecten beheren en configureren](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier)voor meer informatie.

### <a name="ssh"></a>SSH

Nadat de VM is gemaakt, als deze is geconfigureerd met SSH-toegang, u zich aanmelden met behulp van SSH. Gebruik de accountreferenties die u hebt gemaakt in de sectie **Basisbeginselen** van stap 3 voor de interface van de tekstshell. Op Windows u een SSH-clienttool zoals [PuTTY](https://www.putty.org)downloaden. Als u de voorkeur geeft aan een grafisch bureaublad (X Window System), u X11 forwarding gebruiken op PuTTY.

> [!NOTE]
> De X2Go-client presteerde beter dan X11 forwarding tijdens het testen. We raden u aan de X2Go-client te gebruiken voor een grafische desktopinterface.

### <a name="x2go"></a>X2Go

De Linux VM is al ingericht met X2Go Server en klaar om clientverbindingen te accepteren. Voer de volgende procedure op uw client uit om verbinding te maken met het grafische bureaublad van Linux VM:

1. Download en installeer de X2Go-client voor uw clientplatform van [X2Go.](https://wiki.x2go.org/doku.php/doc:installation:x2goclient)
1. Noteer het openbare IP-adres van de virtuele machine, dat u vinden in de Azure-portal door de virtuele machine te openen die u zojuist hebt gemaakt.

   ![Ubuntu machine IP-adres](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

1. Voer de X2Go-client uit. Als het venster 'Nieuwe sessie' niet automatisch wordt weergegeven, gaat u naar Sessie-> Nieuwe sessie.

1. Voer in het resulterende configuratievenster de volgende configuratieparameters in:
   * **Tabblad Sessie:**
     * **Host:** Voer het IP-adres van uw VM in, waar u eerder nota van hebt genomen.
     * **Login**: Voer de gebruikersnaam in op de Linux VM.
     * **SSH-poort**: Laat het op 22, de standaardwaarde.
     * **Sessietype:** wijzig de waarde in **XFCE**. Momenteel ondersteunt de Linux VM alleen de XFCE-desktop.
   * **Tabblad Media**: U geluidsondersteuning en clientafdrukken uitschakelen als u deze niet hoeft te gebruiken.
   * **Gedeelde mappen:** Als u mappen van uw clientmachines wilt monteren op de Linux-VM, voegt u de clientmachinemappen toe die u met de VM wilt delen op dit tabblad.

   ![X2go-configuratie](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Selecteer **OK**.
1. Klik op het vakje in het rechterdeelvenster van het X2Go-venster om het aanmeldingsscherm voor uw vm weer te geven.
1. Voer het wachtwoord voor uw vm in.
1. Selecteer **OK**.
1. Het kan zijn dat u X2Go toestemming moet geven om uw firewall te omzeilen om de verbinding te voltooien.
1. U ziet nu de grafische interface voor uw Ubuntu DSVM. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub en JupyterLab

De Ubuntu DSVM draait [JupyterHub](https://github.com/jupyterhub/jupyterhub), een multiuser Jupyter server. Ga als volgt te werk om verbinding te maken:

   1. Noteer het openbare IP-adres voor uw vm door te zoeken naar en het selecteren van uw VM in de Azure-portal.
      ![Ubuntu machine IP-adres](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

   1. Open vanaf uw lokale machine een webbrowser\/en navigeer naar https: /your-vm-ip:8000, waarbij u "your-vm-ip" vervangt door het IP-adres waar u eerder nota van hebt genomen.
   1. Uw browser zal waarschijnlijk voorkomen dat u de pagina direct opent en u vertelt dat er een certificaatfout is. De DSVM biedt beveiliging via een zelfondertekend certificaat. In de meeste browsers u na deze waarschuwing doorklikken. Veel browsers blijven een soort visuele waarschuwing geven over het certificaat tijdens uw websessie.
   1. Voer de gebruikersnaam en het wachtwoord in die u hebt gebruikt om de vm te maken en meld u aan. 

      ![Ga in op Jupyter-aanmelding](./media/dsvm-ubuntu-intro/jupyter-login.png)

   1. Blader door de vele voorbeeldnotitieblokken die beschikbaar zijn.

JupyterLab, de volgende generatie Jupyter notebooks en JupyterHub, is ook beschikbaar. Om toegang te krijgen tot het, meld je aan bij\/JupyterHub en blader je naar de URL https: /your-vm-ip:8000/user/your-username/lab, waarbij je "je gebruikersnaam" vervangt door de gebruikersnaam die je hebt gekozen bij het configureren van de VM. Nogmaals, u kan in eerste instantie worden geblokkeerd van de toegang tot de site als gevolg van een certificaat fout.

U JupyterLab instellen als de standaardnotebookserver door deze regel toe te voegen aan: `/etc/jupyterhub/jupyterhub_config.py`

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Volgende stappen

Zo u uw leerproces en verkenning voortzetten:

* De [Data science on the Data Science Virtual Machine for Linux](linux-dsvm-walkthrough.md) walkthrough laat zien hoe je verschillende veelvoorkomende data science taken uitvoeren met de Linux DSVM die hier is ingericht. 
* Ontdek de verschillende data science tools op de DSVM door het uitproberen van de tools beschreven in dit artikel. U `dsvm-more-info` ook draaien op de shell in de virtuele machine voor een basisintroductie en verwijzingen naar meer informatie over de tools geïnstalleerd op de VM.  
* Leer hoe u end-to-end analytische oplossingen systematisch bouwen met behulp van het [Team Data Science Process.](https://aka.ms/tdsp)
* Ga naar de [Azure AI Gallery](https://gallery.azure.ai/) voor voorbeelden van machine learning en gegevensanalyse die gebruikmaken van de Azure AI-services.
* Raadpleeg de juiste [referentiedocumentatie](./reference-ubuntu-vm.md) voor deze virtuele machine.
