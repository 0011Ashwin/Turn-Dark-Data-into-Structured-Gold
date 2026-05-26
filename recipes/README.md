# Recipes Folder

This folder contains **100+ frozen yogurt recipe PDFs** used in the Dark Data to Golden Data project.

## 📋 Contents

All files in this folder are recipe specification documents for various Froyo flavors.

### Example Files:
- `midnight_swirl.pdf` - Premium flavor with cocoa base
- `aura_berry_impact.pdf` - Berry blend
- `cosmic_lemon_glow.pdf` - Citrus variety
- `frozen_caramel_impact.pdf` - Caramel flavor
- `golden_coconut_impact.pdf` - Tropical blend
- `lunar_cinnamon_current.pdf` - Warm spice flavor

And many more...

## 🔍 Data Extraction

These PDFs are processed by **BigQuery Knowledge Catalog** to extract:
- Recipe name and ID
- Ingredient lists with quantities
- Allergen information
- Production procedures
- Temperature specifications
- Shelf life information

## 🔗 Relationships

Each recipe is linked to:
- **Ingredients** - via the `recipe_ingredients` table
- **Suppliers** - indirectly through ingredients
- **Allergens** - extracted from recipes and supplier data
- **Nutritional data** - when available in PDFs

## 📊 Processing

See [../details.md](../details.md) for information about how these recipes are extracted and processed into BigQuery tables.

---

For more information, see the [main README](../README.md).

