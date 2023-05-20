---
title: Använda Adobe PDF Services API för OCR PDF-filer
description: Med OCR (Optical Character Recognition) kan du låsa upp skannad PDF för att extrahera text och skapa sökbara filer
type: Tutorial
role: Developer
level: Beginner
thumbnail: KT-6677.jpg
kt: 6677
keywords: Hero
exl-id: 61a9a2d1-94c3-41c2-8f90-a56a938ef245
source-git-commit: c1937561d607f1eabbc1921d6090858abb13f0d3
workflow-type: tm+mt
source-wordcount: '600'
ht-degree: 4%

---

# Använda Adobe PDF Services API för OCR PDF-filer

![Skapa PDF Hero-bild](assets/OCR_hero.jpg)

Med OCR (Optical Character Recognition) kan du låsa upp inskannad PDF för att extrahera text och skapa sökbara filer. Med våra kraftfulla molnbaserade API:er kan du integrera OCR i alla dokumentarbetsflöden och få den perfekta lösningen för att arkivera, kopiera text och skapa sökbara dokumentindex. Skapa sökbara arkiv från inskannade PDF-arkiv för att låsa upp viktig information och spara tid med snabb sökbarhet. Eller tillämpa OCR på PDF från uppladdade skanningar så att de kan redigeras för användning i arbetsflöden för nystart.

Utvecklare kommer igång på bara några minuter med de färdiga exempelfilerna för OCR.

I den här självstudiekursen beskrivs grunderna för hur du kör din första OCR-åtgärd för PDF Services API med hjälp av exempelfiler för språken Node.js, Java och .Net.

## Steg 1: Skapa dina inloggningsuppgifter och konfigurera din miljö

Använd självstudiekurserna för att komma igång nedan för att skapa API-uppgifter, hämta exempelfiler och konfigurera miljön.

[Komma igång med PDF Services API och Java](gettingstartedjava.md)

[Komma igång med PDF Services API och .Net](gettingstartednet.md)

[Komma igång med PDF Services API och Node.js](createpdffromhtml.md)

## Kör OCR-exemplet i exempelfilerna

Vår OCR-åtgärd tillåter engelska som standard, men ger även stöd för tyska, franska, danska och [andra språk](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#ocr-with-explicit-language). Standardspråket är en-US.

När du anger alternativ med OCR-åtgärden, inklusive specifika språkområden, accepterar metoden också parametern &quot;type&quot; som har två alternativ:

* SEARCHABLE_IMAGE: Originalbilden ändras under rensningsprocessen (den skevas till exempel) innan ett osynligt textlager placeras över den. Den här typen tar bort oönskade artefakter och kan resultera i ett mer läsbart dokument i vissa scenarier.

* SEARCHABLE_IMAGE_EXACT: Gör texten sökbar och markeringsbar. Med det här alternativet behålls originalbilden och ett osynligt textlager placeras över den. Rekommenderas i fall där originalbilden måste vara så exakt som möjligt.

**Java**

1. Öppna kommandotolken.

1. Ändra kataloger till din exempelkodkatalog.

   t.ex. C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-java-samples>.

1. Kör följande kommando::

   `mvn -f pom.xml exec:java -Dexec.mainClass=com.adobe.platform.operation.samples.ocrpdf.OcrPDF`

PDF skapas i katalogen src/main/resources.

**.NET**

1. Öppna kommandotolken.

1. Ändra kataloger till din exempelkodkatalog.

   T.ex. C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-NetSamples

1. Ändra kataloger igen till katalogen OcrPDF.

1. Kör följande kommando::

   `dotnet run OcrPDF.csproj`

PDF skapas i samma katalog.

**Node.js**

1. Öppna kommandotolken.

1. Ändra kataloger till din exempelkodkatalog.

   T.ex. C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-node-samples

1. Kör följande kommando::

   `node src/ocr/ocr-pdf.js`

PDF skapas på den plats som är angiven i utdatakatalogen, som är standard för utdatakatalogen.

## Sluttankar

Med dessa enkla steg som använder exempelfilerna bör du ha ett fungerande exempel som du kan bygga vidare på. Förutom OCR-exemplet som vi använde i den här självstudiekursen finns det ett annat exempel på OCR med de typalternativ och språkområdesalternativ som stöds och som diskuterades tidigare.

Härifrån kan du helt enkelt ersätta dina in- och utfiler som finns i exemplet för att använda din egen PDF för att slutföra ditt konceptbevis för ditt eget användningsfall.

![Konceptbevis](assets/OCR_poc.png)

## Resurser och nästa steg

* Mer hjälp och support finns på Adobe [[!DNL Acrobat Services] API:er](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) användarforum

* PDF Services API [Dokumentation](https://www.adobe.com/go/pdftoolsapi_doc)

* [Vanliga frågor](https://community.adobe.com/t5/document-cloud-sdk/faq-for-document-services-pdf-tools-api/m-p/10726197) för PDF Services API-frågor

* [Kontakta oss](https://www.adobe.com/go/pdftoolsapi_requestform) frågor om licenser och priser
