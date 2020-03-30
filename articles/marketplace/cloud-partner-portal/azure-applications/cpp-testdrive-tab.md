---
title: Azure Application biedt teststation | Azure Marketplace
description: Teststation configureren voor Azure Application-aanbieding op de Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: 2f35dd69c1f29350049ed8f62dd9cbb0e60a7718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289002"
---
# <a name="azure-applications-test-drive-tab"></a>Tabblad Teststation voor Azure-toepassingen

Gebruik het tabblad Teststation om uw klanten een testervaring te bieden.

## <a name="test-drive-benefits"></a>Voordelen voor testdrive

Het creëren van een proefervaring voor uw klanten is een best practice om ervoor te zorgen dat ze met vertrouwen kunnen kopen. Van de beschikbare testopties is Test Drive het meest effectief in het genereren van leads van hoge kwaliteit en een hogere conversie van deze leads.

Het biedt klanten een hands-on, self-guided trial van de belangrijkste functies en voordelen van uw product, gedemonstreerd in een real-world implementatie scenario.

## <a name="how-a-test-drive-works"></a>Hoe een proefrit werkt

Een potentiële klant zoekt en ontdekt uw toepassing op de Marketplace. De klant meldt zich aan en stemt in met de gebruiksvoorwaarden. Op dit moment ontvangt de klant uw vooraf geconfigureerde omgeving om te proberen voor een vast aantal uren, terwijl u een hooggekwalificeerde lead ontvangt om op te volgen. Zie [Wat is Testdrive voor](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive) meer informatie?

## <a name="setting-up-a-test-drive"></a>Een proefrit instellen

Gebruik de volgende stappen om een testrit in te schakelen en te configureren.

### <a name="to-enable-a-test-drive"></a>Ga als lid van de test:

1. Selecteer **onder Nieuwe aanbieding**het tabblad **Teststation.**
2. Selecteer **onder Testdrive**de optie **Ja** voor **Een teststation inschakelen**.

   ![Een proefrit inschakelen](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>Ga als lid van het werk om een teststation te configureren:

Nadat u een testrit hebt ingeschakeld, vult u de volgende formulieren in om de testrit in te stellen:
  
 - Details
 - Technische configuratie
 - Abonnementsgegevens voor implementatie van teststation

De volgende schermopname toont alle testdrive-formulieren. Een sterretje (*) dat aan de veldnaam is toegevoegd, geeft aan dat dit vereist is. 

![Een teststation configureren](./media/managed-app-configure-testdrive.png)

In de volgende tabel worden de velden beschreven die nodig zijn om de testrit voor uw beheerde toepassing in te stellen.  De velden die zijn toegevoegd met een sterretje zijn vereist.

|      Veld         |  Beschrijving      |
|  ---------------   |  ---------------  |
| **Beschrijving\***  |  Beschrijf wat er kan worden gedaan op uw proefrit. U basis-HTML-tags gebruiken om deze beschrijving op te maken. Bijvoorbeeld p &lt;&gt;, &lt;&gt;em &lt;&gt;, &lt;&gt;ul &lt;&gt;, li , ol en rubrieken.                |
| **Gebruikershandleiding\***  |  Upload een gebruikershandleiding die uw klanten kunnen gebruiken om door de Test Drive-ervaring te lopen. Dit document moet een .pdf-bestand zijn.    |
| **Demovideo teststation** |  Een optionele video-walkthrough van uw testrit. Een klant kan deze video bekijken voordat hij een proefrit maakt. Geef een URL aan de video op YouTube of Vimeo. Als u **+ Video toevoegen**selecteert, wordt u gevraagd de volgende informatie te verstrekken:<ul><li>Name</li><li>URL</li><li>Miniatuur (in PNG-indeling, 533 x 324 pixels)</li></ul>  |
| **exemplaren\***      | Configureer hoeveel exemplaren u wilt, in welke regio(s) en hoe snel uw klanten de testdrive kunnen krijgen. Zie [Een teststation publiceren](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive)voor meer informatie .           |
| **Duur van de testrit (uren)\*** | Voer een geheel getal in voor het aantal uren. Het toegestane bereik is van 1 tot 999. |
| **Arm-sjabloon teststation\***     | Upload een gecomprimeerd (.zip)-bestand met uw Azure Resource Manager-sjablonen voor uw app. Zie [Azure Resource Manager Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)voor meer informatie. |
| **Toegangsgegevens\***          | Geef toegangsinformatie nadat uw klant de testrit heeft opgedaan. Bijvoorbeeld een URL om toegang te krijgen tot de testrit en tekengegevens. . U basis-HTML-tags gebruiken om deze beschrijving op te maken. Bijvoorbeeld p &lt;&gt;, &lt;&gt;em &lt;&gt;, &lt;&gt;ul &lt;&gt;, li , ol en rubrieken. |
| **Azure-abonnements-id\***       | Hierdoor krijgt u toegang tot Azure-services en de Azure-portal. Het abonnement is waar resourcegebruik wordt gerapporteerd en services worden gefactureerd. Als u nog geen afzonderlijk Azure-abonnement voor teststations hebt, maakt u een abonnement.  |
| **Azure AD-tenant-id\***          | Geef een bestaande tenant op in Azure Active Directory of maak een tenant voor deze testrit.  |
| **Azure AD-app-id\***             | Een nieuwe toepassing maken en registreren. Microsoft gebruikt deze toepassing om bewerkingen uit te voeren op uw Test Drive-exemplaar.  |
| **Azure AD-app-sleutel\***            | Maak een verificatiesleutel voor de app en plak deze in dit veld.   |
|  |  |

Nadat u alle vereiste informatie hebt opgegeven, selecteert u **Opslaan** om de testrit in te stellen.


## <a name="next-steps"></a>Volgende stappen

[Tabblad voor Marketplace](./cpp-marketplace-tab.md)
