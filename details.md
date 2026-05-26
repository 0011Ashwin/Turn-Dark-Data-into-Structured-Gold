# Project Details: Dark Data to Golden Data

## 📋 Complete Project Architecture

This document provides comprehensive information about the project structure, usage of recipes and suppliers folders, and technical implementation details.

---

## 🏢 Project Overview

**Project Domain**: Frozen Yogurt (Froyo) Franchise Data Analytics  
**Project Goal**: Transform unstructured PDF documents into queryable BigQuery relational data  
**Technology**: Google Cloud Platform (GCP)

---

## 📁 Folder Structure & Content

### 1. `/recipes/` - Frozen Yogurt Recipe PDFs

**Purpose**: Contains detailed specifications for all Froyo flavor recipes

**Content**: 100+ PDF files documenting frozen yogurt formulations

**File Naming Convention**: `[flavor_descriptor_1]_[flavor_descriptor_2].pdf`

**Example Files**:
- `midnight_swirl.pdf` - Midnight Swirl flavor (premium example for allergen queries)
- `arctic_basil_flow.pdf` - Arctic Basil blend
- `aura_berry_impact.pdf` - Berry-based flavor
- `cosmic_lemon_glow.pdf` - Citrus flavor option
- `frozen_caramel_impact.pdf` - Caramel variant
- `golden_coconut_impact.pdf` - Tropical coconut blend
- `lunar_cinnamon_current.pdf` - Warm spice flavor
- `nebula_coffee_spark.pdf` - Coffee-based variant
- `ocean_acai_cloud.pdf` - Superfood acai blend

**Information Contained**:
- ✅ Ingredient lists with quantities
- ✅ Flavor profiles and taste notes
- ✅ Production procedures and temperatures
- ✅ Allergen warnings and cross-contamination risks
- ✅ Nutritional information (if available)
- ✅ Shelf life and storage conditions
- ✅ Ingredient sourcing requirements

**Data Extraction Use Case**: 
These PDFs are processed to create the `recipes` table in BigQuery, containing:
- `recipe_id`, `recipe_name`, `flavor_profile`, `ingredients`, `allergens`, etc.

---

### 2. `/suppliers/` - Supplier Technical Specification Sheets

**Purpose**: Contains detailed technical specifications for all ingredients used in recipes

**Content**: 100+ PDF files from ingredient suppliers

**File Naming Convention**: `[supplier_name]_[ingredient_type]_manual.pdf`

**Example Files by Supplier**:

**Cyber Series**:
- `cyber-flux_197_manual.pdf`
- `cyber-flux_200_manual.pdf`
- `cyber_acai_gum_manual.pdf`
- `cyber_chocolate_syrup_manual.pdf`
- `cyber_soy_protein_manual.pdf`

**Galactic Series**:
- `galactic_acacia_lecithin_manual.pdf`
- `galactic_lavender_oil_manual.pdf`
- `galactic_lychee_lecithin_manual.pdf`

**Glacial Series**:
- `glacial_almond_powder_manual.pdf`
- `glacial_caramel_concentrate_manual.pdf`
- `glacial_locust_bean_gum_manual.pdf`
- `glacial_sunflower_lecithin_manual.pdf`

**Hyper Series**:
- `hyper-synthesizer_501_manual.pdf`
- `hyper_chocolate_extract_manual.pdf`
- `hyper_vanilla_oil_manual.pdf`
- `hyper_whey_isolate_manual.pdf`

**Krypto Series**:
- `krypto-stabilizer_352_manual.pdf`
- `krypto-stabilizer_627_manual.pdf`

**Lunar Series**:
- `lunar_beet_powder_manual.pdf`
- `lunar_chocolate_oil_manual.pdf`
- `lunar_guar_concentrate_manual.pdf`

**Martian Series**:
- `martian_chocolate_concentrate_manual.pdf`
- `martian_crustacean_extract_manual.pdf`
- `martian_egg_isolate_manual.pdf`
- `martian_peanut_lecithin_manual.pdf`

