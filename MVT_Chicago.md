# MVT-Chicago: <Predicted attribute> <regression/classification>
bdanalytics  

**  **    
**Date: (Tue) Mar 10, 2015**    

# Introduction:  

Data: 
Source: 
    Training:   https://courses.edx.org/c4x/MITx/15.071x_2/asset/mvtWeek1.csv  
    New:        <prdct_url>  
Time period: 



# Synopsis:

Based on analysis utilizing <> techniques, <conclusion heading>:  

### ![](<filename>.png)

## Potential next steps include:

# Analysis: 

```r
rm(list=ls())
set.seed(12345)
options(stringsAsFactors=FALSE)
source("~/Dropbox/datascience/R/mydsutils.R")
source("~/Dropbox/datascience/R/myplot.R")
source("~/Dropbox/datascience/R/mypetrinet.R")
# Gather all package requirements here
suppressPackageStartupMessages(require(plyr))
suppressPackageStartupMessages(require(lubridate))

#require(sos); findFn("pinv", maxPages=2, sortby="MaxScore")

# Analysis specific global variables
glb_separate_predict_dataset <- FALSE

script_df <- data.frame(chunk_label="import_data", chunk_step_major=1, chunk_step_minor=0)
print(script_df)
```

```
##   chunk_label chunk_step_major chunk_step_minor
## 1 import_data                1                0
```

## Step `1`: import data

```r
entity_df <- myimport_data(
    url="https://courses.edx.org/c4x/MITx/15.071x_2/asset/mvtWeek1.csv",
    comment="entity_df", print_diagn=TRUE)
```

```
## [1] "Reading file ./data/mvtWeek1.csv..."
## [1] "dimensions of data in ./data/mvtWeek1.csv: 191,641 rows x 11 cols"
##        ID           Date           LocationDescription Arrest Domestic
## 1 8951354 12/31/12 23:15                        STREET  FALSE    FALSE
## 2 8951141 12/31/12 22:00                        STREET  FALSE    FALSE
## 3 8952745 12/31/12 22:00 RESIDENTIAL YARD (FRONT/BACK)  FALSE    FALSE
## 4 8952223 12/31/12 22:00                        STREET  FALSE    FALSE
## 5 8951608 12/31/12 21:30                        STREET  FALSE    FALSE
## 6 8950793 12/31/12 20:30                        STREET   TRUE    FALSE
##   Beat District CommunityArea Year Latitude Longitude
## 1  623        6            69 2012 41.75628 -87.62164
## 2 1213       12            24 2012 41.89879 -87.66130
## 3 1622       16            11 2012 41.96919 -87.76767
## 4  724        7            67 2012 41.76933 -87.65773
## 5  211        2            35 2012 41.83757 -87.62176
## 6 2521       25            19 2012 41.92856 -87.75400
##             ID           Date LocationDescription Arrest Domestic Beat
## 31883  7816289  11/19/10 9:00              STREET  FALSE    FALSE 2234
## 87479  5147277 11/27/06 23:00              STREET  FALSE    FALSE  523
## 138155 3033024  11/6/03 20:30              STREET  FALSE    FALSE  611
## 145834 2748528  5/26/03 11:30              STREET  FALSE    FALSE  915
## 167834 2112320    3/2/02 7:30              STREET   TRUE    FALSE 2524
## 169816 1952378  1/22/02 22:00              STREET   TRUE    FALSE 1512
##        District CommunityArea Year Latitude Longitude
## 31883        22            75 2010 41.68737 -87.66088
## 87479         5            53 2006 41.66620 -87.63342
## 138155        6            71 2003 41.74941 -87.67518
## 145834        9            61 2003 41.80017 -87.67434
## 167834       NA            20 2002 41.92644 -87.73865
## 169816       NA            NA 2002 41.88629 -87.77185
##             ID        Date LocationDescription Arrest Domestic Beat
## 191636 1310755 1/1/01 0:05    RESIDENCE-GARAGE  FALSE    FALSE 2332
## 191637 1310068 1/1/01 0:05              STREET  FALSE    FALSE 1123
## 191638 1313404 1/1/01 0:01              STREET  FALSE    FALSE 2023
## 191639 1313442 1/1/01 0:01              STREET  FALSE    FALSE  911
## 191640 1563324 1/1/01 0:01              STREET  FALSE    FALSE 1712
## 191641 1310463 1/1/01 0:01              STREET  FALSE    FALSE 1911
##        District CommunityArea Year Latitude Longitude
## 191636       NA            NA 2001 41.93978 -87.63969
## 191637       NA            NA 2001 41.88757 -87.71132
## 191638       NA            NA 2001 41.98192 -87.65888
## 191639       NA            NA 2001 41.79902 -87.69654
## 191640       NA            NA 2001 41.97144 -87.72706
## 191641       NA            NA 2001 41.96833 -87.69639
## 'data.frame':	191641 obs. of  11 variables:
##  $ ID                 : int  8951354 8951141 8952745 8952223 8951608 8950793 8950760 8951611 8951802 8950706 ...
##  $ Date               : chr  "12/31/12 23:15" "12/31/12 22:00" "12/31/12 22:00" "12/31/12 22:00" ...
##  $ LocationDescription: chr  "STREET" "STREET" "RESIDENTIAL YARD (FRONT/BACK)" "STREET" ...
##  $ Arrest             : logi  FALSE FALSE FALSE FALSE FALSE TRUE ...
##  $ Domestic           : logi  FALSE FALSE FALSE FALSE FALSE FALSE ...
##  $ Beat               : int  623 1213 1622 724 211 2521 423 231 1021 1215 ...
##  $ District           : int  6 12 16 7 2 25 4 2 10 12 ...
##  $ CommunityArea      : int  69 24 11 67 35 19 48 40 29 24 ...
##  $ Year               : int  2012 2012 2012 2012 2012 2012 2012 2012 2012 2012 ...
##  $ Latitude           : num  41.8 41.9 42 41.8 41.8 ...
##  $ Longitude          : num  -87.6 -87.7 -87.8 -87.7 -87.6 ...
##  - attr(*, "comment")= chr "entity_df"
## NULL
```

