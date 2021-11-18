As you might remember from the [intro](introduction), we are attempting to
understand how lytic bacteriophages can be used as a future therapy for the
multiresistant bacteria MRSA (methicillin-resistant _Staphylococcus aureus_).
In this exercise we will use R Markdown to make a report in form of
a Supplementary Material HTML based on the outputs from the [Snakemake
tutorial](snakemake-10-generalizing-workflows). Among the benefits of having
the supplementary material (or even the full manuscript) in R Markdown format
are:

* It is fully transparent how the text, tables and figures were produced.
* If you get reviewer comments, or realize you've made a mistake somewhere, you
  can easily update your code and regenerate the document with the push of
  a button.
* By making report generation part of your workflow early on in a project, much
  of the eventual manuscript "writes itself". You no longer first have to
  finish the research part and _then_ start creating the tables and figures for
  the paper.

Before you start:

* Make sure that your working directory in R is `workshop-reproducible-research/tutorials/rmarkdown` 
  in the course directory (Session > Set Working Directory).
* Open the file `code/supplementary_material.Rmd`.

> **Note** <br>
> In this tutorial we have used Conda to install all the R packages we need,
> so that you get to practice how you can actually do this in projects of your
> own. You can, however, install things using `install.packages()` or
> `BiocManager::install()` as well, even though this makes it both less
> reproducible and more complicated in most cases.

## Overview

Let's start by taking a look at the YAML header at the top of the file. The
parameters correspond to files (and sample IDs) that are generated by the MRSA
analysis workflow (see the [Snakemake tutorial](snakemake-10-generalizing-workflows))
and contain results that we want to include in the supplementary material
document. We've also specified that we want to render to HTML.

```yaml
---
title: "Supplementary Materials"
output: html_document
params:
    counts_file: "results/tables/counts.tsv"
    multiqc_file: "intermediate/multiqc_general_stats.txt"
    summary_file: "results/tables/counts.tsv.summary"
    rulegraph_file: "results/rulegraph.png"
    SRR_IDs: "SRR935090 SRR935091 SRR935092"
    GSM_IDs: "GSM1186459 GSM1186460 GSM1186461"
---
```

* From a reproducibility perspective it definitely makes sense to include
  information about who authored the document and the date it was generated.
  Add the two lines below to the YAML header. Note that we can include inline
  R code by using `` `r some_code` ``.

```
author: John Doe, Joan Dough, Jan Doh, Dyon Do
date: "`r format(Sys.time(), '%d %B, %Y')`"
```

> **Tip** <br>
> Make it a practice to keep track of all input files and add them as
> parameters rather than hard-coding them later in the R code.

Next, take a look at the `dependencies`, `read_params`, and `read_data` chunks.
They 1) load the required packages, 2) read the parameters and store them in
R objects to be used later in the code, and 3) read the data in the counts
file, the multiqc file, as well as fetch meta data from GEO. These chunks are
provided as is, and you do not need to edit them.

Below these chunks there is some markdown text that contains the Supplementary
Methods section. Note the use of section headers using `#` and `##`. Then there
is a Supplementary Tables and Figures section. This contains four code chunks,
each for a specific table or figure. Have a quick look at the code and see if
you can figure out what it does, but don't worry if you can't understand
everything.

Finally, there is a *Reproducibility* section which describes how the results in
the report can be reproduced. The `session_info` chunk prints information
regarding R version and which packages and versions that are used. We highly
encourage you to include this chunk in all your R Markdown reports: it's an
effortless way to increase reproducibility.

## Rendering options and paths

* Now that you have had a look at the R Markdown document, it is time to Knit!
  We will do this from the R terminal (rather than pressing *Knit*).

```r
rmarkdown::render("code/supplementary_material.Rmd", output_dir = "results")
```

The reason for this is that we can then redirect the output html file to be
saved in the `results/` directory.

