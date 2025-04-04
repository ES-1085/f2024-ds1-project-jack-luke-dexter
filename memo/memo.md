Project memo
================
Luke and Jack

This document should contain a detailed account of the data clean up for
your data and the design choices you are making for your plots. For
instance you will want to document choices you’ve made that were
intentional for your graphic, e.g. color you’ve chosen for the plot.
Think of this document as a code script someone can follow to reproduce
the data cleaning steps and graphics in your handout.

``` r
library(tidyverse)
library(broom)
```

## Data Clean Up Steps for Overall Data

### Step 1: Import and Clean Data

``` r
baseball <- read.csv("../data/pitching_data_2021-2024.csv")
baseball <- baseball %>%
  mutate(pitch_clock = ifelse(year >= 2023, "Yes", "No"))

# Step 1: Keep pitchers with all 4 years of data
baseball <- baseball %>%
  group_by(player_id) %>%
  filter(n_distinct(year) == 4) %>%
  ungroup()

# Step 2: Calculate adjusted pitches and strike rate row by row
baseball_1plot <- baseball %>%
  mutate(
    adjusted_total_pitches = p_total_pitches - ifelse(is.na(p_automatic_ball), 0, p_automatic_ball),
    strike_percent = (p_total_strike / adjusted_total_pitches) * 100
  ) %>%
  filter(!is.na(pitch_clock), adjusted_total_pitches > 0)

# Step 3: Collapse to one row per pitcher per pitch_clock (pre/post)
baseball_1plot <- baseball_1plot %>%
  group_by(player_id, pitch_clock) %>%
  summarise(
    avg_strike_percent = mean(strike_percent, na.rm = TRUE),
    total_pitches = sum(adjusted_total_pitches, na.rm = TRUE),
    .groups = "drop"
  )
```

### Step 2: \_\_\_\_\_\_\_\_

## Plots

### Plot 1: \_\_\_\_\_\_\_\_\_

#### Data cleanup steps specific to plot 1

These data cleaning sections are optional and depend on if you have some
data cleaning steps specific to a particular plot

#### Final Plot 1

``` r
plot1_boxplot <- ggplot(baseball_1plot, aes(x = pitch_clock, y = avg_strike_percent, fill = pitch_clock)) +
  geom_boxplot(alpha = 0.7) +
  labs(
    title = "Strike % by Pitch Clock Era (Adjusted for Automatic Balls)",
    x = "Pitch Clock Implemented?",
    y = "Average Strike %"
  ) +
  theme_minimal()

ggsave("strike_percent_by_pitch_clock.png", plot = plot1_boxplot, width = 8, height = 6)
```

### Plot 2: \_\_\_\_\_\_\_\_\_

### Plot 3: \_\_\_\_\_\_\_\_\_\_\_

Add more plot sections as needed. Each project should have at least 3
plots, but talk to me if you have fewer than 3.

### Plot 4: \_\_\_\_\_\_\_\_\_\_\_