```r
if (glb_separate_predict_dataset) {
    predct_df <- myimport_data(
        url="<prdct_url>", 
        comment="predct_df", print_diagn=TRUE)
} else {
    predct_df <- entity_df[sample(1:nrow(entity_df), nrow(entity_df) / 1000),]
    comment(predct_df) <- "predct_df"
    myprint_df(predct_df)
    str(predct_df)
}         
```

```
##             ID          Date LocationDescription Arrest Domestic Beat
## 62302  6451493 8/23/08 21:00              STREET  FALSE    FALSE 1022
## 97588  4682067 4/11/06 11:45   CONSTRUCTION SITE  FALSE    FALSE 1931
## 139457 2985540 10/9/03 21:50              STREET  FALSE    FALSE 2331
## 189672 1375312  2/11/01 1:15               OTHER  FALSE    FALSE 1732
## 6619   8702591  7/12/12 0:00              STREET  FALSE    FALSE 1924
## 29201  7887067 1/11/11 18:00              STREET  FALSE    FALSE 2223
##        District CommunityArea Year Latitude Longitude
## 62302        10            30 2008 41.85194 -87.70049
## 97588        19             7 2006 41.92531 -87.66470
## 139457       19             6 2003 41.94606 -87.64960
## 189672       NA            NA 2001 41.94395 -87.72352
## 6619         19             6 2012 41.94467 -87.64956
## 29201        22            73 2011 41.71918 -87.64330
##             ID          Date            LocationDescription Arrest
## 92850  4879672 7/23/06 18:00                         STREET  FALSE
## 60358  6540685  10/7/08 8:10         VEHICLE NON-COMMERCIAL  FALSE
## 15098  8393748 12/7/11 16:30                         STREET  FALSE
## 188472 1416248  3/8/01 22:00                         STREET  FALSE
## 162083 2203715 6/25/02 22:00         DRIVEWAY - RESIDENTIAL  FALSE
## 143458 2836736 7/16/03 19:00 PARKING LOT/GARAGE(NON.RESID.)  FALSE
##        Domestic Beat District CommunityArea Year Latitude Longitude
## 92850     FALSE 1811       18             7 2006 41.91183 -87.65329
## 60358     FALSE 1434       14            22 2008 41.91599 -87.68405
## 15098     FALSE 1814       18             7 2011 41.91433 -87.63373
## 188472    FALSE  524       NA            NA 2001 41.67097 -87.64914
## 162083    FALSE 1712       NA            13 2002 41.98224 -87.71386
## 143458    FALSE  324        3            69 2003 41.76286 -87.59926
##             ID           Date            LocationDescription Arrest
## 169640 1959281  1/26/02 11:31 PARKING LOT/GARAGE(NON.RESID.)  FALSE
## 58732  6629783 11/23/08 10:00                         STREET  FALSE
## 59558  6584464 10/29/08 11:00                         STREET  FALSE
## 188491 1416577   3/8/01 16:00                         STREET  FALSE
## 1327   8908750  11/29/12 3:00                         STREET  FALSE
## 52519  6960863   6/8/09 16:30                      RESIDENCE  FALSE
##        Domestic Beat District CommunityArea Year Latitude Longitude
## 169640    FALSE 1012       NA            NA 2002 41.85147 -87.73778
## 58732     FALSE  311        3            40 2008 41.78445 -87.62206
## 59558     FALSE 2533       25            25 2008 41.90941 -87.75451
## 188491    FALSE  612       NA            NA 2001 41.75469 -87.65561
## 1327      FALSE 1111       11            23 2012 41.90147 -87.73663
## 52519     FALSE  834        8            70 2009 41.74025 -87.72176
## 'data.frame':	191 obs. of  11 variables:
##  $ ID                 : int  6451493 4682067 2985540 1375312 8702591 7887067 2930413 8946210 5838497 5086047 ...
##  $ Date               : chr  "8/23/08 21:00" "4/11/06 11:45" "10/9/03 21:50" "2/11/01 1:15" ...
##  $ LocationDescription: chr  "STREET" "CONSTRUCTION SITE" "STREET" "OTHER" ...
##  $ Arrest             : logi  FALSE FALSE FALSE FALSE FALSE FALSE ...
##  $ Domestic           : logi  FALSE FALSE FALSE FALSE FALSE FALSE ...
##  $ Beat               : int  1022 1931 2331 1732 1924 2223 1723 2525 621 1133 ...
##  $ District           : int  10 19 19 NA 19 22 17 25 6 11 ...
##  $ CommunityArea      : int  30 7 6 NA 6 73 14 22 71 27 ...
##  $ Year               : int  2008 2006 2003 2001 2012 2011 2003 2012 2007 2006 ...
##  $ Latitude           : num  41.9 41.9 41.9 41.9 41.9 ...
##  $ Longitude          : num  -87.7 -87.7 -87.6 -87.7 -87.6 ...
##  - attr(*, "comment")= chr "predct_df"
```

