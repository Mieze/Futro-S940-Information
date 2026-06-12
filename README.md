# Futro S940 Information
Hacking the Futro S940

## Datasheets of the SoC
Detailed datasheets of the Intel Pentium Silver and Intel Celeron SoC's are available for download at Intel's website. Volume 1 describes the general architecture and the hardware interfaces:

[Intel Pentium Silver and Intel Celeron Processors - Datasheet Volume 1 od 2](https://www.google.com/url?sa=t&source=web&rct=j&opi=89978449&url=https://cdrdv2-public.intel.com/336560/336560-glk-datasheet-volume1_rev005.pdf&ved=2ahUKEwiKvofb8oGVAxUQA9sEHQs9DP8QFnoECBoQAQ&usg=AOvVaw2OrarG-PAaiSxW3P3Zl4a8)

Volume 2 contains the detailed documentation of the SoCs internal registers and it's integrated PCIe devices:

[Intel Pentium Silver and Intel Celeron Processors - Datasheet Volume 2 od 2](https://www.google.com/url?sa=t&source=web&rct=j&opi=89978449&url=https://cdrdv2-public.intel.com/336561/336561-glk-datasheet-volume2.pdf&ved=2ahUKEwj8lJzl4PqUAxWPX_EDHWhaAYwQFnoECBwQAQ&usg=AOvVaw2_Evcj3hlVqmmZjpHsN-nM)


## Extract the UEFI Setup Variables
Todo

The information provided below applies to version V5.0.0.13 R1.14.0 for the D3543-A1x board, which is used in the Fujitsu Futro S940 thin clients. Other versions may have different setup variables. Before you change any variable, make sure that you are the same version, because you might render your board unbootable if you change the wrong variable. Proceed with extreme caution!!!

There is a tool to read/write UEFI setup variables called setup_var.efi, which is a rewritten version of the modified grub shell used to change UEFi variables in the past. Go to https://github.com/datasone/setup_var.efi to download the tool and get instructions how to use it.

## Enable ASPM
There is a global switch to enable ASPM support, which has to be changed first before you may setup ASPM for individual PCIe ports.

|Name       |Variable|Value|Description          |
|-----------|--------|-----|---------------------|
|Native ASPM|   0x4E0|    0|ASPM support disabled|
|           |        |    1|ASPM support enabled |


Each PCIe root port has its own variable to configure ASPM support:

|PCI Express Root Port|Variable|lspci device|Description          |
|---------------------|--------|------------|---------------------|
|#1|0x303|00:14.0|unknown device|
|#2|0x304|00:14.1|unknown device|
|#3|0x305|00:13.0|PCIe x1 Slot|
|#4|0x306|00:13.1|M.2 Key E|
|#5|0x307|00:13.2|RTL8111 onboard LAN|
|#6|0x308|00:13.3|PoE connector|


These variables can take one of the following values to setup ASPM support for the corresponding port:

|Name|Value|Description|
|----|-----|-----------|
|ASPM|0|ASPM disabled|
||1|L0s supported|
||2|L1 supported|
||3|L0s and L1 supported|
||4|Autoconfigure ASPM support|

**Please note that a PCIe root port needs the ClkReq# signal signal to be present for L1 to work. Unfortunately, the PoE port is missing ClkReq# so that ASPM L1 won't work on this port.**

With ASPM L0s and L1 enabled for all integrated PCIe bridges **lspci -vvv** outputs:
```
00:13.0 PCI bridge: Intel Corporation Gemini Lake PCI Express Root Port (rev f3) (prog-if 00 [Normal decode])
	Subsystem: Fujitsu Technology Solutions Device 1240
	Control: I/O+ Mem+ BusMaster+ SpecCycle- MemWINV- VGASnoop- ParErr- Stepping- SERR- FastB2B- DisINTx+
	Status: Cap+ 66MHz- UDF- FastB2B- ParErr- DEVSEL=fast >TAbort- <TAbort- <MAbort- >SERR- <PERR- INTx-
	Latency: 0, Cache Line Size: 64 bytes
	Interrupt: pin A routed to IRQ 123
	IOMMU group: 4
	Bus: primary=00, secondary=01, subordinate=01, sec-latency=0
	I/O behind bridge: e000-efff [size=4K] [16-bit]
	Memory behind bridge: a1300000-a13fffff [size=1M] [32-bit]
	Prefetchable memory behind bridge: 00000000fff00000-00000000000fffff [disabled] [64-bit]
	Secondary status: 66MHz- FastB2B- ParErr- DEVSEL=fast >TAbort- <TAbort- <MAbort- <SERR- <PERR-
	BridgeCtl: Parity- SERR+ NoISA- VGA- VGA16+ MAbort- >Reset- FastB2B-
		PriDiscTmr- SecDiscTmr- DiscTmrStat- DiscTmrSERREn-
	Capabilities: [40] Express (v2) Root Port (Slot+), IntMsgNum 0
		DevCap:	MaxPayload 256 bytes, PhantFunc 0
			ExtTag- RBE+ TEE-IO-
		DevCtl:	CorrErr- NonFatalErr- FatalErr- UnsupReq-
			RlxdOrd- ExtTag- PhantFunc- AuxPwr- NoSnoop-
			MaxPayload 256 bytes, MaxReadReq 128 bytes
		DevSta:	CorrErr+ NonFatalErr- FatalErr- UnsupReq- AuxPwr+ TransPend-
		LnkCap:	Port #3, Speed 5GT/s, Width x1, ASPM L0s L1, Exit Latency L0s <1us, L1 <4us
			ClockPM- Surprise- LLActRep+ BwNot+ ASPMOptComp+
		LnkCtl:	ASPM L1 Enabled; RCB 64 bytes, LnkDisable- CommClk+
			ExtSynch- ClockPM- AutWidDis- BWInt+ AutBWInt+ FltModeDis-
		LnkSta:	Speed 5GT/s, Width x1
			TrErr- Train- SlotClk+ DLActive+ BWMgmt- ABWMgmt-
		SltCap:	AttnBtn- PwrCtrl- MRL- AttnInd- PwrInd- HotPlug- Surprise-
			Slot #1, PowerLimit 10W; Interlock- NoCompl+
		SltCtl:	Enable: AttnBtn- PwrFlt- MRL- PresDet- CmdCplt- HPIrq- LinkChg-
			Control: AttnInd Unknown, PwrInd Unknown, Power- Interlock-
		SltSta:	Status: AttnBtn- PowerFlt- MRL- CmdCplt- PresDet+ Interlock-
			Changed: MRL- PresDet- LinkState+
		RootCap: CRSVisible-
		RootCtl: ErrCorrectable- ErrNon-Fatal- ErrFatal- PMEIntEna- CRSVisible-
		RootSta: PME ReqID 0000, PMEStatus- PMEPending-
		DevCap2: Completion Timeout: Range ABC, TimeoutDis+ NROPrPrP- LTR+
			 10BitTagComp- 10BitTagReq- OBFF Not Supported, ExtFmt- EETLPPrefix-
			 EmergencyPowerReduction Not Supported, EmergencyPowerReductionInit-
			 FRS- LN System CLS Not Supported, TPHComp- ExtTPHComp- ARIFwd+
			 AtomicOpsCap: Routing- 32bit- 64bit- 128bitCAS-
		DevCtl2: Completion Timeout: 50us to 50ms, TimeoutDis+ ARIFwd+
			 AtomicOpsCtl: ReqEn- EgressBlck-
			 IDOReq- IDOCompl- LTR+ EmergencyPowerReductionReq-
			 10BitTagReq- OBFF Disabled, EETLPPrefixBlk-
		LnkCap2: Supported Link Speeds: 2.5-5GT/s, Crosslink- Retimer- 2Retimers- DRS-
		LnkCtl2: Target Link Speed: 5GT/s, EnterCompliance- SpeedDis-
			 Transmit Margin: Normal Operating Range, EnterModifiedCompliance- ComplianceSOS-
			 Compliance Preset/De-emphasis: -6dB de-emphasis, 0dB preshoot
		LnkSta2: Current De-emphasis Level: -6dB, EqualizationComplete- EqualizationPhase1-
			 EqualizationPhase2- EqualizationPhase3- LinkEqualizationRequest-
			 Retimer- 2Retimers- CrosslinkRes: unsupported, FltMode-
	Capabilities: [80] MSI: Enable+ Count=1/1 Maskable- 64bit-
		Address: fee00218  Data: 0000
	Capabilities: [90] Subsystem: Fujitsu Technology Solutions Device 1240
	Capabilities: [a0] Power Management version 3
		Flags: PMEClk- DSI- D1- D2- AuxCurrent=0mA PME(D0+,D1-,D2-,D3hot+,D3cold+)
		Status: D0 NoSoftRst- PME-Enable- DSel=0 DScale=0 PME-
	Capabilities: [100 v0] Null
	Capabilities: [140 v1] Access Control Services
		ACSCap:	SrcValid+ TransBlk+ ReqRedir+ CmpltRedir+ UpstreamFwd- EgressCtrl- DirectTrans-
		ACSCtl:	SrcValid+ TransBlk- ReqRedir+ CmpltRedir+ UpstreamFwd- EgressCtrl- DirectTrans-
	Capabilities: [150 v0] Null
	Capabilities: [200 v1] L1 PM Substates
		L1SubCap: PCI-PM_L1.2+ PCI-PM_L1.1+ ASPM_L1.2+ ASPM_L1.1+ L1_PM_Substates+
			  PortCommonModeRestoreTime=40us PortTPowerOnTime=10us
		L1SubCtl1: PCI-PM_L1.2+ PCI-PM_L1.1+ ASPM_L1.2+ ASPM_L1.1+
			   T_CommonMode=150us LTR1.2_Threshold=306176ns
		L1SubCtl2: T_PwrOn=150us
	Kernel driver in use: pcieport
	Kernel modules: shpchp

00:13.1 PCI bridge: Intel Corporation Gemini Lake PCI Express Root Port (rev f3) (prog-if 00 [Normal decode])
	Subsystem: Fujitsu Technology Solutions Device 1240
	Control: I/O+ Mem+ BusMaster+ SpecCycle- MemWINV- VGASnoop- ParErr- Stepping- SERR- FastB2B- DisINTx+
	Status: Cap+ 66MHz- UDF- FastB2B- ParErr- DEVSEL=fast >TAbort- <TAbort- <MAbort- >SERR- <PERR- INTx-
	Latency: 0, Cache Line Size: 64 bytes
	Interrupt: pin B routed to IRQ 124
	IOMMU group: 5
	Bus: primary=00, secondary=02, subordinate=02, sec-latency=0
	I/O behind bridge: f000-0fff [disabled] [16-bit]
	Memory behind bridge: a1000000-a11fffff [size=2M] [32-bit]
	Prefetchable memory behind bridge: 00000000fff00000-00000000000fffff [disabled] [64-bit]
	Secondary status: 66MHz- FastB2B- ParErr- DEVSEL=fast >TAbort- <TAbort- <MAbort- <SERR- <PERR-
	BridgeCtl: Parity- SERR+ NoISA- VGA- VGA16+ MAbort- >Reset- FastB2B-
		PriDiscTmr- SecDiscTmr- DiscTmrStat- DiscTmrSERREn-
	Capabilities: [40] Express (v2) Root Port (Slot+), IntMsgNum 0
		DevCap:	MaxPayload 256 bytes, PhantFunc 0
			ExtTag- RBE+ TEE-IO-
		DevCtl:	CorrErr- NonFatalErr- FatalErr- UnsupReq-
			RlxdOrd- ExtTag- PhantFunc- AuxPwr- NoSnoop-
			MaxPayload 128 bytes, MaxReadReq 128 bytes
		DevSta:	CorrErr- NonFatalErr- FatalErr- UnsupReq- AuxPwr+ TransPend-
		LnkCap:	Port #4, Speed 5GT/s, Width x1, ASPM L0s L1, Exit Latency L0s <1us, L1 <4us
			ClockPM- Surprise- LLActRep+ BwNot+ ASPMOptComp+
		LnkCtl:	ASPM L0s L1 Enabled; RCB 64 bytes, LnkDisable- CommClk+
			ExtSynch- ClockPM- AutWidDis- BWInt+ AutBWInt+ FltModeDis-
		LnkSta:	Speed 5GT/s, Width x1
			TrErr- Train- SlotClk+ DLActive+ BWMgmt- ABWMgmt-
		SltCap:	AttnBtn- PwrCtrl- MRL- AttnInd- PwrInd- HotPlug- Surprise-
			Slot #2, PowerLimit 10W; Interlock- NoCompl+
		SltCtl:	Enable: AttnBtn- PwrFlt- MRL- PresDet- CmdCplt- HPIrq- LinkChg-
			Control: AttnInd Unknown, PwrInd Unknown, Power- Interlock-
		SltSta:	Status: AttnBtn- PowerFlt- MRL- CmdCplt- PresDet+ Interlock-
			Changed: MRL- PresDet- LinkState+
		RootCap: CRSVisible-
		RootCtl: ErrCorrectable- ErrNon-Fatal- ErrFatal- PMEIntEna- CRSVisible-
		RootSta: PME ReqID 0000, PMEStatus- PMEPending-
		DevCap2: Completion Timeout: Range ABC, TimeoutDis+ NROPrPrP- LTR+
			 10BitTagComp- 10BitTagReq- OBFF Not Supported, ExtFmt- EETLPPrefix-
			 EmergencyPowerReduction Not Supported, EmergencyPowerReductionInit-
			 FRS- LN System CLS Not Supported, TPHComp- ExtTPHComp- ARIFwd+
			 AtomicOpsCap: Routing- 32bit- 64bit- 128bitCAS-
		DevCtl2: Completion Timeout: 50us to 50ms, TimeoutDis+ ARIFwd-
			 AtomicOpsCtl: ReqEn- EgressBlck-
			 IDOReq- IDOCompl- LTR+ EmergencyPowerReductionReq-
			 10BitTagReq- OBFF Disabled, EETLPPrefixBlk-
		LnkCap2: Supported Link Speeds: 2.5-5GT/s, Crosslink- Retimer- 2Retimers- DRS-
		LnkCtl2: Target Link Speed: 5GT/s, EnterCompliance- SpeedDis-
			 Transmit Margin: Normal Operating Range, EnterModifiedCompliance- ComplianceSOS-
			 Compliance Preset/De-emphasis: -6dB de-emphasis, 0dB preshoot
		LnkSta2: Current De-emphasis Level: -6dB, EqualizationComplete- EqualizationPhase1-
			 EqualizationPhase2- EqualizationPhase3- LinkEqualizationRequest-
			 Retimer- 2Retimers- CrosslinkRes: unsupported, FltMode-
	Capabilities: [80] MSI: Enable+ Count=1/1 Maskable- 64bit-
		Address: fee00238  Data: 0000
	Capabilities: [90] Subsystem: Fujitsu Technology Solutions Device 1240
	Capabilities: [a0] Power Management version 3
		Flags: PMEClk- DSI- D1- D2- AuxCurrent=0mA PME(D0+,D1-,D2-,D3hot+,D3cold+)
		Status: D0 NoSoftRst- PME-Enable- DSel=0 DScale=0 PME-
	Capabilities: [100 v0] Null
	Capabilities: [140 v1] Access Control Services
		ACSCap:	SrcValid+ TransBlk+ ReqRedir+ CmpltRedir+ UpstreamFwd- EgressCtrl- DirectTrans-
		ACSCtl:	SrcValid+ TransBlk- ReqRedir+ CmpltRedir+ UpstreamFwd- EgressCtrl- DirectTrans-
	Capabilities: [150 v0] Null
	Capabilities: [200 v1] L1 PM Substates
		L1SubCap: PCI-PM_L1.2+ PCI-PM_L1.1+ ASPM_L1.2+ ASPM_L1.1+ L1_PM_Substates+
			  PortCommonModeRestoreTime=40us PortTPowerOnTime=10us
		L1SubCtl1: PCI-PM_L1.2+ PCI-PM_L1.1+ ASPM_L1.2+ ASPM_L1.1+
			   T_CommonMode=40us LTR1.2_Threshold=98304ns
		L1SubCtl2: T_PwrOn=52us
	Kernel driver in use: pcieport
	Kernel modules: shpchp

00:13.2 PCI bridge: Intel Corporation Gemini Lake PCI Express Root Port (rev f3) (prog-if 00 [Normal decode])
	Subsystem: Fujitsu Technology Solutions Device 1240
	Control: I/O+ Mem+ BusMaster+ SpecCycle- MemWINV- VGASnoop- ParErr- Stepping- SERR- FastB2B- DisINTx+
	Status: Cap+ 66MHz- UDF- FastB2B- ParErr- DEVSEL=fast >TAbort- <TAbort- <MAbort- >SERR- <PERR- INTx-
	Latency: 0, Cache Line Size: 64 bytes
	Interrupt: pin C routed to IRQ 125
	IOMMU group: 6
	Bus: primary=00, secondary=03, subordinate=03, sec-latency=0
	I/O behind bridge: d000-dfff [size=4K] [16-bit]
	Memory behind bridge: a1200000-a12fffff [size=1M] [32-bit]
	Prefetchable memory behind bridge: 00000000fff00000-00000000000fffff [disabled] [64-bit]
	Secondary status: 66MHz- FastB2B- ParErr- DEVSEL=fast >TAbort- <TAbort- <MAbort- <SERR- <PERR-
	BridgeCtl: Parity- SERR+ NoISA- VGA- VGA16+ MAbort- >Reset- FastB2B-
		PriDiscTmr- SecDiscTmr- DiscTmrStat- DiscTmrSERREn-
	Capabilities: [40] Express (v2) Root Port (Slot+), IntMsgNum 0
		DevCap:	MaxPayload 256 bytes, PhantFunc 0
			ExtTag- RBE+ TEE-IO-
		DevCtl:	CorrErr- NonFatalErr- FatalErr- UnsupReq-
			RlxdOrd- ExtTag- PhantFunc- AuxPwr- NoSnoop-
			MaxPayload 128 bytes, MaxReadReq 128 bytes
		DevSta:	CorrErr- NonFatalErr- FatalErr- UnsupReq- AuxPwr+ TransPend-
		LnkCap:	Port #5, Speed 5GT/s, Width x1, ASPM L0s L1, Exit Latency L0s <1us, L1 <4us
			ClockPM- Surprise- LLActRep+ BwNot+ ASPMOptComp+
		LnkCtl:	ASPM L1 Enabled; RCB 64 bytes, LnkDisable- CommClk+
			ExtSynch- ClockPM- AutWidDis- BWInt+ AutBWInt+ FltModeDis-
		LnkSta:	Speed 2.5GT/s, Width x1
			TrErr- Train- SlotClk+ DLActive+ BWMgmt- ABWMgmt-
		SltCap:	AttnBtn- PwrCtrl- MRL- AttnInd- PwrInd- HotPlug- Surprise-
			Slot #0, PowerLimit 10W; Interlock- NoCompl+
		SltCtl:	Enable: AttnBtn- PwrFlt- MRL- PresDet- CmdCplt- HPIrq- LinkChg-
			Control: AttnInd Unknown, PwrInd Unknown, Power- Interlock-
		SltSta:	Status: AttnBtn- PowerFlt- MRL- CmdCplt- PresDet+ Interlock-
			Changed: MRL- PresDet- LinkState+
		RootCap: CRSVisible-
		RootCtl: ErrCorrectable- ErrNon-Fatal- ErrFatal- PMEIntEna- CRSVisible-
		RootSta: PME ReqID 0000, PMEStatus- PMEPending-
		DevCap2: Completion Timeout: Range ABC, TimeoutDis+ NROPrPrP- LTR+
			 10BitTagComp- 10BitTagReq- OBFF Not Supported, ExtFmt- EETLPPrefix-
			 EmergencyPowerReduction Not Supported, EmergencyPowerReductionInit-
			 FRS- LN System CLS Not Supported, TPHComp- ExtTPHComp- ARIFwd+
			 AtomicOpsCap: Routing- 32bit- 64bit- 128bitCAS-
		DevCtl2: Completion Timeout: 50us to 50ms, TimeoutDis+ ARIFwd-
			 AtomicOpsCtl: ReqEn- EgressBlck-
			 IDOReq- IDOCompl- LTR+ EmergencyPowerReductionReq-
			 10BitTagReq- OBFF Disabled, EETLPPrefixBlk-
		LnkCap2: Supported Link Speeds: 2.5-5GT/s, Crosslink- Retimer- 2Retimers- DRS-
		LnkCtl2: Target Link Speed: 5GT/s, EnterCompliance- SpeedDis-
			 Transmit Margin: Normal Operating Range, EnterModifiedCompliance- ComplianceSOS-
			 Compliance Preset/De-emphasis: -6dB de-emphasis, 0dB preshoot
		LnkSta2: Current De-emphasis Level: -3.5dB, EqualizationComplete- EqualizationPhase1-
			 EqualizationPhase2- EqualizationPhase3- LinkEqualizationRequest-
			 Retimer- 2Retimers- CrosslinkRes: unsupported, FltMode-
	Capabilities: [80] MSI: Enable+ Count=1/1 Maskable- 64bit-
		Address: fee00258  Data: 0000
	Capabilities: [90] Subsystem: Fujitsu Technology Solutions Device 1240
	Capabilities: [a0] Power Management version 3
		Flags: PMEClk- DSI- D1- D2- AuxCurrent=0mA PME(D0+,D1-,D2-,D3hot+,D3cold+)
		Status: D0 NoSoftRst- PME-Enable- DSel=0 DScale=0 PME-
	Capabilities: [100 v0] Null
	Capabilities: [140 v1] Access Control Services
		ACSCap:	SrcValid+ TransBlk+ ReqRedir+ CmpltRedir+ UpstreamFwd- EgressCtrl- DirectTrans-
		ACSCtl:	SrcValid+ TransBlk- ReqRedir+ CmpltRedir+ UpstreamFwd- EgressCtrl- DirectTrans-
	Capabilities: [150 v0] Null
	Capabilities: [200 v1] L1 PM Substates
		L1SubCap: PCI-PM_L1.2+ PCI-PM_L1.1+ ASPM_L1.2+ ASPM_L1.1+ L1_PM_Substates+
			  PortCommonModeRestoreTime=40us PortTPowerOnTime=10us
		L1SubCtl1: PCI-PM_L1.2- PCI-PM_L1.1- ASPM_L1.2- ASPM_L1.1-
			   T_CommonMode=0us LTR1.2_Threshold=0ns
		L1SubCtl2: T_PwrOn=10us
	Kernel driver in use: pcieport
	Kernel modules: shpchp

00:13.3 PCI bridge: Intel Corporation Gemini Lake PCI Express Root Port (rev f3) (prog-if 00 [Normal decode])
	Subsystem: Fujitsu Technology Solutions Device 1240
	Control: I/O+ Mem+ BusMaster+ SpecCycle- MemWINV- VGASnoop- ParErr- Stepping- SERR- FastB2B- DisINTx+
	Status: Cap+ 66MHz- UDF- FastB2B- ParErr- DEVSEL=fast >TAbort- <TAbort- <MAbort- >SERR- <PERR- INTx-
	Latency: 0, Cache Line Size: 64 bytes
	Interrupt: pin D routed to IRQ 126
	IOMMU group: 7
	Bus: primary=00, secondary=04, subordinate=04, sec-latency=0
	I/O behind bridge: f000-0fff [disabled] [16-bit]
	Memory behind bridge: fff00000-000fffff [disabled] [32-bit]
	Prefetchable memory behind bridge: 00000000fff00000-00000000000fffff [disabled] [64-bit]
	Secondary status: 66MHz- FastB2B- ParErr- DEVSEL=fast >TAbort- <TAbort- <MAbort- <SERR- <PERR-
	BridgeCtl: Parity- SERR+ NoISA- VGA- VGA16+ MAbort- >Reset- FastB2B-
		PriDiscTmr- SecDiscTmr- DiscTmrStat- DiscTmrSERREn-
	Capabilities: [40] Express (v2) Root Port (Slot+), IntMsgNum 0
		DevCap:	MaxPayload 256 bytes, PhantFunc 0
			ExtTag- RBE+ TEE-IO-
		DevCtl:	CorrErr- NonFatalErr- FatalErr- UnsupReq-
			RlxdOrd- ExtTag- PhantFunc- AuxPwr- NoSnoop-
			MaxPayload 256 bytes, MaxReadReq 128 bytes
		DevSta:	CorrErr- NonFatalErr- FatalErr- UnsupReq- AuxPwr+ TransPend-
		LnkCap:	Port #6, Speed 5GT/s, Width x1, ASPM L0s L1, Exit Latency L0s <1us, L1 <4us
			ClockPM- Surprise- LLActRep+ BwNot+ ASPMOptComp+
		LnkCtl:	ASPM L0s L1 Enabled; RCB 64 bytes, LnkDisable- CommClk-
			ExtSynch- ClockPM- AutWidDis- BWInt+ AutBWInt+ FltModeDis-
		LnkSta:	Speed 2.5GT/s, Width x0
			TrErr- Train+ SlotClk+ DLActive- BWMgmt- ABWMgmt-
		SltCap:	AttnBtn- PwrCtrl- MRL- AttnInd- PwrInd- HotPlug- Surprise-
			Slot #3, PowerLimit 10W; Interlock- NoCompl+
		SltCtl:	Enable: AttnBtn- PwrFlt- MRL- PresDet- CmdCplt- HPIrq- LinkChg-
			Control: AttnInd Unknown, PwrInd Unknown, Power- Interlock-
		SltSta:	Status: AttnBtn- PowerFlt- MRL- CmdCplt- PresDet- Interlock-
			Changed: MRL- PresDet- LinkState-
		RootCap: CRSVisible-
		RootCtl: ErrCorrectable- ErrNon-Fatal- ErrFatal- PMEIntEna- CRSVisible-
		RootSta: PME ReqID 0000, PMEStatus- PMEPending-
		DevCap2: Completion Timeout: Range ABC, TimeoutDis+ NROPrPrP- LTR+
			 10BitTagComp- 10BitTagReq- OBFF Not Supported, ExtFmt- EETLPPrefix-
			 EmergencyPowerReduction Not Supported, EmergencyPowerReductionInit-
			 FRS- LN System CLS Not Supported, TPHComp- ExtTPHComp- ARIFwd+
			 AtomicOpsCap: Routing- 32bit- 64bit- 128bitCAS-
		DevCtl2: Completion Timeout: 50us to 50ms, TimeoutDis+ ARIFwd-
			 AtomicOpsCtl: ReqEn- EgressBlck-
			 IDOReq- IDOCompl- LTR+ EmergencyPowerReductionReq-
			 10BitTagReq- OBFF Disabled, EETLPPrefixBlk-
		LnkCap2: Supported Link Speeds: 2.5-5GT/s, Crosslink- Retimer- 2Retimers- DRS-
		LnkCtl2: Target Link Speed: 2.5GT/s, EnterCompliance- SpeedDis-
			 Transmit Margin: Normal Operating Range, EnterModifiedCompliance- ComplianceSOS-
			 Compliance Preset/De-emphasis: -6dB de-emphasis, 0dB preshoot
		LnkSta2: Current De-emphasis Level: -3.5dB, EqualizationComplete- EqualizationPhase1-
			 EqualizationPhase2- EqualizationPhase3- LinkEqualizationRequest-
			 Retimer- 2Retimers- CrosslinkRes: unsupported, FltMode-
	Capabilities: [80] MSI: Enable+ Count=1/1 Maskable- 64bit-
		Address: fee00278  Data: 0000
	Capabilities: [90] Subsystem: Fujitsu Technology Solutions Device 1240
	Capabilities: [a0] Power Management version 3
		Flags: PMEClk- DSI- D1- D2- AuxCurrent=0mA PME(D0+,D1-,D2-,D3hot+,D3cold+)
		Status: D3 NoSoftRst- PME-Enable+ DSel=0 DScale=0 PME-
	Capabilities: [100 v0] Null
	Capabilities: [140 v1] Access Control Services
		ACSCap:	SrcValid+ TransBlk+ ReqRedir+ CmpltRedir+ UpstreamFwd- EgressCtrl- DirectTrans-
		ACSCtl:	SrcValid+ TransBlk- ReqRedir+ CmpltRedir+ UpstreamFwd- EgressCtrl- DirectTrans-
	Capabilities: [150 v0] Null
	Capabilities: [200 v1] L1 PM Substates
		L1SubCap: PCI-PM_L1.2+ PCI-PM_L1.1+ ASPM_L1.2+ ASPM_L1.1+ L1_PM_Substates+
			  PortCommonModeRestoreTime=40us PortTPowerOnTime=10us
		L1SubCtl1: PCI-PM_L1.2- PCI-PM_L1.1- ASPM_L1.2- ASPM_L1.1-
			   T_CommonMode=0us LTR1.2_Threshold=0ns
		L1SubCtl2: T_PwrOn=10us
	Kernel driver in use: pcieport
	Kernel modules: shpchp

00:14.0 PCI bridge: Intel Corporation Gemini Lake PCI Express Root Port (rev f3) (prog-if 00 [Normal decode])
	Subsystem: Fujitsu Technology Solutions Device 1240
	Control: I/O+ Mem+ BusMaster+ SpecCycle- MemWINV- VGASnoop- ParErr- Stepping- SERR- FastB2B- DisINTx+
	Status: Cap+ 66MHz- UDF- FastB2B- ParErr- DEVSEL=fast >TAbort- <TAbort- <MAbort- >SERR- <PERR- INTx-
	Latency: 0, Cache Line Size: 64 bytes
	Interrupt: pin A routed to IRQ 127
	IOMMU group: 8
	Bus: primary=00, secondary=05, subordinate=05, sec-latency=0
	I/O behind bridge: f000-0fff [disabled] [16-bit]
	Memory behind bridge: fff00000-000fffff [disabled] [32-bit]
	Prefetchable memory behind bridge: 00000000fff00000-00000000000fffff [disabled] [64-bit]
	Secondary status: 66MHz- FastB2B- ParErr- DEVSEL=fast >TAbort- <TAbort- <MAbort- <SERR- <PERR-
	BridgeCtl: Parity- SERR+ NoISA- VGA- VGA16+ MAbort- >Reset- FastB2B-
		PriDiscTmr- SecDiscTmr- DiscTmrStat- DiscTmrSERREn-
	Capabilities: [40] Express (v2) Root Port (Slot-), IntMsgNum 0
		DevCap:	MaxPayload 256 bytes, PhantFunc 0
			ExtTag- RBE+ TEE-IO-
		DevCtl:	CorrErr- NonFatalErr- FatalErr- UnsupReq-
			RlxdOrd- ExtTag- PhantFunc- AuxPwr- NoSnoop-
			MaxPayload 256 bytes, MaxReadReq 128 bytes
		DevSta:	CorrErr- NonFatalErr- FatalErr- UnsupReq- AuxPwr+ TransPend-
		LnkCap:	Port #1, Speed 5GT/s, Width x1, ASPM not supported
			ClockPM- Surprise- LLActRep+ BwNot+ ASPMOptComp+
		LnkCtl:	ASPM Disabled; RCB 64 bytes, LnkDisable- CommClk-
			ExtSynch- ClockPM- AutWidDis- BWInt+ AutBWInt+ FltModeDis-
		LnkSta:	Speed 2.5GT/s, Width x0
			TrErr- Train- SlotClk+ DLActive- BWMgmt- ABWMgmt-
		RootCap: CRSVisible-
		RootCtl: ErrCorrectable- ErrNon-Fatal- ErrFatal- PMEIntEna- CRSVisible-
		RootSta: PME ReqID 0000, PMEStatus- PMEPending-
		DevCap2: Completion Timeout: Range ABC, TimeoutDis+ NROPrPrP- LTR+
			 10BitTagComp- 10BitTagReq- OBFF Not Supported, ExtFmt- EETLPPrefix-
			 EmergencyPowerReduction Not Supported, EmergencyPowerReductionInit-
			 FRS- LN System CLS Not Supported, TPHComp- ExtTPHComp- ARIFwd+
			 AtomicOpsCap: Routing- 32bit- 64bit- 128bitCAS-
		DevCtl2: Completion Timeout: 50us to 50ms, TimeoutDis+ ARIFwd-
			 AtomicOpsCtl: ReqEn- EgressBlck-
			 IDOReq- IDOCompl- LTR- EmergencyPowerReductionReq-
			 10BitTagReq- OBFF Disabled, EETLPPrefixBlk-
		LnkCap2: Supported Link Speeds: 2.5-5GT/s, Crosslink- Retimer- 2Retimers- DRS-
		LnkCtl2: Target Link Speed: 2.5GT/s, EnterCompliance- SpeedDis-
			 Transmit Margin: Normal Operating Range, EnterModifiedCompliance- ComplianceSOS-
			 Compliance Preset/De-emphasis: -6dB de-emphasis, 0dB preshoot
		LnkSta2: Current De-emphasis Level: -3.5dB, EqualizationComplete- EqualizationPhase1-
			 EqualizationPhase2- EqualizationPhase3- LinkEqualizationRequest-
			 Retimer- 2Retimers- CrosslinkRes: unsupported, FltMode-
	Capabilities: [80] MSI: Enable+ Count=1/1 Maskable- 64bit-
		Address: fee002b8  Data: 0000
	Capabilities: [90] Subsystem: Fujitsu Technology Solutions Device 1240
	Capabilities: [a0] Power Management version 3
		Flags: PMEClk- DSI- D1- D2- AuxCurrent=0mA PME(D0+,D1-,D2-,D3hot+,D3cold+)
		Status: D3 NoSoftRst- PME-Enable+ DSel=0 DScale=0 PME-
	Capabilities: [100 v0] Null
	Capabilities: [140 v1] Access Control Services
		ACSCap:	SrcValid+ TransBlk+ ReqRedir+ CmpltRedir+ UpstreamFwd- EgressCtrl- DirectTrans-
		ACSCtl:	SrcValid+ TransBlk- ReqRedir+ CmpltRedir+ UpstreamFwd- EgressCtrl- DirectTrans-
	Capabilities: [150 v0] Null
	Capabilities: [200 v1] L1 PM Substates
		L1SubCap: PCI-PM_L1.2+ PCI-PM_L1.1+ ASPM_L1.2+ ASPM_L1.1+ L1_PM_Substates+
			  PortCommonModeRestoreTime=40us PortTPowerOnTime=10us
		L1SubCtl1: PCI-PM_L1.2- PCI-PM_L1.1- ASPM_L1.2- ASPM_L1.1-
			   T_CommonMode=0us LTR1.2_Threshold=0ns
		L1SubCtl2: T_PwrOn=10us
	Kernel driver in use: pcieport
	Kernel modules: shpchp

00:14.1 PCI bridge: Intel Corporation Gemini Lake PCI Express Root Port (rev f3) (prog-if 00 [Normal decode])
	Subsystem: Fujitsu Technology Solutions Device 1240
	Control: I/O+ Mem+ BusMaster+ SpecCycle- MemWINV- VGASnoop- ParErr- Stepping- SERR- FastB2B- DisINTx+
	Status: Cap+ 66MHz- UDF- FastB2B- ParErr- DEVSEL=fast >TAbort- <TAbort- <MAbort- >SERR- <PERR- INTx-
	Latency: 0, Cache Line Size: 64 bytes
	Interrupt: pin B routed to IRQ 128
	IOMMU group: 9
	Bus: primary=00, secondary=06, subordinate=06, sec-latency=0
	I/O behind bridge: f000-0fff [disabled] [16-bit]
	Memory behind bridge: fff00000-000fffff [disabled] [32-bit]
	Prefetchable memory behind bridge: 00000000fff00000-00000000000fffff [disabled] [64-bit]
	Secondary status: 66MHz- FastB2B- ParErr- DEVSEL=fast >TAbort- <TAbort- <MAbort- <SERR- <PERR-
	BridgeCtl: Parity- SERR+ NoISA- VGA- VGA16+ MAbort- >Reset- FastB2B-
		PriDiscTmr- SecDiscTmr- DiscTmrStat- DiscTmrSERREn-
	Capabilities: [40] Express (v2) Root Port (Slot-), IntMsgNum 0
		DevCap:	MaxPayload 256 bytes, PhantFunc 0
			ExtTag- RBE+ TEE-IO-
		DevCtl:	CorrErr- NonFatalErr- FatalErr- UnsupReq-
			RlxdOrd- ExtTag- PhantFunc- AuxPwr- NoSnoop-
			MaxPayload 256 bytes, MaxReadReq 128 bytes
		DevSta:	CorrErr- NonFatalErr- FatalErr- UnsupReq- AuxPwr+ TransPend-
		LnkCap:	Port #2, Speed 5GT/s, Width x1, ASPM L0s L1, Exit Latency L0s <1us, L1 <4us
			ClockPM- Surprise- LLActRep+ BwNot+ ASPMOptComp+
		LnkCtl:	ASPM L0s L1 Enabled; RCB 64 bytes, LnkDisable- CommClk-
			ExtSynch- ClockPM- AutWidDis- BWInt+ AutBWInt+ FltModeDis-
		LnkSta:	Speed 2.5GT/s, Width x0
			TrErr- Train- SlotClk+ DLActive- BWMgmt- ABWMgmt-
		RootCap: CRSVisible-
		RootCtl: ErrCorrectable- ErrNon-Fatal- ErrFatal- PMEIntEna- CRSVisible-
		RootSta: PME ReqID 0000, PMEStatus- PMEPending-
		DevCap2: Completion Timeout: Range ABC, TimeoutDis+ NROPrPrP- LTR+
			 10BitTagComp- 10BitTagReq- OBFF Not Supported, ExtFmt- EETLPPrefix-
			 EmergencyPowerReduction Not Supported, EmergencyPowerReductionInit-
			 FRS- LN System CLS Not Supported, TPHComp- ExtTPHComp- ARIFwd+
			 AtomicOpsCap: Routing- 32bit- 64bit- 128bitCAS-
		DevCtl2: Completion Timeout: 50us to 50ms, TimeoutDis+ ARIFwd-
			 AtomicOpsCtl: ReqEn- EgressBlck-
			 IDOReq- IDOCompl- LTR- EmergencyPowerReductionReq-
			 10BitTagReq- OBFF Disabled, EETLPPrefixBlk-
		LnkCap2: Supported Link Speeds: 2.5-5GT/s, Crosslink- Retimer- 2Retimers- DRS-
		LnkCtl2: Target Link Speed: 2.5GT/s, EnterCompliance- SpeedDis-
			 Transmit Margin: Normal Operating Range, EnterModifiedCompliance- ComplianceSOS-
			 Compliance Preset/De-emphasis: -6dB de-emphasis, 0dB preshoot
		LnkSta2: Current De-emphasis Level: -3.5dB, EqualizationComplete- EqualizationPhase1-
			 EqualizationPhase2- EqualizationPhase3- LinkEqualizationRequest-
			 Retimer- 2Retimers- CrosslinkRes: unsupported, FltMode-
	Capabilities: [80] MSI: Enable+ Count=1/1 Maskable- 64bit-
		Address: fee002d8  Data: 0000
	Capabilities: [90] Subsystem: Fujitsu Technology Solutions Device 1240
	Capabilities: [a0] Power Management version 3
		Flags: PMEClk- DSI- D1- D2- AuxCurrent=0mA PME(D0+,D1-,D2-,D3hot+,D3cold+)
		Status: D3 NoSoftRst- PME-Enable+ DSel=0 DScale=0 PME-
	Capabilities: [100 v0] Null
	Capabilities: [140 v1] Access Control Services
		ACSCap:	SrcValid+ TransBlk+ ReqRedir+ CmpltRedir+ UpstreamFwd- EgressCtrl- DirectTrans-
		ACSCtl:	SrcValid+ TransBlk- ReqRedir+ CmpltRedir+ UpstreamFwd- EgressCtrl- DirectTrans-
	Capabilities: [150 v0] Null
	Capabilities: [200 v1] L1 PM Substates
		L1SubCap: PCI-PM_L1.2+ PCI-PM_L1.1+ ASPM_L1.2+ ASPM_L1.1+ L1_PM_Substates+
			  PortCommonModeRestoreTime=40us PortTPowerOnTime=10us
		L1SubCtl1: PCI-PM_L1.2- PCI-PM_L1.1- ASPM_L1.2- ASPM_L1.1-
			   T_CommonMode=0us LTR1.2_Threshold=0ns
		L1SubCtl2: T_PwrOn=10us
	Kernel driver in use: pcieport
	Kernel modules: shpchp
```


My machine is eqipped with an RTL8125A 2.5Gbit Ethernet controller in the PCIex1 slot, a Mediatek MT7922 WLAN module in the m.2 key-e socket and the RTL8111 onboard LAN. ASPM is enabled for all of these chips as **lspci -vvv** confirms:

```
01:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8125 2.5GbE Controller
	Subsystem: Realtek Semiconductor Co., Ltd. Device 0123
	Control: I/O+ Mem+ BusMaster+ SpecCycle- MemWINV- VGASnoop- ParErr- Stepping- SERR- FastB2B- DisINTx+
	Status: Cap+ 66MHz- UDF- FastB2B- ParErr- DEVSEL=fast >TAbort- <TAbort- <MAbort- >SERR- <PERR- INTx-
	Latency: 0, Cache Line Size: 64 bytes
	Interrupt: pin A routed to IRQ 22
	IOMMU group: 15
	Region 0: I/O ports at e000 [size=256]
	Region 2: Memory at a1310000 (64-bit, non-prefetchable) [size=64K]
	Region 4: Memory at a1320000 (64-bit, non-prefetchable) [size=16K]
	Expansion ROM at a1300000 [disabled] [size=64K]
	Capabilities: [40] Power Management version 3
		Flags: PMEClk- DSI- D1+ D2+ AuxCurrent=375mA PME(D0+,D1+,D2+,D3hot+,D3cold+)
		Status: D0 NoSoftRst+ PME-Enable- DSel=0 DScale=0 PME-
	Capabilities: [50] MSI: Enable- Count=1/1 Maskable+ 64bit+
		Address: 0000000000000000  Data: 0000
		Masking: 00000000  Pending: 00000000
	Capabilities: [70] Express (v2) Endpoint, IntMsgNum 1
		DevCap:	MaxPayload 512 bytes, PhantFunc 0, Latency L0s <512ns, L1 <64us
			ExtTag+ AttnBtn- AttnInd- PwrInd- RBE+ FLReset+ SlotPowerLimit 10W TEE-IO-
		DevCtl:	CorrErr- NonFatalErr- FatalErr- UnsupReq-
			RlxdOrd+ ExtTag+ PhantFunc- AuxPwr- NoSnoop- FLReset-
			MaxPayload 256 bytes, MaxReadReq 4096 bytes
		DevSta:	CorrErr+ NonFatalErr- FatalErr- UnsupReq+ AuxPwr+ TransPend-
		LnkCap:	Port #0, Speed 5GT/s, Width x1, ASPM L0s L1, Exit Latency L0s unlimited, L1 <64us
			ClockPM+ Surprise- LLActRep- BwNot- ASPMOptComp+
		LnkCtl:	ASPM L1 Enabled; RCB 64 bytes, LnkDisable- CommClk+
			ExtSynch- ClockPM+ AutWidDis- BWInt- AutBWInt- FltModeDis-
		LnkSta:	Speed 5GT/s, Width x1
			TrErr- Train- SlotClk+ DLActive- BWMgmt- ABWMgmt-
		DevCap2: Completion Timeout: Range ABCD, TimeoutDis+ NROPrPrP- LTR+
			 10BitTagComp- 10BitTagReq- OBFF Via message/WAKE#, ExtFmt- EETLPPrefix-
			 EmergencyPowerReduction Not Supported, EmergencyPowerReductionInit-
			 FRS- TPHComp+ ExtTPHComp-
			 AtomicOpsCap: 32bit- 64bit- 128bitCAS-
		DevCtl2: Completion Timeout: 50us to 50ms, TimeoutDis-
			 AtomicOpsCtl: ReqEn-
			 IDOReq- IDOCompl- LTR+ EmergencyPowerReductionReq-
			 10BitTagReq- OBFF Disabled, EETLPPrefixBlk-
		LnkCap2: Supported Link Speeds: 2.5-5GT/s, Crosslink- Retimer- 2Retimers- DRS-
		LnkCtl2: Target Link Speed: 5GT/s, EnterCompliance- SpeedDis-
			 Transmit Margin: Normal Operating Range, EnterModifiedCompliance- ComplianceSOS-
			 Compliance Preset/De-emphasis: -6dB de-emphasis, 0dB preshoot
		LnkSta2: Current De-emphasis Level: -6dB, EqualizationComplete- EqualizationPhase1-
			 EqualizationPhase2- EqualizationPhase3- LinkEqualizationRequest-
			 Retimer- 2Retimers- CrosslinkRes: unsupported, FltMode-
	Capabilities: [b0] MSI-X: Enable+ Count=4 Masked-
		Vector table: BAR=4 offset=00000000
		PBA: BAR=4 offset=00000800
	Capabilities: [d0] Vital Product Data
pcilib: sysfs_read_vpd: read failed: No such device
		Not readable
	Capabilities: [100 v2] Advanced Error Reporting
		UESta:	DLP- SDES- TLP- FCP- CmpltTO- CmpltAbrt- UnxCmplt- RxOF- MalfTLP-
			ECRC- UnsupReq- ACSViol- UncorrIntErr- BlockedTLP- AtomicOpBlocked- TLPBlockedErr-
			PoisonTLPBlocked- DMWrReqBlocked- IDECheck- MisIDETLP- PCRC_CHECK- TLPXlatBlocked-
		UEMsk:	DLP- SDES- TLP- FCP- CmpltTO- CmpltAbrt- UnxCmplt- RxOF- MalfTLP-
			ECRC- UnsupReq- ACSViol- UncorrIntErr+ BlockedTLP- AtomicOpBlocked- TLPBlockedErr-
			PoisonTLPBlocked- DMWrReqBlocked- IDECheck- MisIDETLP- PCRC_CHECK- TLPXlatBlocked-
		UESvrt:	DLP+ SDES+ TLP- FCP+ CmpltTO- CmpltAbrt- UnxCmplt- RxOF+ MalfTLP+
			ECRC- UnsupReq- ACSViol- UncorrIntErr+ BlockedTLP- AtomicOpBlocked- TLPBlockedErr-
			PoisonTLPBlocked- DMWrReqBlocked- IDECheck- MisIDETLP- PCRC_CHECK- TLPXlatBlocked-
		CESta:	RxErr+ BadTLP+ BadDLLP+ Rollover+ Timeout+ AdvNonFatalErr+ CorrIntErr- HeaderOF-
		CEMsk:	RxErr- BadTLP- BadDLLP- Rollover- Timeout- AdvNonFatalErr+ CorrIntErr+ HeaderOF+
		AERCap:	First Error Pointer: 00, ECRCGenCap+ ECRCGenEn- ECRCChkCap+ ECRCChkEn-
			MultHdrRecCap- MultHdrRecEn- TLPPfxPres- HdrLogCap-
		HeaderLog: 00000000 00000000 00000000 00000000
	Capabilities: [148 v1] Virtual Channel
		Caps:	LPEVC=0 RefClk=100ns PATEntryBits=1
		Arb:	Fixed- WRR32- WRR64- WRR128-
		Ctrl:	ArbSelect=Fixed
		Status:	InProgress-
		VC0:	Caps:	PATOffset=00 MaxTimeSlots=1 RejSnoopTrans-
			Arb:	Fixed- WRR32- WRR64- WRR128- TWRR128- WRR256-
			Ctrl:	Enable+ ID=0 ArbSelect=Fixed TC/VC=ff
			Status:	NegoPending- InProgress-
	Capabilities: [168 v1] Device Serial Number 01-00-00-00-68-4c-e0-00
	Capabilities: [178 v1] Alternative Routing-ID Interpretation (ARI)
		ARICap:	MFVC- ACS-, Next Function: 1
		ARICtl:	MFVC- ACS-, Function Group: 0
	Capabilities: [188 v1] Single Root I/O Virtualization (SR-IOV)
		IOVCap:	Migration- 10BitTagReq- IntMsgNum 0
		IOVCtl:	Enable- Migration- Interrupt- MSE- ARIHierarchy+ 10BitTagReq-
		IOVSta:	Migration-
		Initial VFs: 7, Total VFs: 7, Number of VFs: 0, Function Dependency Link: 00
		VF offset: 5, stride: 1, Device ID: 8125
		Supported Page Size: 00000553, System Page Size: 00000001
		Region 2: Memory at 00000000a1330000 (64-bit, non-prefetchable)
		Region 4: Memory at 00000000a13a0000 (64-bit, non-prefetchable)
		VF Migration: offset: 00000000, BIR: 0
	Capabilities: [1c8 v1] Transaction Processing Hints
		No steering table available
	Capabilities: [254 v1] Latency Tolerance Reporting
		Max snoop latency: 3145728ns
		Max no snoop latency: 3145728ns
	Capabilities: [25c v1] L1 PM Substates
		L1SubCap: PCI-PM_L1.2+ PCI-PM_L1.1+ ASPM_L1.2+ ASPM_L1.1+ L1_PM_Substates+
			  PortCommonModeRestoreTime=150us PortTPowerOnTime=150us
		L1SubCtl1: PCI-PM_L1.2+ PCI-PM_L1.1+ ASPM_L1.2+ ASPM_L1.1+
			   T_CommonMode=0us LTR1.2_Threshold=306176ns
		L1SubCtl2: T_PwrOn=150us
	Capabilities: [26c v1] Vendor Specific Information: ID=0002 Rev=4 Len=100 <?>
	Kernel driver in use: r8169
	Kernel modules: r8169

02:00.0 Network controller: MEDIATEK Corp. MT7922 802.11ax PCI Express Wireless Network Adapter
	Subsystem: Lite-On Communications Inc Device 3804
	Control: I/O- Mem+ BusMaster+ SpecCycle- MemWINV- VGASnoop- ParErr- Stepping- SERR- FastB2B- DisINTx+
	Status: Cap+ 66MHz- UDF- FastB2B- ParErr- DEVSEL=fast >TAbort- <TAbort- <MAbort- >SERR- <PERR- INTx-
	Latency: 0, Cache Line Size: 64 bytes
	Interrupt: pin A routed to IRQ 139
	IOMMU group: 16
	Region 0: Memory at a1000000 (64-bit, prefetchable) [size=1M]
	Region 2: Memory at a1100000 (64-bit, non-prefetchable) [size=32K]
	Capabilities: [80] Express (v2) Endpoint, IntMsgNum 0
		DevCap:	MaxPayload 128 bytes, PhantFunc 0, Latency L0s unlimited, L1 unlimited
			ExtTag+ AttnBtn- AttnInd- PwrInd- RBE+ FLReset+ SlotPowerLimit 10W TEE-IO-
		DevCtl:	CorrErr- NonFatalErr- FatalErr- UnsupReq-
			RlxdOrd- ExtTag+ PhantFunc- AuxPwr- NoSnoop+ FLReset-
			MaxPayload 128 bytes, MaxReadReq 512 bytes
		DevSta:	CorrErr- NonFatalErr- FatalErr- UnsupReq- AuxPwr- TransPend-
		LnkCap:	Port #1, Speed 5GT/s, Width x1, ASPM L0s L1, Exit Latency L0s <2us, L1 <8us
			ClockPM- Surprise- LLActRep- BwNot- ASPMOptComp+
		LnkCtl:	ASPM L0s L1 Enabled; RCB 64 bytes, LnkDisable- CommClk+
			ExtSynch- ClockPM- AutWidDis- BWInt- AutBWInt- FltModeDis-
		LnkSta:	Speed 5GT/s, Width x1
			TrErr- Train- SlotClk+ DLActive- BWMgmt- ABWMgmt-
		DevCap2: Completion Timeout: Range ABCD, TimeoutDis+ NROPrPrP- LTR+
			 10BitTagComp- 10BitTagReq- OBFF Not Supported, ExtFmt+ EETLPPrefix-
			 EmergencyPowerReduction Not Supported, EmergencyPowerReductionInit-
			 FRS- TPHComp- ExtTPHComp-
			 AtomicOpsCap: 32bit- 64bit- 128bitCAS-
		DevCtl2: Completion Timeout: 260ms to 900ms, TimeoutDis-
			 AtomicOpsCtl: ReqEn-
			 IDOReq- IDOCompl- LTR+ EmergencyPowerReductionReq-
			 10BitTagReq- OBFF Disabled, EETLPPrefixBlk-
		LnkCap2: Supported Link Speeds: 2.5-5GT/s, Crosslink- Retimer- 2Retimers- DRS-
		LnkCtl2: Target Link Speed: 5GT/s, EnterCompliance- SpeedDis-
			 Transmit Margin: Normal Operating Range, EnterModifiedCompliance- ComplianceSOS-
			 Compliance Preset/De-emphasis: -6dB de-emphasis, 0dB preshoot
		LnkSta2: Current De-emphasis Level: -6dB, EqualizationComplete- EqualizationPhase1-
			 EqualizationPhase2- EqualizationPhase3- LinkEqualizationRequest-
			 Retimer- 2Retimers- CrosslinkRes: unsupported, FltMode-
	Capabilities: [e0] MSI: Enable+ Count=1/32 Maskable+ 64bit+
		Address: 00000000fee00678  Data: 0000
		Masking: fffffffe  Pending: 00000000
	Capabilities: [f8] Power Management version 3
		Flags: PMEClk- DSI- D1- D2- AuxCurrent=0mA PME(D0+,D1-,D2-,D3hot+,D3cold+)
		Status: D0 NoSoftRst+ PME-Enable- DSel=0 DScale=0 PME-
	Capabilities: [100 v1] Vendor Specific Information: ID=1556 Rev=1 Len=008 <?>
	Capabilities: [108 v1] Latency Tolerance Reporting
		Max snoop latency: 3145728ns
		Max no snoop latency: 3145728ns
	Capabilities: [110 v1] L1 PM Substates
		L1SubCap: PCI-PM_L1.2+ PCI-PM_L1.1+ ASPM_L1.2+ ASPM_L1.1+ L1_PM_Substates+
			  PortCommonModeRestoreTime=3us PortTPowerOnTime=52us
		L1SubCtl1: PCI-PM_L1.2+ PCI-PM_L1.1+ ASPM_L1.2+ ASPM_L1.1+
			   T_CommonMode=0us LTR1.2_Threshold=98304ns
		L1SubCtl2: T_PwrOn=52us
	Capabilities: [200 v2] Advanced Error Reporting
		UESta:	DLP- SDES- TLP- FCP- CmpltTO- CmpltAbrt- UnxCmplt- RxOF- MalfTLP-
			ECRC- UnsupReq- ACSViol- UncorrIntErr- BlockedTLP- AtomicOpBlocked- TLPBlockedErr-
			PoisonTLPBlocked- DMWrReqBlocked- IDECheck- MisIDETLP- PCRC_CHECK- TLPXlatBlocked-
		UEMsk:	DLP- SDES- TLP- FCP- CmpltTO- CmpltAbrt- UnxCmplt- RxOF- MalfTLP-
			ECRC- UnsupReq- ACSViol- UncorrIntErr+ BlockedTLP- AtomicOpBlocked- TLPBlockedErr-
			PoisonTLPBlocked- DMWrReqBlocked- IDECheck- MisIDETLP- PCRC_CHECK- TLPXlatBlocked-
		UESvrt:	DLP+ SDES+ TLP- FCP+ CmpltTO- CmpltAbrt- UnxCmplt- RxOF+ MalfTLP+
			ECRC- UnsupReq- ACSViol- UncorrIntErr+ BlockedTLP- AtomicOpBlocked- TLPBlockedErr-
			PoisonTLPBlocked- DMWrReqBlocked- IDECheck- MisIDETLP- PCRC_CHECK- TLPXlatBlocked-
		CESta:	RxErr- BadTLP- BadDLLP- Rollover- Timeout- AdvNonFatalErr- CorrIntErr- HeaderOF-
		CEMsk:	RxErr- BadTLP- BadDLLP- Rollover- Timeout- AdvNonFatalErr+ CorrIntErr+ HeaderOF-
		AERCap:	First Error Pointer: 00, ECRCGenCap- ECRCGenEn- ECRCChkCap- ECRCChkEn-
			MultHdrRecCap- MultHdrRecEn- TLPPfxPres- HdrLogCap-
		HeaderLog: 00000000 00000000 00000000 00000000
	Kernel driver in use: mt7921e
	Kernel modules: mt7921e

03:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8111/8168/8211/8411 PCI Express Gigabit Ethernet Controller (rev 0c)
	DeviceName: Onboard - RTK Ethernet
	Subsystem: Fujitsu Technology Solutions Device 11ff
	Control: I/O+ Mem+ BusMaster- SpecCycle- MemWINV- VGASnoop- ParErr- Stepping- SERR- FastB2B- DisINTx+
	Status: Cap+ 66MHz- UDF- FastB2B- ParErr- DEVSEL=fast >TAbort- <TAbort- <MAbort- >SERR- <PERR- INTx-
	Interrupt: pin A routed to IRQ 20
	IOMMU group: 17
	Region 0: I/O ports at d000 [size=256]
	Region 2: Memory at a1204000 (64-bit, non-prefetchable) [size=4K]
	Region 4: Memory at a1200000 (64-bit, prefetchable) [size=16K]
	Capabilities: [40] Power Management version 3
		Flags: PMEClk- DSI- D1+ D2+ AuxCurrent=375mA PME(D0+,D1+,D2+,D3hot+,D3cold+)
		Status: D3 NoSoftRst+ PME-Enable+ DSel=0 DScale=0 PME-
	Capabilities: [50] MSI: Enable- Count=1/1 Maskable- 64bit+
		Address: 0000000000000000  Data: 0000
	Capabilities: [70] Express (v2) Endpoint, IntMsgNum 1
		DevCap:	MaxPayload 128 bytes, PhantFunc 0, Latency L0s <512ns, L1 <64us
			ExtTag- AttnBtn- AttnInd- PwrInd- RBE+ FLReset- SlotPowerLimit 10W TEE-IO-
		DevCtl:	CorrErr- NonFatalErr- FatalErr- UnsupReq-
			RlxdOrd+ ExtTag- PhantFunc- AuxPwr- NoSnoop-
			MaxPayload 128 bytes, MaxReadReq 4096 bytes
		DevSta:	CorrErr- NonFatalErr- FatalErr- UnsupReq- AuxPwr+ TransPend-
		LnkCap:	Port #0, Speed 2.5GT/s, Width x1, ASPM L0s L1, Exit Latency L0s unlimited, L1 <64us
			ClockPM+ Surprise- LLActRep- BwNot- ASPMOptComp+
		LnkCtl:	ASPM L1 Enabled; RCB 64 bytes, LnkDisable- CommClk+
			ExtSynch- ClockPM+ AutWidDis- BWInt- AutBWInt- FltModeDis-
		LnkSta:	Speed 2.5GT/s, Width x1
			TrErr- Train- SlotClk+ DLActive- BWMgmt- ABWMgmt-
		DevCap2: Completion Timeout: Range ABCD, TimeoutDis+ NROPrPrP- LTR+
			 10BitTagComp- 10BitTagReq- OBFF Via message/WAKE#, ExtFmt- EETLPPrefix-
			 EmergencyPowerReduction Not Supported, EmergencyPowerReductionInit-
			 FRS- TPHComp- ExtTPHComp-
			 AtomicOpsCap: 32bit- 64bit- 128bitCAS-
		DevCtl2: Completion Timeout: 50us to 50ms, TimeoutDis-
			 AtomicOpsCtl: ReqEn-
			 IDOReq- IDOCompl- LTR+ EmergencyPowerReductionReq-
			 10BitTagReq- OBFF Disabled, EETLPPrefixBlk-
		LnkCap2: Supported Link Speeds: 2.5GT/s, Crosslink- Retimer- 2Retimers- DRS-
		LnkCtl2: Target Link Speed: 2.5GT/s, EnterCompliance- SpeedDis-
			 Transmit Margin: Normal Operating Range, EnterModifiedCompliance- ComplianceSOS-
			 Compliance Preset/De-emphasis: -6dB de-emphasis, 0dB preshoot
		LnkSta2: Current De-emphasis Level: -6dB, EqualizationComplete- EqualizationPhase1-
			 EqualizationPhase2- EqualizationPhase3- LinkEqualizationRequest-
			 Retimer- 2Retimers- CrosslinkRes: unsupported, FltMode-
	Capabilities: [b0] MSI-X: Enable+ Count=4 Masked-
		Vector table: BAR=4 offset=00000000
		PBA: BAR=4 offset=00000800
	Capabilities: [d0] Vital Product Data
pcilib: sysfs_read_vpd: read failed: No such device
		Not readable
	Capabilities: [100 v1] Advanced Error Reporting
		UESta:	DLP- SDES- TLP- FCP- CmpltTO- CmpltAbrt- UnxCmplt- RxOF- MalfTLP-
			ECRC- UnsupReq- ACSViol- UncorrIntErr- BlockedTLP- AtomicOpBlocked- TLPBlockedErr-
			PoisonTLPBlocked- DMWrReqBlocked- IDECheck- MisIDETLP- PCRC_CHECK- TLPXlatBlocked-
		UEMsk:	DLP- SDES- TLP- FCP- CmpltTO- CmpltAbrt- UnxCmplt- RxOF- MalfTLP-
			ECRC- UnsupReq- ACSViol- UncorrIntErr+ BlockedTLP- AtomicOpBlocked- TLPBlockedErr-
			PoisonTLPBlocked- DMWrReqBlocked- IDECheck- MisIDETLP- PCRC_CHECK- TLPXlatBlocked-
		UESvrt:	DLP+ SDES+ TLP- FCP+ CmpltTO- CmpltAbrt- UnxCmplt- RxOF+ MalfTLP+
			ECRC- UnsupReq- ACSViol- UncorrIntErr+ BlockedTLP- AtomicOpBlocked- TLPBlockedErr-
			PoisonTLPBlocked- DMWrReqBlocked- IDECheck- MisIDETLP- PCRC_CHECK- TLPXlatBlocked-
		CESta:	RxErr- BadTLP- BadDLLP- Rollover- Timeout- AdvNonFatalErr- CorrIntErr- HeaderOF-
		CEMsk:	RxErr- BadTLP- BadDLLP- Rollover- Timeout- AdvNonFatalErr+ CorrIntErr+ HeaderOF-
		AERCap:	First Error Pointer: 00, ECRCGenCap+ ECRCGenEn- ECRCChkCap+ ECRCChkEn-
			MultHdrRecCap- MultHdrRecEn- TLPPfxPres- HdrLogCap-
		HeaderLog: 00000000 00000000 00000000 00000000
	Capabilities: [140 v1] Virtual Channel
		Caps:	LPEVC=0 RefClk=100ns PATEntryBits=1
		Arb:	Fixed- WRR32- WRR64- WRR128-
		Ctrl:	ArbSelect=Fixed
		Status:	InProgress-
		VC0:	Caps:	PATOffset=00 MaxTimeSlots=1 RejSnoopTrans-
			Arb:	Fixed- WRR32- WRR64- WRR128- TWRR128- WRR256-
			Ctrl:	Enable+ ID=0 ArbSelect=Fixed TC/VC=ff
			Status:	NegoPending- InProgress-
	Capabilities: [160 v1] Device Serial Number 00-00-00-00-00-00-00-00
	Capabilities: [170 v1] Latency Tolerance Reporting
		Max snoop latency: 3145728ns
		Max no snoop latency: 3145728ns
	Kernel driver in use: r8169
	Kernel modules: r8169
```
</details>

With ASPM enabled I measure an average idle power consumption of 2.24W for the SoC using **powerstat -R**. Before enabling it, I measured values above 2.5W without any PCIe add-on devices (without RTL8125A and MT7922). The effect of ASPM is not huge, most likely you'll save about 0.5W in idle mode but this might result in 1-2°C lower  SoC temperatures. That's why I think it's well worth the effort:
```
root@pantera:~# powerstat -R
Running for 60.0 seconds (60 samples at 1.0 second intervals).
Power measurements will start in 0 seconds time.

  Time    User  Nice   Sys  Idle    IO  Run Ctxt/s  IRQ/s Fork Exec Exit  Watts
17:09:41   0.0   0.0   0.3  99.7   0.0    2    354    525    0    0    0   2.34 
17:09:42   0.3   0.0   0.3  99.5   0.0    2    339    504    0    0    0   2.34 
17:09:43   0.0   0.0   0.0 100.0   0.0    2    157    197    0    0    0   2.27 
17:09:44   0.0   0.0   0.0 100.0   0.0    2     73     67    0    0    0   2.23 
17:09:45   0.0   0.0   0.2  99.8   0.0    2     74     61    0    0    0   2.24 
17:09:46   0.0   0.0   0.0 100.0   0.0    2    139    118    0    0    0   2.25 
17:09:47   0.0   0.0   0.0 100.0   0.0    2     72     46    0    0    0   2.23 
17:09:48   0.0   0.0   0.0 100.0   0.0    2    103     67    0    0    0   2.23 
17:09:49   0.0   0.0   0.0 100.0   0.0    2     85     57    0    0    0   2.23 
17:09:50   0.0   0.0   0.0 100.0   0.0    2     99     73    0    0    0   2.23 
17:09:51   0.0   0.0   0.0 100.0   0.0    2     75     53    0    0    0   2.23 
17:09:52   0.0   0.0   0.2  99.8   0.0    2     74     55    0    0    0   2.23 
17:09:53   0.0   0.0   0.0 100.0   0.0    2     64     44    0    0    0   2.23 
17:09:54   0.0   0.0   0.0 100.0   0.0    2    135    111    0    0    0   2.25 
17:09:55   0.0   0.0   0.0 100.0   0.0    2     77     59    0    0    0   2.23 
17:09:56   0.0   0.0   0.0 100.0   0.0    2     80     57    0    0    0   2.23 
17:09:57   0.0   0.0   0.0 100.0   0.0    2     87     64    0    0    0   2.23 
17:09:58   0.0   0.0   0.7  99.3   0.0    2    220    172    1    0    0   2.30 
17:09:59   0.2   0.0   0.0  99.8   0.0    2    325    203    0    0    0   2.26 
17:10:00   0.0   0.0   0.0 100.0   0.0    2     79     59    0    0    0   2.23 
17:10:01   0.0   0.0   0.0 100.0   0.0    2     97     59    0    0    0   2.23 
17:10:02   0.0   0.0   0.0 100.0   0.0    2     95     76    0    0    0   2.24 
17:10:03   0.0   0.0   0.0 100.0   0.0    2     67     46    0    0    0   2.23 
17:10:04   0.0   0.0   0.0 100.0   0.0    2     93     81    0    0    0   2.24 
17:10:05   0.0   0.0   0.0 100.0   0.0    2     72     46    0    0    0   2.23 
17:10:06   0.0   0.0   0.2  99.8   0.0    2    110     89    0    0    0   2.24 
17:10:07   0.0   0.0   0.0 100.0   0.0    2     73     50    0    0    0   2.23 
17:10:08   0.0   0.0   0.0 100.0   0.0    2     79     61    0    0    0   2.23 
17:10:09   0.0   0.0   0.0 100.0   0.0    2     71     46    0    0    0   2.23 
17:10:10   0.0   0.0   0.0 100.0   0.0    2    109     86    0    0    0   2.23 
17:10:11   0.0   0.0   0.0 100.0   0.0    2     77     49    0    0    0   2.23 
17:10:12   0.0   0.0   0.0 100.0   0.0    2     77     65    0    0    0   2.23 
17:10:13   0.0   0.0   0.0 100.0   0.0    2     59     45    0    0    0   2.23 
17:10:14   0.0   0.0   0.0 100.0   0.0    2    121     89    0    0    0   2.23 
17:10:15   0.0   0.0   0.0 100.0   0.0    2     65     42    0    0    0   2.22 
17:10:16   0.0   0.0   0.0 100.0   0.0    2     75     49    0    0    0   2.23 
17:10:17   0.0   0.0   0.0 100.0   0.0    2     90     73    0    0    0   2.23 
17:10:18   0.0   0.0   0.0 100.0   0.0    2    103     78    0    0    0   2.23 
17:10:19   0.0   0.0   0.2  99.8   0.0    2     63     40    0    0    0   2.23 
17:10:20   0.0   0.0   0.5  99.5   0.0    2    211    177    0    0    0   2.30 
17:10:21   0.2   0.0   0.0  99.8   0.0    2     67     46    0    0    0   2.23 
17:10:22   0.0   0.0   0.0 100.0   0.0    2     99     78    0    0    0   2.24 
17:10:23   0.0   0.0   0.0 100.0   0.0    2     59     38    0    0    0   2.23 
17:10:24   0.0   0.0   0.0 100.0   0.0    2     74     58    0    0    0   2.23 
17:10:25   0.0   0.0   0.0 100.0   0.0    2     65     42    0    0    0   2.23 
17:10:26   0.0   0.0   0.0 100.0   0.0    2    100     78    0    0    0   2.24 
17:10:27   0.0   0.0   0.2  99.8   0.0    2    103     67    0    0    0   2.23 
17:10:28   0.0   0.0   0.0 100.0   0.0    2     76     55    0    0    0   2.23 
17:10:29   0.0   0.0   0.0 100.0   0.0    2     85     58    0    0    0   2.23 
17:10:30   0.0   0.0   0.0 100.0   0.0    2     89     70    0    0    0   2.24 
17:10:31   0.0   0.0   0.0 100.0   0.0    2     70     51    0    0    0   2.23 
17:10:32   0.0   0.0   0.0 100.0   0.0    2     59     46    0    0    0   2.23 
17:10:33   0.0   0.0   0.0 100.0   0.0    2     65     48    0    0    0   2.23 
17:10:34   0.0   0.0   0.2  99.8   0.0    2    104     80    0    0    0   2.23 
17:10:35   0.0   0.0   0.0 100.0   0.0    1     69     57    0    0    0   2.23 
17:10:36   0.0   0.0   0.0 100.0   0.0    2     76     57    0    0    0   2.23 
17:10:37   0.0   0.0   0.0 100.0   0.0    2     73     46    0    0    0   2.23 
17:10:38   0.0   0.0   0.0 100.0   0.0    2     92     72    0    0    0   2.23 
17:10:39   0.0   0.0   0.0 100.0   0.0    2     69     45    0    0    0   2.23 
17:10:40   0.0   0.0   0.0 100.0   0.0    2    113     76    0    0    0   2.23 
-------- ----- ----- ----- ----- ----- ---- ------ ------ ---- ---- ---- ------ 
 Average   0.0   0.0   0.1  99.9   0.0  2.0  102.1   85.1  0.0  0.0  0.0   2.24 
 GeoMean   0.0   0.0   0.0  99.9   0.0  2.0   91.7   69.1  0.0  0.0  0.0   2.24 
  StdDev   0.1   0.0   0.1   0.2   0.0  0.1   62.6   87.2  0.1  0.0  0.0   0.02 
-------- ----- ----- ----- ----- ----- ---- ------ ------ ---- ---- ---- ------ 
 Minimum   0.0   0.0   0.0  99.3   0.0  1.0   59.0   38.0  0.0  0.0  0.0   2.22 
 Maximum   0.3   0.0   0.7 100.0   0.0  2.0  354.0  525.0  1.0  0.0  0.0   2.34 
-------- ----- ----- ----- ----- ----- ---- ------ ------ ---- ---- ---- ------ 
Summary:
CPU:   2.24 Watts on average with standard deviation 0.02  
Note: power read from RAPL domains: uncore, pkg-0, core, dram.
These readings do not cover all the hardware in this device.
```


