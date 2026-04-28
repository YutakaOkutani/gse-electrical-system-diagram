```mermaid
graph LR
    %% 点火点 (Ignition Point)
    subgraph Ignition_Point [点火点 BOX]
        B1[DC Battery] --> Fuse1[DC Fuse]
        Fuse1 --> SW_Main1[Main Switch]
        SW_Main1 --> VCC1[VCC]
        B1 -.-> GND_Point[GND]

        VCC1 --> LED1[LED + R]
        GND_Point --- LED1
        
        VCC1 --> SW_Fill[Fill SW]
        VCC1 --> SW_Dump[Dump SW]
        VCC1 --> SW_O2[O2 SW]
        VCC1 --> SW_Fire[Fire SW]
    end

    %% 信号線 (LANケーブル)
    subgraph LAN_Cable [LANケーブル / 約70m]
        L_Fire[Fire Signal]
        L_O2[O2 Signal]
        L_Dump[Dump Signal]
        L_Fill[Fill Signal]
        L_GND[GND Signal]
    end

    SW_Fire --> L_Fire
    SW_O2 --> L_O2
    SW_Dump --> L_Dump
    SW_Fill --> L_Fill
    GND_Point --- L_GND

    %% GSE (Control Box)
    subgraph GSE [GSE Control Box]
        B2[DC Battery] --> Fuse2[DC Fuse]
        Fuse2 --> SW_Main2[Main Switch]
        SW_Main2 --> VCC2[VCC]
        B2 -.-> GND_GSE[GND]
        
        VCC2 --> LED2[LED + R]
        GND_GSE --- LED2
        L_GND --- GND_GSE
        
        %% Relay Coils
        L_Fire --> RLY_Fire_Coil(Relay Coil: Fire)
        L_O2 --> RLY_O2_Coil(Relay Coil: O2)
        L_Dump --> RLY_Dump_Coil(Relay Coil: Dump)
        L_Fill --> RLY_Fill_Coil(Relay Coil: Fill)
        
        RLY_Fire_Coil --- GND_GSE
        RLY_O2_Coil --- GND_GSE
        RLY_Dump_Coil --- GND_GSE
        RLY_Fill_Coil --- GND_GSE
        
        %% Relay Contacts (状態保留)
        VCC2 --> RLY_Fire_Contact[Relay Contact: Fire / TBD]
        VCC2 --> RLY_O2_Contact[Relay Contact: O2 / TBD]
        VCC2 --> RLY_Dump_Contact[Relay Contact: Dump / TBD]
        VCC2 --> RLY_Fill_Contact[Relay Contact: Fill / TBD]
        
        %% Actuators (Solenoid Valves)
        RLY_O2_Contact --> Valve_O2[O2 Solenoid Valve]
        RLY_Dump_Contact --> Valve_Dump[Dump Solenoid Valve]
        RLY_Fill_Contact --> Valve_Fill[Fill Solenoid Valve]
        
        Valve_O2 --- GND_GSE
        Valve_Dump --- GND_GSE
        Valve_Fill --- GND_GSE
    end

    %% Igniter Box
    RLY_Fire_Contact --> L_Fire_Out[Fire Out Line]
    
    subgraph Igniter_Box [Igniter Box]
        L_Fire_Out --> Ign_Relay_Coil(Relay Coil)
        Ign_Relay_Coil --- GND_GSE
        
        AC_Power[AC 100V 電源] --> Fuse3[AC ヒューズ]
        Fuse3 --> Ign_Relay_Contact[Relay Contact / TBD]
        
        Ign_Relay_Contact --> Neon[ネオントランス 9kV]
        AC_Power --> Neon
        
        Neon --> OUT1[OUT 1]
        Neon --> OUT2[OUT 2]
    end
