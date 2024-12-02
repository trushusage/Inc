graph LR
	title[Incoming Swift Flow]
	
	%% TODO: 
	%% YID neydi?
	%% SWFTO_POSTED_FL=C ve diğerleri neydi?
	%% GÖE creates kisimlari doğru mu?
	
	SAA --> Paygate
	SAA --> Actimize --> SAA
	SAA --> MQ --> Mimari --> Harmoni --> ONSWIN --> SWROUTE
	SWROUTE --> |AUTO|ONNSTRF
	SWROUTE --> |AUTO|ONNSTRF_VOSTRO
	SWROUTE --> |AUTO|ONNSTRF_V2
	SWROUTE --> |MANUEL ve AUTO SWFTO_POSTED_FL=C olan kayitlarin BRD ve YID devir işlemleri yapılır|Devir_Batchi_JOBS.HMN.FXT.INCOMING_PAYMENT_BATCH
	Devir_Batchi_JOBS.HMN.FXT.INCOMING_PAYMENT_BATCH --> |creates|swmuh
	Devir_Batchi_JOBS.HMN.FXT.INCOMING_PAYMENT_BATCH --> |creates|accounting
	Devir_Batchi_JOBS.HMN.FXT.INCOMING_PAYMENT_BATCH --> |creates|commission
	Devir_Batchi_JOBS.HMN.FXT.INCOMING_PAYMENT_BATCH --> |creates|swift
	SWROUTE --> |MANUEL|GÖE
  %%	GÖE --> |creates?|swmuh
	%%	GÖE --> |creates?|accounting
	%%	GÖE --> |creates?|commission
	%%	GÖE --> |creates?|swift
	
	%% action start color.
	%% style 3 fill:#b36e00;
	
	%% end or close to end color.
	%% style 1 fill:#01734c;
	
	%% Helper method color.
	%% style 2 fill:#00008b;

































graph LR
	title[SWROUTE]
	
	%% TODO: 
	%% SWFTO_POSTED_FL=C ve diğerleri neydi?
	
	LOOP_curToBeRouted --> CheckMessage
	CheckMessage --> Field70_72Check
	CheckMessage --> UpdateMessage
	CheckMessage --> CheckandUpdate
	subgraph RouteRec.REFTYPE
		CheckandUpdate --> |1|VOSTRO --> accCheck
		CheckandUpdate --> |2|VSTACCOUNT --> accCheck
		CheckandUpdate --> |3 Vostro|EXISTB --> accCheck
		CheckandUpdate --> |4 Vostro|EXISTD --> accCheck
		CheckandUpdate --> |5|VSTRULE --> accCheck
		CheckandUpdate --> |6|DLC --> accCheck
		CheckandUpdate --> |7|SEND --> DLC_VIEW_ALL_MASTER
		CheckandUpdate --> |8|*DLC* --> accCheck
		CheckandUpdate --> |9|FXHVL --> FXHVLRefcheck
		CheckandUpdate --> |10|SMWUH --> SWMUHRefcheck
		CheckandUpdate --> |11|*FXHVL* --> ExtendedRefcheck
		CheckandUpdate --> |12|BRNCH* --> BranchRefcheck
		CheckandUpdate --> |13|TMNTM --> GaranteeRefcheck
		CheckandUpdate --> |14|ATMNTM --> GaranteeRefcheck
		CheckandUpdate --> |15|*TMNTM* --> ExtendedRefcheck
		CheckandUpdate --> |16|/acc --> accCheck
		CheckandUpdate --> |17 TESTCheck|TEST --> accCheck
		CheckandUpdate --> |18|CEKTAKAS --> CEKTAKASCheck
		CheckandUpdate --> |19|MASAK --> MASAKCheck
		CheckandUpdate --> |20|ARASTIRMA --> accCheck
		CheckandUpdate --> |21|FREEFORMAT --> freeFormatCheck
		CheckandUpdate --> |22|FREECHQ
		CheckandUpdate --> |23|ELSE --> WildCardCheck
													 ELSE --> SWIFTGEN.DLCRefcheck
	end
	
	%% action start color.
	%% style 3 fill:#b36e00;
	
	%% end or close to end color.
	%% style 1 fill:#01734c;
	
	%% Helper method color.
	%% style 2 fill:#00008b;





















