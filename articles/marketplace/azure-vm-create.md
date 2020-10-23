---
title: Maak een aanbieding voor de virtuele machine op Azure Marketplace.
description: Meer informatie over het maken van een aanbieding voor de virtuele machine in micro soft Commercial Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/20/2020
ms.openlocfilehash: 5964292d7037465456efe22283a5552a2bfc7ad8
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331257"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>Een aanbieding voor een virtuele machine maken op Azure Marketplace

In dit artikel wordt beschreven hoe u een Azure virtual machine-aanbieding maakt voor [Azure Marketplace](https://azuremarketplace.microsoft.com/). Het biedt zowel Windows-als op Linux gebaseerde virtuele machines die een besturings systeem, een virtuele harde schijf (VHD) en Maxi maal 16 gegevens schijven bevatten.

Voordat u begint, moet u [een commercieel Marketplace-account maken in het partner centrum](partner-center-portal/create-account.md). Zorg ervoor dat uw account is inge schreven in het Commercial Marketplace-programma.

## <a name="before-you-begin"></a>Voordat u begint

Als u dit nog niet hebt gedaan, controleert u [de aanbieding van een virtuele machine plannen](marketplace-virtual-machines.md). Hierin worden de technische vereisten voor uw virtuele machine uitgelegd en worden de gegevens en assets vermeld die u nodig hebt bij het maken van uw aanbieding. 

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het linkerdeel venster de optie **Commercial Marketplace**-  >  **overzicht**.
3. Selecteer op de pagina **overzicht** de optie **nieuwe**  >  **Azure virtual machine**aanbieden.

    ![Scherm afbeelding met de opties in het linkerdeel venster en de knop Nieuw aanbod.](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Nadat uw aanbieding is gepubliceerd, worden alle wijzigingen die u in het partner centrum aanbrengt, alleen op Azure Marketplace weer gegeven nadat u de aanbieding opnieuw hebt gepubliceerd. Zorg ervoor dat u een aanbieding altijd opnieuw publiceert nadat u de wijzigingen hebt aangebracht.

Voer een **aanbiedings-id**in. Dit is een unieke id voor elke aanbieding in uw account.

- Deze ID is zichtbaar voor klanten in het webadres voor de Azure Marketplace-aanbieding en in Azure PowerShell en de Azure CLI, indien van toepassing.
- Gebruik alleen kleine letters en cijfers. De ID kan afbreek streepjes en onderstrepings tekens bevatten, maar mag niet langer zijn dan 50. Als u bijvoorbeeld **test-aanbieding-1**invoert, is het webadres van de aanbieding `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- De aanbiedings-ID kan niet worden gewijzigd nadat u **maken**hebt geselecteerd.

Voer een **alias**voor de aanbieding in. De aanbiedings alias is de naam die wordt gebruikt voor de aanbieding in Partner Center.

- Deze naam wordt niet gebruikt op Azure Marketplace. Dit wijkt af van de naam van de aanbieding en andere waarden die aan klanten worden weer gegeven.

## <a name="enable-a-test-drive-optional"></a>Een test drive inschakelen (optioneel)

Een test drive is een fantastische manier om uw aanbieding aan potentiële klanten te laten presen teren door hen gedurende een vast aantal uur toegang te geven tot een vooraf geconfigureerde omgeving. Het bieden van een test drive resulteert in een verhoogde conversie frequentie en genereert uiterst gekwalificeerde leads. Zie [Wat is een test drive?](partner-center-portal/test-drive.md)voor meer informatie over test stations.

> [!TIP]
> Een test drive wijkt af van een gratis proef versie. U kunt een test drive, een gratis proef versie of beide aanbieden. Ze bieden klanten uw oplossing voor een vaste periode. Een test drive bevat echter ook een zelf doorgeleide rond leiding door de belangrijkste functies en voor delen van uw product die worden getoond in een scenario met een praktijk implementatie.

**Een test drive inschakelen**
1.  Schakel onder **test station**het selectie vakje **een test drive inschakelen** in.
1.  Selecteer in de lijst die wordt weer gegeven het test drive type.

## <a name="configure-lead-management"></a>Leadbeheer configureren

Wanneer u uw aanbieding publiceert naar de commerciële Marketplace met het partner centrum, sluit u deze aan op het CRM-systeem (Customer Relationship Management). Zo kunt u contact gegevens van klanten ontvangen zodra iemand interesse in of gebruikt voor uw product. Verbinding maken met een CRM is vereist als u een test drive wilt inschakelen (Zie de voor gaande sectie). Anders is het maken van een verbinding met een CRM optioneel.

1. Selecteer de koppeling **verbinding** onder **leads van klanten**.
1. Selecteer in het dialoog venster **verbindings Details** een doel voor de lead in de lijst.
1. Vul de velden in die worden weer gegeven. Raadpleeg de volgende artikelen voor gedetailleerde stappen:

   - [Uw aanbieding configureren voor het verzenden van leads naar de Azure-tabel](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Uw aanbieding configureren voor het verzenden van leads naar Dynamics 365 Customer engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (voorheen Dynamics CRM Online)
   - [Uw aanbieding configureren voor het verzenden van leads naar een HTTPS-eind punt](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Uw aanbieding configureren voor het verzenden van leads naar Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Uw aanbieding configureren voor het verzenden van leads naar Sales Force](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Als u de configuratie die u hebt ingevoerd, wilt valideren, selecteert u de koppeling **valideren** .
1. Selecteer **OK**om het dialoog venster te sluiten.

## <a name="resell-through-csps"></a>Door de Csp's door verkopen

Breid het bereik van uw aanbieding uit door deze beschikbaar te maken voor partners in het [Cloud Solution Provider-programma (CSP)](https://azure.microsoft.com/offers/ms-azr-0145p/) . Alle BYOL-abonnementen (voor het maken van uw eigen licentie) worden automatisch aangemeld bij het programma. U kunt er ook voor kiezen om uw keuze te maken in uw niet-BYOL plannen.

Selecteer **maken** om de aanbieding te genereren en door te gaan.

## <a name="next-steps"></a>Volgende stappen

- [Eigenschappen van aanbieding voor de virtuele machine configureren](azure-vm-create-properties.md)
- [Best practices voor aanbiedingsvermeldingen](gtm-offer-listing-best-practices.md)