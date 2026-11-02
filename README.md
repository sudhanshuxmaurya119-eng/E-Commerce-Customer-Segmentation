# E-Commerce Customer Segmentation

A machine learning case study that segments e-commerce customers using **RFM (Recency, Frequency, Monetary) analysis** and unsupervised clustering. The notebook compares **K-Means** and **DBSCAN**, visualizes customer groups with **PCA** and **t-SNE**, and maps the K-Means segments to marketing personas.

## Project Overview

E-commerce businesses can use customer segmentation to understand purchasing behaviour and plan more targeted marketing campaigns. This project builds customer-level RFM features from retail transaction data, transforms and scales those features, applies clustering algorithms, and exports customer and cluster profiles.

## Objectives

- Clean and prepare retail transaction data.
- Calculate RFM features for each customer.
- Reduce skewness with a log transformation and standardize the features.
- Explore the number of K-Means clusters using the elbow method and silhouette scores.
- Compare K-Means and DBSCAN clustering.
- Visualize segments using PCA and t-SNE.
- Create customer profiles and marketing personas.
- Export segmentation results to CSV files.

## Dataset

The notebook expects an Excel workbook named **`online_retail_II.xlsx`** with two sheets:

- `Year 2009-2010`
- `Year 2010-2011`

Place the workbook in the notebook's working directory, or update the file path in the notebook. The notebook currently uses a Google Colab-style path:

```python
file = '/content/online_retail_II.xlsx'
```

The transaction data is expected to contain fields such as `Customer ID`, `Invoice`, `InvoiceDate`, `Quantity`, `Price`, and `Description`.

**Note:** The dataset workbook is not included with this README; provide it separately before running the notebook.

## Tech Stack

- **Language:** Python
- **Data handling:** pandas, NumPy
- **Visualization:** Matplotlib, Seaborn
- **Preprocessing:** scikit-learn `StandardScaler`
- **Clustering:** scikit-learn `KMeans`, `DBSCAN`
- **Dimensionality reduction:** scikit-learn `PCA`, `TSNE`
- **Evaluation:** scikit-learn `silhouette_score`

## Workflow

1. **Load data** — read both Excel sheets and combine them into one DataFrame.
2. **Clean data** — handle missing customer IDs and descriptions, remove duplicate records, exclude cancelled invoices, and remove invalid prices or quantities.
3. **Calculate revenue** — derive transaction revenue from quantity and price.
4. **Build RFM features** — aggregate transactions by customer:
   - **Recency:** days since the customer's most recent invoice, relative to the notebook's reference date.
   - **Frequency:** number of distinct invoices.
   - **Monetary:** total revenue attributed to the customer.
5. **Transform and scale** — apply `log1p` to the RFM features and standardize them before clustering.
6. **Evaluate K-Means** — inspect the elbow curve and silhouette scores for candidate values of K; compare K=2 and K=4.
7. **Fit K-Means** — use four clusters for the notebook's final segmentation.
8. **Fit DBSCAN** — inspect a k-distance graph and test several `eps` values; the notebook's final DBSCAN configuration uses `eps=0.35` and `min_samples=5`.
9. **Visualize** — project the scaled RFM features into two dimensions using PCA and t-SNE.
10. **Profile and export** — summarize cluster characteristics, assign marketing personas to K-Means clusters, and save CSV results.

## Models and Evaluation

### K-Means
The notebook evaluates K values from 2 through 10 using:
- **Elbow method (WCSS/inertia):** helps identify where adding clusters gives diminishing returns.
- **Silhouette score:** measures how well-separated clusters are; higher is generally better.

The final K-Means model uses `n_clusters=4`, `random_state=42`, and `n_init=10`.

### DBSCAN
DBSCAN groups dense regions and labels points that do not belong to a dense cluster as noise (`-1`). The notebook explores different `eps` values and uses `eps=0.35`, `min_samples=5` for its final DBSCAN run.