```r
script_df <- rbind(script_df, 
                   data.frame(chunk_label="inspect_data", 
                              chunk_step_major=max(script_df$chunk_step_major)+1, 
                              chunk_step_minor=1))
print(script_df)
```

```
##    chunk_label chunk_step_major chunk_step_minor
## 1  import_data                1                0
## 2 inspect_data                2                1
```

### Step `2`.`1`: inspect data

```r
#print(str(entity_df))
#View(entity_df)

# List info gathered for various columns
#   ID: a unique identifier for each observation
#   Date: the date the crime occurred
#   LocationDescription: the location where the crime occurred
#   Arrest: whether or not an arrest was made for the crime (TRUE if an arrest was made, and FALSE if an arrest was not made)
#   Domestic: whether or not the crime was a domestic crime, meaning that it was committed against a family member (TRUE if it was domestic, and FALSE if it was not domestic)
#   Beat: the area, or "beat" in which the crime occurred. This is the smallest regional division defined by the Chicago police department.
#   District: the police district in which the crime occured. Each district is composed of many beats, and are defined by the Chicago Police Department.
#   CommunityArea: the community area in which the crime occurred. Since the 1920s, Chicago has been divided into what are called "community areas", of which there are now 77. The community areas were devised in an attempt to create socially homogeneous regions.
#   Year: the year in which the crime occurred.
#   Latitude: the latitude of the location at which the crime occurred.
#   Longitude: the longitude of the location at which the crime occurred.

# Create new features that help diagnostics
#   Convert factors to dummy variables
#   Potential Enhancements:
#       One code chunk to cycle thru entity_df & predct_df ?
#           Use with / within ?
#           for (df in c(entity_df, predct_df)) cycles thru column names
#           for (df in list(entity_df, predct_df)) does not change the actual dataframes
#
#       Build splines   require(splines); bsBasis <- bs(training$age, df=3)

entity_df <- mutate(entity_df, 
    LocationDescription_fctr=as.factor(LocationDescription),
    
    Date.my=as.Date(strptime(Date, "%m/%d/%y %H:%M")),
    Year=year(Date.my),
    Month=months(Date.my),
    Weekday=weekdays(Date.my)
    
                    )

predct_df <- mutate(predct_df, 
    LocationDescription_fctr=as.factor(LocationDescription),
    
    Date.my=as.Date(strptime(Date, "%m/%d/%y %H:%M")),
    Year=year(Date.my),    
    Month=months(Date.my),
    Weekday=weekdays(Date.my)
    
                    )

print(summary(entity_df))
```

