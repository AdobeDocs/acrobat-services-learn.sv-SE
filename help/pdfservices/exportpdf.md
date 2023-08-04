---
title: Använda PDF Services API för att exportera PDF till Word, PowerPoint med mera
description: Läs om hur du kör PDF Services API-exportåtgärden med hjälp av exempelfiler för språken Node.js, Java och .Net
feature: PDF Services API
role: Developer
level: Intermediate
type: Tutorial
jira: KT-6674
thumbnail: KT-6674.jpg
exl-id: 55f5b04e-0249-47d9-9131-2f9ec01db7e8
source-git-commit: 5222e1626f4e79c02298e81d621216469753ca72
workflow-type: tm+mt
source-wordcount: '500'
ht-degree: 5%

---

# Använda PDF Services API för att exportera PDF till Word, PowerPoint med mera

![SKAPA PDF-HJÄLTEBILD](assets/ExportPDF_hero.jpg)

Adobe PDF Services API konverterar PDF-filer till MS Office, text och bilder med API:er. Det finns många vanliga användningssätt för att låsa upp existerande PDF för innehållsredigering och innehållsanalys, och med PDF Services API kan utvecklare enkelt integrera den här funktionen i existerande system och applikationer. Konvertera PDF-filer till MS Word för att redigera innehåll, godkänna och sedan skicka filer för signering för att skapa anpassade kontraktarbetsflöden. Eller exportera PDF-innehåll till MS Excel-format för fakturering och ekonomiska beräkningar eller dataanalys.

Exportåtgärden stöder följande PDF-filkonverteringar:

* PDF till Microsoft Word (DOC, DOCX)
* PDF till Microsoft PowerPoint (PPTX)
* PDF till Microsoft Excel (XLSX)
* PDF till text (RTF)
* PDF till bild (JPEG, PNG)

I den här självstudiekursen får du lära dig grunderna i hur du kör din första API-export av PDF Services med hjälp av exempelfiler för språk som Node.js, Java och .Net.

## Steg 1: Skapa dina inloggningsuppgifter och konfigurera din miljö:

Använd självstudiekurserna för att komma i gång nedan för att skapa dina API-uppgifter, hämta exempelfiler och konfigurera din miljö.

[Komma igång med PDF Services API och Java](gettingstartedjava.md)

[Komma igång med PDF Services API och .Net](gettingstartednet.md)

[Kom igång med PDF Services API och Node.js](createpdffromhtml.md)

## Steg 2: Kör åtgärden för att exportera PDF med hjälp av exempelfilerna

**Java**

1. Öppna kommandotolken.

1. Ändra kataloger till din exempelkodkatalog.

   Till exempel C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-java-samples

1. Kör följande kommando::

   `mvn -f pom.xml exec:java -Dexec.mainClass=com.adobe.platform.operation.samples.exportpdf.ExportPDFToDOCX`

Din PDF skapas i katalogen src/main/resources.

**.NET**

1. Öppna kommandotolken.

1. Ändra kataloger till din exempelkodkatalog.

   Till exempel C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-NetSamples

1. Ändra kataloger igen till ExportPDFtoDocx -katalogen.

1. Kör följande kommando::

   `dotnet run ExportPDFToDocx.csproj`

Din PDF skapas i samma katalog.

**Node.js**

1. Öppna kommandotolken.

1. Ändra kataloger till din exempelkodkatalog.

   Till exempel C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-node-samples

1. Kör följande kommando::

   `node src/ocr/ocr-pdf.js`

PDF skapas på den plats som anges i utdata, vilket som standard är katalogen pdfServicesSdkResult.

## Avslutande tankar

Du bör nu ha ett fungerande exempel som kan importeras till dina befintliga program för att starta ett koncepttest. I var och en av exempelkatalogerna kan du se ytterligare ett exempel för att exportera PDF-filer till bildformat. I samma steg ovan kan du köra provet också. Om du vill ändra till ett annat format kan du uppdatera koden till det nya format du vill ha:

SupportedTargetFormats.PPTX

Och destinationsresultatet:

output/exportPdfOutput.PPTX

Till ett annat format.

## Resurser och nästa steg

* Om du behöver mer hjälp och support går du till [[!DNL Adobe Acrobat Services] API:er](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) community-forum

* PDF Services API [Dokumentation](https://www.adobe.com/go/pdftoolsapi_doc)

* [VANLIGA FRÅGOR](https://community.adobe.com/t5/document-cloud-sdk/faq-for-document-services-pdf-tools-api/m-p/10726197) för PDF Services API-frågor

* [Kontakta oss](https://www.adobe.com/go/pdftoolsapi_requestform) för frågor om licenser och priser
