# ===============================================
# Fill in the following fields
# ===============================================
# Title:Exploring Political Language: Correspondence Analysis & TF-IDF on State of the Union Speeches
# Description:A Shiny app that explores the content of US Presidents' State of the Union addresses 
# using text mining techniques.
# Details: This app allows users to interactively explore SOTU speech data,visualize word frequencies, and compare linguistic patterns across presidents, years, or parties. 
# It includes plots, summaries, and searchable data tables.
# Author:Yuan Ying 
# Date:April 18, 2025


# ===============================================
# Required packages
# (you can use other packages if you want to)
# ===============================================
library(shiny)
library(tidyverse)
library(tidytext)
library(DT)
library(FactoMineR)
library(factoextra)
library(tm)

# ===============================================
# Load and process data
# ===============================================
sotu <- read_csv("sotu.csv", col_types = "cccc")
sotu_tokens <- sotu |> unnest_tokens(word, text)
clean_tokens <- sotu_tokens |> anti_join(stop_words, by = "word")

# ===============================================
# UI
# ===============================================
ui <- fluidPage(
  
  titlePanel("Exploring Political Language: Correspondence Analysis & TF-IDF on State of the Union Speeches"),
  
  fluidRow(
    column(3,
           sliderInput("year_range", "Year Range:",
                       min = min(as.numeric(sotu$year)),
                       max = max(as.numeric(sotu$year)),
                       value = c(1950, 2020),
                       step = 1)
    ),
    column(3,
           numericInput("top_n", "Top N tf-idf words to display:", value = 10, min = 5, max = 30)
    ),
    column(3,
           radioButtons("doc_type", "TF-IDF by:",
                        choices = c("President", "Party"),
                        selected = "President")
    ),
    column(3,
           checkboxInput("remove_stop", "Remove Stopwords?", value = TRUE)
    )
  ),
  
  hr(),
  
  tabsetPanel(
    tabPanel("Correspondence Analysis",
             h3("Presidents and Word Association (CA)"),
             plotOutput("plot1", height = "650px"),
             hr(),
             dataTableOutput("table1")
    ),
    tabPanel("TF-IDF",
             h3("Top tf-idf Words by Document"),
             plotOutput("plot2", height = "1000px"),
             hr(),
             dataTableOutput("table2")
    )
  )
)

# ===============================================
# Server
# ===============================================
server <- function(input, output) {
  
  # Filtered tokenized data
  filtered_tokens <- reactive({
    df <- sotu |> 
      filter(as.numeric(year) >= input$year_range[1],
             as.numeric(year) <= input$year_range[2]) |> 
      unnest_tokens(word, text)
    
    if (input$remove_stop) {
      df <- df |> anti_join(stop_words, by = "word")
    }
    df
  })
  
  # ========================
  # Tab 1 - Correspondence Analysis
  # ========================
  
  output$plot1 <- renderPlot({
    df <- filtered_tokens()
    
    # Keep top 200 words
    top_words <- df |> count(word, sort = TRUE) |> slice_head(n = 200)
    df <- df |> filter(word %in% top_words$word)
    
    dtm_data <- df |> count(name, word) |> pivot_wider(names_from = word, values_from = n, values_fill = 0)
    rownames_mat <- dtm_data$name
    dtm_matrix <- as.matrix(dtm_data[,-1])
    rownames(dtm_matrix) <- rownames_mat
    
    ca <- CA(dtm_matrix, graph = FALSE)
    
    # Prepare coordinates for ggplot
    coords_row <- as_tibble(ca$row$coord, rownames = "President")
    coords_col <- as_tibble(ca$col$coord, rownames = "Word")
    coords_row$type <- "President"
    coords_col$type <- "Word"
    names(coords_row)[2:3] <- c("Dim1", "Dim2")
    names(coords_col)[2:3] <- c("Dim1", "Dim2")
    combined <- bind_rows(coords_row, coords_col)
    
    ggplot(combined, aes(x = Dim1, y = Dim2, color = type, label = ifelse(type == "President", President, ""))) +
      geom_point(alpha = 0.7) +
      geom_text(data = filter(combined, type == "President"), vjust = -0.6, size = 3.2) +
      theme_minimal() +
      labs(title = "Correspondence Analysis (Top 200 Words)",
           x = "Dimension 1", y = "Dimension 2", color = "Type")
  })
  
  output$table1 <- renderDataTable({
    df <- filtered_tokens()
    top_words <- df |> count(word, sort = TRUE) |> slice_head(n = 200)
    
    df |> 
      filter(word %in% top_words$word) |>
      count(name, word, sort = TRUE)
  })
  
  # ========================
  # Tab 2 - TF-IDF
  # ========================
  
  tfidf_df <- reactive({
    df <- filtered_tokens()
    
    # 限制最多展示的词数（防止炸图）
    top_n <- min(input$top_n, 15)
    
    if (input$doc_type == "President") {
      grouped <- df |> count(name, word, sort = TRUE) |> bind_tf_idf(word, name, n)
    } else {
      grouped <- df |> count(party, word, sort = TRUE) |> bind_tf_idf(word, party, n)
    }
    
    grouped |> arrange(desc(tf_idf)) |> 
      group_by(if (input$doc_type == "President") name else party) |> 
      slice_head(n = top_n)
  })
  
  output$plot2 <- renderPlot({
    df <- tfidf_df()
    
    ggplot(df, aes(x = tf_idf, y = reorder(word, tf_idf))) +
      geom_col() +
      facet_wrap(as.formula(paste("~", if (input$doc_type == "President") "name" else "party")), scales = "free") +
      theme_minimal(base_size = 11) +
      theme(axis.text.y = element_text(size = 8)) +
      labs(x = "tf-idf", y = "Word", title = "Top tf-idf Words")
  })
  
  output$table2 <- renderDataTable({
    tfidf_df()
  })
}

# ===============================================
# Run the App
# ===============================================
shinyApp(ui = ui, server = server)