```
##        ID              Date           LocationDescription   Arrest       
##  Min.   :1310022   Length:191641      Length:191641       Mode :logical  
##  1st Qu.:2832144   Class :character   Class :character    FALSE:176105   
##  Median :4762956   Mode  :character   Mode  :character    TRUE :15536    
##  Mean   :4968629                                          NA's :0        
##  3rd Qu.:7201878                                                         
##  Max.   :9181151                                                         
##                                                                          
##   Domestic            Beat         District     CommunityArea  
##  Mode :logical   Min.   : 111   Min.   : 1.00   Min.   : 0     
##  FALSE:191226    1st Qu.: 722   1st Qu.: 6.00   1st Qu.:22     
##  TRUE :415       Median :1121   Median :10.00   Median :32     
##  NA's :0         Mean   :1259   Mean   :11.82   Mean   :38     
##                  3rd Qu.:1733   3rd Qu.:17.00   3rd Qu.:60     
##                  Max.   :2535   Max.   :31.00   Max.   :77     
##                                 NA's   :43056   NA's   :24616  
##       Year         Latitude       Longitude     
##  Min.   :2001   Min.   :41.64   Min.   :-87.93  
##  1st Qu.:2003   1st Qu.:41.77   1st Qu.:-87.72  
##  Median :2006   Median :41.85   Median :-87.68  
##  Mean   :2006   Mean   :41.84   Mean   :-87.68  
##  3rd Qu.:2009   3rd Qu.:41.92   3rd Qu.:-87.64  
##  Max.   :2012   Max.   :42.02   Max.   :-87.52  
##                 NA's   :2276    NA's   :2276    
##                    LocationDescription_fctr    Date.my          
##  STREET                        :156564      Min.   :2001-01-01  
##  PARKING LOT/GARAGE(NON.RESID.): 14852      1st Qu.:2003-07-10  
##  OTHER                         :  4573      Median :2006-05-21  
##  ALLEY                         :  2308      Mean   :2006-08-23  
##  GAS STATION                   :  2111      3rd Qu.:2009-10-24  
##  DRIVEWAY - RESIDENTIAL        :  1675      Max.   :2012-12-31  
##  (Other)                       :  9558                          
##     Month             Weekday         
##  Length:191641      Length:191641     
##  Class :character   Class :character  
##  Mode  :character   Mode  :character  
##                                       
##                                       
##                                       
## 
```

```r
print(summary(predct_df))
```

