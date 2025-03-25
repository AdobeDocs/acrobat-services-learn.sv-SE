---
title: Använda Adobe PDF Services API för att OCR PDF-filer
description: Med OCR (Optical Character Recognition) kan du låsa upp skannade PDF för att extrahera text och skapa sökbara filer
feature: PDF Services API
role: Developer
level: Beginner
type: Tutorial
jira: KT-6677
thumbnail: KT-6677.jpg
exl-id: 61a9a2d1-94c3-41c2-8f90-a56a938ef245
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '567'
ht-degree: 0%

---

# Använda Adobe PDF Services API för att OCR PDF-filer

![Skapa PDF-hjälpbild](assets/OCR_hero.jpg)

Med OCR (Optical Character Recognition) kan du låsa upp skannade PDF för att extrahera text och skapa sökbara filer. Integrera OCR i valfritt dokumentarbetsflöde med hjälp av våra kraftfulla molnbaserade API:er, vilket ger den perfekta lösningen för att arkivera, kopiera text och skapa sökbara dokumentindex. Skapa sökbara arkiv från skannade PDF-databaser för att låsa upp viktig information och spara tid med snabb sökbarhet. Eller tillämpa OCR på ditt PDF från uppladdade skanningar så att du kan redigera dem för användning i arbetsflöden för nybörjare.

Utvecklare kan komma igång på bara några minuter med de redo att köra exempelfiler för OCR.

I den här självstudiekursen får du lära dig grunderna i hur du kör din första OCR-åtgärd med PDF Services API med hjälp av exempelfiler för språk som Node.js, Java och .Net.

## Steg 1: Skapa dina inloggningsuppgifter och konfigurera din miljö

Använd självstudiekurserna för att komma i gång nedan för att skapa dina API-inloggningsuppgifter, hämta exempelfiler och konfigurera din miljö.

[Komma igång med PDF Services API och Java](gettingstartedjava.md)

[Komma igång med PDF Services API och .Net](gettingstartednet.md)

[Kom igång med PDF Services API och Node.js](createpdffromhtml.md)

## Kör OCR-exemplet i exempelfilerna

Vår OCR-åtgärd stöder engelska som standard, men även tyska, franska, danska och [andra språk](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#ocr-with-explicit-language). Standardspråket är en-us locale.

När du skickar alternativ med OCR-åtgärd, inklusive specifika språkinställningar, accepterar metoden också parametern &quot;type&quot; som har två alternativ:

* SEARCHABLE_IMAGE: Ändrar den ursprungliga bilden under rensningsprocessen (den skevas till exempel) innan ett osynligt textlager placeras över den. Den här typen tar bort oönskade artefakter och kan leda till ett mer läsbart dokument i vissa scenarier.

* SEARCHABLE_IMAGE_EXACT: Säkerställer att texten är sökbar och valbar. Med det här alternativet behålls originalbilden och ett osynligt textlager placeras över den. Rekommenderas för fall som kräver maximal återgivning av originalbilden.

**Java**

1. Öppna en kommandotolk.

1. Ändra kataloger till din exempelkodkatalog.

   T.ex. C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-java-samples>.

1. Kör följande kommando:

   `mvn -f pom.xml exec:java -Dexec.mainClass=com.adobe.platform.operation.samples.ocrpdf.OcrPDF`

Din PDF skapas i katalogen src/main/resources.

**.NET**

1. Öppna en kommandotolk.

1. Ändra kataloger till din exempelkodkatalog.

   T.ex. C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-NetSamples

1. Ändra kataloger igen till OcrPDF-katalogen.

1. Kör följande kommando:

   `dotnet run OcrPDF.csproj`

Din PDF skapas i samma katalog.

**Node.js**

1. Öppna en kommandotolk.

1. Ändra kataloger till din exempelkodkatalog.

   T.ex. C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-node-samples

1. Kör följande kommando:

   `node src/ocr/ocr-pdf.js`

PDF skapas på den plats som är angiven i utdatakatalogen, vilken som standard är utdatakatalogen.

## Avslutande tankar

I de här enkla stegen med hjälp av exempelfilerna bör du ha ett fungerande exempel som du kan bygga vidare på. Utöver OCR-exemplet som vi använde i den här självstudiekursen finns det ett annat exempel på OCR som använder de typer och språkinställningar som stöds och som diskuterades tidigare.

Härifrån kan du helt enkelt ersätta dina in- och utfiler som finns i exemplet för att använda din egen PDF för att slutföra ditt konceptbevis för ditt eget användningsfall.

![Koncepttest](assets/OCR_poc.png)

## Resurser och nästa steg

* Om du vill ha mer hjälp och support kan du gå till användarforumet för [[!DNL Acrobat Services] API:er](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) i Adobe

* PDF Services API [Dokumentation](https://www.adobe.com/go/pdftoolsapi_doc)

* [Vanliga frågor](https://community.adobe.com/t5/contentarchivals/contentarchivedpage/message-uid/10726197) om API-frågor för PDF-tjänster

* [Kontakta oss](https://www.adobe.com/go/pdftoolsapi_requestform) om du har frågor om licenser och priser
