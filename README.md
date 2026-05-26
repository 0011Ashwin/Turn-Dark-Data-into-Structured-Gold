![Dark Data to Golden Data](dark%20data%20into%20structure%20golden%20data.png)

# Dark Data to Golden Data: Unlocking PDFs with BigQuery Knowledge Catalog

## Overview

We all know the pain of "dark data." It's the PDFs, images, and text files sitting in cloud storage buckets, completely invisible to your SQL queries and BI dashboards. Traditionally, unlocking this data required complex OCR pipelines, manual data entry, or fragile custom scripts.

**Not anymore.**

This project demonstrates how to convert **400+ unstructured PDF files** — spanning text, tables, and images — into cleanly structured **BigQuery tables with relationships automatically inferred** between them. And you can do it in **minutes** using **BigQuery Knowledge Catalog** and **Dataplex**.

---

## 📊 Project Context: Froyo Franchise

To make this real, imagine you manage the data for a **fast-growing Frozen Yogurt (Froyo) franchise**.

You have:
- **Hundreds of recipes** - Each frozen yogurt flavor with detailed ingredients and specifications
- **Supplier spec sheets** - Technical documentation for each ingredient from multiple suppliers
- **Complex relationships** - Links between recipes, ingredients, and allergen information

### The Problem
A customer asks: *"I'm really interested in your **Midnight Swirl froyo**. Are there any allergens in it?"*

Traditionally, your system would have to:
1. Find the "Midnight Swirl" recipe PDF
2. Read the ingredients (e.g., "Cocoa Powder", "Dairy Base", "Emulsifier X")
3. Search through dozens of Supplier PDFs to find spec sheets for those specific ingredients
4. Check the supplier sheets for hidden allergens tied to those ingredients

Trying to build an AI agent that does this on the fly by reading 400 raw PDFs at runtime is **slow, expensive, and prone to hallucination**.

### The Solution
Using **semantic inference**, we extract all of this into a **relational database first**, making our future AI agent:
- ⚡ Lightning-fast
- 🎯 100% grounded in factual SQL data
- 🔗 Relationship-aware (recipes → ingredients → suppliers → allergens)

---

## 🎯 What You'll Build

A complete data pipeline that:

1. **Ingests** 400+ PDF files from Cloud Storage
2. **Extracts** structured data using BigQuery Knowledge Catalog's semantic inference
3. **Infers** relationships between recipes, ingredients, suppliers, and allergens
4. **Stores** results in BigQuery as relational tables
5. **Powers** AI agents with clean, queryable data

---

## 📚 What You'll Learn

- ✅ How to set up a Cloud Storage bucket for PDF source files
- ✅ How to configure and run Datascan jobs with semantic inference in Knowledge Catalog
- ✅ How to extract structured data from unstructured PDFs automatically
- ✅ How to infer relationships and context between documents
- ✅ How to store results in BigQuery for relational queries
- ✅ How to use BigQuery Agents to chat with your newly created dataset

---

## 📋 Project Structure

```
d:\dark-data/
├── README.md                                  # Main project documentation
├── details.md                                 # Detailed project information
├── copilot-instructions.md                    # Project rules and execution guidelines
├── quickstart.py                              # Python script for BigLake registration
├── template.yaml                              # Dataproc Serverless session template
├── LICENSE.txt                                # Apache 2.0 License
├── parquet.zip                                # Parquet files for customer data
├── recipes/                                   # 100+ Froyo recipe PDFs
│   └── *.pdf                                  # Individual recipe documents
└── suppliers/                                 # 100+ Supplier specification PDFs
    └── *_manual.pdf                          # Supplier technical documentation
```

---

## 📁 Key Folders

### `recipes/` - Froyo Recipe PDFs
Contains **100+ recipe PDFs** with detailed information about frozen yogurt flavors:
- Ingredient lists
- Flavor profiles
- Production specifications
- Allergen information
- Nutritional data

Example recipes:
- `midnight_swirl.pdf` - The featured example in customer queries
- `aura_berry_impact.pdf`
- `cosmic_lemon_glow.pdf`
- And many more...

