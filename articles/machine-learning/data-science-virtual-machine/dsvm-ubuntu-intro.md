---
title: 'Quickstart: Een Ubuntu-DSVM maken'
description: Configureer en maak een Data Science Virtual Machine voor Linux (Ubuntu) om analyses en machine learning te maken.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/10/2019
ms.openlocfilehash: 5b50355391180c4a499a0f4e77c3897f8969cdb8
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170946"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-linux-ubuntu"></a>Quickstart: De Data Science Virtual Machine instellen voor Linux (Ubuntu)

Ga aan de slag met de Ubuntu-Data Science Virtual Machine.

## <a name="prerequisites"></a>Vereisten

Als u een Windows-Data Science Virtual Machine wilt maken, moet u een Azure-abonnement hebben. [Probeer Azure gratis](https://azure.com/free)uit.

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Maken van uw virtuele Machine voor Datatechnologie voor Linux

Hier volgen de stappen voor het maken van een exemplaar van de Data Science Virtual Machine voor Linux:

1. Ga naar de [Azure Portal](https://portal.azure.com) u wordt mogelijk gevraagd om u aan te melden bij uw Azure-account als u nog niet bent aangemeld.
1. Zoek de vermelding van de virtuele machine door in ' data Science virtual machine ' te typen en Data Science Virtual Machine voor Linux (Ubuntu) te selecteren.
    
    ![Ubuntu VM-vermelding](./media/dsvm-ubuntu-intro/search-ubuntu.png)

1. Selecteer in het volgende venster **maken**.

    ![Wizard voor het maken van een virtuele machine](./media/dsvm-ubuntu-intro/create-linux.png)

1. U moet worden omgeleid naar de Blade een virtuele machine maken.
   
   ![Tabblad basis informatie dat overeenkomt met de virtuele machine Ubuntu](./media/dsvm-ubuntu-intro/review-create-ubuntu.png)

1. Voer de volgende informatie in voor het configureren van elke stap van de wizard:

    1. **Grondbeginselen van**:
    
       * **Abonnement**: Als u meer dan één abonnement hebt, selecteert u de computer waarop de machine wordt gemaakt en gefactureerd. U hebt privileges voor het maken van resources nodig voor dit abonnement.
       * **Resourcegroep**: Een nieuwe groep maken of een bestaand item gebruiken.
       * **Naam van virtuele machine**: Voer de naam van de virtuele machine in. Dit wordt weer gegeven in uw Azure Portal.
       * **Regio**: Selecteer het Data Center dat het meest geschikt is. Voor de snelste netwerk toegang is het het Data Center dat het meren deel van uw gegevens heeft of zich het dichtst bij uw fysieke locatie bevindt. Meer informatie over [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Installatie kopie**: Laat de standaardwaarde staan.
       * **Grootte**: Dit moet automatisch worden ingevuld met een grootte die geschikt is voor algemene werk belastingen. Meer informatie over de [grootten van virtuele Linux-machines in azure](../../virtual-machines/linux/sizes.md).
       * **Verificatietype**: Selecteer ' wacht woord ' voor een snellere installatie. 
         
         > [!NOTE]
         > Als u JupyterHub wilt gebruiken, zorg er dan voor dat u ' wacht woord ' selecteert, omdat JupyterHub *niet* is geconfigureerd voor het gebruik van open bare SSH-sleutels.

       * **Gebruikersnaam**: Voer de gebruikers naam van de beheerder in. Dit is de gebruikers naam die u gaat gebruiken om u aan te melden bij uw virtuele machine en hoeft niet hetzelfde te zijn als uw Azure-gebruikers naam. Gebruik *geen* hoofd letters.
         
         > [!NOTE]
         > Als u hoofdletter kapitalen gebruikt in uw gebruikers naam, werkt JupyterHub niet en wordt er een interne server fout van 500 aangetroffen.

       * **Wachtwoord**: Voer het wacht woord in dat u wilt gebruiken om u aan te melden bij de virtuele machine.    
    
   1. Selecteer **Controleren + maken**.
   1. **Controleren en maken**
      * Controleer of alle informatie die u hebt ingevoerd juist is. 
      * Selecteer **Maken**.
    
    De inrichting duurt ongeveer 5 minuten. De status wordt weer gegeven in de Azure Portal.

## <a name="how-to-access-the-ubuntu-data-science-virtual-machine"></a>Toegang tot de Ubuntu-Data Science Virtual Machine

U kunt op een van de volgende drie manieren toegang krijgen tot de Ubuntu-DSVM:

  * SSH voor terminal-sessies
  * X2Go voor grafische sessies
  * JupyterHub en Jjupyterlab voor Jupyter-notebooks

U kunt ook een Data Science Virtual Machine aan Azure Notebooks koppelen om Jupyter-notebooks op de virtuele machine uit te voeren en de beperkingen van de gratis servicelaag over te slaan. Zie [Azure notebooks projecten beheren en configureren](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier)voor meer informatie.

### <a name="ssh"></a>SSH

Nadat de VM is gemaakt, kunt u zich met SSH aanmelden als deze is geconfigureerd met SSH-toegang. Gebruik de accountreferenties op die u hebt gemaakt in de **basisbeginselen** sectie van stap 3 voor de tekst shell-interface. In Windows kunt u een SSH-client hulpprogramma downloaden, zoals [putty](https://www.putty.org). Als u liever een grafisch bureau blad (X Window-Systeem) hebt, kunt u X11 door sturen gebruiken op PuTTy.

> [!NOTE]
> De client X2Go beter dan X11 doorsturen tests uitgevoerd. Het is raadzaam om met behulp van de client X2Go voor een grafische interface voor het bureaublad.

### <a name="x2go"></a>X2Go

De virtuele Linux-machine is al ingericht met de X2Go-server en is klaar om client verbindingen te accepteren. Voor verbinding met het bureaublad van de Linux-VM-grafische, voer de volgende procedure op de client:

1. Download en installeer de client X2Go voor uw clientplatform van [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Noteer het open bare IP-adres van de virtuele machine, dat u in de Azure Portal kunt vinden door de virtuele machine die u zojuist hebt gemaakt, te openen.

   ![IP-adres van Ubuntu-computer](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

1. Voer de X2Go-client uit. Als het venster nieuwe sessie niet automatisch wordt pop-up, gaat u naar sessie-> nieuwe sessie.

1. Voer in het venster resulterende configuratie de volgende configuratie parameters in:
   * **Tabblad sessie**:
     * **Host**: Voer het IP-adres van uw VM in, die u eerder hebt genoteerd.
     * **Aanmelding**: Voer de gebruikers naam in op de virtuele Linux-machine.
     * **SSH-poort**: De standaard waarde is 25.
     * **Sessie type**: Wijzig de waarde in **xfce**. De Linux-VM ondersteunt momenteel alleen het XFCE-bureau blad.
   * **Tabblad Media**: U kunt geluids ondersteuning en client afdrukken uitschakelen als u deze niet nodig hebt.
   * **Gedeelde mappen**: Als u mappen wilt van uw client machines die op de virtuele Linux-machine zijn gekoppeld, voegt u de client computer directory's toe die u wilt delen met de virtuele machine op dit tabblad.

   ![X2go-configuratie](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Selecteer **OK**.
1. Klik op het vak in het rechterdeel venster van het venster X2Go om het aanmeldings scherm voor uw virtuele machine weer te geven.
1. Voer het wacht woord voor uw virtuele machine in.
1. Selecteer **OK**.
1. Mogelijk moet u X2Go toestemming geven om uw firewall te omzeilen om verbinding te kunnen maken.
1. U ziet nu de grafische interface voor uw Ubuntu DSVM. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub en Jjupyterlab

De Ubuntu DSVM voert [JupyterHub](https://github.com/jupyterhub/jupyterhub), een Jupyter-server voor meerdere gebruikers. Voer de volgende stappen uit om verbinding te maken:

   1. Noteer het open bare IP-adres voor uw virtuele machine door de virtuele machine in de Azure Portal te zoeken en te selecteren.
      ![IP-adres van Ubuntu-computer](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

   1. Open vanaf uw lokale computer een webbrowser en ga naar https:\//your-VM-IP: 8000, vervang ' Your-VM-IP ' door het IP-adres dat u eerder hebt genoteerd.
   1. Voer de gebruikers naam en het wacht woord in die u hebt gebruikt om de virtuele machine te maken en meld u aan. 

      ![Jupyter-aanmelding invoeren](./media/dsvm-ubuntu-intro/jupyter-login.png)

   1. Blader door de vele beschik bare voor beelden van notitie blokken.

Jjupyterlab, is de volgende generatie van Jupyter notebooks en JupyterHub, ook beschikbaar. Meld u aan bij JupyterHub en blader vervolgens naar de URL https:\//your-VM-IP: 8000/User/uw-username/Lab en vervang ' uw-gebruikers naam ' door de gebruikers naam die u hebt gekozen bij het configureren van de virtuele machine.

U kunt Jjupyterlab instellen als de standaard notebook server door deze regel toe te `/etc/jupyterhub/jupyterhub_config.py`voegen aan:

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Volgende stappen

Hier ziet u hoe u kunt doorgaan met uw learning en verkennen:

* [In de gegevens wetenschap van het scenario data Science virtual machine voor Linux](linux-dsvm-walkthrough.md) ziet u hoe u verschillende algemene data Science-taken kunt uitvoeren met de Linux-DSVM die hier is ingericht. 
* Bekijk de verschillende hulpprogram ma's voor gegevens wetenschap op het DSVM door de hulpprogram ma's die in dit artikel worden beschreven, uit te proberen. U kunt ook uitvoeren `dsvm-more-info` op de shell in de virtuele machine voor een basis kennis Making en verwijzingen naar meer informatie over de hulpprogram ma's die op de VM zijn geïnstalleerd.  
* Informatie over het bouwen van analytische oplossingen voor end-to-end systematisch met behulp van de [Team Data Science Process](https://aka.ms/tdsp).
* Ga naar de [Azure AI Gallery](https://gallery.azure.ai/) voor machine learning en data analytics-voorbeelden die gebruikmaken van de Azure AI-services.
* Raadpleeg de juiste [referentie documentatie](./reference-ubuntu-vm.md) voor deze virtuele machine.