```
##        ID              Date           LocationDescription   Arrest       
##  Min.   :1356257   Length:191         Length:191          Mode :logical  
##  1st Qu.:2521540   Class :character   Class :character    FALSE:175      
##  Median :4328909   Mode  :character   Mode  :character    TRUE :16       
##  Mean   :4670799                                          NA's :0        
##  3rd Qu.:6688620                                                         
##  Max.   :8946210                                                         
##                                                                          
##   Domestic            Beat         District     CommunityArea  
##  Mode :logical   Min.   : 133   Min.   : 1.00   Min.   : 1.00  
##  FALSE:190       1st Qu.: 734   1st Qu.: 7.00   1st Qu.:19.50  
##  TRUE :1         Median :1124   Median :11.00   Median :30.00  
##  NA's :0         Mean   :1279   Mean   :12.04   Mean   :36.86  
##                  3rd Qu.:1732   3rd Qu.:17.00   3rd Qu.:61.00  
##                  Max.   :2533   Max.   :25.00   Max.   :77.00  
##                                 NA's   :51      NA's   :32     
##       Year         Latitude       Longitude     
##  Min.   :2001   Min.   :41.65   Min.   :-87.83  
##  1st Qu.:2002   1st Qu.:41.77   1st Qu.:-87.71  
##  Median :2005   Median :41.85   Median :-87.68  
##  Mean   :2006   Mean   :41.84   Mean   :-87.68  
##  3rd Qu.:2008   3rd Qu.:41.92   3rd Qu.:-87.64  
##  Max.   :2012   Max.   :42.01   Max.   :-87.55  
##                 NA's   :4       NA's   :4       
##                    LocationDescription_fctr    Date.my          
##  STREET                        :148         Min.   :2001-01-30  
##  PARKING LOT/GARAGE(NON.RESID.): 14         1st Qu.:2002-12-31  
##  OTHER                         :  6         Median :2005-09-22  
##  VEHICLE NON-COMMERCIAL        :  5         Mean   :2006-03-10  
##  ALLEY                         :  3         3rd Qu.:2009-01-01  
##  RESIDENCE                     :  3         Max.   :2012-12-27  
##  (Other)                       : 12                             
##     Month             Weekday         
##  Length:191         Length:191        
##  Class :character   Class :character  
##  Mode  :character   Mode  :character  
##                                       
##                                       
##                                       
## 
```

```r
#pairs(subset(entity_df, select=-c(col_symbol)))

# Histogram of predictor in entity_df & predct_df
# Check for predct_df & entity_df features range mismatches

# Other diagnostics:
print(table(entity_df$LocationDescription))
```

