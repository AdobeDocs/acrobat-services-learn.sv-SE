---
title: Komma igång med Adobe PDF Services API och .Net
description: Utvecklare kan komma igång på bara några minuter med de färdiga exempelfilerna för att komma åt alla tillgängliga webbtjänster
type: Tutorial
role: Developer
level: Beginner
thumbnail: KT-6675.jpg
kt: 6675
keywords: Aktuellt
exl-id: 22c59c75-fd99-4467-a6f6-917fb246469a
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '524'
ht-degree: 0%

---

# Komma igång med Adobe PDF Services API och .Net

![Skapa PDF Hero-bild](assets/GettingStartedJava_hero.jpg)

Utvecklare kan komma igång på bara några minuter med de färdiga exempelfilerna för att komma åt alla tillgängliga webbtjänster. Den här självstudiekursen vägleder dig genom alla steg för att börja köra exemplen med PDF Services .Net SDK:

## Steg 1: Hämta autentiseringsuppgifter och hämta exempelfiler

Det första steget är att hämta en autentiseringsuppgift (API-nyckel) för att låsa upp användningen. [Registrera dig för den kostnadsfria provperioden här](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) och klicka på Kom igång för att skapa dina nya inloggningsuppgifter.

![Steg 1](assets/GettingStartedJava_step1.png)

Det är viktigt att välja ett personligt konto för att registrera dig för den kostnadsfria provperioden:

![Personligt](assets/GettingStartedJava_personal.png)

I nästa steg väljer du API-tjänsten för PDF-tjänster och lägger sedan till ett namn och en beskrivning för dina inloggningsuppgifter.

Det finns en kryssruta för att &quot;Skapa personligt kodexempel&quot;. Välj det här alternativet om du vill att dina nya inloggningsuppgifter ska läggas till automatiskt i exempelfilerna, vilket sparar det manuella steget med att lägga till dem i projektet.

Välj sedan Node.js som språk för att ta emot de Node.js-specifika exemplen och klicka på knappen Skapa autentiseringsuppgifter.

![Autentiseringsuppgifter](assets/GettingStartedJava_credentials.png)

Du får en .zip-fil att hämta som heter PDFToolsSDK-.NetSamples.zip och som kan sparas i det lokala filsystemet.

## Steg 2: Konfigurera .Net-miljön och kör exempelkoden

1. Hämta och installera [.Net SDK](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install)
1. Extrahera den hämtade **[!UICONTROL PDFToolsSDK-.NetSamples.zip]** och packa upp innehållet
1. cd till exempelrotkatalogen **[!UICONTROL adobe-DC.PDFTools.SDK.NET.Samples]**
1. Kör från exempelrotkatalogen `dotnet build`

   C:\Temp\PDFToolsAPI\ PDFToolsSDK-.NetSamples\adobe-DC.PDFTools.SDK.NET.Samples>dotnet build

   Nu kan du köra exempelfilerna!

   I de här sista stegen visas hur du kör ditt första exempel med åtgärden Skapa PDF från Word:

1. Från katalogen för rotkatalogen i exemplen till mappen CreatePDFFromDocx, cd CreatePDFFromDocx/

   C:\Temp\PDFToolsAPI\ PDFToolsSDK-.NetSamples\adobe-DC.PDFTools.SDK.NET.Samples>cd CreatePDFFromDocx/

1. löpa `dotnet run CreatePDFFromDocx.csproj`

   C:\Temp\PDFToolsAPI\ PDFToolsSDK-.NetSamples\adobe-DC.PDFTools.SDK.NET.Samples\CreatePDFFromDocx>dotnet run CreatePDFFromDocx.csproj

Ditt PDF skapas på den plats som är angiven i utdatafilen, vilket som standard är samma mapp.

## Sluttankar

PDF Services API kan hjälpa dig att eliminera manuella processer genom att automatisera vanliga arbetsflöden och flytta bearbetningsbördan till molnet. I en värld där alla webbläsare behandlar PDF olika genom att utnyttja Adobe PDF Embed API tillsammans med PDF Services API, kan du skapa effektiva, tillförlitliga och förutsägbara processer som körs och visas korrekt **varje gång** oavsett plattform eller enhet.

## Resurser och nästa steg

* Mer hjälp och support finns på [[!DNL Adobe Acrobat Services] API:er](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) användarforum

* PDF Services API [Dokumentation](https://www.adobe.com/go/pdftoolsapi_doc)

* [Vanliga frågor](https://community.adobe.com/t5/document-cloud-sdk/faq-for-document-services-pdf-tools-api/m-p/10726197) för PDF Services API-frågor

* [Kontakta oss](https://www.adobe.com/go/pdftoolsapi_requestform) frågor om licenser och priser

* Relaterade artiklar

   [Nya PDF Services API har ännu fler funktioner för dokumentarbetsflöden](https://community.adobe.com/t5/document-services-apis/new-pdf-tools-api-brings-more-capabilities-for-document-services/m-p/11294170)

   [Juli Offentliggörande av [!DNL Adobe Acrobat Services]: PDF Embed and PDF Services](https://medium.com/adobetech/july-release-of-adobe-document-services-pdf-embed-and-pdf-tools-17211bf7776d)
