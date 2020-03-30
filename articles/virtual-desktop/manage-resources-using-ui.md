---
title: Beheerbeheerhulpprogramma implementeren met een Azure Resource Manager-sjabloon - Azure
description: Een hulpprogramma voor de gebruikersinterface installeren met een Azure Resource Manager-sjabloon om Windows Virtual Desktop-bronnen te beheren.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dac17da4aa3d3d73ccbbc101cba4326a17fec9b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127781"
---
# <a name="deploy-a-management-tool-with-an-azure-resource-manager-template"></a>Een beheerhulpprogramma implementeren met een Azure Resource Manager-sjabloon

In de instructies in dit artikel vindt u hoe u de gebruikersinterface implementeert met behulp van een Azure Resource Manager-sjabloon.

## <a name="important-considerations"></a>Belangrijke overwegingen

Aangezien de app toestemming nodig heeft om te communiceren met Windows Virtual Desktop, biedt deze tool geen ondersteuning voor B2B-scenario's (Business-to-Business). Het abonnement van elke Azure Active Directory -tenant (AAD) heeft een eigen afzonderlijke implementatie van het beheerprogramma nodig.

Deze beheertool is een voorbeeld. Microsoft zal belangrijke beveiligings- en kwaliteitsupdates bieden. De [broncode is beschikbaar in GitHub.](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy) Klanten en partners worden aangemoedigd om de tool aan te passen aan hun zakelijke behoeften.

