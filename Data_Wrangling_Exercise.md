Data Wrangling Exerise 1
================

Basic Data Manipulation
-----------------------

By Mike Halpert
10.19.2016

Loading the gdata, stringr, tidyr packages:

    ## gdata: read.xls support for 'XLS' (Excel 97-2004) files ENABLED.

    ## 

    ## gdata: read.xls support for 'XLSX' (Excel 2007+) files ENABLED.

    ## 
    ## Attaching package: 'gdata'

    ## The following object is masked from 'package:stats':
    ## 
    ##     nobs

    ## The following object is masked from 'package:utils':
    ## 
    ##     object.size

    ## The following object is masked from 'package:base':
    ## 
    ##     startsWith

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:gdata':
    ## 
    ##     combine, first, last

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

Loading the data file(and saving original as csv):

``` r
refine_df <- read.xls("refine.xlsx", sheet = 1, stringsAsFactors = FALSE)
write.csv(refine_df, file ="refine_original.csv")
```

1: Clean up brand names

Clean up the 'company' column, so all of the misspellings of the brand names are standardized. For example, you can transform the values in the column to be: philips, akzo, van houten and unilever (all lowercase).

``` r
refine_df$company <- tolower(refine_df$company)

refine_df$company <- gsub("^a.*", "azko", refine_df$company)
refine_df$company <- gsub("^p.*|^f.*", "phillips", refine_df$company)
refine_df$company <- gsub("^v.*", "van houten", refine_df$company)
refine_df$company <- gsub("^u.*", "unilver", refine_df$company)
```

2: Separate product code and number

Separate the product code and product number into separate columns i.e. add two new columns called product\_code and product\_number, containing the product code and number respectively

``` r
refine_df_separated <- separate(refine_df, Product.code...number., c("product_code", "product_number"))
```

3: Add product categories

You learn that the product codes actually represent the following product categories: p = Smartphone v = TV x = Laptop q = Tablet In order to make the data more readable, add a column with the product category for each record.

``` r
refine_df_separated<- mutate(refine_df_separated, product_category = product_code)
```

    ## Warning: failed to assign NativeSymbolInfo for env since env is already
    ## defined in the 'lazyeval' namespace

``` r
refine_df_separated$product_category <- gsub("^p.*", "Smartphone",refine_df_separated$product_category)
refine_df_separated$product_category <- gsub("^v.*", "TV", refine_df_separated$product_category)
refine_df_separated$product_category <- gsub("^x.*", "Laptop", refine_df_separated$product_category)
refine_df_separated$product_category <- gsub("^q.*", "Tablet", refine_df_separated$product_category)
```

4: Add full address for geocoding

You'd like to view the customer information on a map. In order to do that, the addresses need to be in a form that can be easily geocoded. Create a new column full\_address that concatenates the three address fields (address, city, country), separated by commas.

``` r
refine_df_separated <- unite(refine_df_separated, full_address, address, city, country, sep = ",", remove = TRUE)
```

5: Create dummy variables for company and product category

Both the company name and product category are categorical variables i.e. they take only a fixed set of values. In order to use them in further analysis you need to create dummy variables. Create dummy binary variables for each of them with the prefix company\_ and product\_ i.e. Add four binary (1 or 0) columns for company: company\_philips, company\_akzo, company\_van\_houten and company\_unilever Add four binary (1 or 0) columns for product category: product\_smartphone, product\_tv, product\_laptop and product\_tablet

``` r
refine_df_separated$company_phillips <- as.logical("FALSE")
refine_df_separated$company_azko <- as.logical("FALSE")
refine_df_separated$company_van_houten <- as.logical("FALSE")
refine_df_separated$company_unilever <- as.logical("FALSE")
refine_df_separated$product_smartphone <- as.logical("FALSE")
refine_df_separated$product_tv <- as.logical("FALSE")
refine_df_separated$product_laptop <- as.logical("FALSE")
refine_df_separated$product_tablet <-as.logical("FALSE")
```

6: Submit the project on Github

Include your code, the original data as a CSV file refine\_original.csv, and the cleaned up data as a CSV file called refine\_clean.csv

``` r
write.csv(refine_df_separated, file ="refine_clean.csv")
```
