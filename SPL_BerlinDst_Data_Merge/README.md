Name of Quantlet: SPL_BerlinDst_Data_Merge

Published in: 'Statistical Programming Languages - Student Project on ''Livability of Berlin Districts: Comparative Analysis'' '

Description: Merges tables from quantlets 1-3 and produces basic data statistics

Keywords: data preparation, merge, data description, aggregation 

Author: Aleksandra Kudaeva

See also: other Quantlets in this project

Submitted: 15.03.2019

Datafile: SPL_BerlinDst_Data_Prep_1.csv, SPL_BerlinDst_Data_Prep_2.csv, SPL_BerlinDst_Data_Prep_3.csv

Output:  
- SPL_BerlinDst_Data_Merge.csv

## R Code 
```
#=======================LOAD PACKAGES===========================================
# Install packages if not installed before
if(!require("pastecs")){install.packages("pastecs")}
if(!require("dplyr")){install.packages("dplyr")}

# Load libraries
library(pastecs)
library(dplyr)

#======================RUN HELPFULL FUNCTIONS ==================================
# Use function from Malgorzata Olesiewitz
DistricToFullName = function (column){
    # Author: Malgorzata Olesiewitz
    # Function re-names the districts of Berlin with their full names 
    # without special signs. The function identifies the district by 
    # three or four letters of its name. 
    #
    # Args:
    #      column: vector or a column containing names of Berlin
    #      districts.Name can have any form, special signs can be
    #      use and additional information can be added to it. Vector 
    #      must be character type.Only first 3-4letters need to be correct.  
    #      
    # Returns: 
    #        Vector of replaced district names by its official names  
    #        without special signs.  
    
    column[grepl("mit", column, 
                 ignore.case = TRUE)] = "Mitte" 
    column[grepl("fri", column, 
                 ignore.case = TRUE)] = "Friedrichshain-Kreuzberg"
    column[grepl("pank", column,
                 ignore.case = TRUE)] = "Pankow"
    column[grepl("mar", column,
                 ignore.case = TRUE)] = "Marzahn-Hellersdorf" 
    column[grepl("char", column,
                 ignore.case = TRUE)] = "Charlottenburg-Wilmersdorf"
    column[grepl("spa", column,
                 ignore.case = TRUE)] = "Spandau" 
    column[grepl("ste", column,
                 ignore.case = TRUE)] = "Steglitz-Zehlendorf"  
    column[grepl("tem", column,
                 ignore.case = TRUE)] = "Tempelhof-Schoneberg" 
    column[grepl("trep", column,
                 ignore.case = TRUE)] = "Treptow-Kopenick" 
    column[grepl("neu", column,
                 ignore.case = TRUE)] = "Neukolln" 
    column[grepl("lich", column,
                 ignore.case = TRUE)] = "Lichtenberg" 
    column[grepl("rein", column,
                 ignore.case = TRUE)] = "Reinickendorf"
    
    return(column)
}

#================ READING IN ALL PREPARED DATA SETS ============================
# Working directory is a project directory (if not automatic) 
# setwd("")

# Read data from the previous quantlets
dt1 = read.csv2("./SPL_BerlinDst_Data_Merge/SPL_BerlinDst_Data_Prep_1.csv",
                sep = ";",
                dec = ",", 
                row.names = 1,
                stringsAsFactors = FALSE)

dt2 = read.csv2("./SPL_BerlinDst_Data_Merge/SPL_BerlinDst_Data_Prep_2.csv", 
                sep = ",",
                dec = ".",
                row.names = 1,
                stringsAsFactors = FALSE)

dt3 = read.csv2("./SPL_BerlinDst_Data_Merge/SPL_BerlinDst_Data_Prep_3.csv", 
                sep = ";",
                dec = ",", 
                stringsAsFactors = FALSE)

#==================== MERGE THE DATA SETS ======================================
# Change districts name to the standard writing
dt2$District = DistricToFullName(dt2$District)
dt3$District = DistricToFullName(dt3$District)

# Merge loaded data sets
lvbInDt = merge(dt1, 
                dt2[,-1],  #delete district number in the first column
                by = "District") %>%
    merge(dt3, by = "District")

# Save file to csv

write.csv2(lvbInDt, "./SPL_BerlinDst_Data_Merge/SPL_BerlinDst_Data_Merge.csv")

#==================== DATA ANALYSIS ============================================
# Produce main statistics of the data

stats = lvbInDt %>% 
    select(-c(District, Nr)) %>%
    stat.desc()  #aggregated statistics

```