graph TD
	title[BREDBANK ve Devir Batch]
	
	Cursor --> SWF_Restricted_Country_and_Bank_Control
	SWF_Restricted_Country_and_Bank_Control --> MASAK_Control
	MASAK_Control --> Msg_Actimize_Control
	Msg_Actimize_Control --> MX_Truncation_Control
	MX_Truncation_Control --> |iptal mesaji var mi?| GSRP_Control
	GSRP_Control --> Devir_Servisinden_hangi_muhabir_banka_kullanilacak_ve_hangi_swift_mesajlari_yaratilacak
	Devir_Servisinden_hangi_muhabir_banka_kullanilacak_ve_hangi_swift_mesajlari_yaratilacak --> Swift_103_191_202_202COV_Msg_Yaratimi
	Swift_103_191_202_202COV_Msg_Yaratimi --> Swmuh_kaydi
	Swmuh_kaydi --> Swfto_update_işlendi
	Swfto_update_işlendi --> Commission
	Commission --> Accounting_Muhasebe_Kaydi_Yaratma
	Accounting_Muhasebe_Kaydi_Yaratma --> Accounting
	Accounting --> NostroDiaryService.addNsdry
	NostroDiaryService.addNsdry --> GPI_Kaydi_oluşturuldu
	GPI_Kaydi_oluşturuldu --> openItem_oluşturulmasi
	openItem_oluşturulmasi --> swfti_yaratimi
	swfti_yaratimi --> mt191___komisyon_mesaji_icin_dosya_olusturuluyor






















