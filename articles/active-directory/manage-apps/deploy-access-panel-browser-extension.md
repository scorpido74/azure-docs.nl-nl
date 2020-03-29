---
title: Azure Access Panel Extension for IE implementeren met behulp van een GPO | Microsoft Documenten
description: Groepsbeleid gebruiken om de invoegtoepassing Internet Explorer te implementeren voor de mijn apps-portal.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71c342ede77349b3f6c22093e5877ad5f5ce6549
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67807688"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>How to: De extensie Access Panel voor Internet Explorer implementeren met groepsbeleid

In deze zelfstudie ziet u hoe u groepsbeleid gebruikt om de extensie Van het Toegangspaneel voor Internet Explorer op afstand te installeren op de machines van uw gebruikers. Deze extensie is vereist voor Internet Explorer-gebruikers die zich moeten aanmelden bij apps die zijn geconfigureerd met [een enkele aanmelding op basis van een wachtwoord.](what-is-single-sign-on.md#password-based-sso)

Het wordt aanbevolen dat beheerders de implementatie van deze extensie automatiseren. Anders moeten gebruikers de extensie zelf downloaden en installeren, wat gevoelig is voor gebruikersfouten en beheerdersmachtigingen vereist. Deze zelfstudie heeft betrekking op één methode voor het automatiseren van software-implementaties met behulp van groepsbeleid. [Meer informatie over groepsbeleid.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

De extensie Access Panel is ook beschikbaar voor [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) en [Firefox,](https://go.microsoft.com/fwLink/?LinkID=626998)waarvoor geen beheerdersmachtigingen nodig zijn om te installeren.

## <a name="prerequisites"></a>Vereisten

* U hebt [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)ingesteld en u hebt de machines van uw gebruikers bij uw domein gevoegd.
* U moet de machtiging 'Instellingen bewerken' hebben om het groepsbeleidsobject (GPO) te bewerken. Standaard hebben leden van de volgende beveiligingsgroepen deze toestemming: domeinbeheerders, bedrijfsbeheerders en eigenaren van groepsbeleidseigenaren. [Meer informatie.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Stap 1: Het distributiepunt maken

Eerst moet u het installatiepakket op een netwerklocatie plaatsen die toegankelijk is voor de machines waarop u de extensie op afstand wilt installeren. Voer de volgende stappen uit om dit te doen:

1. Meld u aan bij de server als beheerder.
1. Ga in het venster **Serverbeheer** naar **Bestanden en opslagservices**.

    ![Bestanden en opslagservices openen](./media/deploy-access-panel-browser-extension/files-services.png)

1. Ga naar het tabblad **Aandelen.** Klik vervolgens op **Taken** > **Nieuw delen...**

    ![Schermafbeelding toont waar u Nieuw delen vinden in het scherm Taken](./media/deploy-access-panel-browser-extension/shares.png)

1. Vul de **wizard Nieuwe delen in** en stel machtigingen in om ervoor te zorgen dat deze toegankelijk is vanaf de machines van uw gebruikers. [Meer informatie over aandelen.](https://technet.microsoft.com/library/cc753175.aspx)
1. Download het volgende Microsoft Windows Installer-pakket (.msi-bestand): [Access Panel Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
1. Kopieer het installatiepakket naar een gewenste locatie op het aandeel.

    ![Het .msi-bestand naar het aandeel kopiëren](./media/deploy-access-panel-browser-extension/copy-package.png)

1. Controleer of uw clientmachines vanuit het aandeel toegang hebben tot het installatiepakket.

## <a name="step-2-create-the-group-policy-object"></a>Stap 2: Het groepsbeleidsobject maken

1. Meld u aan bij de server die uw AD DS-installatie (Active Directory Domain Services) host.
1. Ga in Serverbeheer naar**Beleidbeheer** **tools** > .

    ![Ga naar Tools > Groepsbeleidsbeheer](./media/deploy-access-panel-browser-extension/tools-gpm.png)

1. Bekijk in het linkerdeelvenster van het venster **Groepsbeleidsbeheer** de hiërarchie van de organisatie-eenheid (OU) en bepaal op welk bereik u het groepsbeleid wilt toepassen. U bijvoorbeeld besluiten om een kleine organisatievoor u te kiezen om te implementeren voor een paar gebruikers voor het testen, of u een top-level OU kiezen om te implementeren in uw hele organisatie.

   > [!NOTE]
   > Als u uw organisatie-eenheden (OB's) wilt maken of bewerken, schakelt u terug naar Serverbeheer en gaat u naar **Hulpmiddelen** > **Active Directory- gebruikers en computers**.

1. Zodra u een organisatie-eenheid hebt geselecteerd, klikt u er met de rechtermuisknop op en selecteert **u Een GPO maken in dit domein en deze hier koppelen...**

    ![Schermafbeelding van de optie Een nieuwe GPO maken](./media/deploy-access-panel-browser-extension/create-gpo.png)

1. Typ in de **nieuwe GPO-prompt** een naam voor het nieuwe groepsbeleidsobject.
1. Klik met de rechtermuisknop op het groepsbeleidsobject dat u hebt gemaakt en selecteer **Bewerken**.

## <a name="step-3-assign-the-installation-package"></a>Stap 3: Het installatiepakket toewijzen

1. Bepaal of u de extensie wilt implementeren op basis **van computerconfiguratie** of **gebruikersconfiguratie.** Bij gebruik van [computerconfiguratie](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx)wordt de extensie op de computer geïnstalleerd, ongeacht op welke gebruikers zich aanmelden. Met [gebruikersconfiguratie](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx)hebben gebruikers de extensie voor hen geïnstalleerd, ongeacht op welke computers ze zich aanmelden.
1. Ga in het linkerdeelvenster van het venster **Groepsbeleidsbeheereditor** naar een van de volgende mappaden, afhankelijk van het type configuratie dat u hebt gekozen:

   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`

1. Klik met de rechtermuisknop op **Software-installatie**en selecteer **Nieuw** > **pakket...**
1. Ga naar de gedeelde map met het installatiepakket uit [stap 1: Maak het distributiepunt,](#step-1-create-the-distribution-point)selecteer het .msi-bestand en klik op **Openen**.

   > [!IMPORTANT]
   > Als het aandeel zich op dezelfde server bevindt, controleert u of u de .msi opent via het netwerkbestandspad, in plaats van het lokale bestandspad.

    ![Het installatiepakket selecteren in de gedeelde map](./media/deploy-access-panel-browser-extension/select-package.png)

1. Selecteer **toegewezen** voor uw implementatiemethode in de prompt **Software** implementeren. Klik vervolgens op **OK**.

De extensie wordt nu geïmplementeerd in de organisatie-eenheid die u hebt geselecteerd. [Meer informatie over groepsbeleidssoftware-installatie.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Stap 4: De extensie automatisch inschakelen voor Internet Explorer

Naast het uitvoeren van het installatieprogramma moet elke extensie voor Internet Explorer expliciet worden ingeschakeld voordat deze kan worden gebruikt. Volg de onderstaande stappen om de extensie van het toegangspaneel in te schakelen met groepsbeleid:

1. Ga in het venster **Groepsbeleidsbeheereditor** naar een van de volgende paden, afhankelijk van welk type configuratie u hebt gekozen in [stap 3: Het installatiepakket toewijzen:](#step-3-assign-the-installation-package)

   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

1. Klik met de rechtermuisknop **op Lijst toevoegen**en selecteer **Bewerken**.

    ![Klik met de rechtermuisknop op 'Lijst toevoegen' en selecteer 'Bewerken'](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)

1. Selecteer **ingeschakeld**in het venster **Lijst toevoegen** . Klik vervolgens onder de sectie **Opties** op **Weergeven...**.

    ![Klik op Inschakelen en klik vervolgens op Weergeven...](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)

1. Voer in het venster **Inhoud weergeven** de volgende stappen uit:

   1. Kopieer en plak de volgende klasse-id voor de eerste kolom (het veld **Waardenaam):**`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   1. Typ voor de tweede kolom (het veld **Waarde)** de volgende waarde:`1`
   1. Klik op **OK** om het venster **Inhoud weergeven** te sluiten.

      ![De waarden invullen zoals opgegeven in de vorige stap](./media/deploy-access-panel-browser-extension/show-contents.png)

1. Klik op **OK** om de wijzigingen toe te passen en het venster **Lijst toevoegen te** sluiten.

De extensie moet nu worden ingeschakeld voor de machines in de geselecteerde ou. [Meer informatie over het gebruik van groepsbeleid om invoegtoepassingen van Internet Explorer in te schakelen of uit te schakelen.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Stap 5 (optioneel): Prompt 'Wachtwoord onthouden' uitschakelen

Wanneer gebruikers zich aanmelden bij websites met de extensie Access Panel, kan Internet Explorer de volgende prompt weergeven met de vraag 'Wilt u uw wachtwoord opslaan?'

![Toont de "Wilt u uw wachtwoord opslaan..." Prompt](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

Als u wilt voorkomen dat uw gebruikers deze prompt kunnen zien, volgt u de onderstaande stappen om te voorkomen dat automatisch aanvullen wachtwoorden onthoudt:

1. Ga in het venster **Groepsbeleidsbeheereditor** naar het onderstaande pad. Deze configuratie-instelling is alleen beschikbaar onder **Gebruikersconfiguratie**.

   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
1. Zoek de instelling met de naam **Schakel de functie Automatisch aanvullen in voor gebruikersnamen en wachtwoorden op formulieren.**

   > [!NOTE]
   > Eerdere versies van Active Directory kunnen deze instelling met de naam **Niet toestaan dat automatisch aanvullen wachtwoorden opslaat.** De configuratie voor die instelling verschilt van de instelling die in deze zelfstudie wordt beschreven.

    ![Vergeet niet om dit te zoeken onder Gebruikersinstellingen](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)

1. Klik met de rechtermuisknop op de bovenstaande instelling en selecteer **Bewerken**.
1. Selecteer **Uitgeschakeld**in het venster getiteld **De functie Automatisch aanvullen inschakelen voor gebruikersnamen en wachtwoorden op formulieren**.

    ![Selecteer de optie 'Uitgeschakeld' voor de functie Automatisch aanvullen inschakelen](./media/deploy-access-panel-browser-extension/disable-passwords.png)

1. Klik op **OK** om deze wijzigingen toe te passen en het venster te sluiten.

Gebruikers kunnen hun referenties niet langer opslaan of automatisch aanvullen gebruiken om toegang te krijgen tot eerder opgeslagen referenties. Met dit beleid kunnen gebruikers echter automatisch aanvullen blijven gebruiken voor andere typen formuliervelden, zoals zoekvelden.

> [!WARNING]
> Als dit beleid is ingeschakeld nadat gebruikers ervoor hebben gekozen om bepaalde referenties op te slaan, wordt in dit beleid de referenties *niet* gewist die al zijn opgeslagen.

## <a name="step-6-testing-the-deployment"></a>Stap 6: De implementatie testen

Volg de onderstaande stappen om te controleren of de implementatie van de extensie is geslaagd:

1. Als u **computerconfiguratie**hebt geïmplementeerd, meldt u zich aan bij een clientmachine die behoort tot de organisatie-eenheid die u hebt geselecteerd in [stap 2: Het groepsbeleidsobject maken](#step-2-create-the-group-policy-object). Als u bent geïmplementeerd met **gebruikersconfiguratie,** moet u zich aanmelden als een gebruiker die tot die organisatie is.
1. Er kunnen enkele aanmeldingen nodig zijn om de groepsbeleidswijzigingen volledig bij te werken met deze machine. Als u de update wilt forceren, opent u een **opdrachtpromptvenster** en voert u de volgende opdracht uit:`gpupdate /force`
1. U moet de machine opnieuw opstarten om de installatie te laten plaatsvinden. Opstarten kan aanzienlijk meer tijd in beslag nemen dan normaal terwijl de extensie wordt geïnstalleerd.
1. Open **Internet Explorer**na het opnieuw opstarten . Klik in de rechterbovenhoek van het venster op **Extra** (het tandwielpictogram) en selecteer **Vervolgens Invoegtoepassingen beheren**.
1. Controleer in het venster **Invoegtoepassingen beheren** of de extensie van het **toegangspaneel** is geïnstalleerd en of de **status** is ingesteld op **Ingeschakeld**.

   ![Controleren of de extensie van het toegangspaneel is geïnstalleerd en ingeschakeld](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="learn-more"></a>Meer informatie

* [Toepassingstoegang en één aanmelding met Azure Active Directory](what-is-single-sign-on.md)
* [Problemen met de access panelextensie voor Internet Explorer oplossen](manage-access-panel-browser-extension.md)
