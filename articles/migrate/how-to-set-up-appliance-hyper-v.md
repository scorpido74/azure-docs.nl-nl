---
title: Een Azure Migrate apparaat instellen voor Hyper-V
description: Meer informatie over het instellen van een Azure Migrate apparaat om Hyper-V-Vm's te beoordelen en te migreren.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 1676ad3dde5a97d2afdb64595d51f5502f9dcc3d
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185920"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Een apparaat instellen voor virtuele Hyper-V-machines

In dit artikel wordt beschreven hoe u het Azure Migrate apparaat instelt als u virtuele Hyper-V-machines wilt beoordelen met het hulp programma Azure Migrate server Assessment, of virtuele VMware-machines naar Azure migreert met behulp van het hulp programma voor migratie van Azure Migrate server.

Het Hyper-V-VM-apparaat is een licht gewicht apparaat dat wordt gebruikt door Azure Migrate server-evaluatie/-migratie om het volgende te doen:

- On-premises Hyper-V-Vm's detecteren.
- Meta gegevens en prestatie gegevens voor gedetecteerde Vm's verzenden naar Azure Migrate server evaluatie/migratie.

Meer [informatie](migrate-appliance.md) over het Azure migrate apparaat.


## <a name="appliance-deployment-steps"></a>Implementatie stappen voor het apparaat

Als u het apparaat wilt instellen, doet u het volgende:
- Down load een gecomprimeerde Hyper-V VHD vanuit het Azure Portal.
- Maak het apparaat en controleer of het verbinding kan maken met Azure Migrate server beoordeling.
- Configureer het apparaat voor de eerste keer en registreer het bij het Azure Migrate-project.

## <a name="download-the-vhd"></a>De VHD downloaden

Down load de sjabloon voor de gezipte VHD voor het apparaat.

