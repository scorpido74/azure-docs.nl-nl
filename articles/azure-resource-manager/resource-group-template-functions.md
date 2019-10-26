---
title: Resource Manager-sjabloon functies | Microsoft Docs
description: Hierin worden de functies beschreven die u kunt gebruiken in een Azure Resource Manager sjabloon om waarden op te halen, te werken met teken reeksen en cijfers en implementatie gegevens op te halen.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: tomfitz
ms.openlocfilehash: 04358f21af79838f894dc50d5d9c165da67a332c
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894164"
---
# <a name="azure-resource-manager-template-functions"></a>Azure Resource Manager-sjabloon functies

In dit artikel worden alle functies beschreven die u kunt gebruiken in een Azure Resource Manager sjabloon. Zie de [syntaxis van sjablonen](template-expressions.md)voor meer informatie over het gebruik van functies in uw sjabloon.

Zie door de [gebruiker gedefinieerde functies](resource-group-authoring-templates.md#functions)om uw eigen functies te maken.

<a id="array" aria-hidden="true" />
<a id="coalesce" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="json" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-and-object-functions"></a>Matrix- en objectfuncties
Resource Manager biedt verschillende functies voor het werken met matrices en objecten.

* [array](resource-group-template-functions-array.md#array)
* [Voeg](resource-group-template-functions-array.md#coalesce)
* [concat](resource-group-template-functions-array.md#concat)
* [daarin](resource-group-template-functions-array.md#contains)
* [createArray](resource-group-template-functions-array.md#createarray)
* [gelaten](resource-group-template-functions-array.md#empty)
* [instantie](resource-group-template-functions-array.md#first)
* [Snij punt](resource-group-template-functions-array.md#intersection)
* [json](resource-group-template-functions-array.md#json)
* [duren](resource-group-template-functions-array.md#last)
* [lange](resource-group-template-functions-array.md#length)
* [min](resource-group-template-functions-array.md#min)
* [max](resource-group-template-functions-array.md#max)
* [bereik](resource-group-template-functions-array.md#range)
* [skip](resource-group-template-functions-array.md#skip)
* [Houd](resource-group-template-functions-array.md#take)
* [Réunion](resource-group-template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Vergelijkingsfuncties
Resource Manager biedt verschillende functies voor het maken van vergelijkingen in uw sjablonen.

* [gelijk is aan](resource-group-template-functions-comparison.md#equals)
* [jonge](resource-group-template-functions-comparison.md#less)
* [lessOrEquals](resource-group-template-functions-comparison.md#lessorequals)
* [groter](resource-group-template-functions-comparison.md#greater)
* [greaterOrEquals](resource-group-template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="deployment-value-functions"></a>Implementatie waarden functies
Resource Manager biedt de volgende functies voor het ophalen van waarden uit secties van de sjabloon en waarden die betrekking hebben op de implementatie:

* [inhoudsdistributiepad](resource-group-template-functions-deployment.md#deployment)
* [parameters](resource-group-template-functions-deployment.md#parameters)
* [variabelen](resource-group-template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Logische functies
Resource Manager biedt de volgende functies voor het werken met logische voor waarden:

* [maar](resource-group-template-functions-logical.md#and)
* [bool](resource-group-template-functions-logical.md#bool)
* [if](resource-group-template-functions-logical.md#if)
* [ten](resource-group-template-functions-logical.md#not)
* [or](resource-group-template-functions-logical.md#or)

<a id="add" aria-hidden="true" />
<a id="copyindex" aria-hidden="true" />
<a id="div" aria-hidden="true" />
<a id="float" aria-hidden="true" />
<a id="int" aria-hidden="true" />
<a id="minint" aria-hidden="true" />
<a id="maxint" aria-hidden="true" />
<a id="mod" aria-hidden="true" />
<a id="mul" aria-hidden="true" />
<a id="sub" aria-hidden="true" />

## <a name="numeric-functions"></a>Numerieke functies
Resource Manager biedt de volgende functies voor het werken met gehele getallen:

* [add](resource-group-template-functions-numeric.md#add)
* [Functie copyindex](resource-group-template-functions-numeric.md#copyindex)
* [div](resource-group-template-functions-numeric.md#div)
* [float](resource-group-template-functions-numeric.md#float)
* [int](resource-group-template-functions-numeric.md#int)
* [min](resource-group-template-functions-numeric.md#min)
* [max](resource-group-template-functions-numeric.md#max)
* [mod](resource-group-template-functions-numeric.md#mod)
* [mul](resource-group-template-functions-numeric.md#mul)
* [sub](resource-group-template-functions-numeric.md#sub)

<a id="extensionResourceId" aria-hidden="true" />
<a id="listkeys" aria-hidden="true" />
<a id="list" aria-hidden="true" />
<a id="providers" aria-hidden="true" />
<a id="reference" aria-hidden="true" />
<a id="resourcegroup" aria-hidden="true" />
<a id="resourceid" aria-hidden="true" />
<a id="subscription" aria-hidden="true" />
<a id="subscriptionResourceId" aria-hidden="true" />
<a id="tenantResourceId" aria-hidden="true" />

## <a name="resource-functions"></a>Resourcefuncties
Resource Manager biedt de volgende functies voor het ophalen van resource waarden:

* [extensionResourceId](resource-group-template-functions-resource.md#extensionresourceid)
* [listAccountSas](resource-group-template-functions-resource.md#list)
* [Listkeys ophalen](resource-group-template-functions-resource.md#listkeys)
* [listSecrets](resource-group-template-functions-resource.md#list)
* [orderverzamellijst](resource-group-template-functions-resource.md#list)
* [hardwareproviders](resource-group-template-functions-resource.md#providers)
* [referentielaag](resource-group-template-functions-resource.md#reference)
* [resourceGroup](resource-group-template-functions-resource.md#resourcegroup)
* [resourceId](resource-group-template-functions-resource.md#resourceid)
* [abonnement](resource-group-template-functions-resource.md#subscription)
* [subscriptionResourceId](resource-group-template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](resource-group-template-functions-resource.md#tenantresourceid)

<a id="base64" aria-hidden="true" />
<a id="base64tojson" aria-hidden="true" />
<a id="base64tostring" aria-hidden="true" />
<a id="concat" aria-hidden="true" />
<a id="containsstring" aria-hidden="true" />
<a id="datauri" aria-hidden="true" />
<a id="datauritostring" aria-hidden="true" />
<a id="emptystring" aria-hidden="true" />
<a id="endswith" aria-hidden="true" />
<a id="firststring" aria-hidden="true" />
<a id="guid" aria-hidden="true" />
<a id="indexof" aria-hidden="true" />
<a id="laststring" aria-hidden="true" />
<a id="lastindexof" aria-hidden="true" />
<a id="lengthstring" aria-hidden="true" />
<a id="padleft" aria-hidden="true" />
<a id="replace" aria-hidden="true" />
<a id="skipstring" aria-hidden="true" />
<a id="split" aria-hidden="true" />
<a id="startswith" aria-hidden="true" />
<a id="string" aria-hidden="true" />
<a id="substring" aria-hidden="true" />
<a id="takestring" aria-hidden="true" />
<a id="tolower" aria-hidden="true" />
<a id="toupper" aria-hidden="true" />
<a id="trim" aria-hidden="true" />
<a id="uniquestring" aria-hidden="true" />
<a id="uri" aria-hidden="true" />
<a id="uricomponent" aria-hidden="true" />
<a id="uricomponenttostring" aria-hidden="true" />

## <a name="string-functions"></a>Tekenreeksfuncties
Resource Manager biedt de volgende functies voor het werken met teken reeksen:

* [base64](resource-group-template-functions-string.md#base64)
* [base64ToJson](resource-group-template-functions-string.md#base64tojson)
* [base64ToString](resource-group-template-functions-string.md#base64tostring)
* [concat](resource-group-template-functions-string.md#concat)
* [daarin](resource-group-template-functions-string.md#contains)
* [dataUri](resource-group-template-functions-string.md#datauri)
* [dataUriToString](resource-group-template-functions-string.md#datauritostring)
* [gelaten](resource-group-template-functions-string.md#empty)
* [endsWith](resource-group-template-functions-string.md#endswith)
* [instantie](resource-group-template-functions-string.md#first)
* [Formatteer](resource-group-template-functions-string.md#format)
* [GPT](resource-group-template-functions-string.md#guid)
* [indexOf](resource-group-template-functions-string.md#indexof)
* [duren](resource-group-template-functions-string.md#last)
* [lastIndexOf](resource-group-template-functions-string.md#lastindexof)
* [lange](resource-group-template-functions-string.md#length)
* [newGuid](resource-group-template-functions-string.md#newguid)
* [padLeft](resource-group-template-functions-string.md#padleft)
* [vervangen](resource-group-template-functions-string.md#replace)
* [skip](resource-group-template-functions-string.md#skip)
* [split](resource-group-template-functions-string.md#split)
* [startsWith](resource-group-template-functions-string.md#startswith)
* [tekenreeksexpressie](resource-group-template-functions-string.md#string)
* [subtekenreeks](resource-group-template-functions-string.md#substring)
* [Houd](resource-group-template-functions-string.md#take)
* [toLower](resource-group-template-functions-string.md#tolower)
* [toUpper](resource-group-template-functions-string.md#toupper)
* [trim](resource-group-template-functions-string.md#trim)
* [uniqueString](resource-group-template-functions-string.md#uniquestring)
* [URI](resource-group-template-functions-string.md#uri)
* [uriComponent](resource-group-template-functions-string.md#uricomponent)
* [uriComponentToString](resource-group-template-functions-string.md#uricomponenttostring)
* [utcNow](resource-group-template-functions-string.md#utcnow)

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure Resource Manager sjablonen ontwerpen](resource-group-authoring-templates.md) voor een beschrijving van de secties in een Azure Resource Manager sjabloon
* Zie voor het samen voegen van meerdere sjablonen [gekoppelde sjablonen gebruiken met Azure Resource Manager](resource-group-linked-templates.md)
* Als u een bepaald aantal keer wilt herhalen bij het maken van een type resource, raadpleegt u [meerdere exemplaren van resources maken in azure Resource Manager](resource-group-create-multiple.md)
* Zie [een toepassing implementeren met Azure Resource Manager sjabloon](resource-group-template-deploy.md) voor meer informatie over het implementeren van de sjabloon die u hebt gemaakt.
