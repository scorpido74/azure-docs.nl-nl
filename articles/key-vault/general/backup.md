---
title: Back-up voor Azure Key Vault | Microsoft Docs
description: Gebruik dit document om een back-up te maken van een geheim, sleutel of certificaat dat is opgeslagen in Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: sudbalas
ms.openlocfilehash: 8a152e2771f0b207e81f42c6ecae2e4d14605051
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156302"
---
# <a name="azure-key-vault-backup"></a>Back-up voor Azure Key Vault

In dit document wordt uitgelegd hoe u een back-up maakt van de afzonderlijke geheimen, sleutels en certificaten die zijn opgeslagen in uw sleutelkluis. Deze back-up is bedoeld om u een offline kopie te bieden van al uw geheimen in het onwaarschijnlijke geval dat u geen toegang meer hebt tot uw sleutelkluis.

## <a name="overview"></a>Overzicht

Key Vault biedt automatisch verschillende functies om de beschikbaarheid te behouden en gegevensverlies te voorkomen. Deze back-up dient alleen te worden uitgevoerd als er een kritieke zakelijke reden is om een back-up van uw geheimen te onderhouden. Het maken van een back-up van geheimen in uw sleutelkluis kan extra operationele uitdagingen veroorzaken, zoals het onderhouden van meerdere logboeken, machtigingen en back-ups wanneer geheimen verlopen of draaien.

Key Vault onderhoudt de beschikbaarheid in noodscenario's en voert automatisch failoververzoeken uit naar een gekoppelde regio zonder dat tussenkomst van een gebruiker is vereist. Zie de volgende koppeling voor meer informatie. [Herstel na noodgevallen van Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)

Key Vault beschermt uw geheimen tegen onbedoelde en schadelijke verwijdering door middel van voorlopig verwijderen en opschonen. Als u uw geheimen wilt beveiligen tegen onbedoelde of opzettelijke verwijdering, moet u de beveiligingsfuncties voor voorlopig verwijderen en opschonen voor uw sleutelkluis configureren. Raadpleeg het volgende document voor meer informatie. [Herstel van Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview-soft-delete)

## <a name="limitations"></a>Beperkingen

Azure Key Vault biedt momenteel geen ondersteuning voor een manier om in één bewerking een back-up te maken van een volledige sleutelkluis. Pogingen om de opdrachten in dit document te gebruiken om een automatische back-up van een sleutelkluis uit te voeren, worden niet ondersteund door Microsoft of het Azure Key Vault-team.

Als u de opdrachten in het onderstaande document probeert te gebruiken om aangepaste automatisering te maken, kunnen er fouten optreden.

* Het maken van een back-up van geheimen met meerdere versies kan time-outfouten veroorzaken.
* Met een back-up wordt een momentopname gemaakt van een bepaald tijdstip. Geheimen kunnen tijdens een back-up worden vernieuwd, waardoor het kan gebeuren dat versleutelingssleutels niet overeenkomen.
* Als u de limieten voor de sleutelkluisservice voor aanvragen per seconde overschrijdt, wordt uw sleutelkluis beperkt en zal de back-up mislukken.

## <a name="design-considerations"></a>Overwegingen bij het ontwerpen

Wanneer u een back-up maakt van een object dat is opgeslagen in de sleutelkluis (geheim, sleutel of certificaat), wordt het object door de back-upbewerking gedownload als een versleutelde blob. Deze blob kan niet buiten Azure worden ontsleuteld. Als u bruikbare gegevens uit deze blob wilt ophalen, moet u de blob herstellen in een sleutelkluis binnen hetzelfde Azure-abonnement en dezelfde Azure-geografie.
[Azure-geografieën](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="prerequisites"></a>Vereisten

* Machtigingen op inzenderniveau of hoger voor een Azure-abonnement
* Een primaire sleutelkluis met geheimen waarvan u een back-up wilt maken
* Een secundaire sleutelkluis waarnaar geheimen worden teruggezet.

## <a name="back-up-and-restore-with-azure-portal"></a>Back-ups maken en herstellen met Azure Portal

### <a name="back-up"></a>Een back-up maken

1. Navigeer naar de Azure Portal.
2. Selecteer uw sleutelkluis.
3. Navigeer naar het object (geheim, sleutel of certificaat) waarvan u een back-up wilt maken.

    ![Installatiekopie](../media/backup-1.png)

4. Selecteer het object.
5. Selecteer Back-up downloaden

    ![Installatiekopie](../media/backup-2.png)
    
6. Klik op de knop Downloaden.

    ![Installatiekopie](../media/backup-3.png)
    
7. Sla de versleutelde blob op een veilige locatie op.

### <a name="restore"></a>Herstellen

1. Navigeer naar de Azure Portal.
2. Selecteer uw sleutelkluis.
3. Navigeer naar het type object (geheim, sleutel of certificaat) dat u wilt herstellen.
4. Selecteer Back-up terugzetten

    ![Installatiekopie](../media/backup-4.png)
    
5. Blader naar de locatie waar u de versleutelde blob hebt opgeslagen.
6. Selecteer “OK”.

## <a name="back-up-and-restore-with-the-azure-cli"></a>Back-ups maken en herstellen met Azure CLI

```azurecli
## Login To Azure
az login

## Set your Subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a Provider
az provider register -n Microsoft.KeyVault

## Backup a Certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>Volgende stappen

Schakel logboekregistratie en -controle in voor uw Azure Key Vault. [Logboekregistratie van Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/logging)
