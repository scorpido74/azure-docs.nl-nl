---
title: Azure AD Connect - Het TLS/SSL-certificaat bijwerken voor een AD FS-farm | Microsoft Documenten
description: In dit document worden de stappen beschreven om het TLS/SSL-certificaat van een AD FS-farm bij te werken met Azure AD Connect.
services: active-directory
manager: daveba
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/09/2018
ms.subservice: hybrid
author: billmath
ms.custom: seohack1
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cc768162d98402fe52b52b2826a9dbf2840a581
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331741"
---
# <a name="update-the-tlsssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Het TLS/SSL-certificaat bijwerken voor een AD FS-farm (Active Directory Federation Services)

## <a name="overview"></a>Overzicht
In dit artikel wordt beschreven hoe u Azure AD Connect gebruiken om het TLS/SSL-certificaat voor een AD FS-farm (Active Directory Federation Services) bij te werken. U het azure AD Connect-hulpprogramma gebruiken om het TLS/SSL-certificaat voor de AD FS-farm eenvoudig bij te werken, zelfs als de geselecteerde gebruikersaanmeldingsmethode geen AD FS is.

U de hele bewerking van het bijwerken van TLS/SSL-certificaat voor de AD FS-farm op alle WAP-servers (Federation and Web Application Proxy) uitvoeren in drie eenvoudige stappen:

![Drie stappen](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>Zie Inzicht in certificaten die worden gebruikt door AD FS voor meer informatie over certificaten die worden gebruikt door [AD FS.](https://technet.microsoft.com/library/cc730660.aspx)

## <a name="prerequisites"></a>Vereisten

* **AD FS-farm**: controleer of uw AD FS-farm Windows Server 2012 R2 of hoger is.
* **Azure AD Connect:** controleer of de versie van Azure AD Connect 1.1.553.0 of hoger is. U gebruikt het **AD FS SSL-certificaat voor taakbijwerken.**

![TLS-taak bijwerken](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Stap 1: Informatie geven over AD FS-boerderij

Azure AD Connect probeert automatisch informatie over de AD FS-farm te verkrijgen door:
1. De farminformatie opvragen vanuit AD FS (Windows Server 2016 of hoger).
2. Verwijzing naar de informatie uit eerdere uitvoeringen, die lokaal worden opgeslagen met Azure AD Connect.

U de lijst met servers wijzigen die worden weergegeven door de servers toe te voegen of te verwijderen om de huidige configuratie van de AD FS-farm weer te geven. Zodra de servergegevens zijn verstrekt, geeft Azure AD Connect de verbindings- en huidige TLS/SSL-certificaatstatus weer.

![Ad FS-servergegevens](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

Als de lijst een server bevat die geen deel meer uitmaakt van de AD FS-farm, klikt u op **Verwijderen** om de server te verwijderen uit de lijst met servers in uw AD FS-farm.

![Offlineserver in lijst](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Het verwijderen van een server uit de lijst met servers voor een AD FS-farm in Azure AD Connect is een lokale bewerking en werkt de informatie bij voor de AD FS-farm die Azure AD Connect lokaal onderhoudt. Azure AD Connect wijzigt de configuratie op AD FS niet om de wijziging weer te geven.    

## <a name="step-2-provide-a-new-tlsssl-certificate"></a>Stap 2: Een nieuw TLS/SSL-certificaat verstrekken

Nadat u de informatie over AD FS-farmservers hebt bevestigd, vraagt Azure AD Connect om het nieuwe TLS/SSL-certificaat. Geef een met een wachtwoord beveiligd PFX-certificaat op om de installatie voort te zetten.

![TLS/SSL-certificaat](./media/how-to-connect-fed-ssl-update/certificate.png)

Nadat u het certificaat hebt verstrekt, ondergaat Azure AD Connect een reeks vereisten. Controleer het certificaat om te controleren of het certificaat correct is voor het AD FS-farm:

-   De naam van het onderwerp/het alternatieve onderwerp voor het certificaat is dezelfde als de naam van de federatieservice of het is een wildcardcertificaat.
-   Het certificaat is langer dan 30 dagen geldig.
-   De vertrouwensketen van certificaten is geldig.
-   Het certificaat is beveiligd met een wachtwoord.

## <a name="step-3-select-servers-for-the-update"></a>Stap 3: Servers selecteren voor de update

Selecteer in de volgende stap de servers die het TLS/SSL-certificaat moeten hebben bijgewerkt. Servers die offline zijn, kunnen niet worden geselecteerd voor de update.

![Servers selecteren om bij te werken](./media/how-to-connect-fed-ssl-update/selectservers.png)

Nadat u de configuratie hebt voltooid, geeft Azure AD Connect het bericht weer dat de status van de update aangeeft en biedt een optie om de AD FS-aanmelding te verifiëren.

![Configuratie voltooid](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Veelgestelde vragen

* **Wat moet de onderwerpnaam zijn van het certificaat voor het nieuwe AD FS TLS/SSL-certificaat?**

    Azure AD Connect controleert of de naam van het onderwerp/het alternatieve onderwerp van het certificaat de naam van de federatieservice bevat. Als uw federatieservicenaam bijvoorbeeld fs.contoso.com is, moet de naam van het onderwerp/het alternatieve onderwerp worden fs.contoso.com.  Wildcardcertificaten worden ook geaccepteerd.

* **Waarom word ik opnieuw om referenties gevraagd op de WAP-serverpagina?**

    Als de referenties die u verstrekt voor het maken van verbinding met AD FS-servers niet ook de bevoegdheid hebben om de WAP-servers te beheren, vraagt Azure AD Connect om referenties met beheerdersbevoegdheden op de WAP-servers.

* **De server wordt als offline weergegeven. Wat moet ik doen?**

    Azure AD Connect kan geen bewerking uitvoeren als de server offline is. Als de server deel uitmaakt van de AD FS-farm, controleert u de verbinding met de server. Nadat u het probleem hebt opgelost, drukt u op het vernieuwingspictogram om de status in de wizard bij te werken. Als de server eerder deel uitmaakte van de farm, maar nu niet meer bestaat, klikt u op **Verwijderen** om deze te verwijderen uit de lijst met servers die Azure AD Connect onderhoudt. Als u de server uit de lijst in Azure AD Connect verwijdert, wordt de AD FS-configuratie zelf niet gewijzigd. Als u AD FS gebruikt in Windows Server 2016 of hoger, blijft de server in de configuratie-instellingen staan en wordt deze de volgende keer dat de taak wordt uitgevoerd opnieuw weergegeven.

* **Kan ik een subset van mijn farmservers bijwerken met het nieuwe TLS/SSL-certificaat?**

    Ja. U het **SSL-certificaat voor taak-update** altijd opnieuw uitvoeren om de resterende servers bij te werken. Op de pagina **Servern voor SSL-certificaatupdate** u de lijst met servers op **ssl-vervaldatum** sorteren om eenvoudig toegang te krijgen tot de servers die nog niet zijn bijgewerkt.

* **Ik verwijderde de server in de vorige run, maar het wordt nog steeds weergegeven als offline en vermeld op de AD FS-servers pagina. Waarom is de offline server er nog steeds, zelfs nadat ik hem heb verwijderd?**

    Als u de server uit de lijst in Azure AD Connect verwijdert, wordt deze niet verwijderd in de AD FS-configuratie. Azure AD Connect verwijst naar AD FS (Windows Server 2016 of hoger) voor alle informatie over de farm. Als de server nog steeds aanwezig is in de AD FS-configuratie, wordt deze weer in de lijst weergegeven.  

## <a name="next-steps"></a>Volgende stappen

- [Azure AD Connect en federatie](how-to-connect-fed-whatis.md)
- [Beheer en aanpassing van Active Directory Federation Services met Azure AD Connect](how-to-connect-fed-management.md)