graph LR
	title[Gelen Yabacı Para Transferleri Ekrani]
	
	%% TODO: 
	%% GÖE creates kisimlari doğru mu?
	
	%% subgraph FE
	%% end
	
	%% subgraph BE
	%% end
	
	%% subgraph Oracle_PL_SQL
	%% 		HMN_API_SWMUH.SAVE_INCOMING_FX
	%% 		HMN_API_SWMUH.GETSWIFTMESSAGEUSEDFLAGSSTATUS_OR_2
	%% end	
	
	onSave --> |isVTN and Swift Mesaji Gonderim Checkbox sadece 103 gecili ise|Kota
	onSave --> |Degilse|onSaveFXTransaction --> saveIncomingFx
	saveIncomingFx --> |1 IHR, HIK, DIG, ise| SET_REF_DLCREF
	saveIncomingFx --> |2| SET_REF_SUB_PayToCustomerFromBranch_DumpRef_DumpRateParity
	saveIncomingFx --> |3| SET_REF_ReturnPayment
	saveIncomingFx --> |4| VTN
	saveIncomingFx --> |5| SET_OPENITEM
	saveIncomingFx --> |if| BREDBANK
	saveIncomingFx --> SET_Commission
	saveIncomingFx --> SET_CreditPayment
	saveIncomingFx --> SET_REF_Garantiler
	saveIncomingFx --> FaxMail
	saveIncomingFx --> SET_DLCSwiftPaymentDetailDTO
	saveIncomingFx --> LHD
	saveIncomingFx --> SET_Sermaye
	saveIncomingFx --> |Daha onceden ele alinmis bir mesaja islem yapmamak icin kontrol| IncomingFxService.validateIncomingFx
		IncomingFxService.validateIncomingFx --> getSwiftMessageUsedFlagStatus --> HMN_API_SWMUH.GETSWIFTMESSAGEUSEDFLAGSSTATUS_OR_2
		IncomingFxService.validateIncomingFx --> saveUpdateIncomingFx --> HMN_API_SWMUH.SAVE_INCOMING_FX
			HMN_API_SWMUH.SAVE_INCOMING_FX --> |insert| DLC_SWIFT_PAYMENTS --> |Muhasebe Procedure ama muhasebe kaydi attigi yeri bulamadim daha cok ekranlar ile alakali gibi| L202_CHK.CALL.PERFORM
				SWMUH_COMMIT_PROCESS --> SWMUH_STARTUP
					SWMUH_STARTUP --> SWMUH_ps	
						SWMUH_ps --> |Kontrat Uretimi| L003_CONTRACT_GEN.PERFORM
							SWMUH_ps --> |Update Contaract| NSTRC.NSTRC.NSTRC_NSTCONT
							SWMUH_ps --> |set SWMUH_MASTER_NO 4554| LHD_or_LHC_or_GHI_or_IAC_or_IAD_or_MSR_or_KRD_or_VAL_or_VLC_or_YID_or_BRD
							SWMUH_ps --> |set SWMUH_MASTER_NO 8049| VTN
							SWMUH_ps --> |set SWMUH_MASTER_NO 4555| IHR
							SWMUH_ps --> |set SWMUH_MASTER_NO 4556| MUS
							%% SWMUH_ps --> en sondaki GOTO_UPDATEWORKITEMS kismina gidilemedi.
					SWMUH_COMMIT_PROCESS --> SWMUH_COMMIT
						SWMUH_COMMIT --> SWMUH_PL
			HMN_API_SWMUH.SAVE_INCOMING_FX --> CREATE_Default_Devir_Mesaji
			HMN_API_SWMUH.SAVE_INCOMING_FX --> create_Accounting?
			HMN_API_SWMUH.SAVE_INCOMING_FX --> anything_else?
	saveIncomingFx --> |Mesajsiz İslem|!isFreeVTNOutgoing
	saveIncomingFx --> finishSaveProcess
		finishSaveProcess --> validateAfterApproval
	%% TODO
	saveIncomingFx --> todo
	
	
	GÖE --> onFooterRemoteControl_Before_Approval --> validateBeforeApproval
		%% NTV, VTV, VTN, YID, BRD işlem tipleri 2FAControl tabidir.
		validateBeforeApproval --> |1|2FAControl
			%% 2FAControl --> |IP and ProcessID Control|isAuthorizedToUseProcess
		validateBeforeApproval --> |2|!isReverseTransaction
			!isReverseTransaction --> |2.1|Gecmis_Valorlu_Iade_Mesajı_Control
			!isReverseTransaction --> |2.2 !isVTN|Cutoff_Control
			!isReverseTransaction --> |2.3 swmuh osn rel|Duplicate_Acc_Control
	%% Account Type List: Nostro, Musteri, Kebir, Istira, Teminat, UNKNOWN, Temlik, Vostro
	%% Yetersiz Bakiye vb
		validateBeforeApproval --> |3 Account Type == VOSTRO|LRC_Control
			LRC_Control --> Customer_Confirmation_MSGForApprove
	GÖE --> onFooterRemoteControl_After_Approval
		onFooterRemoteControl_After_Approval --> |1 save upid, user name surname, user code, process note|is_Akislari
		onFooterRemoteControl_After_Approval --> |2|validateAfterApproval
			validateAfterApproval --> |2.1 if DIG or VTN|Masak_Control
				Masak_Control --> |1 Masak Banned Flag = M|Gonderici_yasak_listesinde[Gonderici bilgilerinin yasakli listede olmasi sebebinden masak tedbir muhasebesi ile GL'e odenmistir]
				Masak_Control --> |2 Masak Banned Flag = S|Alici_hesap_yasakli_listede[Alici hesap bilgilerinin yasakli listede olmasi sebebinden aAlicinin blokeli hesabina swmuh muhasebesi yapilmistir]
				Masak_Control --> |3|End
			validateAfterApproval --> |2.2|End
	%%	GÖE --> |creates?|swmuh
	%%	GÖE --> |creates?|accounting
	%%	GÖE --> |creates?|commission
	%%	GÖE --> |creates?|swift
	
	
	
	%% action start color.
	%% style 3 fill:#b36e00;
	
	%% end or close to end color.
	%% style 1 fill:#01734c;
	
	%% Helper method color.
	%% style 2 fill:#00008b;



















graph LR
	title[Gelen Yabancı Para Transfleri Ekrani]
	
	BeforePerformTransaction --> gsrpControl
		gsrpControl --> |msgType contains 103, PaymentType not GHI, LHD|gsrpControls
	BeforePerformTransaction --> confirmIncomingFx
		confirmIncomingFx --> HMN_API_SWMUH
			HMN_API_SWMUH --> todo
	
	AfterPerformTransaction --> abc















git config --global alias.acep '!f() { \
    git add . && \
    echo -e "\033[32mStaged files\033[0m" && \
    git diff --cached --name-only && \
    git commit && \
    commit_hash=$(git rev-parse HEAD) && \
    echo -e "\033[32mCommitted Message for $commit_hash\033[0m" && \
    git log -1 --pretty=%B && \
    git push origin HEAD && \
    echo -e "\033[32mPushed\033[0m\n"; \
}; f'




git config --global alias.fcim '!f() { \
    base_branch="$1"; \
    feature_branch=$(git rev-parse --abbrev-ref HEAD); \
    integration_branch="integration_${base_branch#*/}_$feature_branch"; \
    git fetch && \
    echo -e "\033[32m# Fetched\033[0m" && \
    git checkout -b "$integration_branch" origin/"$base_branch" && \
    echo -e "\033[32m# $integration_branch created\033[0m" && \
    git merge "$feature_branch" && \
    echo -e "\033[32m# Merged with $feature_branch\033[0m" && \
    echo -e "\033[33mAfter that: git add <conflicted_files> && git commit -m \"Merge: Conflict solved.\" && git push origin \"$integration_branch\"\033[0m" ; \
}; f'



git log --pretty=format:'%C(yellow)%h%Creset %C(cyan)%ad%Creset %C(white)%s%Creset %C(red)%d%Creset' --abbrev-commit --date=relative


