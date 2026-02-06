#  SBA Loan Default Prediction 
### 結合 Elastic Net 特徵篩選與 XGBoost / Random Forest 之信用風險評估

---

##  專案摘要 (Executive Summary)
本專案利用美國中小企業局 (SBA) 的歷史貸款資料，建立高準確度的違約預測模型。面對 **80 萬筆大數據**與高維度特徵，開發了一套 **混合模型流程 (Hybrid Pipeline)**：
1. **特徵瘦身**：利用 **Elastic Net** 進行特徵稀疏化，精確剔除雜訊。
2. **非線性建模**：銜接 **XGBoost** 與 **Random Forest** 捕捉複雜的非線性關係。
3. **最終表現**：在測試集達到 **97.95%** 的準確度，具備極佳的泛化能力與實務參考價值。

---

## 🛠 技術棧 (Tech Stack)
* **程式語言**: `Python`
* **資料處理**: `Pandas`, `Numpy`
* **視覺化**: `Matplotlib`, `Seaborn`
* **機器學習**: `Scikit-learn`, `XGBoost`
* **模型優化**: `GridSearchCV` (3-Fold Cross Validation)

---

##  數據處理與特徵工程 (Data Engineering)

### 1. 資料清洗 (Data Cleaning)
* **精簡特徵**：刪除無學習意義欄位（如 `ID`, `Name`, `Zip`）及類別過多之城市欄位 (`City`)。
* **標籤處理**：轉換目標變數 `MIS_Status` 為二元標籤（0: 還清, 1: 違約）。
* **防止洩漏**：嚴格剔除 **資料洩漏 (Data Leakage)** 相關欄位，確保預測真實性。
* **時間工程**：將日期格式轉換為「距紀錄首日之天數」，量化貸款批准與撥款的時間跨度。
* **缺失值處理**：將重要欄位的缺失值標記為新類別（NaN category），保留其背後的資訊。

### 2. 進階特徵工程 (Feature Engineering)
* **編碼策略**：
    * **One-Hot Encoding**：處理低種類類別特徵。
    * **Target Encoding**：針對高種類類別，將其轉換為「歷史違約率」，有效提取地理與銀行端的風險關聯。
* **數值分布優化**：
    * **對數轉換 (Log Transformation)**：針對金額類欄位進行取對數處理，壓縮嚴重的右偏態分佈（Right-Skewed），縮小 Scale 使模型更易收斂。
    * **標準化**：使用 `StandardScaler` 統一。

---

##  特徵選取 (Feature Selection)
採用 **Elastic Net (L1/L2 Regularization)** 作為變數過濾器：
* **自動化篩選**：利用 L1 懲罰項自動將影響力微弱的變數係數壓至零。
* **效益**：將原始維度縮減至精華特徵，大幅降低運算成本，並提升非線性模型的預測穩定性。

---

##  實驗結果與分析 (Results)

### 模型表現對比
| 模型 | Accuracy | 核心影響因子 (Top Features) |
| :--- | :---: | :--- |
| **Random Forest** | 97.10% | `Term` (40%) |
| **XGBoost** | **97.95%** | `Term` (29%), `ApprovalFY` (27%) |

### 視覺化分析

#### 1. XGBoost 效能評估
* **混淆矩陣**：展現模型對「違約者 (1)」極強的捕捉能力（Recall），有效降低銀行潛在呆帳風險。
<img width="565" height="432" alt="xgboost混淆矩陣" src="https://github.com/user-attachments/assets/7cbafdeb-64d1-4a15-8026-f343d53dc816" />

* **特徵重要性**：模型高度依賴 `Term` (貸款期限) 與 `ApprovalFY` (批准財政年度)。
<img width="611" height="413" alt="xgboost參數重要性" src="https://github.com/user-attachments/assets/d62d141a-d70f-41ec-a134-ab6ec9fc5584" />

#### 2. Random Forest 效能評估
* **混淆矩陣**：
<img width="565" height="432" alt="rf混淆矩陣" src="https://github.com/user-attachments/assets/ed903d88-7046-4c04-a323-5efd6e2c401f" />

* **特徵重要性**：`Term` 佔比高達 40%，印證了還款期限與違約機率的高度相關性。
<img width="647" height="413" alt="rf參數重要性" src="https://github.com/user-attachments/assets/05b20f6b-714f-4369-9822-fea7c6a6f984" />

---

##  專案結論
1.  **業務洞察**：模型揭示了 `Term` 是判斷貸款風險的關鍵。在金融實務中，期限較短的貸款通常伴隨更高的違約機率，這與分析結果完全吻合。
2.  **技術驗證**：透過 `GridSearchCV` 與 `Cross-Validation` 進行嚴謹調參，確保了 97% 以上的高準確率並非來自於過擬合，具備實務佈署價值。