1. In **migratie doelen** > **servers** > **Azure migrate: Server evaluatie**, klikt u op **ontdekken**.
2. In **computers detecteren** > **zijn uw machines gevirtualiseerd? klikt u**op **Ja, met Hyper-V**.
3. Klik op **downloaden** om het VHD-bestand te downloaden.

    ![Virtuele machine downloaden](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Beveiliging controleren

Controleer of het gecomprimeerde bestand is beveiligd, voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor de VHD te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gebruiksvoorbeeld: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Voor de toestel versie 2.19.11.12 moet de gegenereerde hash overeenkomen met deze instellingen.

  **Algoritme** | **Hash-waarde**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31



## <a name="create-the-appliance-vm"></a>De apparaat-VM maken

Importeer het gedownloade bestand en maak de virtuele machine.

1. Pak het gecomprimeerde VHD-bestand uit in een map op de Hyper-V-host die als host fungeert voor de toestel-VM. Er worden drie mappen geëxtraheerd.
2. Open Hyper-V-beheer. Klik in **acties**op **virtuele machine importeren**.

    ![VHD implementeren](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. Klik in de wizard virtuele machine importeren > **voordat u begint**op **volgende**.
3. In **map zoeken**geeft u de map op die de geëxtraheerde VHD bevat. Klik op **Volgende**.
1. Klik in **virtuele machine selecteren**op **volgende**.
2. In **import type kiezen**klikt u op **de virtuele machine kopiëren (een nieuwe unieke id maken)** . Klik op **Volgende**.
3. Laat in **doel kiezen**de standaard instelling ongewijzigd. Klik op **Volgende**.
4. In **opslag mappen**, behoud de standaard instelling. Klik op **Volgende**.
5. Geef in **netwerk kiezen**de virtuele switch op die door de VM moet worden gebruikt. De switch heeft Internet connectiviteit nodig om gegevens naar Azure te verzenden.
6. Controleer de instellingen in **samen vatting**. Klik vervolgens op **volt ooien**.
7. Start de virtuele machine in Hyper-V-beheer > **virtual machines**.


### <a name="verify-appliance-access-to-azure"></a>Toestel toegang tot Azure controleren

Zorg ervoor dat de virtuele machine van het apparaat verbinding kan maken met [Azure-url's](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

## <a name="configure-the-appliance"></a>Het apparaat configureren

Het apparaat voor de eerste keer instellen.

1. Klik in Hyper-V-beheer > **virtual machines**met de rechter muisknop op de virtuele machine > **verbinding maken**.
2. Geef de taal, de tijd zone en het wacht woord op voor het apparaat.
3. Open een browser op een computer die verbinding kan maken met de virtuele machine en open de URL van de Web-App van het apparaat: **https:// *-apparaatnaam of IP-adres*: 44368**.

   U kunt de app ook vanuit het toestel bureau blad openen door te klikken op de snelkoppeling naar de app.
1. Ga als volgt te werk in de web-app > vereisten in te **stellen**:
    - **Licentie**: accepteer de licentie voorwaarden en lees de informatie van derden.
    - **Connectiviteit**: de app controleert of de virtuele machine toegang heeft tot internet. Als de virtuele machine gebruikmaakt van een proxy:
        - Klik op **proxy-instellingen**en geef het proxy adres en de luister poort op in de notatie http://ProxyIPAddress of http://ProxyFQDN.
        - Geef referenties op als de proxy verificatie nodig heeft.
        - Alleen HTTP-proxy wordt ondersteund.
    - **Tijd synchronisatie**: tijd wordt gecontroleerd. De tijd op het apparaat moet zijn gesynchroniseerd met internet tijd zodat de machine detectie goed werkt.
    - **Updates installeren**: Azure migrate server beoordeling controleert of de meest recente updates zijn geïnstalleerd op het apparaat.

### <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Klik op **Aanmelden**. Als deze niet wordt weer gegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
2. Meld u aan met uw Azure-referenties op het tabblad Nieuw.
    - Meld u aan met uw gebruikers naam en wacht woord.
    - Aanmelden met een pincode wordt niet ondersteund.
3. Nadat u zich hebt aangemeld, gaat u terug naar de web-app.
4. Selecteer het abonnement waarin het Azure Migrate-project is gemaakt. Selecteer vervolgens het project.
5. Geef een naam op voor het apparaat. De naam moet alfanumeriek zijn met 14 tekens of minder.
6. Klik op **registreren**.


### <a name="delegate-credentials-for-smb-vhds"></a>Referenties voor SMB-Vhd's delegeren

Als u virtuele harde schijven uitvoert op Smb's, moet u de overdracht van referenties van het apparaat naar de Hyper-V-hosts inschakelen. Als u dit wilt doen vanaf het apparaat:

1. Voer de volgende opdracht uit op de apparaat-VM. HyperVHost1/HyperVHost2 zijn voor beelden van hostnamen.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. U kunt dit ook doen in het Lokale groepsbeleidsobjecteditor op het apparaat:
    - Klik **in beleid voor lokale computer** > **Computer configuratie**op **Beheersjablonen** > **systeem** > **referenties voor delegeren**.
    - Dubbel klik op **delegeren van nieuwe referenties toestaan**en selecteer **ingeschakeld**.
    - Klik in **Opties**op **weer geven**en voeg elke Hyper-V-host die u wilt detecteren, toe aan de lijst met **wsman/** als voor voegsel.
    - Dubbel klik in het overdragen van **referenties**op het **delegeren van nieuwe referenties toestaan met NTLM-Server verificatie**. Voeg nogmaals elke Hyper-V-host die u wilt detecteren, toe aan de lijst met **wsman/** als voor voegsel.

## <a name="start-continuous-discovery"></a>Continue detectie starten

Maak verbinding van het apparaat met Hyper-V-hosts of-clusters en start de VM-detectie.

1. Geef bij **gebruikers naam** en **wacht woord**de account referenties op die door het apparaat worden gebruikt voor het detecteren van vm's. Geef een beschrijvende naam op voor de referenties en klik op **Details opslaan**.
2. Klik op **host toevoegen**en geef de gegevens van de Hyper-V-host/het cluster op.
3. Klik op **valideren**. Na validatie wordt het aantal Vm's weer gegeven dat op elke host/het cluster kan worden gedetecteerd.
    - Als de validatie voor een host mislukt, controleert u de fout door de muis aanwijzer boven het pictogram in de kolom **status** te bewegen. Los problemen op en valideer opnieuw.
    - Als u hosts of clusters wilt verwijderen, selecteert u > **verwijderen**.
    - U kunt een specifieke host niet verwijderen uit een cluster. U kunt alleen het hele cluster verwijderen.
    - U kunt een cluster toevoegen, zelfs als er problemen zijn met specifieke hosts in het cluster.
4. Klik na validatie op **opslaan en start de detectie** om het detectie proces te starten.

De detectie wordt gestart. Het duurt ongeveer 15 minuten voor de meta gegevens van gedetecteerde Vm's die in de Azure Portal worden weer gegeven.

## <a name="verify-vms-in-the-portal"></a>VM's verifiëren in de portal

Nadat de detectie is voltooid, kunt u controleren of de virtuele machines in de portal worden weer gegeven.

1. Open het Azure Migrate dash board.
2. Klik in **Azure migrate-Servers** > pagina **Azure migrate: Server beoordeling** op het pictogram met het aantal voor **gedetecteerde servers**.


## <a name="next-steps"></a>Volgende stappen

Probeer de [Hyper-V-evaluatie](tutorial-assess-hyper-v.md) uit met Azure migrate server-evaluatie.