### `suppliers/` - Supplier Specification Sheets
Contains **100+ supplier manual PDFs** with technical specifications for ingredients:
- Ingredient sources
- Allergen data
- Safety information
- Processing details
- Compliance certifications

Example suppliers:
- `cyber-flux_197_manual.pdf`
- `hyper-synthesizer_501_manual.pdf`
- `krypto-stabilizer_352_manual.pdf`
- And many more...

---

## 🚀 Getting Started

### Prerequisites
- A Google Cloud project with billing enabled
- Basic familiarity with SQL and Java
- Browser (Chrome or Firefox)

### Next Steps
1. Review [details.md](details.md) for complete project architecture
2. Check [copilot-instructions.md](copilot-instructions.md) for critical execution rules
3. Run `quickstart.py` to register your data with BigLake
4. Use the provided Spark notebook template for advanced analytics

---

## 🔧 Technology Stack

- **Google Cloud Storage** - PDF document storage
- **BigQuery Knowledge Catalog** - Semantic data extraction
- **Dataplex** - Data discovery and governance
- **BigQuery** - Relational data warehouse
- **BigLake** - Open table formats (Iceberg)
- **Dataproc Serverless** - Spark notebook execution
- **BigQuery Agents** - AI-powered chat interface

---

## 📖 Documentation

- **[details.md](details.md)** - Complete project architecture and configuration
- **[copilot-instructions.md](copilot-instructions.md)** - Critical execution rules and best practices
- **[TROUBLESHOOTER.md](TROUBLESHOOTER.md)** - Common issues and solutions
- **[PROJECT_PIONEER_CHALLENGE.md](PROJECT_PIONEER_CHALLENGE.md)** - Advanced challenge scenarios
- **[template.yaml](template.yaml)** - Dataproc Serverless session configuration
- **[quickstart.py](quickstart.py)** - BigLake table registration script

---

## 🏆 Code Vipassana Challenge Submission

**Participating in Code Vipassana?** This project is designed for the **Dark Data to Agent Chat** codelab challenge!

### Submission Resources:
- **[CV_QUICK_ACTION_PLAN.md](CV_QUICK_ACTION_PLAN.md)** - Start here! Quick steps to complete your submission
- **[CODE_VIPASSANA_SUBMISSION_GUIDE.md](CODE_VIPASSANA_SUBMISSION_GUIDE.md)** - Comprehensive submission guide
- **[CV_SUBMISSION_TEMPLATES.md](CV_SUBMISSION_TEMPLATES.md)** - Sample templates for your submission

### Challenge Options:
- **Troubleshooter Challenge** - Solve real-world data extraction problems (4-6 hours)
- **Project Pioneer Challenge** - Build complete system across 4 levels (6-48 hours)

### Codelab:
**[Dark Data to Agent Chat](https://codelabs.developers.google.com/dark-data-agent-chat#0)**

### Quick Start for CV Submission:
1. 📧 Find your Code Vipassana credentials in your email
2. 📋 Review [CV_QUICK_ACTION_PLAN.md](CV_QUICK_ACTION_PLAN.md) (5 min read)
3. 🎯 Choose your challenge (Troubleshooter or Pioneer)
4. ✅ Complete your chosen challenge
5. 📝 Use templates from [CV_SUBMISSION_TEMPLATES.md](CV_SUBMISSION_TEMPLATES.md)
6. 🚀 Submit your work

---

## 📝 License

Licensed under the Apache License, Version 2.0. See [LICENSE.txt](LICENSE.txt) for details.

---

## 🤝 Support & Resources

- **Documentation**: See links above for complete guides
- **Troubleshooting**: Check [TROUBLESHOOTER.md](TROUBLESHOOTER.md) for solutions
- **Advanced Learning**: Try the [PROJECT_PIONEER_CHALLENGE.md](PROJECT_PIONEER_CHALLENGE.md)

---

**Transform your dark data into golden insights. Start building now! ✨**
