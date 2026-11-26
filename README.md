📊 Retail Sales 2022–2025: do revenue and profit differ by year?

A practice project on a public Kaggle dataset: one question, two workflows, and an honest list of what the data cannot tell me. 🧭

🎯 The question

Do revenue and profit differ from year to year? If they do, why?

✅ Short answer

- 📉📈 Yes. 2024 dips, and 2025 rebounds strongly.
- ⚖️ The rate barely moves. Profit ÷ cost stays around 25% every year, so profit follows sales volume, not a change in profitability.
- 🧺 2023 is the interesting year: orders grew 11.4%, but sales grew only 0.6% and profit fell 1.3%. The average order got smaller (about 515 → 465).
- 🛑 I stopped there on purpose. See "Why I stopped here" below.

📈 Results

2022

- Orders: 3,900
- Sales: 2,008,644
- Profit: 406,577
- Profit ÷ cost: 25.4%
- Average order (sales ÷ orders): 515

2023

- Orders: 4,345
- Sales: 2,021,353
- Profit: 401,259
- Profit ÷ cost: 24.8%
- Average order (sales ÷ orders): 465

2024

- Orders: 4,127
- Sales: 1,905,208
- Profit: 382,799
- Profit ÷ cost: 25.1%
- Average order (sales ÷ orders): 462

2025

- Orders: 4,686
- Sales: 2,298,887
- Profit: 454,804
- Profit ÷ cost: 24.7%
- Average order (sales ÷ orders): 491

Orders = distinct "Order_ID". Dividing profit by "Sales_Amount" instead gives about 20% in every year, so the flat pattern does not depend on the denominator.

🔧 Workflow 1: from raw file to dashboard

flowchart LR
  A["📥 Kaggle CSV"] --> B["🧹 pandas: light cleaning"]
  B --> C["🦆 DuckDB SQL"]
  C --> D["📄 Row-level table"]
  C --> E["📄 Yearly summary tables"]
  D --> F["🔢 Power BI cards + slicer (DAX)"]
  E --> G["📈 Power BI charts"]
  E -. cross-check .-> F

1. 📥 Download the dataset with "kagglehub" in Google Colab.
2. 🧹 Light cleaning in pandas: drop duplicates, drop unused columns, standardize text (trim spaces, consistent casing).
3. 🦆 DuckDB SQL builds three CSVs:
   - "1_profit_orders_by_year.csv": orders and profit per year
   - "2_margin_by_year.csv": sales, cost, profit and profit ÷ cost per year
   - "3_data_for_dax.csv": one row per order line (year, channel, category, quantity, price, sales, cost, profit, order id)
4. 🔢 Power BI takes cards and the year slicer from the row-level table (DAX measures for totals and year-over-year change). Charts come from the yearly SQL tables.
5. 🔁 Cross-check: the yearly SQL tables are the reference, and the numbers on the dashboard have to match them.

💡 Why split it this way: SQL does the calculation, so Power BI stays thin and easy to read. The trade-off: adding a new metric means going back to SQL.

🧪 The Power BI part is a practice build, not a service I offer.

🖼️ Dashboard preview

<img width="4000" height="1800" alt="1000012944" src="https://github.com/user-attachments/assets/17d55b51-d924-40de-b9b4-27d399ee1d40" />


Practice build: cards and year slicer from the row-level table, chart from the yearly SQL table.

«⚠️ Known issue — room for improvement: The card labelled "Revenue" currently displays "Cost_Amount" instead of "Sales_Amount", while "Avg Order Value" uses "Sales_Amount". As a result, the "Revenue" card is incorrectly labelled and does not match the Sales figures in the results above.

I'm not fully satisfied with this dashboard, and I don't intend to treat this version as the standard for my future work. I see every project as an opportunity to learn, identify weaknesses and raise my standards. Continuous improvement matters more than simply finishing a project. I'll keep refining my analytical skills, accuracy and dashboard design with each new portfolio project rather than standing still. This is v1, not the finish line. 🚀»

🕵️ Workflow 2: how I audited the data

flowchart LR
  A["🤔 Doubt a column"] --> B["🧮 Test it with math"] --> C["🗂️ Collect several pieces of evidence"] --> D["⚖️ Conclusion + confidence"] --> E["📉 Chart it last"]

Charts come last because they make a conclusion easy to read, not stronger. Applied here:

- 🤔 Doubt: what do "Unit_Price", "Sales_Amount" and "Cost_Amount" really mean?
- 🧮 Test: on sampled rows, "Sales_Amount ≈ Quantity × Unit_Price × (1 − discount)" (row 1: 2 × 85.29 × 0.95 = 162.05), and "Profit = Sales_Amount − Cost_Amount" (162.05 − 115.61 = 46.44). Differences are at rounding level.
- 🗂️ Evidence: the same relationships hold on every sampled row, and profit ÷ cost is almost identical across four years.
- ⚖️ Conclusion: ✅ the column relationships are consistent on the rows I checked. ❓ What is inside "Cost_Amount" is not verified.

🚧 Limitations (what I could not verify)

- 🧪 Possibly synthetic data. Profit ÷ cost is almost flat across years, which real businesses rarely show. This is a suspicion, not proof.
- 🙈 "Cost_Amount" is not broken down. <!-- verify against sales_data_dictionary.csv, which ships with the dataset --> The dataset does not say which fees or deductions are inside it, so "Profit" is taken as given and is not verified as net profit. The ratio above is labelled "profit ÷ cost" for that reason.
- 🧾 All order statuses are included. Totals are not filtered to completed orders, and returns are not netted out.
- 🔍 The 2024 dip was not investigated beyond order count and basket size.
- 🧪 **Practice dataset, not real business data.** This Kaggle dataset is simulated and intended for learning and analysis practice. Its findings should not be treated as evidence for real business decisions.
🧭 Why I stopped here

The question was whether revenue and profit differ by year, and why. The answer is yes, and mostly through volume and basket size, because profit ÷ cost barely moves.

I also checked one extra hypothesis: do deeper discounts push products into losses? The evidence is not strong enough to report as a finding. "Cost_Amount" is not broken down and the ratio is almost uniform, so naming a product-level "cause" would be a story, not a result. 🐍🦶 Adding more charts here would add noise, not answers.

🔄 Continuous improvement

This repo is a v1. I keep a short log so the next project starts from what this one taught me, not from zero.

📝 Changelog

- v1.0 — First end-to-end build: Kaggle → DuckDB → Power BI. Learned the pipeline and the audit routine on a public dataset.
- v1.1 — README improvements: Added limitations, known issue and credits to explain clearly what the data and dashboard can and cannot support.
- Next project: Apply the checklist below using lessons from v1.

✅ Lessons I will apply next time

- [ ] 📖 Read the dataset's own documentation first and write a short column dictionary (what each column is, and what the documentation does not say it includes) before running any query.
- [ ] 🏷️ Name every dashboard metric after the exact column behind it (for example "Total Cost_Amount", not "Revenue").
- [ ] 🧾 Decide the scope up front (all order statuses or completed only), state it, and report both if it changes the answer.
- [ ] 🔢 Log row counts before and after every cleaning step.
- [ ] 🤖 Turn the SQL-versus-dashboard cross-check into a small automated check that runs before export.

❓ Open questions

Things I could not settle on my own. If you know, an issue or a message is very welcome 🙌

- When is a ratio "too flat" to be real business data? Is there a standard rule of thumb or test?
- How do you document a cost column that may hide unknown deductions, so a reader knows how far to trust a profit figure?

🙏 Credits

- 📦 Dataset: ""retail-sales-dataset"" (https://www.kaggle.com/datasets/danielsowah123/retail-sales-dataset) by "danielsowah123" on Kaggle. All credit for the data goes to the author. Usage follows the license shown on the dataset page.
- 🧰 Tools: pandas, DuckDB, Power BI Desktop, Google Colab and kagglehub, all by their respective authors.
- ✍️ Analysis and conclusions in this repo are mine, and so are the limits I describe above.

🛠️ Tools

🐍 Python (pandas) · 🦆 DuckDB · 📊 Power BI Desktop (DAX) · ☁️ Google Colab · 📦 kagglehub

▶️ Reproduce

1. Open the notebook in Colab. The dataset downloads automatically ("danielsowah123/retail-sales-dataset" on Kaggle, file "Sales_transactions_2022_2025.csv").
2. Run all cells. The three CSVs are written to a Google Drive folder.
3. Load the CSVs in Power BI Desktop.

The data file is not included in this repo. Check the dataset's license on its Kaggle page.
