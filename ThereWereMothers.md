# There Were Mothers
Suzanne Hartley  
January 1, 2016  



The [Trans-Atlantic Slave Trade Database](http://www.slavevoyages.org) has 
information on more than 35,000 slave voyages. An interactive interface allows researchers to create listings, tables, charts, and maps using information from the database. 

Available [data downloads](http://www.slavevoyages.org/voyage/download) include a subset of the [African Names Database](http://www.slavevoyages.org/resources/names-database) which identifies 67,460 Africans taken from captured slave ships or from African trading sites from 1819 to 1845. 

In this exploratory study, I identify females who are mothers. They are not the only mothers --- but they are the females who the data can identify as such.


  
### Data  

The downloaded file was read into R as a dataframe named *indata*:  

```r
indata <- read.csv("AfricanNamesDatabase.csv")
```

Below is shown the overall structure and content of the data:


```
## 'data.frame':	67460 obs. of  12 variables:
##  $ id      : Factor w/ 67460 levels "1","10","100",..: 1 11095 22200 33310 44379 52423 53534 64622 66119 2 ...
##  $ name    : Factor w/ 48610 levels "","[sic] Manuel",..: 12948 22196 16706 41791 33904 29026 44090 45607 45570 42247 ...
##  $ age     : int  30 30 28 22 16 22 20 30 18 23 ...
##  $ height  : num  62.5 64 65 62.5 59 67.5 62 65.5 61.5 62 ...
##  $ sexage  : Factor w/ 7 levels "","Boy","Female",..: 6 6 6 6 6 6 6 6 6 6 ...
##  $ country : Factor w/ 559 levels "","\\Baronoke",..: 1 1 1 302 302 1 302 302 1 1 ...
##  $ voyageId: int  2314 2315 2315 2315 2315 2315 2315 2315 2315 2315 ...
##  $ shipname: Factor w/ 277 levels "","Adelaide",..: 182 84 84 84 84 84 84 84 84 84 ...
##  $ datearr : int  1819 1819 1819 1819 1819 1819 1819 1819 1819 1819 ...
##  $ majbuypt: Factor w/ 47 levels "","Ambriz","Anomabu",..: 28 45 45 45 45 45 45 45 45 45 ...
##  $ majselpt: Factor w/ 5 levels "","Bahamas, port unspecified",..: 3 3 3 3 3 3 3 3 3 3 ...
##  $ source  : Factor w/ 43 levels "","FO313/56,1",..: 33 33 33 33 33 33 33 33 33 33 ...
```

For convenience, some of the "Factor" variables are converted to plain string types:


```r
indata$name <- as.character(indata$name)
indata$sexage <- as.character(indata$sexage)
indata$country <- as.character(indata$country)
indata$shipname <- as.character(indata$shipname)
indata$majbuypt <- as.character(indata$majbuypt)
indata$majselpt <- as.character(indata$majselpt)
```

### Identifying children who can be matched with a mother

Many of the children in the data set are named simply with reference to a parent (assumed to be mother), and a separate dataframe (*offspring*) is made as follows:


```r
options(width=120)
o <- grep("[Ii]nfant|[Cc]hild", indata$name)
offspring <- indata[o,]
offspring$name[1:20]
```

```
##  [1] "Askua's Infant"             "Ponger's Infant"            "Asemma's child"             "Janar's child"             
##  [5] "Laque's child"              "Aquepadoe's child"          "Betardy's child"            "Adingah's child"           
##  [9] "Raybaund's Infant"          "Odrayday (Infant 9 mths)"   "Ahwarr (Infant 10 mths)"    "Arrahcholo (Infant 8 mths)"
## [13] "Arrayboo (Infant 8 mths)"   "Onekay (Infant 10 mths)"    "Egga's infant (11 mths)"    "Olukaye's infant (13 mths)"
## [17] "Ajaiay's infant (10mths)"   "Ahjarme's child"            "Ahkenhaybay's child"        "Ego's child (11mths)"
```

Only the first 20 names of 270 offspring are shown. The full list of names is given in ![Appendix A][]. The above strings are stripped of unwanted characters, leaving the name of the mother:  


```r
offspring$mothername <- gsub("[Ii]nfant|[Cc]hild|'s|\\(.*| | of", "", offspring$name)
offspring$mothername[1:20]
```

```
##  [1] "Askua"       "Ponger"      "Asemma"      "Janar"       "Laque"       "Aquepadoe"   "Betardy"     "Adingah"    
##  [9] "Raybaund"    "Odrayday"    "Ahwarr"      "Arrahcholo"  "Arrayboo"    "Onekay"      "Egga"        "Olukaye"    
## [17] "Ajaiay"      "Ahjarme"     "Ahkenhaybay" "Ego"
```
    
The full list is given in ![Appendix B][].   
  
  
    
### Awoosoo and Hyenah  

Before moving on, let's examine these particular names:  


```r
offspring[grep("Awoosoo",offspring$mothername),c("mothername","age","height","sexage","voyageId","shipname","majbuypt")]
```

```
##        mothername age height sexage voyageId shipname    majbuypt
## 16270     Awoosoo   3     31    Boy     3017    Uniao Lagos, Onim
## 17339 Awoosoo/1st  NA     26    Boy     3020 Carolina Lagos, Onim
## 17345 Awoosoo/2nd   2     33   Girl     3020 Carolina Lagos, Onim
## 17347 Awoosoo/3rd   2     36   Girl     3020 Carolina Lagos, Onim
```

```r
subset(indata[grep("Awoosoo",indata$name),c("name","age","height","sexage","voyageId","shipname")], voyageId==3017 | voyageId==3020)
```

```
##                                name age height sexage voyageId shipname
## 16269                       Awoosoo  35     62  Woman     3017    Uniao
## 16270              Awoosoo's Infant   3     31    Boy     3017    Uniao
## 17338                   Awoosoo/1st  36     64  Woman     3020 Carolina
## 17339 Awoosoo/1st's Infant (8 mths)  NA     26    Boy     3020 Carolina
## 17344                   Awoosoo/2nd  29     60  Woman     3020 Carolina
## 17345          Awoosoo/2nd's Infant   2     33   Girl     3020 Carolina
## 17346                   Awoosoo/3rd  26     61  Woman     3020 Carolina
## 17347          Awoosoo/3rd's Infant   2     36   Girl     3020 Carolina
## 17387                       Awoosoo  23     66  Woman     3020 Carolina
## 17428                       Awoosoo  24     61  Woman     3020 Carolina
## 17448                       Awoosoo  27     61  Woman     3020 Carolina
```
   
It is a simple matter to match Awoosoo, aged 35, with her 3-year-old son - traveling together on the *Uniao* out of Lagos. But on board the *Carolina* are six women named Awoosoo. Fortunately, their names were recorded so that the three mothers are matched with the respective children: 

* Awoosoo, age 36, mother of 8-month-old boy  
* Awoosoo, age 29, mother of 2-year-old girl  
* Awoosoo, age 26, mother of 2-year-old girl  

But this is not always the case. Also on the *Carolina* is a 14-month-old girl with six possible matches for a mother:  
  

```r
subset(offspring, mothername=="Hyenah" & voyageId==3020)[, c("name","age","height","sexage","voyageId","shipname")]
```

```
##                            name age height sexage voyageId shipname
## 17367 Hyenah's Infant (14 mths)  NA     33   Girl     3020 Carolina
```

```r
subset(indata, name=="Hyenah" & voyageId==3020)[, c("name","age","height","sexage","voyageId","shipname")]
```

```
##         name age height sexage voyageId shipname
## 17311 Hyenah  21     67    Man     3020 Carolina
## 17328 Hyenah  27     66    Man     3020 Carolina
## 17366 Hyenah  24     60  Woman     3020 Carolina
## 17378 Hyenah  24     59  Woman     3020 Carolina
## 17414 Hyenah  17     59  Woman     3020 Carolina
## 17427 Hyenah  27     59  Woman     3020 Carolina
## 17473 Hyenah  34     64  Woman     3020 Carolina
## 17512 Hyenah  34     62  Woman     3020 Carolina
## 17553 Hyenah   8     52    Boy     3020 Carolina
## 17564 Hyenah   8     58    Boy     3020 Carolina
## 17586 Hyenah  11     56    Boy     3020 Carolina
## 17616 Hyenah   9     50   Girl     3020 Carolina
## 17622 Hyenah   7     47   Girl     3020 Carolina
## 17648 Hyenah  10     58   Girl     3020 Carolina
## 17657 Hyenah   6     46   Girl     3020 Carolina
```
   
So it will not be possible to match up all offspring with mothers --- but that is the next step.  
  
  
### Matching child with mother by name and voyage  
  
The *offspring* dataframe is merged with the *indata* dataframe using *mothername* and *name* columns, respectively, in addition to *voyageId*. Unwanted columns are dropped from from each dataframe before the merge, and the resulting dataframe, *moms*, is given new column names. Finally, *moms* is subset to include only *Woman* and *Female* with respect to *mothersexage*. (By inspection, it was found that all mother candidates with *mothersexage* recorded as *girl* were aged 12 years or younger.) The *mothersexage* column was then dropped.  
  

```r
olist <- c("id","name","mothername","age","sexage","voyageId")
ilist <- c("name", "age", "sexage", "voyageId")
moms <- merge(offspring[,olist], indata[,ilist], by.x=c("mothername","voyageId"), by.y=c("name","voyageId"))
moms <- moms[,c("voyageId","id","name","age.x","sexage.x","mothername","age.y","sexage.y")]
names(moms) <- c("voyageId","childid","childname","childage","childsexage","mothername","motherage","mothersexage")
moms <- subset(moms, mothersexage %in% c("Woman", "Female"))
moms$mothersexage <- NULL
```
  
  
  
We can now itentify the voyages with mothers accompanied by children. For convenience, the voyage details are subset as a dataframe, *vdata*:  
  

```r
voyages <- sort(unique(moms$voyageId))
vdata <- indata[,c("voyageId","shipname","datearr","majbuypt")]
```
  
  
Here is the list for voyage 2977. (The complete list is given in ![Appendix C][])  
  

```r
v <- subset(vdata, voyageId == 2977)
print(paste("Shipname: ", v$shipname[1], "  Year: ", v$datearr[1], "  Port:  ", v$majbuypt[1]))
```

```
## [1] "Shipname:  Conceiçao de Maria   Year:  1827   Port:   Whydah"
```

```r
print(subset(moms, voyageId == 2977))
```

```
##     voyageId childid                  childname childage childsexage mothername motherage
## 1       2977   10761   Ababee's child (11 mths)       NA         Boy     Ababee        29
## 27      2977   10768           Ahgassay's child        2         Boy   Ahgassay        30
## 28      2977   10768           Ahgassay's child        2         Boy   Ahgassay        26
## 29      2977   10759           Ahgassee's child        2         Boy   Ahgassee        26
## 46      2977   10771           Ahkimfoo's child        2        Girl   Ahkimfoo        32
## 69      2977   10812              Allubah Child        1         Boy    Allubah        31
## 70      2977   10812              Allubah Child        1         Boy    Allubah        19
## 71      2977   10812              Allubah Child        1         Boy    Allubah        24
## 110     2977   10810    Barquay Child (10 mths)       NA         Boy    Barquay        26
## 128     2977   10763    Daday's child (16 mths)       NA         Boy      Daday        31
## 217     2977   10814     Kachah Child (18 mths)       NA         Boy     Kachah        28
## 251     2977   10766 Mahhesay's child (3 weeks)       NA         Boy   Mahhesay        27
## 252     2977   10766 Mahhesay's child (3 weeks)       NA         Boy   Mahhesay        27
```
  
  
    
### Multiple matches  
  
Previously, we found an example of a child who could be matched with more than one possible mother on the voyage. The following table shows all such duplicates:  
  

```r
require(plyr)
matches <- count(moms, c("voyageId","childid","mothername"))
matches[matches$freq > 1,]
```

```
##     voyageId childid mothername freq
## 16      2418   23807      Lango    3
## 24      2931    3218     Ponger    3
## 50      2954    7062     Hyenah    2
## 69      2965    8662     Ballay    2
## 92      2970   10555     Hyenah    8
## 101     2977   10766   Mahhesay    2
## 102     2977   10768   Ahgassay    2
## 105     2977   10812    Allubah    3
## 118     3007   14249     Lahmee    3
## 155     3014   18300   Ohonquay    2
## 159     3017   16320     Akedam    2
## 160     3017   16324     Awosee    2
## 161     3017   16326     Akedam    2
## 169     3017   16342     Hyenah    7
## 171     3019   17078      Ahjai    3
## 201     3020   17441     Hyenah    6
## 205     3020   17449     Ajumah    2
## 218     3023   18953       Adoo    2
## 250     3030   21741      Ahjai    4
```

```r
nrow(matches[matches$freq>1,])
```

```
## [1] 19
```
  
Thus, 19 children had more than one possible match on the voyage.  
  
  
  
### Unique matches    
  

```r
nrow(matches[matches$freq==1,])
```

```
## [1] 235
```
   
235 children were uniquely matched with a mother on the voyage.  
  
  
  
### Unmatched children  
  
The remaining 16 children found no matches:  
   

```r
foundmoms <- offspring$mothername %in% moms$mothername
offspring[!foundmoms,olist]
```

```
##          id                         name   mothername age sexage voyageId
## 5915   5975          Ahkenhaybay's child  Ahkenhaybay   1    Boy     2952
## 6904   6964  Andonnahoe's child (9 mths)   Andonnahoe  NA    Boy     2954
## 7866   7930 Soolallahday's child (8mths) Soolallahday  NA    Boy     2955
## 8083   8151            Dohwommah's child    Dohwommah   3   Girl     2369
## 16248 16322               Monno's Infant        Monno   2   Girl     3017
## 19334 19410            Akamedah's Infant     Akamedah   2    Boy     3024
## 20490 20566            Chekatay's Infant     Chekatay   2    Boy     3027
## 22018 22095           Koomardoh's Infant    Koomardoh   2   Girl     3032
## 22019 22096           Acbackbah's Infant    Acbackbah   1   Girl     3032
## 49477 49570            Mary-ann (infant)     Mary-ann  NA   Girl    59011
## 49478 49571              Yambah (infant)       Yambah  NA   Girl    59011
## 49992 50085                 Sam (infant)          Sam  NA    Boy     3091
## 50109 50202              Boango (infant)       Boango  NA   Girl     3091
## 50110 50203               Gomba (infant)        Gomba  NA   Girl     3091
## 51734 51827                Choo (Infant)         Choo   0    Boy     2681
## 67410 91681                 Amba's child         Amba   1    Boy     2367
```
  
  
These were examined manually in order to find explanations and/or alternate matches. It is possible that the children on voyages 59011 and 3091 have their own names in the *name* field, with "infant" being descriptive as opposed to possessive.  
In two cases, Akamedah and Choo, the name may refer to father instead of mother.   
Dohwammah and Monno were not matched, because included in the mother's *name* field was a note indicating she was pregnant, e.g. "Monno (pregnant)".  
In five cases, it appears that the mother's name had a slightly different spelling:  

* Ahkenhaybay - Ahkenjaybay  
* Soolallahday - Soolullahday  
* Koomardoh - Koomaidoh  
* Acbackbah - Acbackboh  
* Amba - Ambah  

No alternative matches were found for Andonnahoe or Chekatay.  
  
  
  
### Pregnant females  
  
There are probably other females recorded as pregnant:  
  

```r
indata[grep("[Pp]regnant", indata$name), c("id",ilist,"shipname","datearr")]
```

```
##          id                 name age sexage voyageId                     shipname datearr
## 5229   5289   Pangoko (pregnant)  25  Woman     2947                  Bella Eliza    1824
## 7878   7942    Lammay (pregnant)  22  Woman     2955 San Joao (a) Segunda Rosalia    1826
## 7960   8028 Baykeebah (pregnant)  28  Woman     2957                       Netuno    1826
## 8069   8137    Cossee (pregnant)  25  Woman     2369                      Nicanor    1826
## 8082   8150 Dohwommah (pregnant)  30  Woman     2369                      Nicanor    1826
## 8085   8153 Yesterday (pregnant)  27  Woman     2369                      Nicanor    1826
## 9407   9475   Passasa (pregnant)  30  Woman     2841                         Lynx    1827
## 15572 15645       Mop (pregnant)  22  Woman     2882                     Coquette    1829
## 16035 16108   Couttah (pregnant)  21  Woman     3013                     Vingador    1829
## 16247 16321     Monno (pregnant)  26  Woman     3017                        Uniao    1829
## 16308 16382    Awosoo (pregnant)  27  Woman     3017                        Uniao    1829
## 16338 16412    Bahtah (pregnant)  22  Woman     3017                        Uniao    1829
## 16360 16434   Fogolah (pregnant)  24  Woman     3017                        Uniao    1829
## 16367 16441  Mesahday (pregnant)  29  Woman     3017                        Uniao    1829
## 17049 17123  Chepaboo (pregnant)  25  Woman     3019                Donna Barbara    1829
## 18945 19021     Ahjai (pregnant)  23  Woman     3023                       Emilia    1829
## 36475 36558     Bomar (pregnant)  15  Woman     2490                Seis Hermanos    1836
```
  
    
15-year-old Bomar gets the final mention in this story:  
  

```r
indata[indata$id==36558,]
```

```
##          id             name age height sexage country voyageId      shipname datearr    majbuypt majselpt   source
## 36475 36558 Bomar (pregnant)  15     63  Woman             2490 Seis Hermanos    1836 New Calabar Freetown FO84/189
```
  
    
[Voyage 2490](http://www.slavevoyages.org/voyage/2490/variables) indicates that Bomar was traded in the February or March of 1836 in [Kalabari](https://en.wikipedia.org/wiki/Kalabari_Kingdom), in what is now Nigeria. 
  
  
  
### Conclusions  
  
* 254 uniquely-defined mother-child pairs were identified  
* The *name* field needs to be examined in more detail --- there may be other useful information buried in the names.
  



### Appendix A  
#### Identified offpsring  

```
##   [1] "Askua's Infant"                "Ponger's Infant"               "Asemma's child"               
##   [4] "Janar's child"                 "Laque's child"                 "Aquepadoe's child"            
##   [7] "Betardy's child"               "Adingah's child"               "Raybaund's Infant"            
##  [10] "Odrayday (Infant 9 mths)"      "Ahwarr (Infant 10 mths)"       "Arrahcholo (Infant 8 mths)"   
##  [13] "Arrayboo (Infant 8 mths)"      "Onekay (Infant 10 mths)"       "Egga's infant (11 mths)"      
##  [16] "Olukaye's infant (13 mths)"    "Ajaiay's infant (10mths)"      "Ahjarme's child"              
##  [19] "Ahkenhaybay's child"           "Ego's child (11mths)"          "Awoo's child (11mths)"        
##  [22] "Lodday's child (10mths)"       "Mangah's infant"               "Bowsenee's child"             
##  [25] "Pyesor's child"                "Jaysee's child (3 mths)"       "Andonnahoe's child (9 mths)"  
##  [28] "Nayee's child (6 mths)"        "Toh's child (6 mths)"          "Kossay's child (10 mths)"     
##  [31] "Ammahpay's child (10 mths)"    "Noyamme's child (8mths)"       "Yartopay's child (10mths)"    
##  [34] "Farsommah's child (7mths)"     "Hyenah's child (4mths)"        "Zarrah's child (6mths)"       
##  [37] "Soolallahday's child (8mths)"  "Ahbayboh's child (8mths)"      "Daykussa's child"             
##  [40] "Mackah's child (10 mths)"      "Dohwommah's child"             "Kissee Child"                 
##  [43] "Guah Child"                    "Bullay Child (4 mths)"         "Yahbah Child"                 
##  [46] "Hussay Child"                  "Sosay Child (18 mths)"         "Varafoh Child (2 mths)"       
##  [49] "Coyee Child (2 mths)"          "Ahkayee Child (4 mths)"        "Nayjah Child (2 mths)"        
##  [52] "Acocoa Child"                  "Jubalay Child (8 mths)"        "Lajay Child"                  
##  [55] "Koyah Child (18 mths)"         "Ballay Child (9 mths)"         "Doe Child (15 mths)"          
##  [58] "Buboh Child (11 mths)"         "Lahdonay Child"                "Mahfeebee Child (8 mths)"     
##  [61] "Azoyah Child (3 mths)"         "Cumbah's Child (9 mths)"       "Nomeze's child"               
##  [64] "Ahfalladay Child (13 mths)"    "Yannoway Child"                "Addesay Child"                
##  [67] "Allahlee Child (8 mths)"       "Ahdonee Child"                 "Allahboloo Child"             
##  [70] "Allebo Child"                  "Eloh Child (14 mths)"          "Ajobee Child (18 mths)"       
##  [73] "Awosamee Child"                "Ahjolakay Child"               "Ahlukay Child"                
##  [76] "Maimahro Child"                "Ballabay's child"              "Chahbemah's child (9 mths)"   
##  [79] "Azofoukay's child (14 mth)"    "Hyenah's child (14 mths)"      "Banjukoo's child"             
##  [82] "Isayto's child"                "Kossaloo's child"              "Duballay's child"             
##  [85] "Ahneekay's child (6 mths)"     "Ahgassee's child"              "Ababee's child (11 mths)"     
##  [88] "Daday's child (16 mths)"       "Mahhesay's child (3 weeks)"    "Ahgassay's child"             
##  [91] "Ahkimfoo's child"              "Barquay Child (10 mths)"       "Allubah Child"                
##  [94] "Kachah Child (18 mths)"        "Bocah Child (3 mths)"          "Owee Child"                   
##  [97] "Olojoe Child (8 mths)"         "Ahgotay Child (6 mths)"        "Morai Child"                  
## [100] "Awooshue Child"                "Deffahkoa Child (9 mths)"      "Iyakay Child (10 mths)"       
## [103] "Osorombee Child"               "Odoobee Child (10 mths)"       "Oyahcowaday Child"            
## [106] "Abudahpoh's child"             "Yawahtoo's child (18 mths)"    "Bojarkalay's child"           
## [109] "Ingoom Child"                  "Mahdeah's child"               "Ahdowoosee's child"           
## [112] "Allahbah's child"              "Lahmee's child"                "Achabee's child"              
## [115] "Malakay's child"               "Chahpotoo's child"             "Lahtay's child"               
## [118] "Banday's child"                "Aforeyah's child"              "Enotom's child"               
## [121] "Abombolah's child"             "Mahgodolay's child"            "Maria's Infant"               
## [124] "Balank's Infant"               "Mariah's Infant (4 days)"      "Vilantah infant"              
## [127] "Olebee's Infant"               "Agoshay's Infant"              "Akedam's Infant (20 mths)"    
## [130] "Monno's Infant"                "Awosee's Infant"               "Akedam's Infant"              
## [133] "Malekee's Infant (8 mths)"     "Olyannee's Infant"             "Daday's Infant"               
## [136] "Obah's Infant"                 "Aquashee's Infant"             "Abbenebah's Infant"           
## [139] "Ontamay's Infant"              "Hyenah's Infant"               "Awoosoo's Infant"             
## [142] "Oloboh's Infant"               "Ahjai's Infant"                "Akedoh's Infant (15 mths)"    
## [145] "Bahboh's Infant"               "Kanmadefay's Infant (8mths)"   "Sahmagee's Infant (7 mths"    
## [148] "Akedang's Infant (16 mths"     "Arookaloo's Infant"            "Monesah's Infant"             
## [151] "Debahmechay's Infant (16m"     "Alokaday's Infant"             "Massadah's Infant"            
## [154] "Yajabee's Infant"              "Mahdasegee's Infant"           "Sebee's Infant"               
## [157] "Okolahday's Infant (19 mths)"  "Mollolah's Infant (14 mths)"   "Okedah's Infant (9 mths)"     
## [160] "Awoosoo/1st's Infant (8 mths)" "Ahdahsee's Infant"             "Ahdabo's Infant"              
## [163] "Awoosoo/2nd's Infant"          "Awoosoo/3rd's Infant"          "Afolahday's Infant"           
## [166] "Alahbah's Infant (22 mths)"    "Abenee's Infant (8mths)"       "Mallakee's Infant (18mths)"   
## [169] "Ajah's Infant (21mths)"        "Omoneloo's Infant (14mths)"    "Omotahday's Infant"           
## [172] "Ofogee's Infant (26mths)"      "Hyenah's Infant (14 mths)"     "Akahday's Infant (9 mths)"    
## [175] "Bonnah's Infant"               "Lahbosee's Infant (6 mths)"    "Ajumah's Infant (11 mths)"    
## [178] "Mochowah's Infant (16mths)"    "Iyelahday's Infant"            "Asayee's Infant"              
## [181] "Yahomee's Infant"              "Obay's Infant"                 "Bannahko's Infant"            
## [184] "Ahbinnebah's Infant (14mths)"  "Quazzy's Infant"               "Ranagon's Infant"             
## [187] "Anaque's Infant"               "Zovoh's Infant"                "Honquay's Infant"             
## [190] "Naloo's Infant"                "Ogonquai's Infant"             "Alokeway's Infant"            
## [193] "Ahbetah's Infant"              "Amafanah's Infant"             "Noway's Infant"               
## [196] "Ohonquay's Infant"             "Eccoah's Infant"               "Mahgeah's Infant"             
## [199] "Seyah's Infant"                "Balay's Infant"                "Mahboleday's Infant"          
## [202] "Boseyee's Infant"              "Abojatah's Infant"             "Mannah's Infant"              
## [205] "Ahdabemetoh's Infant"          "Iyoboo's Infant"               "Yahjoh's Infant"              
## [208] "Olah's Infant"                 "Akotahlah's Infant"            "Adoo's Infant"                
## [211] "Lahnoh's Infant"               "Hyelah's Infant (15days)"      "Akamedah's Infant"            
## [214] "Daeday's Infant"               "Sonarrah's Infant"             "Olahdaynea's Infant"          
## [217] "Dahmajae's Infant"             "Dahmajae's Infant"             "Dowoo's Infant (3mths)"       
## [220] "Yarroh Infant (9mths)"         "Amballay Infant (16mths)"      "Marwoyea's Infant"            
## [223] "Olokoe Infant"                 "Manolah Infant"                "Yarbenee Infant"              
## [226] "Ogedebah Infant"               "Kobaloo Infant"                "Iebo Infant"                  
## [229] "Palejoe Infant"                "Chekatay's Infant"             "Annekay Infant"               
## [232] "Solay Infant (9 days)"         "Dobay Infant"                  "Kopallarmee Infant"           
## [235] "Infant of Larmee"              "Accam Infant"                  "Ogoloo Infant"                
## [238] "Mattobee Infant"               "Arbee Infant"                  "Lamno Infant"                 
## [241] "Ekoballome Infant"             "Ianokay Infant"                "Amarpay Infant"               
## [244] "Ahjai Infant"                  "Bequai Infant"                 "Koomardoh's Infant"           
## [247] "Acbackbah's Infant"            "Ieray Infant"                  "Gambah Infant"                
## [250] "Yebonday Infant (3mths)"       "Yarpoh Infant (2 mths)"        "Sanay Infant (21 days)"       
## [253] "Dofoh Infant"                  "Gebonah Infant"                "Shuray's Infant (2 mths)"     
## [256] "Kie's Infant (3 mths)"         "Guyah's Infant (3 mths)"       "Lango's Infant (3 mths)"      
## [259] "Marmoh's Infant ( 1mth)"       "Tahfee's Infant (2 mths)"      "Yandefay's Infant (2 mths)"   
## [262] "Mary-ann (infant)"             "Yambah (infant)"               "Sam (infant)"                 
## [265] "Boango (infant)"               "Gomba (infant)"                "Bonah (infant)"               
## [268] "Choo (Infant)"                 "Amba's child"                  "Tallahmee's child"
```

### Appendix B   
#### Mother names  

```
##   [1] "Ababee"       "Abbenebah"    "Abenee"       "Abojatah"     "Abombolah"    "Abudahpoh"    "Acbackbah"   
##   [8] "Accam"        "Achabee"      "Acocoa"       "Addesay"      "Adingah"      "Adoo"         "Afolahday"   
##  [15] "Aforeyah"     "Agoshay"      "Ahbayboh"     "Ahbetah"      "Ahbinnebah"   "Ahdabemetoh"  "Ahdabo"      
##  [22] "Ahdahsee"     "Ahdonee"      "Ahdowoosee"   "Ahfalladay"   "Ahgassay"     "Ahgassee"     "Ahgotay"     
##  [29] "Ahjai"        "Ahjai"        "Ahjarme"      "Ahjolakay"    "Ahkayee"      "Ahkenhaybay"  "Ahkimfoo"    
##  [36] "Ahlukay"      "Ahneekay"     "Ahwarr"       "Ajah"         "Ajaiay"       "Ajobee"       "Ajumah"      
##  [43] "Akahday"      "Akamedah"     "Akedam"       "Akedam"       "Akedang"      "Akedoh"       "Akotahlah"   
##  [50] "Alahbah"      "Allahbah"     "Allahboloo"   "Allahlee"     "Allebo"       "Allubah"      "Alokaday"    
##  [57] "Alokeway"     "Amafanah"     "Amarpay"      "Amba"         "Amballay"     "Ammahpay"     "Anaque"      
##  [64] "Andonnahoe"   "Annekay"      "Aquashee"     "Aquepadoe"    "Arbee"        "Arookaloo"    "Arrahcholo"  
##  [71] "Arrayboo"     "Asayee"       "Asemma"       "Askua"        "Awoo"         "Awooshue"     "Awoosoo"     
##  [78] "Awoosoo/1st"  "Awoosoo/2nd"  "Awoosoo/3rd"  "Awosamee"     "Awosee"       "Azofoukay"    "Azoyah"      
##  [85] "Bahboh"       "Balank"       "Balay"        "Ballabay"     "Ballay"       "Banday"       "Banjukoo"    
##  [92] "Bannahko"     "Barquay"      "Bequai"       "Betardy"      "Boango"       "Bocah"        "Bojarkalay"  
##  [99] "Bonah"        "Bonnah"       "Boseyee"      "Bowsenee"     "Buboh"        "Bullay"       "Chahbemah"   
## [106] "Chahpotoo"    "Chekatay"     "Choo"         "Coyee"        "Cumbah"       "Daday"        "Daday"       
## [113] "Daeday"       "Dahmajae"     "Dahmajae"     "Daykussa"     "Debahmechay"  "Deffahkoa"    "Dobay"       
## [120] "Doe"          "Dofoh"        "Dohwommah"    "Dowoo"        "Duballay"     "Eccoah"       "Egga"        
## [127] "Ego"          "Ekoballome"   "Eloh"         "Enotom"       "Farsommah"    "Gambah"       "Gebonah"     
## [134] "Gomba"        "Guah"         "Guyah"        "Honquay"      "Hussay"       "Hyelah"       "Hyenah"      
## [141] "Hyenah"       "Hyenah"       "Hyenah"       "Ianokay"      "Iebo"         "Ieray"        "Ingoom"      
## [148] "Isayto"       "Iyakay"       "Iyelahday"    "Iyoboo"       "Janar"        "Jaysee"       "Jubalay"     
## [155] "Kachah"       "Kanmadefay"   "Kie"          "Kissee"       "Kobaloo"      "Koomardoh"    "Kopallarmee" 
## [162] "Kossaloo"     "Kossay"       "Koyah"        "Lahbosee"     "Lahdonay"     "Lahmee"       "Lahnoh"      
## [169] "Lahtay"       "Lajay"        "Lamno"        "Lango"        "Laque"        "Larmee"       "Lodday"      
## [176] "Mackah"       "Mahboleday"   "Mahdasegee"   "Mahdeah"      "Mahfeebee"    "Mahgeah"      "Mahgodolay"  
## [183] "Mahhesay"     "Maimahro"     "Malakay"      "Malekee"      "Mallakee"     "Mangah"       "Mannah"      
## [190] "Manolah"      "Maria"        "Mariah"       "Marmoh"       "Marwoyea"     "Mary-ann"     "Massadah"    
## [197] "Mattobee"     "Mochowah"     "Mollolah"     "Monesah"      "Monno"        "Morai"        "Naloo"       
## [204] "Nayee"        "Nayjah"       "Nomeze"       "Noway"        "Noyamme"      "Obah"         "Obay"        
## [211] "Odoobee"      "Odrayday"     "Ofogee"       "Ogedebah"     "Ogoloo"       "Ogonquai"     "Ohonquay"    
## [218] "Okedah"       "Okolahday"    "Olah"         "Olahdaynea"   "Olebee"       "Oloboh"       "Olojoe"      
## [225] "Olokoe"       "Olukaye"      "Olyannee"     "Omoneloo"     "Omotahday"    "Onekay"       "Ontamay"     
## [232] "Osorombee"    "Owee"         "Oyahcowaday"  "Palejoe"      "Ponger"       "Pyesor"       "Quazzy"      
## [239] "Ranagon"      "Raybaund"     "Sahmagee"     "Sam"          "Sanay"        "Sebee"        "Seyah"       
## [246] "Shuray"       "Solay"        "Sonarrah"     "Soolallahday" "Sosay"        "Tahfee"       "Tallahmee"   
## [253] "Toh"          "Varafoh"      "Vilantah"     "Yahbah"       "Yahjoh"       "Yahomee"      "Yajabee"     
## [260] "Yambah"       "Yandefay"     "Yannoway"     "Yarbenee"     "Yarpoh"       "Yarroh"       "Yartopay"    
## [267] "Yawahtoo"     "Yebonday"     "Zarrah"       "Zovoh"
```
  
### Appendix C  
#### Voyages with child-mother matches  

```
## Voyage: 2346  Shipname: Clara (a) Clarita  Year: 1825  Port: Gallinhas
##     voyageId childid               childname childage childsexage mothername motherage
## 243     2346    6587 Lodday's child (10mths)       NA         Boy     Lodday        24
## 
## Voyage: 2347  Shipname: Ana  Year: 1825  Port: Cameroons
##     voyageId childid       childname childage childsexage mothername motherage
## 257     2347    6709 Mangah's infant        1         Boy     Mangah        20
## 
## Voyage: 2367  Shipname: Ninfa Habanera  Year: 1825  Port: Bight of Benin, port unspecified
##     voyageId childid         childname childage childsexage mothername motherage
## 324     2367   91684 Tallahmee's child        3        Girl  Tallahmee        24
## 
## Voyage: 2382  Shipname: Paulita  Year: 1827  Port: Anomabu
##     voyageId childid             childname childage childsexage mothername motherage
## 100     2382    9247 Azoyah Child (3 mths)       NA        Girl     Azoyah        24
## 
## Voyage: 2396  Shipname: El Almirante  Year: 1829  Port: Lagos, Onim
##     voyageId childid       childname childage childsexage mothername motherage
## 292     2396   16783 Oloboh's Infant        3         Boy     Oloboh        23
## 
## Voyage: 2409  Shipname: Loreto (a) Corunera  Year: 1830  Port: Little Bassa
##     voyageId childid               childname childage childsexage mothername motherage
## 138     2409   22202            Dofoh Infant        3         Boy      Dofoh        24
## 150     2409   22192           Gambah Infant        3        Girl     Gambah        23
## 151     2409   22204          Gebonah Infant        3         Boy    Gebonah        22
## 208     2409   22190            Ieray Infant        2        Girl      Ieray        21
## 315     2409   22199  Sanay Infant (21 days)       NA         Boy      Sanay        25
## 335     2409   22197  Yarpoh Infant (2 mths)       NA         Boy     Yarpoh        24
## 339     2409   22195 Yebonday Infant (3mths)       NA        Girl   Yebonday        26
## 
## Voyage: 2414  Shipname: Nueva Isabelita (a) Numero Un  Year: 1830  Port: Grand Bassa
##     voyageId childid                childname childage childsexage mothername motherage
## 318     2414   23108 Shuray's Infant (2 mths)       NA        Girl     Shuray        28
## 
## Voyage: 2418  Shipname: Maria  Year: 1831  Port: Gallinhas
##     voyageId childid               childname childage childsexage mothername motherage
## 156     2418   23806 Guyah's Infant (3 mths)       NA        Girl      Guyah        20
## 220     2418   23715   Kie's Infant (3 mths)       NA         Boy        Kie        28
## 238     2418   23807 Lango's Infant (3 mths)       NA        Girl      Lango        25
## 239     2418   23807 Lango's Infant (3 mths)       NA        Girl      Lango        24
## 240     2418   23807 Lango's Infant (3 mths)       NA        Girl      Lango        28
## 
## Voyage: 2420  Shipname: Primera  Year: 1831  Port: Gallinhas
##     voyageId childid                  childname childage childsexage mothername motherage
## 263     2420   24062    Marmoh's Infant ( 1mth)       NA         Boy     Marmoh        26
## 323     2420   24116   Tahfee's Infant (2 mths)       NA        Girl     Tahfee        26
## 332     2420   24117 Yandefay's Infant (2 mths)       NA        Girl   Yandefay        30
## 
## Voyage: 2915  Shipname: Conceição de Marie  Year: 1823  Port: Whydah
##     voyageId childid         childname childage childsexage mothername motherage
## 313     2915    3830 Raybaund's Infant        1         Boy   Raybaund        26
## 
## Voyage: 2924  Shipname: Juliana da Praca  Year: 1822  Port: Porto Novo
##     voyageId childid       childname childage childsexage mothername motherage
## 11      2924    3658 Adingah's child        1      Female    Adingah        29
## 112     2924    3654 Betardy's child        1      Female    Betardy        26
## 
## Voyage: 2931  Shipname: Comerciante  Year: 1822  Port: Cameroons River
##     voyageId childid       childname childage childsexage mothername motherage
## 88      2931    3216  Askua's Infant        1      Female      Askua        22
## 307     2931    3218 Ponger's Infant        1      Female     Ponger        20
## 308     2931    3218 Ponger's Infant        1      Female     Ponger        19
## 309     2931    3218 Ponger's Infant        1      Female     Ponger        15
## 
## Voyage: 2935  Shipname: S António de Lisboã  Year: 1822  Port: Porto Novo
##     voyageId childid         childname childage childsexage mothername motherage
## 81      2935    3476 Aquepadoe's child        5         Boy  Aquepadoe        26
## 87      2935    3463    Asemma's child        1         Boy     Asemma        23
## 214     2935    3465     Janar's child        1         Boy      Janar        26
## 241     2935    3470     Laque's child        1         Boy      Laque        23
## 
## Voyage: 2946  Shipname: Avizo  Year: 1824  Port: Badagry
##     voyageId childid                  childname childage childsexage mothername motherage
## 49      2946    4931    Ahwarr (Infant 10 mths)       NA         Boy     Ahwarr        23
## 84      2946    4933 Arrahcholo (Infant 8 mths)       NA        Girl Arrahcholo        26
## 85      2946    4935   Arrayboo (Infant 8 mths)       NA         Boy   Arrayboo        27
## 280     2946    4929   Odrayday (Infant 9 mths)       NA         Boy   Odrayday        24
## 301     2946    4937    Onekay (Infant 10 mths)       NA        Girl     Onekay        24
## 
## Voyage: 2947  Shipname: Bella Eliza  Year: 1824  Port: Lagos, Onim
##     voyageId childid                  childname childage childsexage mothername motherage
## 144     2947    5270    Egga's infant (11 mths)       NA        Girl       Egga        28
## 297     2947    5272 Olukaye's infant (13 mths)       NA         Boy    Olukaye        26
## 
## Voyage: 2948  Shipname: Bom Fim  Year: 1825  Port: Lagos, Onim
##    voyageId childid                childname childage childsexage mothername motherage
## 51     2948    5510 Ajaiay's infant (10mths)       NA        Girl     Ajaiay        24
## 
## Voyage: 2952  Shipname: Bom Jesus dos Navigantes  Year: 1825  Port: Lagos, Onim
##     voyageId childid             childname childage childsexage mothername motherage
## 43      2952    5973       Ahjarme's child        1         Boy    Ahjarme        23
## 89      2952    5980 Awoo's child (11mths)       NA         Boy       Awoo        26
## 145     2952    5978  Ego's child (11mths)       NA      Female        Ego        24
## 
## Voyage: 2954  Shipname: Paquete de Bahia  Year: 1825  Port: Whydah
##     voyageId childid                  childname childage childsexage mothername motherage
## 77      2954    7052 Ammahpay's child (10 mths)       NA         Boy   Ammahpay        24
## 118     2954    6956           Bowsenee's child        1         Boy   Bowsenee        25
## 149     2954    7060  Farsommah's child (7mths)       NA        Girl  Farsommah        26
## 161     2954    7062     Hyenah's child (4mths)       NA        Girl     Hyenah        25
## 162     2954    7062     Hyenah's child (4mths)       NA        Girl     Hyenah        16
## 215     2954    6962    Jaysee's child (3 mths)       NA        Girl     Jaysee        25
## 226     2954    7024   Kossay's child (10 mths)       NA        Girl     Kossay        27
## 272     2954    6966     Nayee's child (6 mths)       NA         Boy      Nayee        22
## 276     2954    7054    Noyamme's child (8mths)       NA         Boy    Noyamme        24
## 310     2954    6958             Pyesor's child        1         Boy     Pyesor        24
## 325     2954    6974       Toh's child (6 mths)       NA        Girl        Toh        26
## 337     2954    7057  Yartopay's child (10mths)       NA         Boy   Yartopay        26
## 340     2954    7064     Zarrah's child (6mths)       NA         Boy     Zarrah        25
## 
## Voyage: 2955  Shipname: San Joao (a) Segunda Rosalia  Year: 1826  Port: Lagos, Onim
##     voyageId childid                childname childage childsexage mothername motherage
## 18      2955    7932 Ahbayboh's child (8mths)       NA         Boy   Ahbayboh        24
## 133     2955    7934         Daykussa's child        1         Boy   Daykussa        25
## 
## Voyage: 2957  Shipname: Netuno  Year: 1826  Port: Anomabu
##     voyageId childid                childname childage childsexage mothername motherage
## 244     2957    8032 Mackah's child (10 mths)       NA        Girl     Mackah        28
## 
## Voyage: 2965  Shipname: Principe de Guinea  Year: 1826  Port: Whydah
##     voyageId childid                childname childage childsexage mothername motherage
## 9       2965    8649             Acocoa Child        1        Girl     Acocoa        36
## 45      2965    8645   Ahkayee Child (4 mths)       NA        Girl    Ahkayee        35
## 105     2965    8662    Ballay Child (9 mths)       NA        Girl     Ballay        35
## 106     2965    8662    Ballay Child (9 mths)       NA        Girl     Ballay        21
## 119     2965    8670    Buboh Child (11 mths)       NA        Girl      Buboh        40
## 120     2965    8633    Bullay Child (4 mths)       NA        Girl     Bullay        24
## 126     2965    8643     Coyee Child (2 mths)       NA        Girl      Coyee        29
## 137     2965    8665      Doe Child (15 mths)       NA        Girl        Doe        36
## 154     2965    8631               Guah Child        1         Boy       Guah        33
## 158     2965    8637             Hussay Child        3        Girl     Hussay        28
## 216     2965    8651   Jubalay Child (8 mths)       NA        Girl    Jubalay        28
## 222     2965    8629             Kissee Child        1         Boy     Kissee        28
## 228     2965    8660    Koyah Child (18 mths)       NA         Boy      Koyah        40
## 230     2965    8674           Lahdonay Child        2         Boy   Lahdonay        27
## 236     2965    8657              Lajay Child        1        Girl      Lajay        31
## 248     2965    8676 Mahfeebee Child (8 mths)       NA         Boy  Mahfeebee        31
## 273     2965    8647    Nayjah Child (2 mths)       NA         Boy     Nayjah        29
## 322     2965    8639    Sosay Child (18 mths)       NA        Girl      Sosay        29
## 326     2965    8641   Varafoh Child (2 mths)       NA        Girl    Varafoh        28
## 328     2965    8635             Yahbah Child        2        Girl     Yahbah        29
## 
## Voyage: 2968  Shipname: Venus  Year: 1827  Port: Whydah
##     voyageId childid                  childname childage childsexage mothername motherage
## 10      2968   10300              Addesay Child        2         Boy    Addesay        32
## 24      2968   10304              Ahdonee Child        3         Boy    Ahdonee        25
## 26      2968   10296 Ahfalladay Child (13 mths)       NA        Girl Ahfalladay        29
## 44      2968   10316            Ahjolakay Child        2         Boy  Ahjolakay        23
## 47      2968   10318              Ahlukay Child        3         Boy    Ahlukay        29
## 52      2968   10312     Ajobee Child (18 mths)       NA        Girl     Ajobee        25
## 66      2968   10306           Allahboloo Child        3        Girl Allahboloo        35
## 67      2968   10302    Allahlee Child (8 mths)       NA        Girl   Allahlee        27
## 68      2968   10308               Allebo Child        2        Girl     Allebo        22
## 95      2968   10314             Awosamee Child        2        Girl   Awosamee        31
## 147     2968   10310       Eloh Child (14 mths)       NA         Boy       Eloh        27
## 253     2968   10320             Maimahro Child        2         Boy   Maimahro        27
## 333     2968   10298             Yannoway Child        2        Girl   Yannoway        27
## 
## Voyage: 2969  Shipname: Invincival  Year: 1827  Port: Cameroons River
##     voyageId childid               childname childage childsexage mothername motherage
## 127     2969    9760 Cumbah's Child (9 mths)       NA         Boy     Cumbah        27
## 274     2969    9763          Nomeze's child        2         Boy     Nomeze        34
## 
## Voyage: 2970  Shipname: Dos Amigos  Year: 1827  Port: Badagry
##     voyageId childid                  childname childage childsexage mothername motherage
## 48      2970   10569  Ahneekay's child (6 mths)       NA        Girl   Ahneekay        28
## 99      2970   10552 Azofoukay's child (14 mth)       NA        Girl  Azofoukay        26
## 104     2970   10548           Ballabay's child        2        Girl   Ballabay        28
## 108     2970   10559           Banjukoo's child        1        Girl   Banjukoo        24
## 121     2970   10550 Chahbemah's child (9 mths)       NA        Girl  Chahbemah        20
## 142     2970   10567           Duballay's child        1        Girl   Duballay        29
## 166     2970   10555   Hyenah's child (14 mths)       NA        Girl     Hyenah        28
## 167     2970   10555   Hyenah's child (14 mths)       NA        Girl     Hyenah        26
## 170     2970   10555   Hyenah's child (14 mths)       NA        Girl     Hyenah        22
## 172     2970   10555   Hyenah's child (14 mths)       NA        Girl     Hyenah        20
## 174     2970   10555   Hyenah's child (14 mths)       NA        Girl     Hyenah        24
## 176     2970   10555   Hyenah's child (14 mths)       NA        Girl     Hyenah        23
## 178     2970   10555   Hyenah's child (14 mths)       NA        Girl     Hyenah        24
## 179     2970   10555   Hyenah's child (14 mths)       NA        Girl     Hyenah        19
## 210     2970   10561             Isayto's child        1         Boy     Isayto        26
## 225     2970   10563           Kossaloo's child        3        Girl   Kossaloo        27
## 
## Voyage: 2977  Shipname: Conceiçao de Maria  Year: 1827  Port: Whydah
##     voyageId childid                  childname childage childsexage mothername motherage
## 1       2977   10761   Ababee's child (11 mths)       NA         Boy     Ababee        29
## 27      2977   10768           Ahgassay's child        2         Boy   Ahgassay        30
## 28      2977   10768           Ahgassay's child        2         Boy   Ahgassay        26
## 29      2977   10759           Ahgassee's child        2         Boy   Ahgassee        26
## 46      2977   10771           Ahkimfoo's child        2        Girl   Ahkimfoo        32
## 69      2977   10812              Allubah Child        1         Boy    Allubah        31
## 70      2977   10812              Allubah Child        1         Boy    Allubah        19
## 71      2977   10812              Allubah Child        1         Boy    Allubah        24
## 110     2977   10810    Barquay Child (10 mths)       NA         Boy    Barquay        26
## 128     2977   10763    Daday's child (16 mths)       NA         Boy      Daday        31
## 217     2977   10814     Kachah Child (18 mths)       NA         Boy     Kachah        28
## 251     2977   10766 Mahhesay's child (3 weeks)       NA         Boy   Mahhesay        27
## 252     2977   10766 Mahhesay's child (3 weeks)       NA         Boy   Mahhesay        27
## 
## Voyage: 2983  Shipname: Henriquetta  Year: 1827  Port: Lagos, Onim
##     voyageId childid              childname childage childsexage mothername motherage
## 30      2983   11750 Ahgotay Child (6 mths)       NA        Girl    Ahgotay        28
## 270     2983   11752            Morai Child        1         Boy      Morai        25
## 293     2983   11748  Olojoe Child (8 mths)       NA        Girl     Olojoe        25
## 304     2983   11746             Owee Child        2         Boy       Owee        27
## 
## Voyage: 2998  Shipname: Nova Virgem  Year: 1828  Port: Bight of Benin, port unspecified
##     voyageId childid                  childname childage childsexage mothername motherage
## 6       2998   13342          Abudahpoh's child        2        Girl  Abudahpoh        26
## 114     2998   13346         Bojarkalay's child        1         Boy Bojarkalay        23
## 338     2998   13344 Yawahtoo's child (18 mths)       NA         Boy   Yawahtoo        23
## 
## Voyage: 2999  Shipname: Clementina  Year: 1828  Port: Cameroons River
##     voyageId childid    childname childage childsexage mothername motherage
## 209     2999   13571 Ingoom Child       NA         Boy     Ingoom        25
## 
## Voyage: 3007  Shipname: Zepherina  Year: 1828  Port: Whydah
##     voyageId childid          childname childage childsexage mothername motherage
## 8       3007   14251    Achabee's child        2         Boy    Achabee        26
## 25      3007   14245 Ahdowoosee's child        2         Boy Ahdowoosee        26
## 65      3007   14247   Allahbah's child        3        Girl   Allahbah        36
## 231     3007   14249     Lahmee's child        2        Girl     Lahmee        19
## 232     3007   14249     Lahmee's child        2        Girl     Lahmee        18
## 233     3007   14249     Lahmee's child        2        Girl     Lahmee        30
## 247     3007   14242    Mahdeah's child        1        Girl    Mahdeah        24
## 254     3007   14253    Malakay's child        2        Girl    Malakay        30
## 
## Voyage: 3008  Shipname: Penha de Franco  Year: 1828  Port: Lagos, Onim
##     voyageId childid          childname childage childsexage mothername motherage
## 5       3008   14434  Abombolah's child        2        Girl  Abombolah        27
## 16      3008   14430   Aforeyah's child        4        Girl   Aforeyah        38
## 107     3008   14428     Banday's child        4        Girl     Banday        33
## 122     3008   14424  Chahpotoo's child        3         Boy  Chahpotoo        37
## 148     3008   14432     Enotom's child        3         Boy     Enotom        28
## 235     3008   14426     Lahtay's child        2         Boy     Lahtay        26
## 250     3008   14436 Mahgodolay's child        2         Boy Mahgodolay        31
## 
## Voyage: 3012  Shipname: Vingador  Year: 1828  Port: Lagos, Onim
##     voyageId childid                childname childage childsexage  mothername motherage
## 90      3012   12782           Awooshue Child        1         Boy    Awooshue        22
## 135     3012   12784 Deffahkoa Child (9 mths)       NA         Boy   Deffahkoa        25
## 211     3012   12786   Iyakay Child (10 mths)       NA         Boy      Iyakay        24
## 279     3012   12800  Odoobee Child (10 mths)       NA         Boy     Odoobee        23
## 303     3012   12788          Osorombee Child        3         Boy   Osorombee        23
## 305     3012   12802        Oyahcowaday Child        2        Girl Oyahcowaday        25
## 
## Voyage: 3013  Shipname: Vingador  Year: 1829  Port: Cacheu
##     voyageId childid                childname childage childsexage mothername motherage
## 102     3013   16079          Balank's Infant        2         Boy     Balank        21
## 260     3013   16077           Maria's Infant        2        Girl      Maria        17
## 262     3013   16081 Mariah's Infant (4 days)       NA         Boy     Mariah        16
## 
## Voyage: 3014  Shipname: Hosse  Year: 1829  Port: Whydah
##     voyageId childid                    childname childage childsexage mothername motherage
## 19      3014   18294             Ahbetah's Infant        0         Boy    Ahbetah        24
## 20      3014   18276 Ahbinnebah's Infant (14mths)       NA        Girl Ahbinnebah        24
## 73      3014   18292            Alokeway's Infant        2         Boy   Alokeway        39
## 74      3014   18296            Amafanah's Infant        1        Girl   Amafanah        29
## 78      3014   18282              Anaque's Infant        2        Girl     Anaque        22
## 86      3014   18268              Asayee's Infant        2         Boy     Asayee        26
## 109     3014   18274            Bannahko's Infant        2        Girl   Bannahko        24
## 143     3014   18362              Eccoah's Infant        2        Girl     Eccoah        30
## 157     3014   18286             Honquay's Infant        2         Boy    Honquay        22
## 212     3014   18266           Iyelahday's Infant        2        Girl  Iyelahday        28
## 267     3014   18264   Mochowah's Infant (16mths)       NA         Boy   Mochowah        25
## 271     3014   18288               Naloo's Infant        2         Boy      Naloo        25
## 275     3014   18298               Noway's Infant        2        Girl      Noway        23
## 278     3014   18272                Obay's Infant        2        Girl       Obay        21
## 284     3014   18290            Ogonquai's Infant        2         Boy   Ogonquai        22
## 285     3014   18300            Ohonquay's Infant        2         Boy   Ohonquay        38
## 286     3014   18300            Ohonquay's Infant        2         Boy   Ohonquay        25
## 311     3014   18278              Quazzy's Infant        3         Boy     Quazzy        37
## 312     3014   18280             Ranagon's Infant        2        Girl    Ranagon        20
## 330     3014   18270             Yahomee's Infant        2         Boy    Yahomee        32
## 341     3014   18284               Zovoh's Infant        1        Girl      Zovoh        26
## 
## Voyage: 3017  Shipname: Uniao  Year: 1829  Port: Lagos, Onim
##     voyageId childid                 childname childage childsexage mothername motherage
## 2       3017   16338        Abbenebah's Infant        3        Girl  Abbenebah        25
## 17      3017   16318          Agoshay's Infant        2         Boy    Agoshay        30
## 57      3017   16320 Akedam's Infant (20 mths)       NA        Girl     Akedam        19
## 58      3017   16320 Akedam's Infant (20 mths)       NA        Girl     Akedam        24
## 59      3017   16326           Akedam's Infant        3        Girl     Akedam        19
## 60      3017   16326           Akedam's Infant        3        Girl     Akedam        24
## 80      3017   16336         Aquashee's Infant        2        Girl   Aquashee        32
## 91      3017   16344          Awoosoo's Infant        3         Boy    Awoosoo        35
## 96      3017   16324           Awosee's Infant        2         Boy     Awosee        23
## 98      3017   16324           Awosee's Infant        2         Boy     Awosee        26
## 129     3017   16332            Daday's Infant        3        Girl      Daday        29
## 181     3017   16342           Hyenah's Infant        4         Boy     Hyenah        32
## 182     3017   16342           Hyenah's Infant        4         Boy     Hyenah        26
## 185     3017   16342           Hyenah's Infant        4         Boy     Hyenah        34
## 186     3017   16342           Hyenah's Infant        4         Boy     Hyenah        26
## 187     3017   16342           Hyenah's Infant        4         Boy     Hyenah        23
## 189     3017   16342           Hyenah's Infant        4         Boy     Hyenah        33
## 190     3017   16342           Hyenah's Infant        4         Boy     Hyenah        14
## 255     3017   16328 Malekee's Infant (8 mths)       NA        Girl    Malekee        20
## 277     3017   16334             Obah's Infant        2         Boy       Obah        27
## 291     3017   16315           Olebee's Infant        3         Boy     Olebee        35
## 298     3017   16330         Olyannee's Infant        2        Girl   Olyannee        31
## 302     3017   16340          Ontamay's Infant        3        Girl    Ontamay        23
## 
## Voyage: 3019  Shipname: Donna Barbara  Year: 1829  Port: Lagos, Onim
##     voyageId childid                   childname childage childsexage  mothername motherage
## 31      3019   17078              Ahjai's Infant        2        Girl       Ahjai        23
## 34      3019   17078              Ahjai's Infant        2        Girl       Ahjai        25
## 36      3019   17078              Ahjai's Infant        2        Girl       Ahjai        31
## 61      3019   17088   Akedang's Infant (16 mths       NA         Boy     Akedang        24
## 62      3019   17080   Akedoh's Infant (15 mths)       NA         Boy      Akedoh        26
## 72      3019   17096           Alokaday's Infant        2        Girl    Alokaday        23
## 83      3019   17090          Arookaloo's Infant        2         Boy   Arookaloo        26
## 101     3019   17082             Bahboh's Infant        0         Boy      Bahboh        29
## 134     3019   17094   Debahmechay's Infant (16m       NA         Boy Debahmechay        22
## 218     3019   17084 Kanmadefay's Infant (8mths)       NA         Boy  Kanmadefay        26
## 246     3019   17102         Mahdasegee's Infant        3         Boy  Mahdasegee        36
## 265     3019   17098           Massadah's Infant        2         Boy    Massadah        31
## 269     3019   17092            Monesah's Infant        2         Boy     Monesah        27
## 314     3019   17086   Sahmagee's Infant (7 mths       NA        Girl    Sahmagee        28
## 316     3019   17104              Sebee's Infant        2         Boy       Sebee        29
## 331     3019   17100            Yajabee's Infant        2         Boy     Yajabee        25
## 
## Voyage: 3020  Shipname: Carolina  Year: 1829  Port: Lagos, Onim
##     voyageId childid                     childname childage childsexage  mothername motherage
## 3       3020   17427       Abenee's Infant (8mths)       NA        Girl      Abenee        19
## 15      3020   17423            Afolahday's Infant        1        Girl   Afolahday        28
## 22      3020   17417               Ahdabo's Infant        2         Boy      Ahdabo        27
## 23      3020   17415             Ahdahsee's Infant        3        Girl    Ahdahsee        26
## 50      3020   17431        Ajah's Infant (21mths)       NA         Boy        Ajah        31
## 53      3020   17449     Ajumah's Infant (11 mths)       NA        Girl      Ajumah        27
## 54      3020   17449     Ajumah's Infant (11 mths)       NA        Girl      Ajumah        29
## 55      3020   17443     Akahday's Infant (9 mths)       NA        Girl     Akahday        29
## 64      3020   17425    Alahbah's Infant (22 mths)       NA         Boy     Alahbah        42
## 92      3020   17413 Awoosoo/1st's Infant (8 mths)       NA         Boy Awoosoo/1st        36
## 93      3020   17419          Awoosoo/2nd's Infant        2        Girl Awoosoo/2nd        29
## 94      3020   17421          Awoosoo/3rd's Infant        2        Girl Awoosoo/3rd        26
## 116     3020   17445               Bonnah's Infant        2         Boy      Bonnah        33
## 192     3020   17441     Hyenah's Infant (14 mths)       NA        Girl      Hyenah        24
## 195     3020   17441     Hyenah's Infant (14 mths)       NA        Girl      Hyenah        27
## 199     3020   17441     Hyenah's Infant (14 mths)       NA        Girl      Hyenah        17
## 200     3020   17441     Hyenah's Infant (14 mths)       NA        Girl      Hyenah        24
## 203     3020   17441     Hyenah's Infant (14 mths)       NA        Girl      Hyenah        34
## 204     3020   17441     Hyenah's Infant (14 mths)       NA        Girl      Hyenah        34
## 229     3020   17447    Lahbosee's Infant (6 mths)       NA         Boy    Lahbosee        38
## 256     3020   17429    Mallakee's Infant (18mths)       NA        Girl    Mallakee        22
## 268     3020   17409   Mollolah's Infant (14 mths)       NA        Girl    Mollolah        29
## 281     3020   17437      Ofogee's Infant (26mths)       NA         Boy      Ofogee        25
## 287     3020   17411      Okedah's Infant (9 mths)       NA        Girl      Okedah        33
## 288     3020   17407  Okolahday's Infant (19 mths)       NA        Girl   Okolahday        20
## 299     3020   17433    Omoneloo's Infant (14mths)       NA        Girl    Omoneloo        23
## 300     3020   17435            Omotahday's Infant        2         Boy   Omotahday        31
## 
## Voyage: 3023  Shipname: Emilia  Year: 1829  Port: Lagos, Onim
##     voyageId childid                childname childage childsexage  mothername motherage
## 4       3023   18939        Abojatah's Infant        2        Girl    Abojatah        25
## 13      3023   18953            Adoo's Infant        2        Girl        Adoo        24
## 14      3023   18953            Adoo's Infant        2        Girl        Adoo        24
## 21      3023   18943     Ahdabemetoh's Infant        2        Girl Ahdabemetoh        24
## 63      3023   18951       Akotahlah's Infant        2         Boy   Akotahlah        25
## 103     3023   18933           Balay's Infant        3        Girl       Balay        28
## 117     3023   18937         Boseyee's Infant        3        Girl     Boseyee        29
## 159     3023   18957 Hyelah's Infant (15days)       NA        Girl      Hyelah        18
## 213     3023   18945          Iyoboo's Infant        2        Girl      Iyoboo        29
## 234     3023   18955          Lahnoh's Infant        2        Girl      Lahnoh        30
## 245     3023   18935      Mahboleday's Infant        2        Girl  Mahboleday        33
## 249     3023   18929         Mahgeah's Infant        2         Boy     Mahgeah        34
## 258     3023   18941          Mannah's Infant        1         Boy      Mannah        28
## 289     3023   18949            Olah's Infant        3         Boy        Olah        27
## 317     3023   18931           Seyah's Infant        2         Boy       Seyah        26
## 329     3023   18947          Yahjoh's Infant        0         Boy      Yahjoh        19
## 
## Voyage: 3024  Shipname: Santo Iago or Santo Jago  Year: 1829  Port: Whydah
##     voyageId childid              childname childage childsexage mothername motherage
## 130     3024   19414        Daeday's Infant        2         Boy     Daeday        22
## 131     3024   19421      Dahmajae's Infant        0        Girl   Dahmajae        28
## 132     3024   19420      Dahmajae's Infant        2        Girl   Dahmajae        28
## 140     3024   19423 Dowoo's Infant (3mths)       NA        Girl      Dowoo        29
## 290     3024   19418    Olahdaynea's Infant        1         Boy Olahdaynea        29
## 321     3024   19416      Sonarrah's Infant        2         Boy   Sonarrah        20
## 
## Voyage: 3026  Shipname: Tentadora  Year: 1829  Port: Lagos, Onim
##     voyageId childid                childname childage childsexage mothername motherage
## 76      3026   20131 Amballay Infant (16mths)       NA        Girl   Amballay        22
## 223     3026   20147           Kobaloo Infant        3         Boy    Kobaloo        29
## 259     3026   20137           Manolah Infant        2         Boy    Manolah        32
## 264     3026   20133        Marwoyea's Infant        2        Girl   Marwoyea        27
## 282     3026   20144          Ogedebah Infant        2         Boy   Ogedebah        24
## 296     3026   20135            Olokoe Infant        2        Girl     Olokoe        29
## 334     3026   20139          Yarbenee Infant        2        Girl   Yarbenee        24
## 336     3026   20129    Yarroh Infant (9mths)       NA        Girl     Yarroh        26
## 
## Voyage: 3027  Shipname: Emilia  Year: 1830  Port: Whydah
##     voyageId childid             childname childage childsexage mothername motherage
## 79      3027   20568        Annekay Infant        2        Girl    Annekay        21
## 207     3027   20563           Iebo Infant        2        Girl       Iebo        24
## 306     3027   20565        Palejoe Infant        2         Boy    Palejoe        22
## 320     3027   20572 Solay Infant (9 days)       NA        Girl      Solay        26
## 
## Voyage: 3028  Shipname: Nao Lendia  Year: 1830  Port: Lagos, Onim
##     voyageId childid          childname childage childsexage  mothername motherage
## 136     3028   20710       Dobay Infant        1         Boy       Dobay        29
## 224     3028   20712 Kopallarmee Infant        2         Boy Kopallarmee        24
## 242     3028   20749   Infant of Larmee        2         Boy      Larmee        28
## 
## Voyage: 3029  Shipname: Nossa Senhora da Guia  Year: 1830  Port: Lagos, Onim
##   voyageId childid    childname childage childsexage mothername motherage
## 7     3029   21512 Accam Infant        2         Boy      Accam        27
## 
## Voyage: 3030  Shipname: Primeira Rosalia  Year: 1830  Port: Lagos, Onim
##     voyageId childid         childname childage childsexage mothername motherage
## 38      3030   21741      Ahjai Infant        1         Boy      Ahjai        17
## 39      3030   21741      Ahjai Infant        1         Boy      Ahjai        31
## 40      3030   21741      Ahjai Infant        1         Boy      Ahjai        17
## 41      3030   21741      Ahjai Infant        1         Boy      Ahjai        20
## 75      3030   21739    Amarpay Infant        1        Girl    Amarpay        20
## 82      3030   21731      Arbee Infant        1         Boy      Arbee        28
## 146     3030   21735 Ekoballome Infant        2        Girl Ekoballome        26
## 206     3030   21737    Ianokay Infant        1         Boy    Ianokay        22
## 237     3030   21733      Lamno Infant        2         Boy      Lamno        24
## 266     3030   21729   Mattobee Infant        1         Boy   Mattobee        20
## 283     3030   21727     Ogoloo Infant        1        Girl     Ogoloo        23
## 
## Voyage: 3032  Shipname: Nova Resolucao  Year: 1830  Port: Keta
##     voyageId childid     childname childage childsexage mothername motherage
## 111     3032   22094 Bequai Infant        1        Girl     Bequai        24
## 
## Voyage: 3091  Shipname: Jacuhy  Year: 1839  Port: Nova Redonda
##     voyageId childid      childname childage childsexage mothername motherage
## 115     3091   50214 Bonah (infant)       NA        Girl      Bonah        29
## 
## Voyage: 59001  Shipname: Toninha  Year: 1827  Port: Bissau
##     voyageId childid            childname childage childsexage mothername motherage
## 113    59001   11384 Bocah Child (3 mths)       NA         Boy      Bocah        24
## 
## Voyage: 59014  Shipname: Aurelia  Year: 1829  Port: Cacheu
##     voyageId childid       childname childage childsexage mothername motherage
## 327    59014   16195 Vilantah infant        2        Girl   Vilantah        18
```

