# Suppliers Folder

This folder contains **100+ supplier technical specification PDFs** used in the Dark Data to Golden Data project.

## 📋 Contents

All files in this folder are supplier manuals and technical documentation for ingredient specifications.

### Supplier Series:

**Cyber Series** (e.g., `cyber-flux_200_manual.pdf`)
- Advanced ingredient formulations
- Proprietary compound specifications

**Galactic Series** (e.g., `galactic_lavender_oil_manual.pdf`)
- Natural and plant-based ingredients
- Organic certification details

**Glacial Series** (e.g., `glacial_caramel_concentrate_manual.pdf`)
- Stabilizers and emulsifiers
- Temperature stability specs

**Hyper Series** (e.g., `hyper-synthesizer_501_manual.pdf`)
- High-performance additives
- Performance metrics and testing

**Krypto Series** (e.g., `krypto-stabilizer_352_manual.pdf`)
- Advanced stabilization compounds
- Long-shelf-life formulations

**Lunar Series** (e.g., `lunar_beet_powder_manual.pdf`)
- Natural colorants and flavors
- Nutritional ingredient data

**Martian Series** (e.g., `martian_peanut_lecithin_manual.pdf`)
- Allergen-containing ingredients
- Comprehensive allergen warnings

**Nebula Series** (e.g., `nebula_passionfruit_syrup_manual.pdf`)
- Flavor concentrates and syrups
- Potency and dilution specs

**Neuro-Matrix Series** (e.g., `neuro-matrix_131_manual.pdf`)
- Complex formulation compounds
- Multi-component specifications

**Opal Series** (e.g., `opal_caramel_extract_manual.pdf`)
- Extract-based ingredients
- Concentration data

**Plasma Series** (e.g., `plasma_basil_oil_manual.pdf`)
- Essential oils and extracts
- Usage guidelines

## 🔍 Data Extraction

These PDFs are processed by **BigQuery Knowledge Catalog** to extract:
- Supplier identification
- Ingredient specifications
- **Allergen information** (CRITICAL)
- Compliance certifications (FDA, EU, etc.)
- Safety data and warnings
- Storage requirements
- Shelf life specifications
- Contact information
- Certificate details

## 🔗 Relationships

Each supplier is linked to:
- **Ingredients** - provided by this supplier
- **Recipes** - indirectly through ingredients
- **Certifications** - compliance and quality standards
- **Allergens** - allergen data for their products

## ⚠️ Allergen Data

This folder's most critical information is **allergen data**. Files contain:
- Primary allergens (nuts, dairy, soy, etc.)
- Cross-contamination warnings
- Processing facility allergen info
- Labeling requirements
- Safety certifications

## 📊 Processing

See [../details.md](../details.md) for information about how these supplier manuals are extracted and processed into BigQuery tables.

## 🚀 Use Cases

### Allergen Checking (Most Common)
- Find all allergens in a specific ingredient
- Check if a supplier product is safe for allergic customers
- Verify compliance with allergen labeling

### Supply Chain Analysis
- Identify all suppliers for each ingredient type
- Verify supplier certifications
- Check alternative suppliers if one is unavailable

### Compliance & Audit
- Verify allergen warning completeness
- Check certification dates and validity
- Generate audit reports

---

For more information, see the [main README](../README.md).