Normally, while rendering, R code in the Rmd file will be executed using the
directory of the Rmd file as working directory (`rmarkdown/code` in this case).
However, it is good practice to write all code as if it would be executed from
the project root directory (`rmarkdown/` in this case). For instance, you can
see that we have specified the files in `params` with relative paths from the
project root directory. To set a different directory as working directory for
all chunks one modifies the knit options like this:

```r
knitr::opts_knit$set(root.dir = '../')
```

Here we set the working directory to the parent directory of the Rmd file
(`../`), in other words, the project root. Use this rather than `setwd()` while
working with Rmd files.

* Take a look at the output. You should find the html file in the `results`
  directory.

## Formatting tables and figures

You will probably get a good idea of the contents of the file, but the tables
look weird and the figures could be better formatted. Let's start by adjusting
the figures!

* Locate the `Setup` chunk. Here, we have already set `echo = FALSE`. Let's add
  some default figure options: `fig.height = 6, fig.width = 6, fig.align
  = 'center'`. This will make the figures slightly smaller than default and
  center them.

* Knit again, using the same R command as above. Do you notice any difference?
  Better, but still not perfect!

Let's improve the tables! We have not talked about tables before. There are
several options to print tables, here we will use the `kable` function which is
part of the `knitr` package.

* Go to the `Sample info` chunk. Replace the last line, `sample_info`, with:

```r
knitr::kable(sample_info)
```

* Knit again and look at the result. You should see a formatted table.
* The column names can be improved, and we could use a table legend. Change to
  use the following:

```r
knitr::kable(sample_info, caption = "Sample info",
             col.names = c("SRR", "GEO", "Strain", "Treatment"))
```

* Knit and check the result.
* Try to fix the table in the `QC statistics` chunk in the same manner. The
  column names are fine here so no need to change them, but add a table legend:
  "QC stats from FastQC". Knit and check your results.

Let's move on to the figures!

* Go to the `Counts barplot` chunk. To add a figure legend we have to use
  a chunk option (so not in the same way as for tables). Add the chunk option:

```r
fig.cap = "Counting statistics per sample, in terms of read counts for genes
           and reads not counted for various reasons."
```

* Knit and check the outcome!
* Next, add a figure legend to the figure in the `gene-heatmap` chunk. Here we
  can try out the possibility to add R code to generate the legend:

```r
fig.cap = paste0("Expression (log-10 counts) of genes with at least ",
                 max_cutoff, " counts in one sample and a CV>", cv_cutoff, ".")
```

This will use the `cv_cutoff` and `max_cutoff` variables to ensure that the
figure legend gives the same information as was used to generate the plot. Note
that figure legends are generated *after* the corresponding code chunk is
evaluated. This means we can use objects defined in the code chunk in the
legend.

* Knit and have a look at the results.

The heatmap still looks a bit odd. Let's play with the `fig.height` and
`out.height` options, like we did above, to scale the figure in a more
appropriate way. Add this to the chunk options: `fig.height = 10,
out.height = "22cm"`. Knit and check the results. Does it look better now?

* Now let's add a third figure! This time we will not plot a figure in R, but
  use an available image file showing the structure of the Snakemake workflow
  used to generate the inputs for this report. Add a new chunk at the end of
  the Supplementary Tables and Figures section containing this code:

```r
knitr::include_graphics(rulegraph_file)
```

* Also, add the chunk options:

```r
fig.cap = "A rule graph showing the different steps of the bioinformatic
           analysis that is included in the Snakemake workflow."
```

and:

```r
out.height = "11cm"
```

* Knit and check the results.

> **Note** <br>
> It is definitely possible to render R Markdown documents as part of
> a Snakemake or Nextflow workflow. This is something we do for the final
> version of the MRSA project (in the Containers tutorial). In such cases it is
> advisable to manage the installation of R and required R packages through
> your conda environment file and use the `rmarkdown::render()` command from
> the shell section of your Snakemake rule or Nexflow process.

> **Quick recap** <br>
> In this section you learned some additional details for making nice
> R Markdown reports in a reproducible research project setting, including
> setting the root directory, adding tables as well as setting figure and
> table captions.