---
title: Använda PDF Services API för att exportera PDF till Word, PowerPoint med mera
description: Lär dig hur du kör PDF Services API-exportåtgärden med exempelfiler för språken Node.js, Java och .Net
type: Tutorial
role: Developer
level: Beginner
thumbnail: KT-6674.jpg
kt: 6674
exl-id: 55f5b04e-0249-47d9-9131-2f9ec01db7e8
source-git-commit: aa5c88fb5725a3d1c50d5c6b73fce7add629b08d
workflow-type: tm+mt
source-wordcount: '500'
ht-degree: 5%

---

# Använda PDF Services API för att exportera PDF till Word, PowerPoint med mera

![Skapa PDF Hero-bild](assets/ExportPDF_hero.jpg)

Adobe PDF Services API konverterar PDF-filer till MS Office, text och bilder med API:er. Det finns många vanliga användningsområden för att låsa upp PDF för redigering och analys av innehåll, och med PDF Services API kan utvecklare enkelt integrera den här funktionen i befintliga system och program. Konvertera PDF-filer till MS Word för att redigera innehåll, godkänna och sedan skicka signaturer för att skapa anpassade kontraktsarbetsflöden. Eller exportera PDF-material till MS Excel-format för fakturering och ekonomiberäkningar eller dataanalys.

Exportåtgärden stöder följande PDF-filkonverteringar:

* PDF till Microsoft Word (DOC, DOCX)
* PDF till Microsoft PowerPoint (PPTX)
* PDF till Microsoft Excel (XLSX)
* PDF till text (RTF)
* PDF till bild (JPEG, PNG)

I den här självstudiekursen lär du dig grunderna i hur du kör din första API-exportåtgärd för PDF Services med hjälp av exempelfiler för språken Node.js, Java och .Net.

## Steg 1: Skapa dina inloggningsuppgifter och konfigurera miljön:

Använd självstudiekurserna för att komma igång nedan för att skapa API-uppgifter, hämta exempelfiler och konfigurera miljön.

[Komma igång med PDF Services API och Java](gettingstartedjava.md)

[Komma igång med PDF Services API och .Net](gettingstartednet.md)

[Komma igång med PDF Services API och Node.js](createpdffromhtml.md)

## Steg 2: Kör export-PDF-åtgärd med exempelfilerna

**Java**

1. Öppna kommandotolken.

1. Ändra kataloger till din exempelkodkatalog.

   Till exempel C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-java-samples

1. Kör följande kommando::

   `mvn -f pom.xml exec:java -Dexec.mainClass=com.adobe.platform.operation.samples.exportpdf.ExportPDFToDOCX`

PDF skapas i katalogen src/main/resources.

**.NET**

1. Öppna kommandotolken.

1. Ändra kataloger till din exempelkodkatalog.

   Till exempel C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-NetSamples

1. Ändra kataloger igen till katalogen ExportPDFtoDocx.

1. Kör följande kommando::

   `dotnet run ExportPDFToDocx.csproj`

PDF skapas i samma katalog.

**Node.js**

1. Öppna kommandotolken.

1. Ändra kataloger till din exempelkodkatalog.

   Till exempel C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-node-samples

1. Kör följande kommando::

   `node src/ocr/ocr-pdf.js`

PDF skapas på den plats som anges i utdata, som är standardkatalogen pdfServicesSdkResult.

## Sluttankar

Du bör nu ha ett fungerande exempel som kan importeras till dina befintliga program för att starta ett konceptbevis. I var och en av exempelkatalogerna kan du se ytterligare exempel på hur du exporterar PDF-filer till bildformat. På samma sätt som ovan kan du köra provet. Om du vill ändra till ett annat format kan du uppdatera koden till det nya format du vill:

SupportedTargetFormats.PPTX

Och destinationsresultatet:

output/exportPdfOutput.PPTX

Till ett annat format.

## Resurser och nästa steg

* Mer hjälp och support finns på [[!DNL Adobe Acrobat Services] API:er](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) användarforum

* PDF Services API [Dokumentation](https://www.adobe.com/go/pdftoolsapi_doc)

* [Vanliga frågor](https://community.adobe.com/t5/document-cloud-sdk/faq-for-document-services-pdf-tools-api/m-p/10726197) för PDF Services API-frågor

* [Kontakta oss](https://www.adobe.com/go/pdftoolsapi_requestform) frågor om licenser och priser
