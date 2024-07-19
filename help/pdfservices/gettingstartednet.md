---
title: Komma igång med Adobe PDF Services API och .Net
description: Utvecklare kan komma igång på bara några minuter med att köra exempelfiler som finns för att komma åt alla tillgängliga webbtjänster
feature: PDF Services API
role: Developer
level: Beginner
type: Tutorial
jira: KT-6675
thumbnail: KT-6675.jpg
keywords: Utvalt
exl-id: 22c59c75-fd99-4467-a6f6-917fb246469a
source-git-commit: 5222e1626f4e79c02298e81d621216469753ca72
workflow-type: tm+mt
source-wordcount: '483'
ht-degree: 0%

---

# Komma igång med Adobe PDF Services API och .Net

![Skapa PDF-hjälpbild](assets/GettingStartedJava_hero.jpg)

Utvecklare kan komma igång på bara några minuter med att köra exempelfiler som finns för att komma åt alla tillgängliga webbtjänster. I den här självstudiekursen går vi igenom alla steg för att börja köra exemplen med PDF Services .Net SDK:

## Steg 1: Inhämta autentiseringsuppgifter och hämta exempelfiler

Det första steget är att hämta en autentiseringsuppgift (API-nyckel) för att låsa upp användning. [Registrera dig för en kostnadsfri provperiod här](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) och klicka på Kom igång för att skapa dina nya autentiseringsuppgifter.

![Steg 1](assets/GettingStartedJava_step1.png)

Det är viktigt att välja ett personligt konto för att registrera dig för den kostnadsfria provperioden:

![Personligt](assets/GettingStartedJava_personal.png)

I nästa steg väljer du API-tjänsten för PDF-tjänster och lägger sedan till ett namn och en beskrivning för dina inloggningsuppgifter.

Det finns en kryssruta för att &quot;Skapa personligt kodexempel&quot;. Välj det här alternativet om du vill att dina nya inloggningsuppgifter ska läggas till automatiskt i exempelfilerna, vilket sparar det manuella steget att lägga till dem i projektet.

Välj sedan Node.js som språk för att ta emot Node.js-specifika exempel och klicka på knappen Skapa autentiseringsuppgifter.

![Autentiseringsuppgifter](assets/GettingStartedJava_credentials.png)

Du får en .zip-fil att hämta som heter PDFToolsSDK-.NetSamples.zip och som kan sparas i ditt lokala filsystem.

## Steg 2: Konfigurera .Net-miljön och kör exempelkoden

1. Hämta och installera [.Net SDK](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install)
1. Extrahera det hämtade **[!UICONTROL PDFToolsSDK-.NetSamples.zip]** och packa upp innehållet
1. cd till exempelrotkatalogen **[!UICONTROL adobe-DC.PDFTools.SDK.NET.Samples]**
1. Kör `dotnet build` från exempelrotkatalogen

   C:\Temp\PDFToolsAPI\ PDFToolsSDK-.NetSamples\adobe-DC.PDFTools.SDK.NET.Samples>dotnet build

   Nu är du redo att köra exempelfilerna!

   De här sista stegen visar hur du kör ditt första exempel med åtgärden Skapa PDF från Word:

1. Från katalogen för rotkatalogen i exemplen till mappen CreatePDFFromDocx, cd CreatePDFFromDocx/

   C:\Temp\PDFToolsAPI\ PDFToolsSDK-.NetSamples\adobe-DC.PDFTools.SDK.NET.Samples>cd CreatePDFFromDocx/

1. kör `dotnet run CreatePDFFromDocx.csproj`

   C:\Temp\PDFToolsAPI\ PDFToolsSDK-.NetSamples\adobe-DC.PDFTools.SDK.NET.Samples\CreatePDFFromDocx>dotnet run CreatePDFFromDocx.csproj

PDF skapas på den plats som är angiven i utdatafilen, som standard i samma mapp.

## Avslutande tankar

Med hjälp av PDF Services API kan du eliminera manuella processer genom att automatisera vanliga arbetsflöden och flytta bearbetningsbördan till molnet. I en värld där alla webbläsare behandlar PDF olika och använder Adobe PDF Embed API tillsammans med PDF Services API, kan du skapa effektiva, tillförlitliga och förutsägbara processer som körs och visas korrekt **varje gång** oavsett plattform eller enhet.

## Resurser och nästa steg

* Om du vill ha mer hjälp och support går du till communityforumet för [[!DNL Adobe Acrobat Services] API:er](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all)

* PDF Services API [Dokumentation](https://www.adobe.com/go/pdftoolsapi_doc)

* [Vanliga frågor](https://community.adobe.com/t5/document-cloud-sdk/faq-for-document-services-pdf-tools-api/m-p/10726197) om API-frågor för PDF-tjänster

* [Kontakta oss](https://www.adobe.com/go/pdftoolsapi_requestform) om du har frågor om licenser och priser

* Relaterade artiklar

  [Det nya PDF Services API har ännu fler funktioner för dokumentarbetsflöden](https://community.adobe.com/t5/document-services-apis/new-pdf-tools-api-brings-more-capabilities-for-document-services/m-p/11294170)

  [Juli-utgåvan av [!DNL Adobe Acrobat Services]: PDF Embed- och PDF-tjänster](https://medium.com/adobetech/july-release-of-adobe-document-services-pdf-embed-and-pdf-tools-17211bf7776d)