```
## 
##                              ABANDONED BUILDING 
##                                               4 
## AIRPORT BUILDING NON-TERMINAL - NON-SECURE AREA 
##                                               4 
##     AIRPORT BUILDING NON-TERMINAL - SECURE AREA 
##                                               1 
##              AIRPORT EXTERIOR - NON-SECURE AREA 
##                                              24 
##                  AIRPORT EXTERIOR - SECURE AREA 
##                                               1 
##                             AIRPORT PARKING LOT 
##                                              11 
##  AIRPORT TERMINAL UPPER LEVEL - NON-SECURE AREA 
##                                               5 
##                   AIRPORT VENDING ESTABLISHMENT 
##                                              10 
##                                AIRPORT/AIRCRAFT 
##                                             363 
##                                           ALLEY 
##                                            2308 
##                                 ANIMAL HOSPITAL 
##                                               1 
##                                       APARTMENT 
##                                             184 
##                                 APPLIANCE STORE 
##                                               1 
##                                   ATHLETIC CLUB 
##                                               9 
##                                            BANK 
##                                               7 
##                                   BAR OR TAVERN 
##                                              17 
##                                      BARBERSHOP 
##                                               4 
##                                   BOWLING ALLEY 
##                                               3 
##                                          BRIDGE 
##                                               2 
##                                        CAR WASH 
##                                              44 
##                                   CHA APARTMENT 
##                                               5 
##                         CHA PARKING LOT/GROUNDS 
##                                             405 
##               CHURCH/SYNAGOGUE/PLACE OF WORSHIP 
##                                              56 
##                                  CLEANING STORE 
##                                               3 
##                      COLLEGE/UNIVERSITY GROUNDS 
##                                              47 
##               COLLEGE/UNIVERSITY RESIDENCE HALL 
##                                               2 
##                    COMMERCIAL / BUSINESS OFFICE 
##                                             126 
##                               CONSTRUCTION SITE 
##                                              35 
##                               CONVENIENCE STORE 
##                                               7 
##                     CTA GARAGE / OTHER PROPERTY 
##                                             148 
##                                       CTA TRAIN 
##                                               1 
##                               CURRENCY EXCHANGE 
##                                               2 
##                                 DAY CARE CENTER 
##                                               5 
##                                DEPARTMENT STORE 
##                                              22 
##                          DRIVEWAY - RESIDENTIAL 
##                                            1675 
##                                      DRUG STORE 
##                                               8 
##                  FACTORY/MANUFACTURING BUILDING 
##                                              16 
##                                    FIRE STATION 
##                                               5 
##                                 FOREST PRESERVE 
##                                               6 
##                                     GAS STATION 
##                                            2111 
##                    GOVERNMENT BUILDING/PROPERTY 
##                                              48 
##                              GROCERY FOOD STORE 
##                                              80 
##                              HIGHWAY/EXPRESSWAY 
##                                              22 
##                       HOSPITAL BUILDING/GROUNDS 
##                                             101 
##                                     HOTEL/MOTEL 
##                                             124 
##                         JAIL / LOCK-UP FACILITY 
##                                               1 
##                  LAKEFRONT/WATERFRONT/RIVERBANK 
##                                               4 
##                                         LIBRARY 
##                                               4 
##                           MEDICAL/DENTAL OFFICE 
##                                               3 
##                             MOVIE HOUSE/THEATER 
##                                              18 
##                                       NEWSSTAND 
##                                               1 
##                    NURSING HOME/RETIREMENT HOME 
##                                              21 
##                                           OTHER 
##                                            4573 
##                 OTHER COMMERCIAL TRANSPORTATION 
##                                               8 
##               OTHER RAILROAD PROP / TRAIN DEPOT 
##                                              28 
##                                   PARK PROPERTY 
##                                             255 
##                  PARKING LOT/GARAGE(NON.RESID.) 
##                                           14852 
##                 POLICE FACILITY/VEH PARKING LOT 
##                                             266 
##                                       RESIDENCE 
##                                            1302 
##                         RESIDENCE PORCH/HALLWAY 
##                                              18 
##                                RESIDENCE-GARAGE 
##                                            1176 
##                   RESIDENTIAL YARD (FRONT/BACK) 
##                                            1536 
##                                      RESTAURANT 
##                                              49 
##                                SAVINGS AND LOAN 
##                                               4 
##                       SCHOOL, PRIVATE, BUILDING 
##                                              14 
##                        SCHOOL, PRIVATE, GROUNDS 
##                                              23 
##                        SCHOOL, PUBLIC, BUILDING 
##                                             114 
##                         SCHOOL, PUBLIC, GROUNDS 
##                                             206 
##                                        SIDEWALK 
##                                             462 
##                              SMALL RETAIL STORE 
##                                              33 
##                            SPORTS ARENA/STADIUM 
##                                             166 
##                                          STREET 
##                                          156564 
##                             TAVERN/LIQUOR STORE 
##                                              14 
##                                         TAXICAB 
##                                              21 
##                                 VACANT LOT/LAND 
##                                             985 
##                          VEHICLE NON-COMMERCIAL 
##                                             817 
##                              VEHICLE-COMMERCIAL 
##                                              23 
##                                       WAREHOUSE 
##                                              17
```

```r
print(table(entity_df$Month))
```

```
## 
##     April    August  December  February   January      July      June 
##     15280     16572     16426     13511     16047     16801     16002 
##     March       May  November   October September 
##     15758     16035     16063     17086     16060
```

```r
print(which.min(table(entity_df$Month)))
```

```
## February 
##        4
```

```r
print(table(entity_df$Weekday))
```

```
## 
##    Friday    Monday  Saturday    Sunday  Thursday   Tuesday Wednesday 
##     29284     27397     27118     26316     27319     26791     27416
```

```r
print(which.max(table(entity_df$Weekday)))
```

```
## Friday 
##      1
```

