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
