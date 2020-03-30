---
title: Een ADCD (Application Developers Controlled Distribution) instellen in IBM zD&T v1 | Microsoft Documenten
description: Voer een IBM Z Development and Test Environment (zD&T)-omgeving uit op Azure Virtual Machines (VM's).
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 66f80c79219090c27da37dfc1d9149df5604961f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68841386"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Een ADCD (Application Developers Controlled Distribution) instellen in IBM zD&T v1

U een IBM Z Development and Test Environment (zD&T)-omgeving uitvoeren op Azure Virtual Machines (VM's). Deze omgeving emuleert de IBM Z-serie architectuur. Het kan een verscheidenheid van Z-serie besturingssystemen of installaties hosten (ook wel Z Instances of Pakketten genoemd), die beschikbaar worden gesteld via aangepaste bundels genaamd de IBM Application Developers Controlled Distributions (ADCDs).

In dit artikel ziet u hoe u een ADCD-exemplaar instelt in een zD-&T-omgeving op Azure. ADCDs maken complete Z-serie implementaties van besturingssysteemen voor ontwikkel- en testomgevingen die draaien in zD&T.

Net als zD&T zijn ADCDs alleen beschikbaar voor IBM-klanten en -partners en zijn ze uitsluitend voor ontwikkelings- en testdoeleinden. Ze mogen niet worden gebruikt voor productieomgevingen. Tal van IBM-installatiepakketten zijn te downloaden via [Passport Advantage](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) of [IBM PartnerWorld.](https://www.ibm.com/partnerworld/public)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als je nog geen account hebt, maak je een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat je begint.

- De [zD-&T-omgeving][ibm-install-z] die eerder is ingesteld op Azure. In dit artikel wordt ervan uitgegaan dat u dezelfde Ubuntu 16.04 VM-afbeelding gebruikt die eerder is gemaakt.

- Toegang tot de ADCD-media via IBM PartnerWorld of Passport Advantage.

- Een [licentieserver.](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html) Dit is nodig om IBM zD&T uit te voeren. De manier waarop u deze maakt, hangt af van de manier waarop u de software van IBM in licentie licentie draagt:

  - **Hardwaregebaseerde licentieserver** vereist een USB-hardwareapparaat dat de Rationele tokens bevat die nodig zijn om toegang te krijgen tot alle delen van de software. U moet dit verkrijgen van IBM.

  - **Softwaregebaseerde licentieserver** vereist dat u een gecentraliseerde server instelt voor het beheer van de licentiesleutels. Deze methode heeft de voorkeur en vereist dat u de sleutels die u ontvangt van IBM in de beheerserver in te stellen.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Download de installatiepakketten van Passport Advantage

Toegang tot de ADCD-media is vereist. In de onderstaande stappen wordt ervan uitgegaan dat u een IBM-klant bent en passport advantage gebruiken. IBM-partners kunnen [IBM PartnerWorld](https://www.ibm.com/partnerworld/public)gebruiken.

> [!NOTE]
> In dit artikel wordt ervan uitgegaan dat een Windows-pc wordt gebruikt om toegang te krijgen tot azure-portal en om de IBM-media te downloaden. Als u een Mac- of Ubuntu-desktop gebruikt, kunnen de opdrachten en het proces voor het verkrijgen van de IBM-media enigszins verschillen.

1. Log in op [Passport Advantage](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Selecteer **Softwaredownloads** en **mediatoegang**.

3. Selecteer **Programmaaanbieding en overeenkomstnummer**en klik op **Doorgaan**.

4. Voer de onderdeelbeschrijving of het onderdeelnummer in en klik op **Finder**.

5. Klik destijds op de lijst met alfabetische volgorde om het product op naam weer te geven en weer te geven.

6. Selecteer **Alle besturingssystemen** in het **veld Besturingssysteem**en **Alle talen** in het **veld Talen**. Klik vervolgens op **Ga.**

7. Klik **op Afzonderlijke bestanden selecteren** om de lijst uit te vouwen en de afzonderlijke media weer te geven die u wilt downloaden.

8. Controleer het pakket(en) dat u wilt downloaden, selecteer **Downloaden**en download de bestanden naar de gewenste map.

## <a name="upload-the-adcd-packages"></a>Het ADCD-pakket(en) uploaden

Nu u de pakketten(en) hebt, moet u ze uploaden naar uw VM op Azure.

1. Start in de Azure-portal een **ssh-sessie** met de Ubuntu VM die u hebt gemaakt. Ga naar uw vm, selecteer het **blad Overzicht** en selecteer **Verbinding maken**.

2. Selecteer het tabblad **SSH** en kopieer de ssh-opdracht naar het klembord.

3. Meld u aan bij uw VM met uw referenties en de [SSH-client](/azure/virtual-machines/linux/use-remote-desktop) naar keuze. Deze demo maakt gebruik van de Linux-extensies voor Windows 10, die een bash shell toevoegt aan de Windows-opdrachtprompt. PuTTY werkt net zo goed.

4. Maak bij het inloggen een directory om de IBM-pakketten te uploaden. Houd in gedachten Linux is geval gevoelig. In deze demo wordt bijvoorbeeld ervan uitgegaan dat de pakketten worden geüpload naar:

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. Upload de bestanden met behulp van een SSH-client zoals[WinSCP](https://winscp.net/eng/index.php). Aangezien SCP deel uitmaakt van SSH, maakt het gebruik van poort 22, wat SSH gebruikt. Als uw lokale computer geen Windows is, u de [scp-opdracht](http://man7.org/linux/man-pages/man1/scp.1.html) typen in uw SSH-sessie.

6. Start de upload naar de Azure VM-map die u hebt gemaakt, wat de afbeeldingsopslag wordt voor zD&T.

    > [!NOTE]
    > Zorg ervoor dat **ADCDTOOLS. XML** is opgenomen in de map **home/MyUserID/ZDT/adcd/nov2017.** U hebt dit later nodig.

7. Wacht tot de bestanden zijn geüpload, wat enige tijd kan duren, afhankelijk van uw verbinding met Azure.

8. Wanneer de uploads zijn voltooid, navigeert u naar de map volumes en decomprimeert u alle **gz-volumes:**

    ```
        gunzip \*.gz
    ```
    
![Verkenner met gedecomprimeerde gz-volumes](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>De afbeeldingsopslag configureren

De volgende stap is het configureren van zD&T om het geüploade pakket(en) te gebruiken. Met het beeldopslagproces binnen zD&T u de afbeeldingen monteren en gebruiken. Het kan SSH of FTP gebruiken.

1. Start de **zDTServer**. Om dit te doen, moet je op het wortelniveau zijn. Voer de volgende twee opdrachten in volgorde in:
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. Let op de URL-uitvoer door de opdracht en gebruik deze URL om toegang te krijgen tot de webserver. Het lijkt op:
     > https://(uw VM-naam of IP-adres): 9443/ZDTMC/index.html
     >
     > Vergeet niet dat uw webtoegang poort 9443 gebruikt. Gebruik dit om in te loggen op de webserver. De gebruikersnaam voor ZD&T is **zdtadmin** en het wachtwoord is **wachtwoord.**

    ![IBM zD&T Enterprise Edition Welkomstscherm](media/02-welcome.png)

3. Selecteer op de pagina **Snel aan de slag** onder **Afbeeldingsopslag** **configureren**.

     ![IBM zD&T Enterprise Edition Snel startscherm](media/03-quickstart.png)

4. Selecteer op de pagina **Afbeeldingsopslag configureren** de optie **SSH File Transfer Protocol**.

5. Typ **Localhost** voor **Hostnaam**en voer het mappad in voor waar u de afbeeldingen hebt geüpload. Bijvoorbeeld /home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Voer de **gebruikersnaam** en **het wachtwoord** voor de vm in. Gebruik de ZD-&T-gebruikersnaam en -wachtwoord niet.

7. Test de verbinding om te controleren of u toegang hebt en selecteer **Opslaan** om de configuratie op te slaan.

## <a name="configure-the-target-environments"></a>De doelomgevingen configureren

De volgende stap is het configureren van de zD&T-doelomgeving. Deze geëmuleerde gehoste omgeving is waar uw afbeeldingen worden uitgevoerd.

1. Selecteer op de pagina **Snel aan de slag** onder **Doelomgevingen** **configureren**.

2. Selecteer **Doel toevoegen**op de pagina **Doelomgevingen configureren** .

3. Selecteer **Linux**. IBM ondersteunt twee soorten omgevingen, Linux en Cloud(OpenStack), maar deze demo draait op Linux.

4. Voer op de pagina **Doelomgeving toevoegen** voor **Hostnaam** **localhost**in . Houd **de SSH-poort** ingesteld op **22**.

5. Voer in het **labelvak Doelomgeving** een label in zoals **MyCICS.**

     ![Scherm doelomgeving toevoegen](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>ADCD configureren en implementeren

Nadat u de vorige configuratiestappen hebt voltooid, moet u zD-&T configureren om de pakket-en doelomgeving te gebruiken. Nogmaals, u gebruikt het beeldopslagproces in zD&T, waarmee u de afbeeldingen monteren en gebruiken. Het kan SSH of FTP gebruiken.

1. Selecteer **ADCD** **op**de pagina **Snel aande a.s.** Er verschijnt een reeks instructies waarin u de stappen wordt verteld die moeten worden voltooid voordat een ADCD-pakket kan worden gemonteerd. Dit verklaart waarom we de doelmap zo noemden als vroeger.

2. Ervan uitgaande dat alle afbeeldingen zijn geüpload naar de juiste mappen, klik op de **afbeelding van ADCD** link weergegeven in de rechterbenedenzijde (weergegeven in stap 7 in de volgende screenshot).

     ![IBM zD&T Enterprise Edition - ADCD-scherm configureren](media/05-adcd.png)

## <a name="create-the-image"></a>De installatiekopie maken

Wanneer de vorige configuratiestap is voltooid, wordt de pagina **Een afbeelding maken met ADCD-componenten** weergegeven.

1. Selecteer het volume (in dit geval november 2017) om de verschillende pakketten in dat volume weer te geven.

2. Selecteer voor deze demo **Customer Information Control System (CICS) - 5.3**.

3. Typ in het vak **Afbeeldingsnaam** een naam voor de afbeelding, zoals **MyCICS Image**.

4. Selecteer de knop **Afbeelding maken** rechtsonder.

     ![IBM zD&T Enterprise Edition - Een afbeelding maken met het scherm ADCD Components](media/06-adcd.png)

5. Kies **Afbeeldingen implementeren**in het venster dat wordt weergegeven en u vertellen dat de afbeelding is geïmplementeerd.

6. Selecteer op de pagina **Een afbeelding implementeren naar een doelomgeving** de afbeelding die u op de vorige pagina hebt gemaakt **(MyCICS Image)** en de doelomgeving die eerder is gemaakt **(MyCICS).**

7. Geef op het volgende scherm uw referenties voor de VM op (dat wil zeggen niet de ztadmin-referentie).

8. Voer in het deelvenster Eigenschappen het aantal **centrale processors (C's)** in, de hoeveelheid **systeemgeheugen (GB)** en de **implementatiemap** voor de lopende afbeelding in. Aangezien dit een demo is, houd het klein.

9. Controleer of het vak is geselecteerd voor **Het automatisch uitgeven van de opdracht IPL aan z/OS na het implementeren**.

     ![Scherm Eigenschappen](media/07-properties.png)

10. Selecteer **Voltooien**.

11. Selecteer **Afbeelding implementeren** van de pagina Een afbeelding implementeren naar een **doelomgeving.**

Uw afbeelding kan nu worden geïmplementeerd en is klaar om te worden gemonteerd door een 3270 terminal emulator.

> [!NOTE]
> Als u een foutmelding ontvangt waarin staat dat u niet genoeg schijfruimte hebt, moet u er rekening mee houden dat de regio 151 Gb vereist.

Gefeliciteerd! U hebt nu een IBM mainframe-omgeving op Azure.

## <a name="learn-more"></a>Meer informatie

- [Mainframe migratie: mythen en feiten](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale op Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [Probleemoplossing](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Demystifying mainframe to Azure migration Demyststifying mainframe to Azure migration Demyststifying mainframe to Azure migration](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
