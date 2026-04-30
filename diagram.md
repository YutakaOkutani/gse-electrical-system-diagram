```mermaid
%%{init: {"theme": "base", "look": "classic", "themeVariables": {"background": "#020617", "mainBkg": "#111827", "primaryTextColor": "#f8fafc", "clusterBkg": "#111827", "clusterBorder": "#64748b", "lineColor": "#cbd5e1", "fontFamily": "Arial, sans-serif"}}}%%
graph LR
    classDef component fill:#1f2937,stroke:#e5e7eb,stroke-width:1.5px,color:#f9fafb;
    classDef switch fill:#3b2f08,stroke:#facc15,stroke-width:2px,color:#fff7cc;
    classDef signal fill:#0b2a4a,stroke:#60a5fa,stroke-width:1.5px,stroke-dasharray:4 3,color:#dbeafe;
    classDef lanSignal fill:#0b2a4a,stroke:#60a5fa,stroke-width:1.5px,stroke-dasharray:4 3,color:#dbeafe,min-width:78px,height:42px;
    classDef relay fill:#2e1a47,stroke:#c084fc,stroke-width:1.5px,color:#f3e8ff;
    classDef actuator fill:#0f3a22,stroke:#4ade80,stroke-width:1.5px,color:#dcfce7;
    classDef ac fill:#4a1414,stroke:#f87171,stroke-width:1.5px,color:#fee2e2;
    classDef hv fill:#4a2a06,stroke:#fb923c,stroke-width:2px,color:#ffedd5;
    classDef ground fill:#111827,stroke:#94a3b8,stroke-width:1.5px,color:#e5e7eb;

    %% 点火点 (Ignition Point)
    subgraph Ignition_Point [ランチコントローラ]
        B1[DC Battery]:::component --> SW_EStop1[エマストスイッチ]:::switch
        SW_EStop1 --> SW_Main1[メインスイッチ]:::switch
        SW_Main1 --> VCC1((信号: VCC)):::signal
        B1 -.-> GND_Point((信号: GND)):::signal

        VCC1 --> R_LED1[抵抗]:::component
        R_LED1 --> LED1[LED]:::component
        LED1 --- GND_Point

        VCC1 --> SW_Fire[Fire スイッチ]:::switch
        VCC1 --> SW_Oxygen[酸素スイッチ]:::switch
        VCC1 --> SW_Dump[Dump スイッチ]:::switch
        VCC1 --> SW_Fill[Fill スイッチ]:::switch
    end

    %% 信号線 (LANケーブル)
    subgraph LAN_Cable [LANケーブル / 約70m]
        direction TB
        L_Fire((信号: Fire)):::lanSignal
        L_Oxygen((信号: 酸素)):::lanSignal
        L_Dump((信号: Dump)):::lanSignal
        L_Fill((信号: Fill)):::lanSignal
        L_GND((信号: GND)):::lanSignal
    end

    SW_Fire --> L_Fire
    SW_Oxygen --> L_Oxygen
    SW_Dump --> L_Dump
    SW_Fill --> L_Fill
    GND_Point --- L_GND

    %% GSE (コントロール BOX)
    subgraph GSE [コントロール BOX]
        B2[DC Battery]:::component --> SW_Main2[メインスイッチ]:::switch
        SW_Main2 --> VCC2((信号: VCC)):::signal
        B2 -.-> GND_GSE((信号: GND)):::signal

        VCC2 --> R_LED2[抵抗]:::component
        R_LED2 --> LED2[LED]:::component
        LED2 --- GND_GSE
        L_GND --- GND_GSE

        %% Relays: physical count = 4
        subgraph RLY_Fire [リレー / Fire]
            RLY_Fire_Coil((コイル)):::relay
            RLY_Fire_Contact[接点]:::relay
        end
        subgraph RLY_Oxygen [リレー / 酸素]
            RLY_Oxygen_Coil((コイル)):::relay
            RLY_Oxygen_Contact[接点]:::relay
        end
        subgraph RLY_Dump [リレー / Dump]
            RLY_Dump_Coil((コイル)):::relay
            RLY_Dump_Contact[接点]:::relay
        end
        subgraph RLY_Fill [リレー / Fill]
            RLY_Fill_Coil((コイル)):::relay
            RLY_Fill_Contact[接点]:::relay
        end

        L_Fire --> RLY_Fire_Coil
        L_Oxygen --> RLY_Oxygen_Coil
        L_Dump --> RLY_Dump_Coil
        L_Fill --> RLY_Fill_Coil

        RLY_Fire_Coil --- GND_GSE
        RLY_Oxygen_Coil --- GND_GSE
        RLY_Dump_Coil --- GND_GSE
        RLY_Fill_Coil --- GND_GSE

        VCC2 --> RLY_Oxygen_Contact
        VCC2 --> RLY_Dump_Contact
        VCC2 --> RLY_Fill_Contact
        RLY_Oxygen_Contact --> RLY_Fire_Contact

        %% 電磁弁
        RLY_Oxygen_Contact --> Valve_Oxygen[酸素電磁弁]:::actuator
        RLY_Dump_Contact --> Valve_Dump[Dump電磁弁]:::actuator
        RLY_Fill_Contact --> Valve_Fill[Fill電磁弁]:::actuator

        Valve_Oxygen --- GND_GSE
        Valve_Dump --- GND_GSE
        Valve_Fill --- GND_GSE
    end

    %% GSE（イグナイター BOX）
    RLY_Fire_Contact --> L_Fire_Out((信号: Fire Out)):::signal

    subgraph Igniter_Box [イグナイター BOX]
        %% Relay: physical count = 1
        subgraph IGN_RLY [リレー / イグナイター]
            Ign_Relay_Coil((コイル)):::relay
            Ign_Relay_Contact[接点]:::relay
        end

        L_Fire_Out --> Ign_Relay_Coil
        Ign_Relay_Coil --- GND_GSE

        AC_Power[AC 100V 電源]:::ac --> Ign_Relay_Contact
        AC_Power -.-> AC_GND((AC GND / 接地)):::ground

        Ign_Relay_Contact --> Neon[ネオントランス（9kV）]:::actuator
        AC_Power --> Fuse3[ヒューズ]:::component
        Fuse3 --> Neon
        Neon -.-> AC_GND

        Neon --> OUT1[高圧出力: OUT 1]:::hv
        Neon --> OUT2[高圧出力: OUT 2]:::hv
    end

    style Ignition_Point fill:#111827,stroke:#64748b,stroke-width:1.5px,color:#f8fafc;
    style LAN_Cable fill:#0f172a,stroke:#60a5fa,stroke-width:1.5px,color:#f8fafc;
    style GSE fill:#111827,stroke:#64748b,stroke-width:1.5px,color:#f8fafc;
    style Igniter_Box fill:#111827,stroke:#64748b,stroke-width:1.5px,color:#f8fafc;
    style RLY_Fire fill:#1f1b2e,stroke:#a855f7,stroke-width:1.5px,color:#f8fafc;
    style RLY_Oxygen fill:#1f1b2e,stroke:#a855f7,stroke-width:1.5px,color:#f8fafc;
    style RLY_Dump fill:#1f1b2e,stroke:#a855f7,stroke-width:1.5px,color:#f8fafc;
    style RLY_Fill fill:#1f1b2e,stroke:#a855f7,stroke-width:1.5px,color:#f8fafc;
    style IGN_RLY fill:#1f1b2e,stroke:#a855f7,stroke-width:1.5px,color:#f8fafc;
```
