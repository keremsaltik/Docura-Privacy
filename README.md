# 🛡️ Docura - Smart Archive & Cloud Vault

![Platform](https://img.shields.io/badge/Platform-iOS%2017.0+-blue.svg)
![Swift](https://img.shields.io/badge/Swift-6.0-orange.svg)
![Architecture](https://img.shields.io/badge/Architecture-MVVM-success.svg)

**Docura** is a privacy-first smart document organizer and vault. It leverages on-device AI to scan, auto-categorize, and encrypt your sensitive documents. With the V2.0 update, Docura introduces seamless **iCloud Synchronization** and **Native PDF Editing**, completely protected behind a zero-knowledge architecture.

📲 **Download Docura on the App Store** *[https://apps.apple.com/tr/app/docura-smart-archive/id6765619893]*

---

## 📸 Screenshots

<p align="center">
  <img src="https://github.com/user-attachments/assets/05a7f4eb-0501-4f68-b095-c3cc3c6ada03" width="250" /> &nbsp; &nbsp;
  <img src="https://github.com/user-attachments/assets/371f9179-73e8-4fb7-8fa2-bc81b9a3a4b9" width="250" /> &nbsp; &nbsp;
  <img src="https://github.com/user-attachments/assets/39f2274e-6625-4141-81bc-f715dc215820" width="250" /> &nbsp; &nbsp;
  <img src="https://github.com/user-attachments/assets/c3bf082e-23b4-457f-ae9a-e22095deabc5" width="250" /> &nbsp; &nbsp;
  <img src="https://github.com/user-attachments/assets/76db70ec-8dff-4fd0-87e9-92995287c39d" width="250" /> &nbsp; &nbsp;
  <img src="https://github.com/user-attachments/assets/056e2253-a043-40c1-b269-6509a4acdefc" width="250" /> &nbsp; &nbsp;
  <img src="https://github.com/user-attachments/assets/11674504-f762-4c0c-889e-a5e0f5368bda" width="250" />
</p>

---

## ✨ Key Features

* ☁️ **End-to-End Encrypted iCloud Sync:** Seamlessly syncs encrypted documents across iOS and macOS devices using `CloudKit` and `Ubiquity Container`, ensuring Apple cannot read your data.
* 🖋️ **Native PDF Editing & Signing:** Built-in custom `PDFKit` wrapper allows users to draw, sign, and annotate directly on PDFs. Modified documents are instantly re-encrypted.
* 🧠 **On-Device AI Classification:** Detects languages on the fly and routes text to 5 localized **BERT Embedding models** for precise categorization.
* 🔒 **High Security:** Every document is encrypted locally using **AES-256 (CryptoKit)**. Encryption keys are safely stored in the device's **Secure Enclave via Keychain**.
* 📲 **Share Extension & Deep Linking:** Send files directly from Safari or WhatsApp to Docura in the background, with a custom URL scheme (`docura://`) for instant deep linking.


---

## 🏗️ Architecture & Tech Stack

This project was built using modern iOS frameworks with a focus on modularity and clean architecture:

* **UI Framework:** SwiftUI
* **Database:** SwiftData 
* **Architecture Pattern:** MVVM (Model-View-ViewModel) using iOS 17's `@Observable` macro.
* **Machine Learning:** Core ML, NaturalLanguage
* **Security:** CryptoKit (AES-256), LocalAuthentication (Face ID), Security (Keychain)

---

## 🧠 Engineering Highlights

Building a scalable, freemium, cloud-connected app required solving complex architectural puzzles:

* **Dual-Storage Architecture:** Decoupled the `SwiftData` database from physical files. Heavy PDFs are now encrypted and stored directly in the File System (`FileManager`), keeping the SQLite database incredibly lightweight (< 2MB) for lightning-fast queries.
* **App Groups & Keychain Migration:** Broke out of the standard app sandbox. Engineered a silent migration script that transferred existing users' data and AES keys into a shared `App Group`, enabling the Share Extension without data loss.
* **StoreKit 2 Integration:** Built a robust, real-time Paywall and Entitlement engine using Apple's latest `StoreKit 2` async/await APIs to manage lifetime and monthly subscriptions securely.
* **Smart Concurrency (Thread Safety):** Solved SwiftData's background-thread deadlocks by extracting `persistentModelID`s before handing off intensive decryption tasks to `Task.detached`, ensuring 120fps UI performance.

---

## 🏗️ V2.0 System Architecture Diagram

```mermaid
graph TD
    %% Input Layer
    subgraph Inputs [1. Data Ingestion]
        A[Main App: Scanner / Import]
        B[Share Extension: Safari / WhatsApp]
    end

    %% Processing & Edit
    subgraph Processing [2. Processing & Edit]
        C{VisionKit & PDFKit}
        D[Data Sanitization]
        C2[PDF Native Editor/Sign]
        C <--> C2
    end

    %% AI Layer
    subgraph AIEngine [3. Hybrid AI Engine]
        E[NLLanguageRecognizer]
        F{Dynamic Model Router}
        G1[BERT TR/EN/ES/RU/AR]
        H{Keyword Safety Net}
    end

    %% Security & Storage
    subgraph Storage [4. Dual-Storage & Security]
        I[CryptoKit AES-256]
        J[(Shared Keychain + iCloud Sync)]
        K1[(SwiftData: Fast Index)]
        K2[File System: Encrypted Blobs]
    end

    %% Cloud Layer
    subgraph Cloud [5. CloudKit & Monetization]
        M[StoreKit 2: Entitlement Check]
        N[CloudKit Database]
        O[iCloud Drive Ubiquity Container]
    end

    %% Flow
    A & B --> C
    C --> D
    D --> E
    E --> F --> G1 --> H
    
    H --> I
    J -.->|Provides Key| I
    I -->|Saves Metadata| K1
    I -->|Saves Heavy Files| K2
    
    M -.->|Unlocks Pro| N & O
    K1 <-->|Syncs| N
    K2 <-->|Upload/Download| O

    classDef ai fill:#2b1d52,stroke:#4facfe,stroke-width:2px,color:#fff;
    classDef security fill:#0d3b2e,stroke:#38b000,stroke-width:2px,color:#fff;
    classDef db fill:#4a154b,stroke:#ff0054,stroke-width:2px,color:#fff;
    classDef cloud fill:#0077b6,stroke:#00b4d8,stroke-width:2px,color:#fff;
    
    class E,F,G1,H ai;
    class I,J security;
    class K1,K2 db;
    class M,N,O cloud;


