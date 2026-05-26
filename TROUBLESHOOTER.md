# Troubleshooter Guide: Dark Data to Golden Data

## 🔧 Common Issues & Solutions

This guide helps you resolve common issues you may encounter when extracting data from PDFs using BigQuery Knowledge Catalog and Dataplex.

---

## 📋 Quick Reference

| Issue | Symptom | Solution |
|-------|---------|----------|
| PDF Extraction Failing | Empty extraction results | [See PDF Format Issues](#pdf-format-issues) |
| BigQuery Permissions | Access denied errors | [See Permissions Issues](#permissions-issues) |
| Relationship Inference | Missing links between tables | [See Semantic Inference Issues](#semantic-inference-issues) |
| Performance Slow | Long extraction time | [See Performance Issues](#performance-issues) |
| Data Quality | Incorrect extractions | [See Data Quality Issues](#data-quality-issues) |

---

## 🔍 PDF Format Issues

### Problem: Some PDFs Not Being Extracted

**Symptoms**:
- Empty result sets for certain PDFs
- Error: "PDF is not readable" or similar messages
- Some recipe/supplier files returning no data

**Root Causes**:
- Scanned PDFs without OCR
- Password-protected documents
- Image-only PDFs
- Corrupted files

**Solutions**:

**Step 1: Verify PDF Quality**
```python
# Check if PDF is searchable
python -m pdfplumber <file.pdf>
# If no text extracted, PDF needs OCR
```

**Step 2: Apply OCR if Needed**
```bash
# Use Google Cloud Document AI for OCR
gcloud documentai processors process <PDF_FILE> \
  --processor-name=projects/<PROJECT_ID>/locations/us/processors/<PROCESSOR_ID> \
  --input-file=<PDF_FILE>
```

**Step 3: Re-upload Processed PDFs**
```bash
# Upload OCR'd PDFs to Cloud Storage
gsutil cp <processed_pdfs>/* gs://<BUCKET_NAME>/
```

**Step 4: Re-run Datascan**
- Trigger new Datascan job in Knowledge Catalog
- Monitor extraction progress in Cloud Console

---

### Problem: Inconsistent Text Extraction

**Symptoms**:
- Ingredient names extracted differently
- Missing allergen information from some files
- Inconsistent formatting across extractions

**Solutions**:

**Step 1: Standardize Document Format**
- Ensure all PDFs use consistent fonts and structure
- Provide template guidance to suppliers

**Step 2: Post-Process Extraction**
```sql
-- Use REGEXP functions to standardize allergen names
SELECT 
  recipe_id,
  REGEXP_REPLACE(allergen, r'\(.*\)', '') AS clean_allergen
FROM `project.dataset.extracted_allergens`
```

**Step 3: Manual Validation**
- Review extracted data against source PDFs
- Create correction table for known issues
- Join corrections in downstream queries

---

## 🔐 Permissions Issues

### Problem: Access Denied When Running Queries

**Error Message**:
```
Permission denied: User does not have bigquery.datasets.get permission
```

**Root Cause**: Service account lacks required permissions

**Solution**:

**Step 1: Check Service Account**
```bash
# Identify your service account
gcloud auth list

# Check current service account
gcloud config get-value account
```

**Step 2: Grant Required Roles**
```bash
# Grant BigQuery roles to service account
gcloud projects add-iam-policy-binding <PROJECT_ID> \
  --member=serviceAccount:<SA_EMAIL> \
  --role=roles/bigquery.dataEditor

# Grant Data Catalog roles
gcloud projects add-iam-policy-binding <PROJECT_ID> \
  --member=serviceAccount:<SA_EMAIL> \
  --role=roles/datacatalog.editor

# Grant Dataplex roles
gcloud projects add-iam-policy-binding <PROJECT_ID> \
  --member=serviceAccount:<SA_EMAIL> \
  --role=roles/dataplex.editor
```

**Step 3: Verify Permissions**
```bash
# List granted permissions
gcloud projects get-iam-policy <PROJECT_ID> \
  --flatten="bindings[].members" \
  --filter="bindings.members:<SA_EMAIL>"
```

---

### Problem: Cannot Access Cloud Storage Bucket

**Error Message**:
```
Access denied: User does not have storage.buckets.get permission
```

**Solution**:

```bash
# Grant Storage permissions
gcloud projects add-iam-policy-binding <PROJECT_ID> \
  --member=serviceAccount:<SA_EMAIL> \
  --role=roles/storage.objectViewer

# Grant Storage Admin if writing results
gcloud projects add-iam-policy-binding <PROJECT_ID> \
  --member=serviceAccount:<SA_EMAIL> \
  --role=roles/storage.admin
```

---

## 🔗 Semantic Inference Issues

### Problem: Relationships Not Being Inferred

**Symptoms**:
- Ingredient → Supplier links missing
- Allergen relationships incomplete
- Recipe → Ingredient mappings not created

**Root Cause**: Dataplex may need configuration or schema hints

**Solutions**:

**Step 1: Verify Dataplex Configuration**
```bash
# Check Dataplex asset status
gcloud dataplex assets describe <ASSET_ID> \
  --location=<LOCATION> \
  --lake=<LAKE_ID>
```

**Step 2: Provide Entity Hints**
```sql
-- Create explicit mappings to help inference
CREATE OR REPLACE TABLE `project.dataset.ingredient_supplier_map` AS
SELECT DISTINCT
  ing.ingredient_id,
  sup.supplier_id,
  ing.ingredient_name,
  sup.supplier_name
FROM `project.dataset.ingredients` ing
CROSS JOIN `project.dataset.suppliers` sup
WHERE LOWER(ing.ingredient_name) LIKE CONCAT('%', LOWER(sup.supplier_name), '%')
```

**Step 3: Run Relationship Discovery**
```bash
# Trigger Dataplex metadata discovery
gcloud dataplex content create \
  --lake=<LAKE_ID> \
  --location=<LOCATION> \
  --project=<PROJECT_ID>
```

---

### Problem: False Relationship Inferences

**Symptoms**:
- Wrong ingredients linked to recipes
- Incorrect supplier matches
- Spurious allergen assignments

**Solution**:

**Step 1: Reduce Inference Scope**
```sql
-- Tighten matching criteria
SELECT *
FROM `project.dataset.recipe_ingredients`
WHERE confidence_score > 0.8
  AND EXACT_STRING_MATCH(recipe_name, ingredient_name)
```

**Step 2: Manual Review Process**
1. Export inferred relationships
2. Have domain expert verify
3. Create approved relationships table
4. Use approved table in production queries

**Step 3: Implement Confidence Scoring**
```sql
-- Add confidence scores to all inferred relationships
SELECT 
  *,
  CASE 
    WHEN exact_match THEN 0.95
    WHEN contains_match THEN 0.70
    ELSE 0.40
  END AS confidence_score
FROM inferred_relationships
```

---

## ⚡ Performance Issues

### Problem: Datascan Jobs Running Very Slowly

**Symptoms**:
- Extraction takes > 1 hour for 400 PDFs
- High CPU/Memory usage
- Frequent timeouts

**Solutions**:

**Step 1: Enable Parallel Processing**
```yaml
# In Datascan configuration
dataScan:
  parallelProcessing:
    enabled: true
    maxParallelJobs: 10
```

**Step 2: Optimize PDF Processing**
```bash
# Split large PDFs before processing
pdftk input.pdf cat 1-50 output output_part1.pdf
pdftk input.pdf cat 51-100 output output_part2.pdf

# Process in parallel
for i in {1..8}; do
  gcloud dataplex scans create \
    --project=<PROJECT_ID> \
    --location=<LOCATION> \
    --lake=<LAKE_ID> \
    --data-source="gs://<BUCKET>/part${i}" &
done
```

**Step 3: Review BigQuery Quotas**
```bash
# Check quota usage
gcloud compute project-info describe <PROJECT_ID> \
  --format="value(quotas[])"
```

---

### Problem: Slow BigQuery Queries on Extracted Data

**Symptoms**:
- Join queries taking > 30 seconds
- High data scanned messages
- Nested loop joins being used

**Solutions**:

**Step 1: Add Clustering**
```sql
CREATE OR REPLACE TABLE `project.dataset.recipes`
CLUSTER BY recipe_name AS
SELECT * FROM `project.dataset.recipes_raw`
```

**Step 2: Partition Large Tables**
```sql
CREATE OR REPLACE TABLE `project.dataset.suppliers`
PARTITION BY DATE(last_updated)
CLUSTER BY supplier_id
AS
SELECT * FROM `project.dataset.suppliers_raw`
```

**Step 3: Create Materialized Views**
```sql
-- Cache common joins
CREATE MATERIALIZED VIEW `project.dataset.recipe_allergens_mv` AS
SELECT 
  r.recipe_name,
  ARRAY_AGG(DISTINCT i.allergens IGNORE NULLS) as all_allergens
FROM `project.dataset.recipes` r
JOIN `project.dataset.recipe_ingredients` ri USING(recipe_id)
JOIN `project.dataset.ingredients` i USING(ingredient_id)
GROUP BY r.recipe_name
```

---

## 📊 Data Quality Issues

### Problem: Incomplete or Incorrect Extractions

**Symptoms**:
- Missing ingredient lists from recipes
- Incomplete allergen data
- Truncated supplier information

**Solutions**:

**Step 1: Data Quality Validation**
```sql
-- Create data quality checks
SELECT 
  'Missing Ingredients' AS issue,
  COUNT(*) AS count
FROM `project.dataset.recipes`
WHERE ingredients IS NULL OR ARRAY_LENGTH(ingredients) = 0

UNION ALL

SELECT
  'Missing Allergens',
  COUNT(*)
FROM `project.dataset.suppliers`
WHERE allergens IS NULL OR ARRAY_LENGTH(allergens) = 0
```

**Step 2: Set Up Monitoring**
```bash
# Create data quality alerts
gcloud monitoring policies create \
  --notification-channels=<CHANNEL_ID> \
  --display-name="Extraction Quality Alert" \
  --condition="missing_data_ratio > 0.1"
```

**Step 3: Manual Enrichment**
```sql
-- Create enrichment table for missing data
CREATE TABLE `project.dataset.manual_enrichment` (
  pdf_file STRING,
  field_name STRING,
  extracted_value STRING,
  corrected_value STRING,
  notes STRING
)

-- Use in queries
SELECT *
FROM original_extraction
LEFT JOIN manual_enrichment USING(pdf_file)
```

---

## 🐛 BigQuery Agents Issues

### Problem: AI Agent Returns Incorrect Information

**Symptoms**:
- Wrong allergen information provided
- Mismatched recipes and ingredients
- Hallucinated data not in database

**Solutions**:

**Step 1: Verify Data Source**
```sql
-- Ensure agent queries are using correct tables
SELECT table_name, row_count
FROM `project.dataset.__TABLES_SUMMARY__`
WHERE row_count > 0
```

**Step 2: Restrict Agent Access**
```python
# In agent configuration, restrict to approved tables
ALLOWED_TABLES = [
  'recipes',
  'ingredients', 
  'suppliers',
  'recipe_ingredients'
]

# Deny access to raw/draft tables
DENIED_PATTERNS = ['*_raw', '*_staging', '*_test']
```

**Step 3: Add Data Grounding**
```sql
-- Create agent-friendly views with validation
CREATE OR REPLACE VIEW `project.dataset.agent_recipes` AS
SELECT 
  recipe_id,
  recipe_name,
  ARRAY_AGG(ingredient_name) as ingredients,
  ARRAY_AGG(allergen) as allergens
FROM `project.dataset.recipes`
JOIN `project.dataset.recipe_ingredients` USING(recipe_id)
JOIN `project.dataset.ingredients` USING(ingredient_id)
WHERE extraction_confidence > 0.8
GROUP BY recipe_id, recipe_name
```

---

## 🔗 BigLake Federation Issues

### Problem: Iceberg Table Queries Failing

**Error Message**:
```
INVALID_ARGUMENT: Failed to read Iceberg table
```

**Solutions**:

**Step 1: Verify Template Configuration**
```yaml
# Check template.yaml kernel settings
spark.sql.catalog.<CATALOG_NAME>: org.apache.iceberg.spark.SparkCatalog
spark.sql.defaultCatalog: <CATALOG_NAME>
```

**Step 2: Check Service Account Permissions**
```bash
# Service account needs:
gcloud projects add-iam-policy-binding <PROJECT_ID> \
  --member=serviceAccount:<SA_EMAIL> \
  --role=roles/bigquery.admin

gcloud projects add-iam-policy-binding <PROJECT_ID> \
  --member=serviceAccount:<SA_EMAIL> \
  --role=roles/dataproc.editor
```

**Step 3: Verify Table Registration**
```python
# In Spark notebook, check table existence
spark.sql("SHOW TABLES IN <CATALOG_NAME>.<NAMESPACE>")
spark.sql("DESCRIBE <CATALOG_NAME>.<NAMESPACE>.<TABLE_NAME>")
```

---

## 📞 Getting Help

### Debug Information to Collect

When troubleshooting, gather:
```bash
# Project info
gcloud config get-value project

# API status
gcloud services list --enabled | grep -E "bigquery|dataplex|datacatalog"

# Recent errors (last 24 hours)
gcloud logging read "resource.type=resource/batch" --limit 50 --format=json

# Quota usage
gcloud compute project-info describe $(gcloud config get-value project) --format="value(quotas[])"
```

### Escalation Path

1. **Check this guide** - Most common issues covered
2. **Review logs** - Cloud Logging for detailed error messages
3. **Test with sample data** - Isolate the issue
4. **Contact GCP Support** - For API-level issues
5. **Review documentation** - Links below

### Useful Documentation Links

- [BigQuery Knowledge Catalog](https://cloud.google.com/bigquery/docs/knowledge-catalog)
- [Dataplex Documentation](https://cloud.google.com/dataplex/docs)
- [Document AI OCR](https://cloud.google.com/document-ai/docs)
- [BigLake with Iceberg](https://cloud.google.com/bigquery/docs/biglake-intro)
- [Dataproc Serverless](https://cloud.google.com/dataproc-serverless/docs)

---

## 📝 Reporting Issues

Found a reproducible bug? Create a report including:
- **Environment**: Project ID, region, GCP version
- **Steps to Reproduce**: Exact commands run
- **Expected Behavior**: What should happen
- **Actual Behavior**: What actually happened
- **Error Messages**: Full stack traces
- **Sample Data**: (if possible) Example PDF or file
- **Logs**: Cloud Logging output

---

**Need more help? Check the [README.md](README.md) and [details.md](details.md) for project overview.**

---

**Last Updated**: May 26, 2026
