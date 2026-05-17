# 🛡️ Docura - Smart AI Archive

![Platform](https://img.shields.io/badge/Platform-iOS%2017.0+-blue.svg)
![Swift](https://img.shields.io/badge/Swift-6.0-orange.svg)
![Architecture](https://img.shields.io/badge/Architecture-MVVM-success.svg)

**Docura** is a privacy-first, offline smart document organizer and vault. It leverages on-device AI to scan, read, auto-categorize, and secure your highly sensitive documents without ever connecting to a third-party server.

📲 **[Download Docura on the App Store](#)** *[https://apps.apple.com/tr/app/docura-smart-archive/id6765619893]*

---

## 📸 Screenshots

<p align="center">
  <img src="https://github.com/user-attachments/assets/05a7f4eb-0501-4f68-b095-c3cc3c6ada03" width="250" /> &nbsp; &nbsp;
  <img src="https://github.com/user-attachments/assets/371f9179-73e8-4fb7-8fa2-bc81b9a3a4b9" width="250" /> &nbsp; &nbsp;
  <img src="https://github.com/user-attachments/assets/39f2274e-6625-4141-81bc-f715dc215820" width="250" /> &nbsp; &nbsp;
  <img src="https://github.com/user-attachments/assets/c3bf082e-23b4-457f-ae9a-e22095deabc5" width="250" /> &nbsp; &nbsp;
  <img src="https://github.com/user-attachments/assets/76db70ec-8dff-4fd0-87e9-92995287c39d" width="250" /> &nbsp; &nbsp;
  <img src="https://github.com/user-attachments/assets/056e2253-a043-40c1-b269-6509a4acdefc" width="250" />
</p>

---

## ✨ Key Features

* 🧠 **On-Device AI Classification:** Scans and automatically categorizes documents (Invoices, IDs, Resumes, Contracts) in milliseconds.
* 🔒 **High Security:** Every document is encrypted locally using **AES-256 (CryptoKit)**. 
* 📸 **Smart Scanner & OCR:** Built-in scanner powered by **VisionKit** and **PDFKit**. Extracts text automatically and sanitizes noisy OCR data.
* 🛡️ **Face ID Integration:** Granular document-level locking. Sensitive files require biometric authentication to be viewed or deleted.
* 🌍 **Multilingual Support:** Fully localized UI and AI models supporting English, Turkish, Spanish, Russian, and Arabic.

---

## 🏗️ Architecture & Tech Stack

This project was built using modern iOS frameworks with a focus on modularity and clean architecture:

* **UI Framework:** SwiftUI
* **Database:** SwiftData 
* **Architecture Pattern:** MVVM (Model-View-ViewModel) using iOS 17's `@Observable` macro.
* **Machine Learning:** Core ML, NaturalLanguage
* **Security:** CryptoKit (AES-256), LocalAuthentication (Face ID), Security (Keychain)

---

### 🧠 Implementation Details
Building an offline-first app with a Share Extension required solving a few interesting architectural puzzles:

* **App Groups & Data Migration:** To support the Share Extension, the app’s database needed to be moved to a shared container. I implemented a migration flow to safely transfer existing users' SwiftData SQLite files and hidden `.externalStorage` blobs to the new `App Group` without disrupting their experience.
* **Lightweight Multilingual Routing:** Instead of embedding a heavy LLM, the app uses `NLLanguageRecognizer` to detect the document's language on the fly. It then routes the text to one of 5 localized Core ML models (kept under 2MB each). I also added a rule-based fallback to handle complex edge cases like Resumes.
* **Shared Keychain Access:** The AES-256 Master Key is shared securely between the main app and the extension via Keychain Sharing, using the `kSecAttrAccessibleWhenUnlockedThisDeviceOnly` attribute to maintain strict local security.
* **Memory Management:** Refactored the UI to use `LazyVStack` and an MVVM approach to handle potentially large document lists efficiently, preventing memory spikes during document rendering.

---

## System Architecture 

```mermaid
graph TD
    %% Input Katmanı
    subgraph Inputs [1. Data Ingestion]
        A[Main App: Camera / Files]
        B[Share Extension: Safari / WhatsApp]
    end

    %% Çıkarım ve Temizlik
    subgraph Processing [2. Processing & OCR]
        C{VisionKit & PDFKit}
        D[Data Sanitization / Text Cleaner]
    end

    %% Yapay Zeka Katmanı
    subgraph AIEngine [3. Hybrid AI & NLP Engine]
        E[NLLanguageRecognizer]
        F{Dynamic Model Router}
        G1[BERT TR]
        G2[BERT EN]
        G3[BERT ES]
        G4[BERT RU]
        G5[BERT AR]
        H{Keyword Fallback / Safety Net}
    end

    %% Güvenlik ve Veritabanı
    subgraph Storage [4. Security & Persistence]
        I[CryptoKit AES-256]
        J[(Keychain Shared Access Group)]
        K[(SwiftData Shared App Group)]
    end

    %% Kullanıcı Deneyimi
    subgraph UX [5. User Experience]
        L[SwiftUI MVVM Dashboard]
        M[Local Notification / UI Feedback]
    end

    %% Akış Yolları
    A --> C
    B --> C
    C --> D
    
    D --> E
    E --> F
    F -->|tr| G1
    F -->|en| G2
    F -->|es| G3
    F -->|ru| G4
    F -->|ar| G5
    
    G1 & G2 & G3 & G4 & G5 --> H
    H -->|Category Decided| I
    
    J -.->|Provides Master Key| I
    I -->|Encrypted Data| K
    
    K -->|Syncs| L
    K -.->|Triggers| M

    %% Renklendirme Stilleri (Şov Kısmı)
    classDef ai fill:#2b1d52,stroke:#4facfe,stroke-width:2px,color:#fff;
    classDef security fill:#0d3b2e,stroke:#38b000,stroke-width:2px,color:#fff;
    classDef db fill:#4a154b,stroke:#ff0054,stroke-width:2px,color:#fff;
    
    class E,F,G1,G2,G3,G4,G5,H ai;
    class I,J security;
    class K db;


