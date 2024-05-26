
<!-- README.md is generated from README.Rmd. Please edit that file -->

# TolRad

<!-- badges: start -->
<!-- badges: end -->

TolRad is a Random Forest Binary Classifier that uses the abundance of
Pfam domains in a bacterial genome to differentiate radiosenstive
species (D10 \< 200Gy) from radiation tolerant species (D10 \> 200Gy).
As detailed in <https://doi.org/10.1101/2023.11.02.562514>, TolRad was
build using Pfam annotations acquired from reference genomes hosted on
UniProt. The accuracy of classifications made on Pfam annotations
assigned using the EggNogMapper (5.0) pipeline was validated. In this
README, examples of how to read in different source files into TolRad
are provided, as well as basic graphical.

Example 1-3 deal with running TolRad over a collection of genome
annotations files in user specified folder

Example 4 deals with running TolRad over annotation data in existing
data frames

## Installation

You can install the development version of TolRad like so:

``` r

library(devtools)
#> Warning: package 'devtools' was built under R version 4.1.2
#> Loading required package: usethis

## or you can download the zip from GitHub and manually install 

# install.packages("~/path/to/download/TolRad_0.1.0.tar.gz", repos = NULL, type = "source")

library(TolRad)
```

## Examples of TolRad Input Files

### Example 1/2: Running TolRad on UniProt data

