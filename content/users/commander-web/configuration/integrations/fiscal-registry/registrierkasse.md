---
title: Registrierkasse
---

There are two stages for setting up the Registrierkasse integration. 
The first stage is a preparation that can be carried out by a hotel without an involvement of Mews. Once the hotel completes the necessary steps of the first stage, they shall contact Mews. 
The second stage must be implemented only under the supervision of the Mews support team.

## Steps needed to be done before Mews steps in: 

- The hotel should be familiar with the concept of a Registrierkasse. Note that the Registrierkasse is a virtual cashier, not a physical one. There is no need to buy any hardware. Mews facilitates the cashier and cashier security device (provided to Mews by “A-Trust” in the form of an API which accepts the username, password & public key of a certificate).
 
- The hotel should download the BMF Belegcheck mobile app (QR code scanner / Startbeleg verifier), as well as generate an authentication code for it. The application will be used once when setting up the integration in order to read the generated QR code and activate the system. 


## Before setting the integration, always get in touch with the Mews support team. The following steps must be carried out together with the support: 

- Mews provides the A-Trust User identifier & A-Trust password to the property. After that, the property registers itself at the Austrian Finanzamt, using the signing certificate Mews obtained from A-Trust. By doing so, the hotel receives access to Finanzonline, which is the Ministry’s extranet.
 
- There always needs to be an additional Bill counter called Registrierkasse. The integration and the bill counter have to be linked. Bills shall not be closed on other counters. 
 
- Mews will provide the hotel with the registry identifier and AES key. The hotel would need to register them on FinanzOnline. This is generally done via a screen sharing with Mews, to ensure all the steps are done correctly. After that, the hotel should create a cashier. 
 
- Once we set up the cashier, there is the first bill that needs to be verified. Here is where the Startbelegt’s code is scanned with the BMF Belegcheck application. The application is either going to show a green tick, meaning everything went well, or it can show a red cross, meaning something went wrong, which would require a further check by the Mews team. 


Once the initial bill is scanned and the integration is activated, hotels should not disable it by themselves, as it would not be possible to re-activate the same Registrierkasse integration. 

## QR code:

QR code appears for every cash or credit card transaction, however, it does not appear on a bill in the case of any external payment such as a bank wire transfer, invoice and others.

## Registrierkasse (DEP) export: 

In the case of a legal check, run the Registrierkasse (DEP) export in the newly created Fiscal report record. It will create a ZIP archive with three files. The file named
dep-export.json is the one that should be submitted to the government.

Every month there has to be a special monthly Fiscal record with an information about
the turnover counter. You could find this record in the new Fiscal record report. It
would be called NULL BELEG and it would have no Mews bill attached.
 
Hotels are obliged to have the DEP export of every register they ever had. Even if some Registrierkasse integration was disabled before, DEP exports for it should be archived.
