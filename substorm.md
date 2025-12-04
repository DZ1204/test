# 增强型 Earthfarseer 模型框架

```mermaid
graph TD
    subgraph Input & Feature Generation
        A[原始 ASI 图像 I(t)] --> P1{P1. 几何校正 & AMGeO 投影}
        B[原始 ASI 图像 I(t-1)] --> P1
        P1 --> P2[校正图像 I'(t)]
        P1 --> P3[校正图像 I'(t-1)]
        P3 & P2 --> F1(P2. RAFT/DOFM 光流计算)
        F1 --> F2(P3. 运动动力学特征计算)
        F2 --> MFE_In[光流场 F + 散度/旋度]
        S[Solar Wind 1D Parameters]
    end

    subgraph Earthfarseer Core Architecture
        subgraph Encoders (特征编码器)
            P2 --> E_Auroral[Auroral Image Encoder (CNN)]
            MFE_In --> E_MFE[Motion Feature Extractor (MFE)]
            S --> E_SW[Solar Wind Encoder (MLP)]

            E_Auroral --> Z_Auroral[空间特征 Z_I]
            E_MFE --> Z_MFE[运动特征 Z_M]
            E_SW --> Z_SW[驱动特征 Z_S]
        end

        Z_Auroral & Z_MFE & Z_SW --> C_T[Temporal Coupling Transformer]

        C_T --> D[Image Decoder (Deconvolution)]
    end

    subgraph Output
        D --> OUT_I[Predicted Auroral Image I'(t+1...t+N)]
        C_T --> OUT_Index[Index Head (Predicted AE/AL/Onset)]
    end

    style P1 fill:#f9f,stroke:#333
    style F1 fill:#f9f,stroke:#333
    style F2 fill:#f9f,stroke:#333
    style E_Auroral fill:#ccf,stroke:#333
    style E_MFE fill:#ccf,stroke:#333
    style C_T fill:#cfc,stroke:#333,stroke-width:2px