The notebook's comparison chart reports these silhouette scores:
- **K-Means:** `0.3746`
- **DBSCAN:** `0.1201`

These are values recorded in the notebook's comparison chart; rerun the notebook on your dataset to reproduce and verify results. Based on this comparison, the notebook selects K-Means as the primary segmentation model.

## Marketing Personas

The notebook maps the four K-Means cluster IDs to these personas:

| Cluster ID | Persona |
|---:|---|
| 0 | Dormant Customers |
| 1 | Potential Loyalists |
| 2 | Champions / VIPs |
| 3 | At-Risk Customers |

These labels are assigned by the notebook. Before using them for business decisions, review each cluster's Recency, Frequency, and Monetary statistics to confirm the labels accurately describe the customers in that cluster.

## Outputs

Depending on which notebook cells are run, the project exports:

- `customer_segmentation_final.csv` — customer-level RFM values, cluster assignments, DBSCAN labels, and persona labels.
- `final_customer_segmentation.csv` — customer-level segmentation results.
- `final_cluster_profiles.csv` — summary profile of K-Means clusters.
- `customer_segment_profiles.csv` — exported K-Means segment profiles.
- `dbscan_cluster_profiles.csv` — summary profile of DBSCAN clusters.
- `pca_visualization.png` — PCA cluster visualization.
- `tsne_visualization.png` — t-SNE cluster visualization.
- `dbscan_visualization.png` — DBSCAN visualization.
- `customer_segmentation_dashboard.png` — dashboard figure, if the relevant notebook cell is saved to that filename.

Some output filenames are saved more than once in the notebook. Existing files may be overwritten when cells are rerun.

## How to Run

### Option 1: Google Colab

1. Upload `E_Commerce_Customer_Segregation.ipynb` to Google Colab.
2. Upload `online_retail_II.xlsx` to the Colab runtime.
3. Confirm the workbook path in the notebook.
4. Run the notebook cells from top to bottom.
5. Download the generated CSV files and plots when prompted.

### Option 2: Run Locally

1. Install Python 3.10 or newer.
2. Put `E_Commerce_Customer_Segregation.ipynb` and `online_retail_II.xlsx` in your project folder.
3. Install the dependencies:

   ```bash
   pip install pandas numpy matplotlib seaborn scikit-learn openpyxl jupyter
   ```

4. Update the dataset path in the notebook to your local file, for example:

   ```python
   file = 'online_retail_II.xlsx'
   ```

5. Launch Jupyter:

   ```bash
   jupyter notebook
   ```

6. Open `E_Commerce_Customer_Segregation.ipynb` and run all cells in order.

## Repository Structure

```text
.
├── E_Commerce_Customer_Segregation.ipynb
├── online_retail_II.xlsx              # Dataset; add separately
├── README.md
├── customer_segmentation_final.csv    # Generated output
├── final_cluster_profiles.csv         # Generated output
└── dbscan_cluster_profiles.csv        # Generated output
```

The CSV and image files are generated by running the notebook; they may not exist before execution.

## Potential Business Uses

- Design re-engagement campaigns for dormant or at-risk customers.
- Reward high-value customers with loyalty benefits.
- Encourage repeat purchases from potential loyalists.
- Compare revenue contribution across customer segments.
- Tailor promotional messages to observed purchasing behaviour.

## Limitations

- Results depend on the quality and date range of the transaction data.
- RFM summarizes past behaviour and does not guarantee future customer behaviour.
- Cluster IDs are arbitrary; their meaning comes from examining cluster profiles.
- PCA and t-SNE are visualization tools and do not themselves establish business value.
- DBSCAN results can be sensitive to `eps`, `min_samples`, and feature scaling.
- The notebook's persona labels should be validated against the actual cluster statistics before operational use.

## Conclusion

This project demonstrates an end-to-end unsupervised learning workflow for e-commerce customer segmentation. It uses RFM features, evaluates K-Means and DBSCAN, visualizes customer groups, and produces customer-level results and cluster profiles that can support more focused marketing strategies.
