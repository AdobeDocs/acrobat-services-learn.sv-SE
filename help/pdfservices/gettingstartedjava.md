---
title: Komma igång med Adobe PDF Services API och Java
description: Utvecklare kan komma igång på bara några minuter med att köra exempelfiler som finns för att komma åt alla tillgängliga webbtjänster
feature: PDF Services API
role: Developer
level: Beginner
type: Tutorial
jira: KT-6676
thumbnail: KT-6676.jpg
exl-id: 4a8f2119-c464-496b-bdc8-35dd387bef25
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '457'
ht-degree: 0%

---

# Komma igång med Adobe PDF Services API och Java

![Skapa PDF-hjälpbild](assets/GettingStartedJava_hero.jpg)

Utvecklare kan komma igång på bara några minuter med att köra exempelfiler som finns för att komma åt alla tillgängliga webbtjänster. I den här självstudiekursen går vi igenom alla steg för att börja köra exempel med hjälp av PDF Services Java SDK:

## Steg 1: Inhämta autentiseringsuppgifter och hämta exempelfiler

Det första steget är att hämta en autentiseringsuppgift (API-nyckel) för att låsa upp användning. [Registrera dig för en kostnadsfri provperiod här](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) och klicka på Kom igång för att skapa dina nya autentiseringsuppgifter.

![Steg 1](assets/GettingStartedJava_step1.png)

Det är viktigt att välja ett personligt konto för att registrera dig för den kostnadsfria provperioden:

![Personligt](assets/GettingStartedJava_personal.png)

I nästa steg väljer du API-tjänsten för PDF-tjänster och lägger sedan till ett namn och en beskrivning för dina inloggningsuppgifter.

Det finns en kryssruta för att &quot;Skapa personligt kodexempel&quot;. Välj det här alternativet om du vill att dina nya inloggningsuppgifter ska läggas till automatiskt i exempelfilerna, vilket sparar det manuella steget att lägga till dem i projektet.

Därefter väljer du Java som språk för att ta emot Java-specifika exempel och klickar på knappen &quot;Skapa referenser&quot;.

![Autentiseringsuppgifter](assets/GettingStartedJava_credentials.png)

Du får en .zip-fil att hämta som heter PDFToolsSDK-JavaSamples.zip och som kan sparas i ditt lokala filsystem.

## Steg 2: Konfigurera din Java-miljö

1. Installera [Java 8 eller senare](https://www.oracle.com/java/technologies/javase-downloads.html) om du inte redan har gjort det.
1. Kör `javac -version` för att verifiera installationen.
1. Kontrollera att mappen JDK bin ingår i variabeln PATH (metoden varierar beroende på operativsystem).
1. Installera [Maven](https://maven.apache.org/install.html) med önskat verktyg om du inte redan har gjort det.

De personliga exemplen innehåller allt från körklar exempelkod, en inbäddad JSON-fil för autentiseringsuppgifter och förkonfigurerade anslutningar till beroenden.

1. Hämta [exempelprojektet](https://github.com/adobe/pdftools-java-sdk-samples).
1. Bygg exempelprojektet med Maven: mvn ren installation.
1. Testa exempelkoden på kommandoraden eller i den IDE du föredrar.

## Avslutande tankar

Med hjälp av PDF Services API kan du eliminera manuella processer genom att automatisera vanliga arbetsflöden och flytta bearbetningsbördan till molnet. I en värld där alla webbläsare behandlar PDF olika och använder Adobe PDF Embed API tillsammans med PDF Services API, kan du skapa effektiva, tillförlitliga och förutsägbara processer som körs och visas korrekt **varje gång** oavsett plattform eller enhet.

## Resurser och nästa steg

* Om du vill ha mer hjälp och support kan du gå till användarforumet för [[!DNL Acrobat Services] API:er](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) i Adobe

* PDF Services API [Dokumentation](https://www.adobe.com/go/pdftoolsapi_doc)

* [Vanliga frågor](https://community.adobe.com/t5/contentarchivals/contentarchivedpage/message-uid/10726197) om API-frågor för PDF-tjänster

* [Kontakta oss](https://www.adobe.com/go/pdftoolsapi_requestform) om du har frågor om licenser och priser

* Relaterade artiklar

  [Det nya PDF Services API har ännu fler funktioner för dokumentarbetsflöden](https://community.adobe.com/t5/acrobat-services-api-discussions/new-pdf-tools-api-brings-more-capabilities-for-document-services/m-p/11294170)

  [Juli-utgåvan av [!DNL Adobe Acrobat Services]: PDF Embed- och PDF-tjänster](https://medium.com/adobetech/july-release-of-adobe-document-services-pdf-embed-and-pdf-tools-17211bf7776d)
