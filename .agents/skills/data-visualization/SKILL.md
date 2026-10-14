---
name: data-visualization
description: Create, improve, and review polished data visualizations in R using ggplot2. Use whenever writing or editing charts in R scripts or Quarto documents.
---

# R Data Visualization
Create accurate, readable, report-ready charts that make the intended comparison easy to understand. Follow the house conventions without requiring the user to repeat them. Prioritize analytical accuracy and accessibility over styling. Treat chart recommendations as defaults with justified exceptions. Do not automatically reject a chart type without considering the task.


## 1. Identify the purpose
Before writing plotting code:
- Identify the question the chart should answer.
- Determine whether the reader needs to compare amounts, rankings, distributions, relationships, changes over time, or parts of a whole.
- Identify the observational unit, variables, measurement units, and intended audience.
- Distinguish unordered categories from categories with a natural order.
- Determine whether exact values, broad patterns, individual observations, or summaries matter most.
- For proportions, identify the denominator and whether total group sizes also matter.
- Use information already provided. Ask a focused question only when missing information would materially change the chart or interpretation. 
- For exploratory work, allow patterns to emerge without assuming a conclusion. For communication, emphasize a finding only when supported by the data.


## 2. Inspect and prepare the data
Before plotting:
- Check variable types, category labels, missing values, duplicates, and group sizes.
- Verify the observational unit and level of aggregation.
- Distinguish missing observations from genuine zeros.
- Do not silently discard observations or replace missing values with zeros.
- Choose counts, sums, means, medians, or percentages according to the question.
- Avoid adding quantities that do not form a meaningful total, such as separate group medians.
- State consequential filtering, aggregation, and missing-value decisions.
- Never invent data, findings, or sources.
- Clearly identify illustrative examples.


## 3. Choose the chart by the comparison
Do not force every dataset into bars or select a chart solely because it looks attractive.

| Reader's task | Starting choice | Checks and exceptions |
|---|---|---|
| Compare amounts across categories | Bars or dots | Prefer horizontal layouts for long labels. |
| Compare small differences between values | Dot plot | A clearly labeled nonzero axis can help; do not truncate bars instead. |
| Read a few exact values | Table | A chart is unnecessary when a small table communicates more clearly. |
| Compare many values across two categorical dimensions | Heatmap | Useful for broad patterns; weaker for precise numerical comparisons. |
| Compare individual proportions | Side-by-side bars or dots | Shared positions make close values easier to compare. |
| Show a simple composition | Pie or 100% stacked bar, when justified | Use few components and a clear part-to-whole purpose. |
| Compare compositions across groups | Carefully chosen 100% stacks or facets | Interior stacked segments lack a common baseline. |
| Compare totals and components | Ordinary stacked bars, when justified | Components must add meaningfully; totals should be central to the task. |
| Show changes over time | Lines | Preserve chronological order; do not imply continuity where inappropriate. |
| Show a numerical distribution | Histogram, density plot, or boxplot | Choose according to whether shape, spread, or summary is most important. |
| Compare two numerical variables | Scatterplot | Inspect overplotting and avoid implying causation. |
| Compare relationships within groups | Faceted plots | Choose scales according to the intended comparison. |


## 4. Handle proportions and stacking carefully
### Pie and donut charts
- Avoid pies when many slices or similar-sized values require precise comparisons.
- Consider pies for few components, simple fractions, or meaningful combinations of parts.
- Do not default to multiple pies for comparing small changes across groups or time.
- Do not claim that donuts are inherently less accurate than pies.
- Do not claim that square pies are universally superior. The cited study tested specific designs and proportion-estimation tasks, including square pies without grids and stacked bars.
- Do not generalize results about stacked bars to ordinary bars sharing a zero baseline.

### Stacked charts
- Stack only quantities that form a meaningful whole.
- Recognize that interior segments are difficult to compare because both boundaries can move.
- Two-component 100% stacks are often easier to compare than stacks with many components.
- Place the focal component or combined group against a shared baseline when this preserves meaningful category order.
- Prefer lines or separate panels when comparing each component's trajectory over time.
- Explain that 100% stacks conceal differences in absolute totals.
- Consider showing each component separately against a shared representation of the whole when both context and comparison matter.

### Likert responses
- Preserve response order.
- Consider diverging bars when the main question concerns agreement versus disagreement.
- Make the treatment of neutral responses explicit.
- Do not let split neutral responses imply greater agreement or disagreement.
- Use separate aligned bars or panels when every response category needs precise comparison.
- Add reference lines, such as 50%, only when they support the question.