Bacterial Pfam annotations can be downloaded from UniProt
(<https://www.uniprot.org/>) by searching for a species by name and
selecting Pfam from the “External Resources” tab. Including the “Entry”
and “Organism” column and downlaoding the results are in excel or tsv
format will allow following script block can be used to process the
annotations and produce a TolRad classification.

``` r

library(TolRad)
library(tidyverse)
#> Warning: package 'tidyverse' was built under R version 4.1.2
#> ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
#> ✔ ggplot2 3.3.6      ✔ purrr   0.3.4 
#> ✔ tibble  3.1.8      ✔ dplyr   1.0.10
#> ✔ tidyr   1.2.1      ✔ stringr 1.4.1 
#> ✔ readr   2.1.3      ✔ forcats 0.5.2
#> Warning: package 'ggplot2' was built under R version 4.1.2
#> Warning: package 'tibble' was built under R version 4.1.2
#> Warning: package 'tidyr' was built under R version 4.1.2
#> Warning: package 'readr' was built under R version 4.1.2
#> Warning: package 'dplyr' was built under R version 4.1.2
#> Warning: package 'stringr' was built under R version 4.1.2
#> Warning: package 'forcats' was built under R version 4.1.2
#> ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
#> ✖ dplyr::filter() masks stats::filter()
#> ✖ dplyr::lag()    masks stats::lag()

test_files <- list.files(path = c("inst/extdata/Example1"),
                         pattern = "*.xlsx",
                       full.names = TRUE)

head(readxl::read_excel(test_files[1]))
#> # A tibble: 6 × 3
#>   Entry  Organism            Pfam                    
#>   <chr>  <chr>               <chr>                   
#> 1 O34162 Cupriavidus necator PF06969;PF04055;        
#> 2 P14611 Cupriavidus necator PF02803;PF00108;        
#> 3 P14697 Cupriavidus necator PF00106;                
#> 4 P31892 Cupriavidus necator PF14720;PF01058;        
#> 5 P39185 Cupriavidus necator PF04879;PF00384;PF01568;
#> 6 P39662 Cupriavidus necator PF00970;PF00042;PF00175;

 TolRad_Predict(path = test_files,
             file_type = "excel")
#> # A tibble: 3 × 3
#>   organismID               Radiosensitive n_zero
#>   <chr>                    <chr>           <dbl>
#> 1 Cupriavidus necator      Yes                 0
#> 2 Geobacter sulfurreducens No                  0
#> 3 Mycoplasma mycoides      No                  3
```

``` r
test_files <- list.files(path = c("inst/extdata/Example2"),
                         pattern = "*.tsv",
                       full.names = TRUE)

head(readr::read_tsv(test_files[1]))
#> Rows: 6614 Columns: 5
#> ── Column specification ────────────────────────────────────────────────────────
#> Delimiter: "\t"
#> chr (5): Entry, Entry_Name, Protein names, Organism, Pfam
#> 
#> ℹ Use `spec()` to retrieve the full column specification for this data.
#> ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
#> # A tibble: 6 × 5
#>   Entry  Entry_Name `Protein names`                                Organ…¹ Pfam 
#>   <chr>  <chr>      <chr>                                          <chr>   <chr>
#> 1 O34162 HEMN_CUPNH Oxygen-independent coproporphyrinogen III oxi… Cupria… PF06…
#> 2 P14611 THIL_CUPNH Acetyl-CoA acetyltransferase (EC 2.3.1.9) (Ac… Cupria… PF02…
#> 3 P14697 PHAB_CUPNH Acetoacetyl-CoA reductase (EC 1.1.1.36)        Cupria… PF00…
#> 4 P31892 MBHS_CUPNH Uptake hydrogenase small subunit (EC 1.12.99.… Cupria… PF14…
#> 5 P39185 NAPA_CUPNH Periplasmic nitrate reductase (EC 1.9.6.1)     Cupria… PF04…
#> 6 P39662 HMP_CUPNH  Flavohemoprotein (FHP) (Flavohemoglobin) (Hem… Cupria… PF00…
#> # … with abbreviated variable name ¹​Organism

TolRad_Predict(path = test_files,
         file_type = "tsv")
#> Rows: 6614 Columns: 5
#> ── Column specification ────────────────────────────────────────────────────────
#> Delimiter: "\t"
#> chr (5): Entry, Entry_Name, Protein names, Organism, Pfam
#> 
#> ℹ Use `spec()` to retrieve the full column specification for this data.
#> ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
#> # A tibble: 1 × 3
#>   organismID                                                      Radio…¹ n_zero
#>   <chr>                                                           <chr>    <dbl>
#> 1 Cupriavidus necator (strain ATCC 17699 / DSM 428 / KCTC 22496 … Yes          0
#> # … with abbreviated variable name ¹​Radiosensitive
```

### Example 3: Running TolRad on EggNogMapper Outputs

Reference genome from NCBI downloaded. Zipped with gzip. Uplaoded to
EggNog Mapper settings

–evalue 0.001 –score 60 –pident 40 –query_cover 20 –subject_cover 20
–itype genome –genepred prodigal –tax_scope auto –target_orthologs all
–go_evidence non-electronic –pfam_realign realign –num_servers 2
–report_orthologs –decorate_gff yes –excel

``` r

enm_files <- list.files(path = c("inst/extdata/Example3"),
                         pattern = "*.tsv",
                         full.names = TRUE)

head(readr::read_tsv(enm_files[1], col_names = TRUE, id="path", skip = 4))
#> Rows: 3191 Columns: 4
#> ── Column specification ────────────────────────────────────────────────────────
#> Delimiter: "\t"
#> chr (3): #query, seed_ortholog, PFAMs
#> 
#> ℹ Use `spec()` to retrieve the full column specification for this data.
#> ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
#> # A tibble: 6 × 4
#>   path                                      `#query`     seed_ortholog    PFAMs 
#>   <chr>                                     <chr>        <chr>            <chr> 
#> 1 inst/extdata/Example3/GCA_000007985.2.tsv AE017180.2_1 243231.GSU0000.1 Bac_D…
#> 2 inst/extdata/Example3/GCA_000007985.2.tsv AE017180.2_2 243231.GSU0001   DNA_p…
#> 3 inst/extdata/Example3/GCA_000007985.2.tsv AE017180.2_3 243231.GSU0002   SMC_N 
#> 4 inst/extdata/Example3/GCA_000007985.2.tsv AE017180.2_4 243231.GSU0003   DNA_g…
#> 5 inst/extdata/Example3/GCA_000007985.2.tsv AE017180.2_5 243231.GSU0004   DNA_g…
#> 6 inst/extdata/Example3/GCA_000007985.2.tsv AE017180.2_6 243231.GSU0005   -
```

columns needed for TolRad… organimsID = path, file_type = “.tsv”, pfamID
= “PFAMs”, pfam_term = “S2”, geneID = “seed_ortholog”

``` r

TolRad_Predict(path = enm_files, 
               organismID = "path", 
               file_type = "tsv_eg", 
               pfamID = "PFAMs", 
               pfam_term = "S2", 
               geneID = "seed_ortholog") -> enm_example

read.csv("inst/extdata/Example3/Example3_Key.csv") -> enm_key
#> Warning in read.table(file = file, header = header, sep = sep, quote = quote, :
#> incomplete final line found by readTableHeader on 'inst/extdata/Example3/
#> Example3_Key.csv'

enm_example %>%
  mutate(organismID = basename(organismID)) %>%
  mutate(organismID = str_remove(organismID, ".tsv")) %>%
  left_join(enm_key, by = c("organismID"= "GenomeID"))
#> # A tibble: 3 × 5
#>   organismID      Radiosensitive n_zero Species                  Phylum         
#>   <chr>           <chr>           <dbl> <chr>                    <chr>          
#> 1 GCA_000007985.2 No                  0 Geobacter sulfurreducens "Thermodesulfo…
#> 2 GCA_000023685.1 No                  3 Mycoplasma mycoides      "Mycoplasmatot…
#> 3 GCA_004798725.1 Yes                 0 Cupriavidus necator      "Pseudomonadot…
```

### Example 4 Filtering Metagenomic Data

``` r

test_data <- readr::read_tsv("inst/extdata/Example2/UP000008210_2023_12_21.tsv")
#> Rows: 6614 Columns: 5
#> ── Column specification ────────────────────────────────────────────────────────
#> Delimiter: "\t"
#> chr (5): Entry, Entry_Name, Protein names, Organism, Pfam
#> 
#> ℹ Use `spec()` to retrieve the full column specification for this data.
#> ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

head(test_data)
#> # A tibble: 6 × 5
#>   Entry  Entry_Name `Protein names`                                Organ…¹ Pfam 
#>   <chr>  <chr>      <chr>                                          <chr>   <chr>
#> 1 O34162 HEMN_CUPNH Oxygen-independent coproporphyrinogen III oxi… Cupria… PF06…
#> 2 P14611 THIL_CUPNH Acetyl-CoA acetyltransferase (EC 2.3.1.9) (Ac… Cupria… PF02…
#> 3 P14697 PHAB_CUPNH Acetoacetyl-CoA reductase (EC 1.1.1.36)        Cupria… PF00…
#> 4 P31892 MBHS_CUPNH Uptake hydrogenase small subunit (EC 1.12.99.… Cupria… PF14…
#> 5 P39185 NAPA_CUPNH Periplasmic nitrate reductase (EC 1.9.6.1)     Cupria… PF04…
#> 6 P39662 HMP_CUPNH  Flavohemoprotein (FHP) (Flavohemoglobin) (Hem… Cupria… PF00…
#> # … with abbreviated variable name ¹​Organism

TolRad_manual(df= test_data)
#> # A tibble: 1 × 3
#>   organismID                                                      Radio…¹ n_zero
#>   <chr>                                                           <chr>    <dbl>
#> 1 Cupriavidus necator (strain ATCC 17699 / DSM 428 / KCTC 22496 … Yes          0
#> # … with abbreviated variable name ¹​Radiosensitive
```

## Philip Sweet 2024
