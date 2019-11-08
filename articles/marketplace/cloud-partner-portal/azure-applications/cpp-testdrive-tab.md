---
title: Azure-toepassing aanbieding test drive | Azure Marketplace
description: Test drive voor Azure-toepassing aanbieding configureren op de Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 3855c600fe35c37ac15783995551a769e00532be
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826117"
---
# <a name="azure-applications-test-drive-tab"></a>Het tabblad test station van Azure-toepassingen

Gebruik het tabblad test drive om uw klanten een proef ervaring te bieden.

## <a name="test-drive-benefits"></a>Schijf voordelen testen

Het maken van een proef ervaring voor uw klanten is een best practice om ervoor te zorgen dat ze met vertrouwen kunnen kopen. Van de beschik bare proef opties is het testen van het station het meest effectief bij het genereren van leads van hoge kwaliteit en een verhoogde conversie van deze leads.

Het biedt klanten een praktijk gerichte, zelf-begeleide proef versie van de belangrijkste functies en voor delen van uw product, zoals wordt getoond in een scenario voor een implementatie.

## <a name="how-a-test-drive-works"></a>Hoe een test drive werkt

Een potentiële klant zoekt en detecteert uw toepassing op Marketplace. De klant meldt zich aan en gaat akkoord met de gebruiks voorwaarden. Op dit moment ontvangt de klant uw vooraf geconfigureerde omgeving om een vast aantal uur te proberen, terwijl u een zeer gekwalificeerde potentiële Lead krijgt om te volgen. Zie [Wat is een test station?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive) voor meer informatie.

## <a name="setting-up-a-test-drive"></a>Instellen van een test drive

Gebruik de volgende stappen om een test drive in te scha kelen en te configureren.

### <a name="to-enable-a-test-drive"></a>Een test drive inschakelen:

1. Selecteer onder **nieuwe aanbieding**het tabblad **test station** .
2. Onder **test drive**selecteert u **Ja** voor **een test station inschakelen**.

   ![Een test drive inschakelen](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>Een test drive configureren:

Nadat u een test drive hebt ingeschakeld, vult u de volgende formulieren in om de test drive in te stellen:
  
 - Details
 - Technische configuratie
 - Details van implementatie abonnement testen

In de volgende scherm opname worden alle formulieren van het test station weer gegeven. Een asterisk (*) die wordt toegevoegd aan de veld naam geeft aan dat deze vereist is. 

![Een test drive configureren](./media/managed-app-configure-testdrive.png)

De volgende tabel beschrijft de vereiste velden voor het instellen van de test drive voor uw beheerde toepassing.  De velden die zijn toegevoegd met een sterretje zijn vereist.

|      Veld         |  Beschrijving      |
|  ---------------   |  ---------------  |
| **Beschrijving\***  |  Beschrijf wat er op uw test station kan worden uitgevoerd. U kunt basis-HTML-tags gebruiken om deze beschrijving op te maken. U kunt bijvoorbeeld &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;Li&gt;, &lt;OL&gt;en koppen.                |
| **Gebruikers handleiding\***  |  Upload een gebruikers handleiding die uw klanten kunnen gebruiken om de ervaring van het test station te door lopen. Dit document moet een PDF-bestand zijn.    |
| **Demo video testen** |  Een optionele video-overzicht van uw test station. Een klant kan deze video bekijken voordat ze een test drive nemen. Geef een URL naar de video op YouTube of Vimeo. Als u **+ video toevoegen**selecteert, wordt u gevraagd de volgende gegevens op te geven:<ul><li>Naam</li><li>URL</li><li>Miniatuur (in PNG-indeling, 533 x 324 pixels)</li></ul>  |
| **Instanties\***      | Configureer het aantal instanties dat u wilt, in welke regio (en) en hoe snel uw klanten het test station kunnen verkrijgen. Zie [een test station publiceren](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive)voor meer informatie.           |
| **Duur van test drive (uren)\*** | Voer een geheel getal in voor het aantal uren. Het toegestane bereik is 1 tot en met 999. |
| **\* van ARM-sjabloon testen**     | Upload een gecomprimeerd bestand (. zip) met uw Azure Resource Manager sjablonen voor uw app. Zie [Azure Resource Manager test station](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)voor meer informatie. |
| **Toegangs gegevens\***          | Geef de toegangs gegevens op nadat de klant het test station heeft opgehaald. Bijvoorbeeld een URL voor toegang tot de test drive en onderteken informatie. . U kunt basis-HTML-tags gebruiken om deze beschrijving op te maken. U kunt bijvoorbeeld &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;Li&gt;, &lt;OL&gt;en koppen. |
| **Azure-abonnements-id\***       | Hiermee wordt toegang verleend aan Azure-Services en de Azure Portal. In het abonnement wordt het resource gebruik gerapporteerd en de services worden gefactureerd. Als u nog geen apart Azure-abonnement voor test stations hebt, maakt u een abonnement.  |
| **Azure AD-Tenant-id\***          | Geef een bestaande Tenant op in Azure Active Directory of maak een Tenant voor deze test drive.  |
| **Azure AD-app-id\***             | Een nieuwe toepassing maken en registreren. Micro soft gebruikt deze toepassing voor het uitvoeren van bewerkingen op uw exemplaar van het test station.  |
| **Azure AD-app sleutel\***            | Maak een verificatie sleutel voor de app en plak deze in dit veld.   |
|  |  |

Nadat u alle vereiste gegevens hebt opgegeven, selecteert u **Opslaan** om het instellen van de test drive te volt ooien.


## <a name="next-steps"></a>Volgende stappen

[Tabblad voor Marketplace](./cpp-marketplace-tab.md)