## 5. Design for better perception
### Direct attention
- Use visual emphasis selectively.
- Highlight the focal information and keep context quieter.
- Avoid giving every element equal emphasis.
- Reduce repeated legend lookup and unnecessary mental calculations.

### Group elements meaningfully
- Place related elements near each other.
- Use consistent appearance for related categories.
- Use connections only when they represent meaningful relationships.
- Avoid spacing, colors, or lines that imply relationships absent from the data.
- Prefer orderly layouts that are easy to scan.

### Remove distractions
- Remove decorative 3D effects, unnecessary backgrounds, heavy borders, and other chart junk.
- Avoid pattern fills by default.
- Retain useful gridlines and reference marks.
- Do not remove necessary context merely to make the chart look minimal.


## 6. Apply the house style
Follow these conventions whenever writing or editing ggplot2 code.

### Setup
- Load `tidyverse` and `cowplot`.
- Use the native pipe `|>`.
- Identify missing dependencies instead of silently installing them.

Define fonts, colors, long titles, captions, and output dimensions near the top:
```r
library(tidyverse)
library(cowplot)

font <- "serif"
```

### Fonts
Define the font once and apply it consistently:
- Use `"serif"` as the default. 
- Cowplot themes: `font_family = font`.
- Text and label geoms: `family = font`.
- Repelled labels: `family = font`.

### Cowplot themes
Use cowplot minimal themes instead of `theme_gray()` or a bare `theme_minimal()`.

| Plot structure | Default theme |
|---|---|
| Vertical bars or columns | `theme_minimal_hgrid()` |
| Horizontal bars or categorical dot/error-bar plots | `theme_minimal_vgrid()` |
| Scatterplots or line charts | `theme_minimal_grid()` |
| Faceted charts | Match the underlying geometry and useful grid direction |

Pass the shared font and an appropriate size:
```r
theme_minimal_hgrid(
  font_family = font,
  font_size = 14
)
```

### Standard theme additions
Use solid white backgrounds and align titles and captions with the plot:
```r
theme(
  plot.title.position = "plot",
  plot.caption.position = "plot",
  plot.caption = element_text(hjust = 0, face = "italic"),
  panel.background = element_rect(fill = "white", color = NA),
  plot.background = element_rect(fill = "white", color = NA),
  panel.grid.minor = element_blank()
)
```

For faceted charts, add readable strips and subtle borders:
```r
theme(
  strip.background = element_rect(fill = "grey80", color = NA),
  panel.grid.minor = element_blank()
) +
  cowplot::panel_border(color = "grey70", size = 0.4)
```

### Direct labels
Prefer direct labels when they remain clear and uncrowded.

For line charts:
- Label each series at its last available nonmissing observation.
- Use `ggrepel::geom_text_repel()` when labels would overlap.
- Make room with axis expansion and plot margins.
- Match the scale function to the axis type: continuous, date, or discrete.

For justified stacked charts:
- Place labels at segment midpoints.
- Match the actual stack order and normalization.
- If labeling beside the final stack, ensure those labels identify all relevant categories.


### Colors
Define a named `plotColors` vector. Names must match the categories mapped to color or fill:
```r
plotColors <- c(
  focus = "#0072B2",
  comparison = "#D55E00",
  context = "grey70"
)
```

Apply mappings directly:
```r
scale_color_manual(values = plotColors)
scale_fill_manual(values = plotColors)
```

Available house swatches. Treat these as individual swatches, not an automatically accessible palette:
- Green: `#8FC977FF`
- Blue: `#80C5DCFF`
- Yellow: `#E8BF4DFF`
- Orange: `#E37D39FF`
- Red: `#980000FF`
- Gray: `grey70`

Further instructions:
- Do not remove names with `as.vector()`. Names preserve the intended category-to-color mapping.
- Avoid relying on red–green distinctions.
- Check contrast, especially for pale colors on white.
- Use dark label text when the series color is too light.
- Use gray to de-emphasize context and a stronger color for the focal series.
- Keep category mappings consistent across related figures.
- Reinforce color with labels, position, shape, or facets when needed.
- Use qualitative palettes for unordered categories.
- Use sequential palettes for numerical magnitudes.
- Use diverging palettes only around a meaningful midpoint.

### Scales and axes
Choose readable breaks based on the data range and output size. Do not hardcode irrelevant years or crowd the axis.

For nonnegative vertical bars:

```r
scale_y_continuous(
  expand = expansion(mult = c(0, 0.05))
)
```

For horizontal bars, apply the equivalent to `scale_x_continuous()`.

