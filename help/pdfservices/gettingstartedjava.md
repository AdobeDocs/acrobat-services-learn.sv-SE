---
title: Komma igång med Adobe PDF Services API och Java
description: Utvecklare kan komma igång på bara några minuter med de färdiga exempelfilerna för att komma åt alla tillgängliga webbtjänster
type: Tutorial
role: Developer
level: Beginner
thumbnail: KT-6676.jpg
kt: 6676
exl-id: 4a8f2119-c464-496b-bdc8-35dd387bef25
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '501'
ht-degree: 0%

---

# Komma igång med Adobe PDF Services API och Java

![Skapa PDF Hero-bild](assets/GettingStartedJava_hero.jpg)

Utvecklare kan komma igång på bara några minuter med de färdiga exempelfilerna för att komma åt alla tillgängliga webbtjänster. Den här självstudiekursen vägleder dig genom alla steg för att börja köra exemplen med PDF Services Java SDK:

## Steg 1: Hämta autentiseringsuppgifter och hämta exempelfiler

Det första steget är att hämta en autentiseringsuppgift (API-nyckel) för att låsa upp användningen. [Registrera dig för den kostnadsfria provperioden här](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) och klicka på Kom igång för att skapa dina nya inloggningsuppgifter.

![Steg 1](assets/GettingStartedJava_step1.png)

Det är viktigt att välja ett personligt konto för att registrera dig för den kostnadsfria provperioden:

![Personligt](assets/GettingStartedJava_personal.png)

I nästa steg väljer du API-tjänsten för PDF-tjänster och lägger sedan till ett namn och en beskrivning för dina inloggningsuppgifter.

Det finns en kryssruta för att &quot;Skapa personligt kodexempel&quot;. Välj det här alternativet om du vill att dina nya inloggningsuppgifter ska läggas till automatiskt i exempelfilerna, vilket sparar det manuella steget med att lägga till dem i projektet.

Välj sedan Java som språk för de Java-specifika exemplen och klicka på knappen Skapa inloggningsuppgifter.

![Autentiseringsuppgifter](assets/GettingStartedJava_credentials.png)

Du får en .zip-fil att hämta som heter PDFToolsSDK-JavaSamples.zip och som kan sparas i det lokala filsystemet.

## Steg 2: Konfigurera Java-miljön

1. Installera [Java 8 eller senare](https://www.oracle.com/java/technologies/javase-downloads.html) om du inte redan gjort det.
1. Kör `javac -version` för att verifiera installationen.
1. Kontrollera att JDK bin-mappen ingår i PATH-variabeln (metoden varierar beroende på operativsystem).
1. Installera [Maven](https://maven.apache.org/install.html) använda det verktyg du föredrar om du inte redan gjort det.

De anpassade exemplen innehåller allt från körklar exempelkod, en inbäddad JSON-autentiseringsfil och förkonfigurerade anslutningar till beroenden.

1. Hämta [exempelprojektet](https://github.com/adobe/pdftools-java-sdk-samples).
1. Bygg exempelprojektet med Maven: mvn ren installation.
1. Testa exempelkoden på kommandoraden eller i önskad IDE.

## Sluttankar

PDF Services API kan hjälpa dig att eliminera manuella processer genom att automatisera vanliga arbetsflöden och flytta bearbetningsbördan till molnet. I en värld där alla webbläsare behandlar PDF olika genom att utnyttja Adobe PDF Embed API tillsammans med PDF Services API, kan du skapa effektiva, tillförlitliga och förutsägbara processer som körs och visas korrekt **varje gång** oavsett plattform eller enhet.

## Resurser och nästa steg

* Mer hjälp och support finns på Adobe [[!DNL Acrobat Services] API:er](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) användarforum

* PDF Services API [Dokumentation](https://www.adobe.com/go/pdftoolsapi_doc)

* [Vanliga frågor](https://community.adobe.com/t5/document-cloud-sdk/faq-for-document-services-pdf-tools-api/m-p/10726197) för PDF Services API-frågor

* [Kontakta oss](https://www.adobe.com/go/pdftoolsapi_requestform) frågor om licenser och priser

* Relaterade artiklar

   [Nya PDF Services API har ännu fler funktioner för dokumentarbetsflöden](https://community.adobe.com/t5/document-services-apis/new-pdf-tools-api-brings-more-capabilities-for-document-services/m-p/11294170)

   [Juli Offentliggörande av [!DNL Adobe Acrobat Services]: PDF Embed and PDF Services](https://medium.com/adobetech/july-release-of-adobe-document-services-pdf-embed-and-pdf-tools-17211bf7776d)
