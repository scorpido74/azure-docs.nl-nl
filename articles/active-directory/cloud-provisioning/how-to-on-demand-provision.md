---
title: Inrichting van inrichten op aanvraag in Azure AD Connect Cloud
description: In dit artikel wordt de functie voor het inrichten op aanvraag beschreven.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72623d69f9fb0cbc68758a362bb977e5e02d2c00
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91637170"
---
# <a name="azure-ad-connect-cloud-provisioning-on-demand-provisioning"></a>Inrichting van inrichten op aanvraag in Azure AD Connect Cloud

Azure AD Connect Cloud inrichting heeft een nieuwe functie geïntroduceerd, waarmee u configuratie wijzigingen kunt testen door deze wijzigingen toe te passen op één gebruiker.  U kunt dit gebruiken om te valideren en te controleren of de wijzigingen die zijn aangebracht in de configuratie correct zijn toegepast en correct worden gesynchroniseerd met Azure AD.  

> [!IMPORTANT] 
> Wanneer u inrichtings op aanvraag gebruikt, worden de bereik filters niet toegepast op de gebruiker die u hebt geselecteerd.  Dit betekent dat u on-demand inrichten kunt gebruiken voor gebruikers buiten de organisatie-eenheden die u hebt opgegeven.


## <a name="using-on-demand-provisioning"></a>Inrichten op aanvraag gebruiken
Volg de onderstaande stappen om de nieuwe functie te gebruiken.


1.  Selecteer in Azure Portal **Azure Active Directory**.
2.  Selecteer **Azure AD Connect**.
3.  Selecteer **inrichting beheren**.

    ![Inrichting beheren](media/how-to-configure/manage1.png)
4. Selecteer uw configuratie onder **configuratie**.
5. Klik onder **valideren** op de knop **een gebruiker inrichten** . 

 ![Een gebruiker inrichten](media/how-to-on-demand-provision/on-demand2.png)

6. In het inrichtings scherm op aanvraag.  Voer de **DN-naam** van een gebruiker in en klik op de knop **inrichten** .  
 
 ![Op aanvraag inrichten](media/how-to-on-demand-provision/on-demand3.png)
7. Zodra het proces is voltooid, ziet u een geslaagd scherm en vier groene selectie vakjes die aangeven dat het is ingericht.  Eventuele fouten worden aan de linkerkant weer gegeven.

  ![Geslaagd](media/how-to-on-demand-provision/on-demand4.png)

Nu kunt u de gebruiker bekijken en bepalen of de wijzigingen die u in de configuratie hebt aangebracht, zijn toegepast.  In de rest van dit document worden de afzonderlijke secties beschreven die worden weer gegeven in de details van een gesynchroniseerde gebruiker.

## <a name="import-user-details"></a>Gebruikers gegevens importeren
Deze sectie bevat informatie over de gebruiker die is geïmporteerd uit Active Directory.  Dit is wat de gebruiker ziet voordat deze wordt ingericht in azure AD.  Klik op de koppeling **Details weer geven** om deze informatie weer te geven.

![Gebruiker importeren](media/how-to-on-demand-provision/on-demand5.png)

Aan de hand van deze informatie kunt u de verschillende kenmerken en hun waarden bekijken die zijn geïmporteerd.  Als u een aangepaste kenmerk toewijzing hebt gemaakt, kunt u hier de waarde zien.
![Gebruikers gegevens importeren](media/how-to-on-demand-provision/on-demand6.png)

## <a name="determine-if-user-is-in-scope-details"></a>Bepalen of de gebruiker zich in de scope Details bevindt
Deze sectie bevat informatie over de vraag of de gebruiker die is geïmporteerd naar Azure AD binnen het bereik valt.  Klik op de koppeling **Details weer geven** om deze informatie weer te geven.

![Gebruikers bereik](media/how-to-on-demand-provision/on-demand7.png)

Met deze informatie kunt u aanvullende informatie bekijken over het bereik van uw gebruikers.

![Details van het gebruikers bereik](media/how-to-on-demand-provision/on-demand10a.png)

## <a name="match-user-between-source-and-target-system-details"></a>Gebruikers zoeken tussen bron-en doel systeem Details
Deze sectie bevat informatie over of de gebruiker al bestaat in azure AD en dat er een koppeling moet worden uitgevoerd in plaats van een nieuwe gebruiker in te richten.  Klik op de koppeling **Details weer geven** om deze informatie weer te geven.
![Details weergeven](media/how-to-on-demand-provision/on-demand8.png)

Aan de hand van deze informatie kunt u zien of er een overeenkomst is gevonden of dat er een nieuwe gebruiker wordt gemaakt.

![Gebruikersgegevens](media/how-to-on-demand-provision/on-demand11.png)

In de overeenkomende Details wordt een bericht weer gegeven met een van de drie volgende bewerkingen.  Dit zijn:
- Maken: er wordt een gebruiker gemaakt in azure AD
- Update: een gebruiker wordt bijgewerkt op basis van een wijziging die is aangebracht in de configuratie
- Verwijderen: een gebruiker wordt verwijderd uit Azure AD.

Afhankelijk van het type bewerking dat u hebt uitgevoerd, is het bericht afhankelijk.

## <a name="perform-action-details"></a>Details van actie uitvoeren
Deze sectie bevat informatie over de gebruiker die is ingericht of geëxporteerd naar Azure AD nadat de configuratie is toegepast.  Dit is wat de gebruiker ziet als deze eenmaal is ingericht in azure AD.  Klik op de koppeling **Details weer geven** om deze informatie weer te geven.
![Details van actie uitvoeren](media/how-to-on-demand-provision/on-demand9.png)

Met deze informatie kunt u de waarden van de kenmerken zien nadat de configuratie is toegepast.  Zien ze er ongeveer uit als er iets anders is geïmporteerd?  Is de configuratie geslaagd?  

Op deze manier kunt u de kenmerk transformatie traceren terwijl deze door de Cloud en in uw Azure AD-Tenant wordt verplaatst.

![Traceer kenmerk](media/how-to-on-demand-provision/on-demand12.png)

## <a name="next-steps"></a>Volgende stappen 

- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)
- [Azure AD Connect Cloud inrichting installeren](how-to-install.md)
 