Handle negative or mixed-sign values separately while preserving the zero baseline.

Use readable formatting:

```r
scales::label_comma()
scales::label_dollar()
scales::label_percent()
```

`label_percent()` assumes proportions between 0 and 1. For percentages already expressed from 0 to 100, use:

```r
scales::label_percent(scale = 1)
```

Use `clip = "off"` only when annotations need to extend outside the panel, and provide sufficient margins.

Remove redundant axis titles, but retain necessary units and context.

### Titles and annotations
Set `labs()` explicitly:
- `x` and `y`: informative labels, or `NULL` when redundant.
- `title`: the subject or a supported finding.
- `subtitle`: relevant context or a supported takeaway when useful.
- `caption`: the actual data source and consequential notes.
- Do not invent sources or conclusions. 
- Avoid repeating the same sentence in the title and subtitle.

### Starting dimensions:
Adjust dimensions to label length, panel count, and destination. Use vector output when appropriate:
- Single chart: approximately 7 × 5 to 9 × 6.5 inches.
- Wide faceted row: approximately 11–12 × 3.5–5.5 inches.


## 7. Deliver the result
Provide:
- The chart or requested R code.
- A brief explanation of the chart choice and major design decisions.
- Exported files when requested or needed.
- When improving an existing chart, preserve its analytical purpose unless the user asks to change it. 
- Explain changes that materially affect interpretation.

## 8. Mandatory scope: make the requested chart only
These rules override workflow, saving, rendering, and delivery instructions in this skill.

- Make only the chart the user requested.
- By default, return ggplot2 code in chat. Do not create or modify files.
- If the user explicitly asks to edit an existing R script or Quarto document, change only the relevant chart code in that file. Preserve all unrelated content.
- Display the chart through the normal R plot viewer or existing notebook output.
- Do not create folders, additional scripts, documents, reports, previews, screenshots, logs, or supporting files.
- Do not export PNG, PDF, SVG, HTML, or other files unless the user explicitly requests an export.
- Do not run `ggsave()`, `dir.create()`, `pdf()`, `png()`, or other file-writing functions unless required by that explicit export request.
- Do not render, knit, or publish an entire Quarto or R Markdown document merely to make or check a chart.
- Do not install packages, modify project settings, or change unrelated files.
- Check the code and, when available without creating files, inspect the chart in the normal plot viewer. Do not create verification artifacts.
- If the user requests an export, create only the requested file in the requested format and location. Do not create additional versions.
- If the user specifies a PDF export, then and only then export a PDF.
- Finish with the requested chart code or a brief description of the authorized chart edit. Do not generate a separate report.

## 8. Complete example
Use this as a house-style reference, not as a template to apply blindly. The example uses built-in data and highlights one category. Axis labels identify the categories, so a fill legend is unnecessary.

```r
library(tidyverse)
library(cowplot)

font <- "serif"

plotColors <- c(
  focus = "#0072B2",
  other = "grey70"
)

chart_title <- "Four-cylinder cars are the largest group in this sample"
chart_subtitle <- "Counts of cars in the built-in mtcars dataset"
chart_caption <- "Source: datasets::mtcars; this sample is not market-wide."

plot_data <- mtcars |>
  count(cyl, name = "cars") |>
  mutate(
    cylinders = factor(
      cyl,
      levels = c(4, 6, 8),
      labels = c("4 cylinders", "6 cylinders", "8 cylinders")
    ),
    emphasis = if_else(cyl == 4, "focus", "other")
  )

plot <- ggplot(
  plot_data,
  aes(x = cylinders, y = cars, fill = emphasis)
) +
  geom_col(width = 0.7) +
  geom_text(
    aes(label = cars),
    family = font,
    vjust = -0.5,
    color = "grey20",
    show.legend = FALSE
  ) +
  scale_fill_manual(values = plotColors) +
  scale_y_continuous(
    breaks = seq(0, 16, 4),
    expand = expansion(mult = c(0, 0.12))
  ) +
  labs(
    x = NULL,
    y = "Number of cars",
    title = chart_title,
    subtitle = chart_subtitle,
    caption = chart_caption
  ) +
  theme_minimal_hgrid(
    font_family = font,
    font_size = 14
  ) +
  theme(
    plot.title.position = "plot",
    plot.caption.position = "plot",
    plot.caption = element_text(hjust = 0, face = "italic"),
    legend.position = "none",
    panel.background = element_rect(fill = "white", color = NA),
    plot.background = element_rect(fill = "white", color = NA),
    panel.grid.minor = element_blank()
  )

plot
```