**Nebula Series**:
- `nebula_almond_extract_manual.pdf`
- `nebula_passionfruit_lecithin_manual.pdf`

**Neuro-Matrix Series**:
- `neuro-matrix_131_manual.pdf`
- `neuro-matrix_914_manual.pdf`

**Opal Series**:
- `opal_agave_isolate_manual.pdf`
- `opal_caramel_extract_manual.pdf`
- `opal_pistachio_powder_manual.pdf`

**Plasma Series**:
- `plasma_almond_extract_manual.pdf`
- `plasma_basil_oil_manual.pdf`

**Information Contained**:
- ✅ Ingredient identification and classification
- ✅ Chemical composition and properties
- ✅ **CRITICAL: Allergen information**
- ✅ Safety data sheets (SDS) references
- ✅ Compliance certifications (FDA, EU, etc.)
- ✅ Storage requirements and stability
- ✅ Shelf life specifications
- ✅ Usage guidelines and restrictions
- ✅ Quality control parameters

**Data Extraction Use Case**: 
These PDFs are processed to create the `suppliers` and `ingredients` tables in BigQuery:
- `supplier_id`, `supplier_name`, `ingredient_id`, `ingredient_name`, `allergens`, `certifications`, etc.

---

## 🔗 Critical Relationships & Semantic Inference

The project automatically infers relationships between:

```
Recipes → Ingredients → Suppliers → Allergens & Certifications
```

### Example Flow: Midnight Swirl Allergen Query

1. **Query**: "Are there allergens in Midnight Swirl froyo?"

2. **Recipe Extraction**:
   - Extract `midnight_swirl.pdf` ingredients list
   - Ingredients: Cocoa Powder, Dairy Base, Emulsifier X, etc.

3. **Ingredient Matching**:
   - Cross-reference ingredients with supplier specs
   - Find which suppliers provide each ingredient

4. **Allergen Detection**:
   - Check each supplier manual for allergen information
   - Example: Dairy Base → `galactic_soy_isolate_manual.pdf` → Contains soy allergen
   - Example: Cocoa Powder → `martian_chocolate_concentrate_manual.pdf` → Contains tree nut allergen

5. **Final Result**:
   ```
   Midnight Swirl Allergens:
   - Dairy
   - Soy
   - Tree Nuts
   ```

---

## 🗂️ Data Processing Pipeline

### Phase 1: Ingestion
- PDFs uploaded to Google Cloud Storage
- Bucket: `gs://<BUCKET_NAME>/`

### Phase 2: Semantic Extraction
- **Tool**: BigQuery Knowledge Catalog + Datascan
- **Process**: Automatic OCR + semantic understanding
- **Output**: Extracted structured data

### Phase 3: Relationship Inference
- **Tool**: Dataplex
- **Process**: Automated entity linking and relationship detection
- **Output**: Inferred connections between tables

### Phase 4: Storage
- **Target**: BigQuery dataset `<BQ_DATASET_NAME>`
- **Format**: Relational tables with proper schema
- **Access**: SQL queries and BigQuery Agents

### Phase 5: Integration
- **Tool**: BigLake (Iceberg tables)
- **Process**: Federated queries across datasets
- **Kernel**: Serverless Spark Notebook (`iceberg-federation-template`)

---

## 📊 Expected BigQuery Schema

After processing, expect tables like:

### `recipes` Table
```sql
- recipe_id (STRING, PRIMARY KEY)
- recipe_name (STRING)
- flavor_profile (STRING)
- ingredients (ARRAY<STRING>)
- allergens (ARRAY<STRING>)
- production_temperature (FLOAT)
- shelf_life_days (INT64)
- extracted_from_pdf (STRING)
```

### `suppliers` Table
```sql
- supplier_id (STRING, PRIMARY KEY)
- supplier_name (STRING)
- contact_info (STRING)
- certifications (ARRAY<STRING>)
```

### `ingredients` Table
```sql
- ingredient_id (STRING, PRIMARY KEY)
- ingredient_name (STRING)
- supplier_id (STRING, FOREIGN KEY)
- allergens (ARRAY<STRING>)
- storage_conditions (STRING)
- compliance_standards (ARRAY<STRING>)
```