```r
print(table(entity_df$Month, entity_df$Arrest))
```

```
##            
##             FALSE  TRUE
##   April     14028  1252
##   August    15243  1329
##   December  15029  1397
##   February  12273  1238
##   January   14612  1435
##   July      15477  1324
##   June      14772  1230
##   March     14460  1298
##   May       14848  1187
##   November  14807  1256
##   October   15744  1342
##   September 14812  1248
```

```r
print(which.max(table(entity_df$Month, entity_df$Arrest)[, 2]))
```

```
## January 
##       5
```

```r
print(table(entity_df$Month, entity_df$Arrest))
```

```
##            
##             FALSE  TRUE
##   April     14028  1252
##   August    15243  1329
##   December  15029  1397
##   February  12273  1238
##   January   14612  1435
##   July      15477  1324
##   June      14772  1230
##   March     14460  1298
##   May       14848  1187
##   November  14807  1256
##   October   15744  1342
##   September 14812  1248
```

```r
print(prblm_3_3_tbl <- table(entity_df$Year, entity_df$Arrest))
```

```
##       
##        FALSE  TRUE
##   2001 18517  2152
##   2002 16638  2115
##   2003 14859  1798
##   2004 15169  1693
##   2005 14956  1528
##   2006 14796  1302
##   2007 13068  1212
##   2008 13425  1020
##   2009 11327   840
##   2010 14796   701
##   2011 15012   625
##   2012 13542   550
```

```r
print(prblm_3_3_df <- data.frame(Year=dimnames(prblm_3_3_tbl)[[1]],
                                 Arrest_FALSE=prblm_3_3_tbl[, 1],
                                 Arrest_TRUE =prblm_3_3_tbl[, 2]))
```

```
##      Year Arrest_FALSE Arrest_TRUE
## 2001 2001        18517        2152
## 2002 2002        16638        2115
## 2003 2003        14859        1798
## 2004 2004        15169        1693
## 2005 2005        14956        1528
## 2006 2006        14796        1302
## 2007 2007        13068        1212
## 2008 2008        13425        1020
## 2009 2009        11327         840
## 2010 2010        14796         701
## 2011 2011        15012         625
## 2012 2012        13542         550
```

```r
print(prblm_3_3_df <- mutate(prblm_3_3_df, 
            Arrest_ratio=(Arrest_TRUE * 1.0) / (Arrest_TRUE + Arrest_FALSE))) 
```

```
##      Year Arrest_FALSE Arrest_TRUE Arrest_ratio
## 2001 2001        18517        2152   0.10411728
## 2002 2002        16638        2115   0.11278195
## 2003 2003        14859        1798   0.10794261
## 2004 2004        15169        1693   0.10040327
## 2005 2005        14956        1528   0.09269595
## 2006 2006        14796        1302   0.08087961
## 2007 2007        13068        1212   0.08487395
## 2008 2008        13425        1020   0.07061267
## 2009 2009        11327         840   0.06903920
## 2010 2010        14796         701   0.04523456
## 2011 2011        15012         625   0.03996930
## 2012 2012        13542         550   0.03902924
```

```r
print(prblm_3_3_df[prblm_3_3_df$Year == 2001, ])
```

```
##      Year Arrest_FALSE Arrest_TRUE Arrest_ratio
## 2001 2001        18517        2152    0.1041173
```

```r
# Other plots:
print(myplot_histogram(entity_df, "Date.my"))
```

```
## stat_bin: binwidth defaulted to range/30. Use 'binwidth = x' to adjust this.
```

![](MVT_Chicago_files/figure-html/inspect_data_1-1.png) 

```r
print(myplot_box(df=entity_df, ycol_names="Date.my"))
```

![](MVT_Chicago_files/figure-html/inspect_data_1-2.png) 

```r
print(myplot_box(df=entity_df, ycol_names="Date.my", xcol_name="Arrest"))
```

```
## Warning in myplot_box(df = entity_df, ycol_names = "Date.my", xcol_name =
## "Arrest"): xcol_name:Arrest is not a factor; creating Arrest_fctr
```

![](MVT_Chicago_files/figure-html/inspect_data_1-3.png) 

