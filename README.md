# Exploring Political Language: Correspondence Analysis & TF-IDF on State of the Union Speeches

## Description
An interactive Shiny application for exploring linguistic patterns in **U.S. Presidents’ State of the Union (SOTU)** speeches using modern text-mining techniques.
Users can analyze speeches by **president, year, or party**, visualizing word usage, tf-idf importance, and correspondence analysis relationships between presidents and vocabulary.

**Live App:** yuanying.shinyapps.io/shinyapp2/

<img src="https://github.com/YennyYing/stat133_shinyapp2/blob/main/epl1.jpg">
<img src="https://github.com/YennyYing/stat133_shinyapp2/blob/main/epl2.jpg">

## Author
Yuan Ying  

## Date
05/02/2025  

---

## How It's Made:

**Tech used:** R, Shiny, tidyverse, tidytext, FactoMineR, factoextra, tm, DT

This project analyzes State of the Union speeches by tokenizing text, removing stopwords, and constructing word-frequency tables.  

Two major analytical components are implemented:

### 1. Correspondence Analysis (CA)
- Builds a document–term matrix for **presidents × words**
- Performs CA using `FactoMineR::CA()`
- Visualizes relationships between presidents and high-frequency vocabulary
- Helps identify presidents who use similar linguistic patterns

### 2. TF-IDF Analysis
- Computes tf-idf scores by **president** or by **party**
- Displays the top N tf-idf words per group
- Uses faceted bar charts for easy comparison across documents
- Allows customization of:
  - Number of words shown  
  - Whether stopwords are removed  
  - Year range filter  

### 3. Interactive Filtering and Tables
- User controls include:
  - Year slider  
  - Stopword toggle  
  - tf-idf grouping (president/party)  
  - Adjustable top-N word display  
- Data tables are rendered using `DT` for sorting and searching

## Features
- Dynamic text tokenization and filtering
- Correspondence Analysis plots for semantic comparison
- TF-IDF visualization with faceted comparisons
- Interactive, searchable data tables of term frequencies
- Clean UI with reactive server logic
- Fully reproducible text-mining pipeline

---

## Packages
```r
library(shiny)
library(tidyverse)
library(tidytext)
library(DT)
library(FactoMineR)
library(factoextra)
library(tm)
```

## Lessons Learned:
Throughout this project, I gained experience in:
- Processing text data using `tidytext`
- Constructing tidy token datasets suitable for CA and tf-idf
- Using `FactoMineR` to analyze word-president associations
- Designing interactive Shiny interfaces for text exploration
- Managing performance when working with large text corpora
- Communicating analytical results through intuitive visualizations

This application demonstrates the power of combining statistics, linguistics, and interactivity to reveal meaningful structure in political speech data.