### `recipe_ingredients` Table (Junction)
```sql
- recipe_id (STRING, FOREIGN KEY)
- ingredient_id (STRING, FOREIGN KEY)
- quantity (FLOAT)
- unit (STRING)
- usage_notes (STRING)
```

---

## 🔧 Configuration Files

### `copilot-instructions.md`
**Purpose**: Critical execution rules for the project  
**Contains**:
- Project ID and configuration variables
- CRITICAL RULES for data processing
- Dataset naming conventions
- Spark notebook kernel requirements
- Data join specifications
- BigLake federation guidelines

### `quickstart.py`
**Purpose**: Register parquet files as BigLake Iceberg tables  
**Functionality**:
- Creates namespace in catalog
- Registers customer data files
- Enables BigLake federation
- Maps to BigQuery datasets

**Usage**:
```bash
python quickstart.py
```

### `template.yaml`
**Purpose**: Dataproc Serverless Spark session template  
**Contains**:
- Session configuration
- Spark properties for Iceberg support
- Service account details
- Network configuration
- Performance tuning parameters

---

## 🎯 Use Cases & Queries

### Use Case 1: Customer Allergen Check
```sql
SELECT DISTINCT ingredient.allergens
FROM `project.dataset.recipes` recipe
JOIN `project.dataset.recipe_ingredients` ri ON recipe.recipe_id = ri.recipe_id
JOIN `project.dataset.ingredients` ingredient ON ri.ingredient_id = ingredient.ingredient_id
WHERE recipe.recipe_name = 'Midnight Swirl'
```

### Use Case 2: Supplier Compliance Search
```sql
SELECT supplier_name, certifications
FROM `project.dataset.suppliers`
WHERE 'FDA' IN UNNEST(certifications)
```

### Use Case 3: Menu Planning
```sql
SELECT recipe_name, COUNT(DISTINCT ingredient_id) as ingredient_count
FROM `project.dataset.recipes`
JOIN `project.dataset.recipe_ingredients` USING(recipe_id)
GROUP BY recipe_name
ORDER BY ingredient_count DESC
LIMIT 10
```

---

## 📚 Technology Components

| Component | Purpose | Integration |
|-----------|---------|-------------|
| **BigQuery Knowledge Catalog** | Semantic extraction from PDFs | Datascan jobs |
| **Dataplex** | Data discovery & relationships | Metadata inference |
| **BigQuery** | Data warehouse | Tables & SQL |
| **BigLake** | Open formats (Iceberg) | Federation queries |
| **Dataproc Serverless** | Spark computation | Advanced analytics |
| **Cloud Storage** | PDF document storage | Source files |

---

## 🔐 Security & Compliance

- **Data Encryption**: At rest and in transit
- **Access Control**: Service accounts with minimal permissions
- **Audit Logging**: All queries logged in Cloud Audit Logs
- **PII Handling**: Customer data separated from recipe data
- **Compliance**: FDA, EU standards for food ingredients

---

## 📈 Performance Expectations

- **Extraction Speed**: 400+ PDFs in minutes
- **Query Response**: Sub-second for relational queries
- **Scalability**: Handles hundreds of ingredients and suppliers
- **Cost**: Serverless = pay only for resources used

---

## 🚀 Next Steps

1. **Setup**: Configure GCP project and enable required APIs
2. **Upload**: Place all PDFs in Cloud Storage bucket
3. **Extract**: Run Datascan with semantic inference
4. **Query**: Use BigQuery Agents to chat with data
5. **Integrate**: Build AI agent for customer queries
6. **Monitor**: Track extraction quality and performance

---

## 📖 Additional Resources

- **Copilot Instructions**: [copilot-instructions.md](copilot-instructions.md)
- **Quickstart Script**: [quickstart.py](quickstart.py)
- **Session Template**: [template.yaml](template.yaml)
- **Main Documentation**: [README.md](README.md)

---

**Transform Your Dark Data Into Golden Insights! ✨**