```r
script_df <- rbind(script_df, 
                   data.frame(chunk_label="extract_features", 
                              chunk_step_major=max(script_df$chunk_step_major)+1, 
                              chunk_step_minor=0))
print(script_df)
```

```
##        chunk_label chunk_step_major chunk_step_minor
## 1      import_data                1                0
## 2     inspect_data                2                1
## 3 extract_features                3                0
```

## Step `3`: extract features

```r
# script_df <- rbind(script_df, 
#                    data.frame(chunk_label="extract_features", 
#                               chunk_step_major=max(script_df$chunk_step_major)+1, 
#                               chunk_step_minor=0))
print(script_df)
```

```
##        chunk_label chunk_step_major chunk_step_minor
## 1      import_data                1                0
## 2     inspect_data                2                1
## 3 extract_features                3                0
```

Null Hypothesis ($\sf{H_{0}}$): mpg is not impacted by am_fctr.  
The variance by am_fctr appears to be independent. 

```r
# print(t.test(subset(cars_df, am_fctr == "automatic")$mpg, 
#              subset(cars_df, am_fctr == "manual")$mpg, 
#              var.equal=FALSE)$conf)
```
We reject the null hypothesis i.e. we have evidence to conclude that am_fctr impacts mpg (95% confidence). Manual transmission is better for miles per gallon versus automatic transmission.

## remove nearZeroVar features (not much variance)
#require(reshape)
#var_features_df <- melt(summaryBy(. ~ factor(0), data=entity_df[, features_lst], 
#                             FUN=var, keep.names=TRUE), 
#                             variable_name=c("feature"))
#names(var_features_df)[2] <- "var"
#print(var_features_df[order(var_features_df$var), ])
# summaryBy ignores factors whereas nearZeroVar inspects factors

# k_fold <- 5
# entity_df[order(entity_df$classe, 
#                   entity_df$user_name, 
#                   entity_df$my.rnorm),"my.cv_ix"] <- 
#     rep(1:k_fold, length.out=nrow(entity_df))
# summaryBy(X ~ my.cv_ix, data=entity_df, FUN=length)
# tapply(entity_df$X, list(entity_df$classe, entity_df$user_name, 
#                            entity_df$my.cv_ix), length)

#require(DAAG)
#entity_df$classe.proper <- as.numeric(entity_df$classe == "A")
#rnorm.glm <- glm(classe.proper ~ rnorm, family=binomial, data=entity_df)
#cv.binary(rnorm.glm, nfolds=k_fold, print.details=TRUE)
#result <- cv.lm(df=entity_df, form.lm=formula(classe ~ rnorm), 
#                    m=k_fold, seed=12345, printit=TRUE)

#plot(mdl_1$finalModel, uniform=TRUE, main="base")
#text(mdl_1$finalModel, use.n=TRUE, all=TRUE, cex=0.8)



```
## R version 3.1.2 (2014-10-31)
## Platform: x86_64-apple-darwin13.4.0 (64-bit)
## 
## locale:
## [1] en_US.UTF-8/en_US.UTF-8/en_US.UTF-8/C/en_US.UTF-8/en_US.UTF-8
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## other attached packages:
## [1] lubridate_1.3.3 plyr_1.8.1      doBy_4.5-13     survival_2.38-1
## [5] ggplot2_1.0.0  
## 
## loaded via a namespace (and not attached):
##  [1] codetools_0.2-10 colorspace_1.2-5 digest_0.6.8     evaluate_0.5.5  
##  [5] formatR_1.0      grid_3.1.2       gtable_0.1.2     htmltools_0.2.6 
##  [9] knitr_1.9        labeling_0.3     lattice_0.20-30  MASS_7.3-39     
## [13] Matrix_1.1-5     memoise_0.2.1    munsell_0.4.2    proto_0.3-10    
## [17] Rcpp_0.11.4      reshape2_1.4.1   rmarkdown_0.5.1  scales_0.2.4    
## [21] splines_3.1.2    stringr_0.6.2    tcltk_3.1.2      tools_3.1.2     
## [25] yaml_2.1.13
```
