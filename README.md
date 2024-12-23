sequenceDiagram
    %% Urun Yonetim Ekrani
 	STP_NonSTP_Batch ->> Business_Rules_DB_Storage: Get
    Business_Rules_DB_Storage ->> STP_NonSTP_Batch: Credit, Black, White, Amount, ClientType, Branch, etc lists

sequenceDiagram
 	STP_NonSTP_Batch ->> Order_DB_Storage: Get_STP_Empty_Records
    Order_DB_Storage ->> STP_NonSTP_Batch: Response Currencies, Amounts, ClientType, SenderCountry, Branch, Narrative 

sequenceDiagram
    STP_NonSTP_Batch ->> Rules_Executer: Execute
    Rules_Executer ->> STP_NonSTP_Batch: Response
    STP_NonSTP_Batch ->> Order_DB_Storage: Update

sequenceDiagram
    Urun_Yonetim_Ekrani ->> Product_Management_DB_Storage: CRUD

sequenceDiagram
 	Enrichment ->> Order_DB_Storage: Get
    Order_DB_Storage ->> Transaction_Reason_DB_Storage: Get
    Transaction_Reason_DB_Storage ->> Order_DB_Storage: Records
    Order_DB_Storage ->> Enrichment: Records

sequenceDiagram
    Enrichment ->> CalculateCommission: Request
    CalculateCommission ->> Enrichment: Response
    Enrichment ->> Order_DB_Storage: insert

sequenceDiagram
    Enrichment ->> Transaction_Reason_Enrichment: Request
    Transaction_Reason_Enrichment ->> Enrichment: Response
    Enrichment ->> Transaction_Reason_DB_Storage: insert

sequenceDiagram
 	Validations_Batch ->> Order_DB_Storage: Get
    Order_DB_Storage ->> Validations_Batch: Records

sequenceDiagram
    Validaitons_Batch ->> NostroAccCheck: Check_Nostro_Closed
    NostroAccCheck ->> Validaitons_Batch: Response
    Validaitons_Batch ->> Order_DB_Storage: insert/Update

sequenceDiagram
    Validaitons_Batch ->> ValorCheck: Check_Nostro_Closed
    ValorCheck ->> Validaitons_Batch: Response
    Validaitons_Batch ->> Order_DB_Storage: insert/Update

sequenceDiagram
    Order_Execution_Batch ->> isPayable: Check_Nostro_Closed
    isPayable ->> Order_Execution_Batch: Response
    Order_Execution_Batch ->> Order_DB_Storage: insert/Update

sequenceDiagram
 	Transansfer_Creation_Batch ->> Order_DB_Storage: Get
    Order_DB_Storage ->> Transansfer_Creation_Batch: Records
    Transansfer_Creation_Batch ->> Transfer_DB_Storage: insert