Naar volgende browsers zijn compatibel met het beheertool:
- Google Chrome 68 of hoger
- Microsoft Edge 40.15063 of hoger
- Mozilla Firefox 52.0 of hoger
- Safari 10 of hoger (alleen macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Wat u nodig hebt om de beheertool te implementeren

Voordat u het beheerprogramma implementeert, hebt u een Azure AD-gebruiker (Azure Directory) nodig om een app-registratie te maken en de beheer-gebruikersinterface te implementeren. Deze gebruiker moet:

- Azure Multi-Factor Authentication (MFA) uitgeschakeld
- Hebt toestemming om resources te maken in uw Azure-abonnement
- Heb toestemming om een Azure AD-toepassing te maken. Volg deze stappen om te controleren of uw gebruiker over de vereiste machtigingen beschikt door de instructies in [Vereiste machtigingen te](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)volgen.

Nadat u de beheertool hebt geïmplementeerd en geconfigureerd, raden we u aan een gebruiker te vragen de beheer-gebruikersinterface te starten om ervoor te zorgen dat alles werkt. De gebruiker die de beheer-gebruikersinterface start, moet een roltoewijzing hebben waarmee hij of zij de Windows Virtual Desktop-tenant kan bekijken of bewerken.

## <a name="deploy-the-management-tool"></a>Hulpprogramma voor beheer implementeren

Controleer voordat u begint of de server- en client-apps toestemming hebben door naar de [windows-toestemmingspagina voor virtuele bureaubladen](https://rdweb.wvd.microsoft.com) te gaan voor de vertegenwoordigde Azure Active Directory (AAD).

Volg deze instructies om de azure resource beheersjabloon te implementeren:

1. Ga naar de [pagina GitHub Azure RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Implementeer de sjabloon naar Azure.
    - Als u een Enterprise-abonnement implementeert, schuift u omlaag en selecteert u **Implementeren naar Azure.** 
    - Als u een abonnement in een Cloud Solution Provider-abonnement implementeert, volgt u de volgende instructies om te implementeren in Azure:
        1. Schuif omlaag en klik met de rechtermuisknop op **Implementeren naar Azure**en selecteer Vervolgens **Koppelingslocatie kopiëren**.
        2. Open een teksteditor zoals Kladblok en plak de koppeling daar.
        3. Direct <https://portal.azure.com/> na en vóór de hashtag (#) voer u een bijteken (@) in, gevolgd door de tenantdomeinnaam. Hier is een voorbeeld van <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>het formaat: .
        4. Meld u aan bij de Azure-portal als gebruiker met machtigingen voor beheerders/inzenderen bij het abonnement van de Cloud Solution Provider.
        5. Plak de koppeling die u naar de teksteditor hebt gekopieerd in de adresbalk.
3. Ga bij het invoeren van de parameters als volgt te werk:
    - Selecteer **false**voor de parameter **isServicePrincipal** .
    - Voer voor de referenties uw Azure AD-referenties in waarbij meervoudige verificatie is uitgeschakeld. Deze referenties worden gebruikt om de Azure AD-toepassing en Azure-bronnen te maken. Zie Voor meer informatie de [actie Wat u nodig hebt om de beheertool te implementeren.](#what-you-need-to-deploy-the-management-tool)
    - Gebruik voor **de applicationName**een unieke naam voor uw app die wordt geregistreerd in uw Azure Active Directory. Deze naam wordt ook gebruikt voor de URL van de web-app. U bijvoorbeeld een naam als 'Apr3UX' gebruiken.
4. Zodra u de parameters opgeeft, accepteert u de algemene voorwaarden en selecteert u **Aankoop**.

## <a name="provide-consent-for-the-management-tool"></a>Toestemming geven voor de beheertool

Nadat de sjabloon GitHub Azure Resource Manager is voltooid, vindt u een brongroep met twee app-services, samen met één app-serviceplan in de Azure-portal.

Voordat u zich aanmeldt en het beheerprogramma gebruikt, moet u toestemming geven voor de nieuwe Azure AD-toepassing die is gekoppeld aan het beheerprogramma. Als u toestemming geeft, kan de beheertool windows Virtual Desktop-beheeroproepen doen namens de gebruiker die momenteel is aangemeld bij de tool.

![Een schermafbeelding met de machtigingen die worden verstrekt wanneer u instemt met de ui-beheertool.](media/management-ui-delegated-permissions.png)

Als u wilt bepalen welke gebruiker u gebruiken om u bij het hulpprogramma aan te melden, gaat u naar de [pagina gebruikersinstellingen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) van Azure Active Directory en neemt u nota van de waarde voor **gebruikers die kunnen instemmen met apps die namens hen toegang krijgen tot bedrijfsgegevens.**

![Een screenshot die laat zien of gebruikers alleen toestemming kunnen verlenen voor toepassingen voor hun gebruiker.](media/management-ui-user-consent-allowed.png)

- Als de waarde is ingesteld op **Ja,** u zich aanmelden met een gebruikersaccount in de Azure Active Directory en alleen toestemming geven voor die gebruiker. Als u zich echter later bij het beheerprogramma aanmeldt bij een andere gebruiker, moet u dezelfde toestemming opnieuw uitvoeren.
- Als de waarde is ingesteld op **Nee,** moet u zich aanmelden als globale beheerder in de Azure Active Directory en beheerderstoestemming geven voor alle gebruikers in de map. Geen andere gebruikers zullen worden geconfronteerd met een toestemming prompt.


Zodra u beslist welke gebruiker u gebruikt om toestemming te geven, volgt u deze instructies om toestemming te geven voor de tool:

1. Ga naar uw Azure-resources, selecteer de Azure App Services-bron met de naam die u in de sjabloon hebt opgegeven (bijvoorbeeld Apr3UX) en navigeer naar de URL die eraan is gekoppeld; bijvoorbeeld . <https://rdmimgmtweb-210520190304.azurewebsites.net>
2. Meld u aan met het juiste Azure Active Directory-gebruikersaccount.
3. Als u bent geverifieerd met een globale beheerder, u nu het selectievakje voor **toestemming namens uw organisatie**inschakelen. Selecteer **Accepteren** om toestemming te geven.
   
   ![Een schermafbeelding met de volledige toestemmingspagina die de gebruiker of beheerder zal zien.](media/management-ui-consent-page.png)

Dit brengt u nu naar de managementtool.

## <a name="use-the-management-tool"></a>Het beheergereedschap gebruiken

Nadat u toestemming hebt gegeven voor de organisatie of voor een bepaalde gebruiker, heeft u op elk gewenst moment toegang tot de beheertool.

Volg deze instructies om de tool te starten:

1. Selecteer de Azure App Services-bron met de naam die u in de sjabloon hebt opgegeven (bijvoorbeeld Apr3UX) en navigeer naar de URL die eraan is gekoppeld; bijvoorbeeld . <https://rdmimgmtweb-210520190304.azurewebsites.net>
2. Meld u aan met uw Windows Virtual Desktop-referenties.
3. Wanneer u wordt gevraagd een tenantgroep te kiezen, selecteert u **Standaardtenantgroep** in de vervolgkeuzelijst.
4. Wanneer u **Standaardtenantgroep**selecteert, moet er een menu aan de linkerkant van het venster worden weergegeven. Zoek in dit menu de naam van uw tenantgroep en selecteer deze.
  
  > [!NOTE]
  > Als u een aangepaste tenantgroep hebt, voert u de naam handmatig in in plaats van te kiezen uit de vervolgkeuzelijst.

## <a name="report-issues"></a>Problemen melden

Als u problemen tegenkomt met het beheerprogramma of andere Windows Virtual Desktop-hulpprogramma's, volgt u de aanwijzingen in [Azure Resource Manager-sjablonen voor Extern bureaublad-services](https://github.com/Azure/RDS-Templates/blob/master/README.md) om deze op GitHub te rapporteren.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u de beheertool implementeren en verbinden, u leren hoe u azure servicehelp gebruiken om serviceproblemen en gezondheidsadviezen te controleren. Zie de [zelfstudie Servicewaarschuwingen instellen](./set-up-service-alerts.md)voor meer informatie.