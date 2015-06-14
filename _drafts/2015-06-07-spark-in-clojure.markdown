---
layout: post
title: "Spark in Clojure"
date: 2015-06-07 19:31
comments: true
categories: [clojure, big data, spark, sql]
sharing: true
published: true
---

> Apache Spark is a fast and general engine for large-scale data processing.

> 100 times faster than Hadoop.

Everyone knows SQL.
But traditional databases are not good in hadling *big* amount of *data*.
Nevertheless, SQL is a good DSL for data processing and
it is much easier to understand Spark if you have similar query implemented in SQL.

This article shows how common SQL queries implemented in Spark.

<!-- more -->

# Spark Intro

Few words about Spark.

It is written in Scala, but contains bindings for
[Java](https://spark.apache.org/docs/latest/api/java/index.html),
[Python](https://spark.apache.org/docs/latest/api/python/index.html)
and [R](https://spark.apache.org/docs/latest/api/R/index.html).

Other guys created bindings for [clojure](https://github.com/gorillalabs/sparkling),
it is not the official one, but the one we will use.

The main Spark abstraction is **RDD** (_Resilient Distributed Dataset_),
in clojure terms it is a lazy sequence distributed over machines.

You can apply **transformations** to RDD. Something, like `map` and `filter`, they are lazy, the same as analogues in clojure.

You can perform **actions** on RDD, `count`, `reduce`, etc.
They are not lazy, and this time calculation happens.

# Prepare Data

I like drinks and as example dataset we will take 
[Iowa Liquor Sales](https://gist.github.com/ecneladis/9ce229173e67e621f927)
dataset.

## Download (~800Mb)

``` bash
curl https://data.iowa.gov/api/views/m3tr-qhgy/rows.csv\?accessType\=DOWNLOAD -o iowa-liquor.csv
```

## Preprocess

``` bash
cat iowa-liquor.csv \
    | awk '(NR-1)%2{$1=$1}1' RS=\" ORS=\" \
    | sed -E "s#([0-9]{2})/([0-9]{2})/([0-9]{4})#\3-\1-\2#" \
	| tr -d '$' \
	| sed '$ d' \
	| tail -n +2 \
	> iowa_liquor_sales_fixed.csv
```

This preprocessing step is just for the sake of simplicity and avoid a lot of hell dealing with data.
Magic drops the header, redundant newline characters (which is inside double quotes),
convert dates and removes dollar sign from money fields.

## Import to Postgres

Create table structure

``` sql
CREATE TABLE iowa_liquor_sales (
  "DATE" date,
  "CONVENIENCE STORE" varchar,
  "STORE" varchar,
  "NAME" varchar,
  "ADDRESS" varchar,
  "CITY" varchar,
  "ZIPCODE" varchar,
  "STORE LOCATION" varchar,
  "COUNTY NUMBER" varchar,
  "COUNTY" varchar,
  "CATEGORY" varchar,
  "CATEGORY NAME" varchar,
  "VENDOR NO" varchar,
  "VENDOR" varchar,
  "ITEM" varchar,
  "DESCRIPTION" varchar,
  "PACK" numeric,
  "LITER SIZE" numeric,
  "STATE BTL COST" numeric,
  "BTL PRICE" numeric,
  "BOTTLE QTY" numeric,
  "TOTAL" numeric
)
```

and import from CSV

``` sql
COPY iowa_liquor_sales 
FROM '/absolute/path/to/iowa_liquor_sales_fixed.csv' 
DELIMITER AS ',' CSV;
```

Be patient, it will take a minute.

## Upload to HDFS


``` bash
hadoop fs -put iowa_liquor_sales_fixed.csv hdfs://localhost:9000/data/iowa_liquor_sales_fixed.csv
```

## Configure Spark

Setup spark-context, it's our working horse.

``` clojure
(defonce sc (-> (conf/spark-conf)
                (conf/app-name "SQL vs Spark")
                (conf/master "local")
                (conf/set "spark.driver.allowMultipleContexts" "true")
                (spark/spark-context)))
```

Specify path to CSV on HDFS

``` clojure
(def iowa-liquor-sales "hdfs://localhost:9000/data/iowa_liquor_sales.csv")
```

From spark perspective, csv is just a plain file,
and we need additional mapping to refer column by names

``` clojure
(def name->index
  {"DATE"                0
   "CONVENIENCE STORE"   1
   "STORE"               2
   "NAME"                3
   "ADDRESS"             4
   "CITY"                5
   "ZIPCODE"             6
   "STORE LOCATION"      7
   "COUNTY NUMBER"       8
   "COUNTY"              9
   "CATEGORY"            10
   "CATEGORY NAME"       11
   "VENDOR NO"           12
   "VENDOR"              13
   "ITEM"                14
   "DESCRIPTION"         15
   "PACK"                16
   "LITER SIZE"          17
   "STATE BTL COST"      18
   "BTL PRICE"           19
   "BOTTLE QTY"          20
   "TOTAL"               21})
```
   
**Note:** We can keep header in file on HDFS and build this mapping in runtime,
but keeping data and metadata separatly is a good practice and this way we avoid a lot of pain.

And the last piece, we want a function to parse one csv line into vector

``` clojure
(defn csv-read-line [line]
  (first (csv/read-csv line)))
```

Let's start.

# SQL vs Spark

## 1. Select Star

Get a sense what data are you working with.

SQL

``` sql
select * from iowa_liquor_sales
```

Spark

``` clojure
(->> (spark/text-file sc iowa-liquor-sales)
     (spark/map csv-read-line)
     (spark/collect))
```

Result

```
Query is running...
```

## 2. Count

Ouch, the query hangs. Seems we have a lot of data.
Return the total number of rows.

SQL

``` sql
select count(*) from iowa_liquor_sales
```

Spark

``` clojure
(->> (spark/text-file sc iowa-liquor-sales)
     (spark/count))
```

Result

```
3049913
```

## 3. Limit

There are over 3 million rows, inspect only 5 of them.

SQL

``` sql
select * from iowa_liquor_sales limit 5
```

Spark

``` clojure
(->> (spark/text-file sc iowa-liquor-sales)
     (spark/map csv-read-line)
     (spark/take 5))
```

Result

```
╔════════════╦══╦══════╦═══════════════════════════════════╦════════════════╦════════════╦═══════╦═══════════════════════════════════════════════════════════════════════════╦════╦═════════════╦═════════╦═════════════════╦═════╦════════════════════════════╦═══════╦════════════════════════╦════╦══════╦═══════╦═══════╦════╦════════╗
║ 2015-02-26 ║  ║ 2515 ║ Hy-Vee Food Store #1 / Mason City ║ 2400 4TH ST SW ║ MASON CITY ║ 50401 ║ 2400 4TH ST SW MASON CITY 50401 (43.148463097000047, -93.236272961999987) ║ 17 ║ Cerro Gordo ║ 1022100 ║ TEQUILA         ║ 434 ║ Luxco-St Louis             ║ 87937 ║ Juarez Tequila Silver  ║ 12 ║ 1000 ║ 6.92  ║ 10.38 ║ 48 ║ 498.24 ║
║ 2015-02-26 ║  ║ 2515 ║ Hy-Vee Food Store #1 / Mason City ║ 2400 4TH ST SW ║ MASON CITY ║ 50401 ║ 2400 4TH ST SW MASON CITY 50401 (43.148463097000047, -93.236272961999987) ║ 17 ║ Cerro Gordo ║ 1022100 ║ TEQUILA         ║ 434 ║ Luxco-St Louis             ║ 87938 ║ Juarez Tequila Silver  ║  6 ║ 1750 ║ 13.50 ║ 20.25 ║  6 ║ 121.50 ║
║ 2015-02-26 ║  ║ 2515 ║ Hy-Vee Food Store #1 / Mason City ║ 2400 4TH ST SW ║ MASON CITY ║ 50401 ║ 2400 4TH ST SW MASON CITY 50401 (43.148463097000047, -93.236272961999987) ║ 17 ║ Cerro Gordo ║ 1012200 ║ SCOTCH WHISKIES ║  55 ║ Sazerac North America      ║  8824 ║ Lauder's               ║ 24 ║  375 ║ 3.21  ║ 4.82  ║  3 ║ 14.46  ║
║ 2015-02-26 ║  ║ 2515 ║ Hy-Vee Food Store #1 / Mason City ║ 2400 4TH ST SW ║ MASON CITY ║ 50401 ║ 2400 4TH ST SW MASON CITY 50401 (43.148463097000047, -93.236272961999987) ║ 17 ║ Cerro Gordo ║ 1022100 ║ TEQUILA         ║ 410 ║ The Patron Spirits Company ║ 88294 ║ Patron Silver Tequila  ║ 12 ║  375 ║ 14.00 ║ 21.00 ║  2 ║ 42.00  ║
║ 2015-02-26 ║  ║ 2515 ║ Hy-Vee Food Store #1 / Mason City ║ 2400 4TH ST SW ║ MASON CITY ║ 50401 ║ 2400 4TH ST SW MASON CITY 50401 (43.148463097000047, -93.236272961999987) ║ 17 ║ Cerro Gordo ║ 1022100 ║ TEQUILA         ║ 421 ║ Sazerac Co., Inc.          ║ 88767 ║ Tortilla White Tequila ║ 12 ║ 1000 ║ 6.64  ║ 9.96  ║ 24 ║ 239.04 ║
╚════════════╩══╩══════╩═══════════════════════════════════╩════════════════╩════════════╩═══════╩═══════════════════════════════════════════════════════════════════════════╩════╩═════════════╩═════════╩═════════════════╩═════╩════════════════════════════╩═══════╩════════════════════════╩════╩══════╩═══════╩═══════╩════╩════════╝
```


## 4. Select Fields

Document is too wide to concentrate, select specific columns.

SQL

``` sql
select "DATE", "CATEGORY NAME", "DESCRIPTION", "TOTAL"
from iowa_liquor_sales limit 5
```

Spark

``` clojure
(->> (spark/text-file sc iowa-liquor-sales)
     (spark/map csv-read-line)
     (spark/map
	   (fn [row]
         (let [indices (->> ["DATE" "CATEGORY NAME" "DESCRIPTION" "TOTAL"]
                            (mapv name->index)
                            (set))]
           (keep-indexed (fn [i v] (if (indices i) v)) row))))
     (spark/take 5))
```

Ugh, so ugly.  
Let's extract select columns
to the function and make sure we never see it again

``` clojure
(->> (spark/text-file sc iowa-liquor-sales)
     (spark/map csv-read-line)
     (columns ["DATE" "CATEGORY NAME" "DESCRIPTION" "TOTAL"])
     (spark/take 5))
```

If you go back and review previous examples, we almost always use the pair

``` clojure
(spark/text-file sc iowa-liquor-sales) ;; read file from HDFS
(spark/map csv-read-line)              ;; parse each csv line into vector 
```

Extract it to function as well.
Functions everywhere!

Not it looks almost like SQL

``` clojure
(defn select-fields []
  (->> (read-csv sc iowa-liquor-sales)
       (columns ["DATE" "CATEGORY NAME" "DESCRIPTION" "TOTAL"])
       (spark/take 5)))

```

Result

```
╔════════════╦═════════════════╦════════════════════════╦════════╗
║ 2015-02-26 ║ TEQUILA         ║ Juarez Tequila Silver  ║ 498.24 ║
║ 2015-02-26 ║ TEQUILA         ║ Juarez Tequila Silver  ║ 121.50 ║
║ 2015-02-26 ║ SCOTCH WHISKIES ║ Lauder's               ║ 14.46  ║
║ 2015-02-26 ║ TEQUILA         ║ Patron Silver Tequila  ║ 42.00  ║
║ 2015-02-26 ║ TEQUILA         ║ Tortilla White Tequila ║ 239.04 ║
╚════════════╩═════════════════╩════════════════════════╩════════╝
```

## 5. Offset

Get another set of 5 rows somewhere in the middle.

SQL

``` sql
select "DATE", "CATEGORY NAME", "DESCRIPTION", "TOTAL"
from iowa_liquor_sales limit 5 offset 1e6
```

Spark

``` clojure
(->> (read-csv sc iowa-liquor-sales)
     (columns ["DATE" "CATEGORY NAME" "DESCRIPTION" "TOTAL"])
     (spark/repartition 1)
     (spark/map-partition-with-index
       (fn [idx iter]
         (when (zero? idx)
           (dotimes [_ 1e6] (.next iter)))
         iter))
     (spark/take 5))
```

Spark does not have a method for skipping rows.
In most cases it would be inefficient solution,
because we don't know how many data we have on each partition.

So the hack is simple:

Repartition all data into one partition (*Ouch.*)

``` clojure
(spark/repartition 1)
```

Apply skipping function at the start of this partition

``` clojure
(when (zero? idx)
  (dotimes [_ 1e6] (.next iter)))
```
  
We use `.next` to access native `java.util.Iterator` method.

Hackish, but works.

Result

```
╔════════════╦═════════════════════════╦════════════════════════════════════╦════════╗
║ 2014-08-13 ║ TEQUILA                 ║ Hornitos Lime Shot                 ║ 236.16 ║
║ 2014-08-13 ║ AMERICAN GRAPE BRANDIES ║ E & J Vs                           ║ 106.56 ║
║ 2014-08-13 ║ IMPORTED SCHNAPPS       ║ Dr. Mcgillicuddy's Cherry Schnapps ║ 312.24 ║
║ 2014-08-13 ║ IMPORTED DRY GINS       ║ Bombay Sapphire Gin                ║ 245.52 ║
║ 2014-08-13 ║ SCOTCH WHISKIES         ║ Scoresby Rare Scotch               ║ 99.00  ║
╚════════════╩═════════════════════════╩════════════════════════════════════╩════════╝
```

## 6. Sample

More efficient way to inspect random data is to use sample

SQL

``` sql
select "DATE", "CATEGORY NAME", "DESCRIPTION", "TOTAL"
from iowa_liquor_sales
order by random()
limit 10
```

Spark

``` clojure
(->> (read-csv sc iowa-liquor-sales)
     (columns ["DATE" "CATEGORY NAME" "DESCRIPTION" "TOTAL"])
     (spark/sample true 5E-6 (System/currentTimeMillis))
     (spark/take 10))
```

Spark has a capability to sample RDD. All you need is to specify
fraction of data, seed and flag which allows replacements.

Result

```
╔════════════╦═══════════════════════════╦══════════════════════════════════════════╦════════╗
║ 2015-01-07 ║ STRAIGHT BOURBON WHISKIES ║ Four Roses Yellow Label                  ║ 64.88  ║
║ 2014-10-22 ║ 80 PROOF VODKA            ║ Five O'clock Vodka                       ║ 10.12  ║
║ 2014-10-09 ║ IMPORTED SCHNAPPS         ║ Dr. Mcgillicuddy's Cherry Schnapps       ║ 198.00 ║
║ 2014-09-02 ║ CANADIAN WHISKIES         ║ Black Velvet                             ║ 94.20  ║
║ 2014-06-30 ║ TEQUILA                   ║ El Jimador Reposado Tequila              ║ 16.73  ║
║ 2014-06-18 ║ 80 PROOF VODKA            ║ Skol Vodka                               ║ 42.68  ║
║ 2014-05-05 ║ FLAVORED RUM              ║ Malibu Coconut Rum                       ║ 134.88 ║
║ 2014-04-23 ║ SCOTCH WHISKIES           ║ Lauder's                                 ║ 110.52 ║
║ 2014-04-08 ║ SCOTCH WHISKIES           ║ Cutty Sark Scotch                        ║ 71.70  ║
║ 2014-03-19 ║ WHISKEY LIQUEUR           ║ Fireball Cinnamon Whiskey Mini Dispenser ║ 133.74 ║
╚════════════╩═══════════════════════════╩══════════════════════════════════════════╩════════╝
```

## 7. Distinct

What alcohol categories do we have?

SQL

``` sql
select distinct "CATEGORY NAME"
from iowa_liquor_sales
```

Spark

``` clojure
(->> (read-csv sc iowa-liquor-sales)
     (columns ["CATEGORY NAME"])
     (spark/distinct)
     (spark/collect))
```

Result

```
╔════════════════════════════════════╗
║ SINGLE BARREL BOURBON WHISKIES     ║
║ PEPPERMINT SCHNAPPS                ║
║ WHITE CREME DE CACAO               ║
║ IMPORTED VODKA                     ║
║ ROCK & RYE                         ║
║ AMERICAN SLOE GINS                 ║
║ WHISKEY LIQUEUR                    ║
║ BARBADOS RUM                       ║
║ IMPORTED GRAPE BRANDIES            ║
║ TENNESSEE WHISKIES                 ║
║ CREME DE ALMOND                    ║
║ IMPORTED SCHNAPPS                  ║
║ SCOTCH WHISKIES                    ║
║ APPLE SCHNAPPS                     ║
║ AMERICAN AMARETTO                  ║
║ IMPORTED DRY GINS                  ║
║ JAMAICA RUM                        ║
║ TRIPLE SEC                         ║
║ SPEARMINT SCHNAPPS                 ║
║ AMERICAN DRY GINS                  ║
║ BLENDED WHISKIES                   ║
║ DECANTERS & SPECIALTY PACKAGES     ║
║ HIGH PROOF BEER                    ║
║ BLACKBERRY BRANDIES                ║
║ DARK CREME DE CACAO                ║
║ DISTILLED SPIRITS SPECIALTY        ║
║ TEQUILA                            ║
║ OTHER PROOF VODKA                  ║
║ PEACH SCHNAPPS                     ║
║ MISC. IMPORTED CORDIALS & LIQUEURS ║
║ TROPICAL FRUIT SCHNAPPS            ║
║ CREAM LIQUEURS                     ║
║ LOW PROOF VODKA                    ║
║ CHERRY BRANDIES                    ║
║ WHITE CREME DE MENTHE              ║
║ RASPBERRY SCHNAPPS                 ║
║ CINNAMON SCHNAPPS                  ║
║ IMPORTED AMARETTO                  ║
║ SINGLE MALT SCOTCH                 ║
║ GREEN CREME DE MENTHE              ║
║ MISCELLANEOUS BRANDIES             ║
║ AMERICAN ALCOHOL                   ║
║ AMERICAN GRAPE BRANDIES            ║
║ FLAVORED VODKA                     ║
║ COFFEE LIQUEURS                    ║
║ IRISH WHISKIES                     ║
║ SPICED RUM                         ║
║ GRAPE SCHNAPPS                     ║
║ 100 PROOF VODKA                    ║
║ ANISETTE                           ║
║ STRAWBERRY SCHNAPPS                ║
║ AMERICAN COCKTAILS                 ║
║ 80 PROOF VODKA                     ║
║ FLAVORED GIN                       ║
║ MISC. AMERICAN CORDIALS & LIQUEURS ║
║ BUTTERSCOTCH SCHNAPPS              ║
║ PUERTO RICO & VIRGIN ISLANDS RUM   ║
║ PEACH BRANDIES                     ║
║ WATERMELON SCHNAPPS                ║
║ BOTTLED IN BOND BOURBON            ║
║ APRICOT BRANDIES                   ║
║ FLAVORED RUM                       ║
║ MISCELLANEOUS SCHNAPPS             ║
║ ROOT BEER SCHNAPPS                 ║
║ STRAIGHT BOURBON WHISKIES          ║
║ STRAIGHT RYE WHISKIES              ║
║ IMPORTED VODKA - MISC              ║
║ CANADIAN WHISKIES                  ║
╚════════════════════════════════════╝
```


## 8. Filters

Ok, we've got a sense what type of drinks people in Iowa like.  
Let's now select some *bourbon* and *whiskey* related categories.

SQL

``` sql
select "CITY", "CATEGORY NAME", "DESCRIPTION", "TOTAL" 
from iowa_liquor_sales
where "CATEGORY NAME" ilike '%bourbon%' or
      "CATEGORY NAME" ilike '%whisk%' 
limit 10;
```

Spark

``` clojure
(->> (read-csv sc iowa-liquor-sales)
     (columns ["CITY" "CATEGORY NAME" "DESCRIPTION" "TOTAL"])
     (spark/filter (fn [[_ cat _ _]] (re-find #"(?i)(bourbon|whisk)" cat)))
     (spark/take 10))
```

Result

```
╔════════════╦═══════════════════════════╦═══════════════════════════════╦════════╗
║ MASON CITY ║ SCOTCH WHISKIES           ║ Lauder's                      ║ 14.46  ║
║ DES MOINES ║ CANADIAN WHISKIES         ║ Black Velvet Toasted Caramel  ║ 121.56 ║
║ DES MOINES ║ CANADIAN WHISKIES         ║ Black Velvet Cinnamon Rush    ║ 121.56 ║
║ DES MOINES ║ CANADIAN WHISKIES         ║ Black Velvet                  ║ 188.40 ║
║ DES MOINES ║ CANADIAN WHISKIES         ║ Black Velvet                  ║ 376.32 ║
║ DES MOINES ║ CANADIAN WHISKIES         ║ Windsor Canadian Pet          ║ 82.62  ║
║ DES MOINES ║ BOTTLED IN BOND BOURBON   ║ Evan Williams Bottled in Bond ║ 152.52 ║
║ DES MOINES ║ STRAIGHT BOURBON WHISKIES ║ Elijah Craig                  ║ 42.12  ║
║ DES MOINES ║ STRAIGHT BOURBON WHISKIES ║ Evan Williams Str Bourbon     ║ 126.60 ║
║ DES MOINES ║ STRAIGHT BOURBON WHISKIES ║ Evan Williams Green Label     ║ 44.46  ║
╚════════════╩═══════════════════════════╩═══════════════════════════════╩════════╝
```

## 9. Filters, Part II

We are not interested in sales lower than $100

SQL

``` sql
select "CITY", "CATEGORY NAME", "DESCRIPTION", "TOTAL" 
from iowa_liquor_sales
where ("CATEGORY NAME" ilike '%bourbon%' or
      "CATEGORY NAME" ilike '%whisk%') and
      "TOTAL" > 100.0
limit 10;
```

Spark

``` clojure
(->> (read-csv sc iowa-liquor-sales)
     (columns ["CITY" "CATEGORY NAME" "DESCRIPTION" "TOTAL"])
     (spark/filter (fn [[_ cat _ _]] (re-find #"(?i)(bourbon|whisk)" cat)))
     (spark/filter (fn [[_ _ _ total]] (> (Double/parseDouble total) 100)))
     (spark/take 10))
```

Few things here.  
We used two different filters here instead of one for better composability.  
For "total"-filter we used `Double/parseDouble` function, because we have all data in raw string representation. If we use several filters at once or perform aggregation (_later_) we need to have typed data, so, provide additional mapping for types.


Mapping would change to this structure

``` clojure
{ "DATE"                {:index 0 :type :date :parse-fn #(.parse (java.text.SimpleDateFormat. "yyyy-MM-dd") %)}
  "CITY"                {:index 5 :type :string :parse-fn idenity}
  "CATEGORY"            {:index 10 :type :string :parse-fn identity}
  "DESCRIPTION"         {:index 15 :type :string :parse-fn identity}
  "TOTAL"               {:index 21 :type :string :parse-fn #(Double/parseDouble %)}
```

Then apply it, during columns retrieval.

``` clojure
(defn- columns [columns rdd]
  (spark/map 
   (fn [row]
     (let [indices (->> columns
                        (map #(get-in name->index [% :index]))
                        (set))]
       (->> row
            (keep-indexed (fn [i v] (if (indices i) v)))
	        ;; this part
            (map-indexed (fn [idx elem]
                           (if (clojure.string/blank? elem)
                             nil
                             (let [column (nth columns idx)
                                   parsefn (get-in name->index [column :parse-fn])]
                               (parsefn elem)))))
            (vec))))
   rdd))
```

Do not forget to filter out nulls.  
Final solution.

``` clojure
(->> (read-csv sc iowa-liquor-sales)
       (columns ["CITY" "CATEGORY NAME" "DESCRIPTION" "TOTAL"])
       (spark/filter (fn [[_ cat _ _]] (and cat (re-find #"(?i)(bourbon|whisk)" cat))))
       (spark/filter (fn [[_ _ _ total]] (and total (> total 100))))
       (spark/take 10))
```



Result

```
╔════════════╦═══════════════════════════╦═══════════════════════════════╦════════╗
║ DES MOINES ║ CANADIAN WHISKIES         ║ Black Velvet Toasted Caramel  ║ 121.56 ║
║ DES MOINES ║ CANADIAN WHISKIES         ║ Black Velvet Cinnamon Rush    ║ 121.56 ║
║ DES MOINES ║ CANADIAN WHISKIES         ║ Black Velvet                  ║ 188.40 ║
║ DES MOINES ║ CANADIAN WHISKIES         ║ Black Velvet                  ║ 376.32 ║
║ DES MOINES ║ BOTTLED IN BOND BOURBON   ║ Evan Williams Bottled in Bond ║ 152.52 ║
║ DES MOINES ║ STRAIGHT BOURBON WHISKIES ║ Evan Williams Str Bourbon     ║ 126.60 ║
║ DES MOINES ║ STRAIGHT BOURBON WHISKIES ║ Jim Beam                      ║ 188.88 ║
║ DES MOINES ║ STRAIGHT BOURBON WHISKIES ║ Maker's Mark                  ║ 311.76 ║
║ DES MOINES ║ STRAIGHT BOURBON WHISKIES ║ Wild Turkey 101               ║ 243.00 ║
║ DES MOINES ║ STRAIGHT BOURBON WHISKIES ║ Woodford Reserve Bourbon      ║ 189.18 ║
╚════════════╩═══════════════════════════╩═══════════════════════════════╩════════╝
```

## 10. Order

Find out the most expensive deal.

SQL

``` sql
select "CITY", "CATEGORY NAME", "DESCRIPTION", "TOTAL" 
from iowa_liquor_sales
where ("CATEGORY NAME" ilike '%bourbon%' or
      "CATEGORY NAME" ilike '%whisk%')
order by "TOTAL" desc
limit 1;
```

Spark

``` clojure
(->> (read-csv sc iowa-liquor-sales)
     (columns ["CATEGORY NAME" "DESCRIPTION" "TOTAL" "BOTTLE QTY"])
     (spark/filter (fn [[cat _ _ _]] (and cat (re-find #"(?i)(bourbon|whisk)" cat))))
     (spark/map-to-pair
      (fn [[_ _ total _ :as row]]
        (spark/tuple total row)))
     (spark/sort-by-key > false)
     (spark/values)
     (spark/take 1))
```

Spark can sort key-value pair by key. To use this functionality we need
to convert our row into key value pair, where key is `total` and value is
whole row.

After sorting, we just need our values back.


Result

```
╔═══════════════════╦═════════════════════════════╦════════╦══════════╗
║ CANADIAN WHISKIES ║ Crown Royal Canadian Whisky ║ 3456.0 ║ 94590.72 ║
╚═══════════════════╩═════════════════════════════╩════════╩══════════╝
```


OMG, someone spent almost $100000 on whiskey!
3456 bottles, nice party!



















## 11. Formulas

We also have a party soon, and we need some really cheap bourbon.

*Note:* Make sure to take bottle volume into account.
Calculate price per liter.

SQL

``` sql
select "CATEGORY NAME", "DESCRIPTION", "TOTAL", "LITER SIZE", "BTL PRICE",
       ("BTL PRICE" * 1000) / "LITER SIZE" as price_per_liter
from iowa_liquor_sales
where "LITER SIZE" > 0 and
      "CATEGORY NAME" ilike '%bourbon%'
order by price_per_liter asc
limit 10;
```

Spark

``` clojure
(->> (read-csv sc iowa-liquor-sales)
     (columns ["CATEGORY NAME" "DESCRIPTION" "TOTAL" "LITER SIZE" "BTL PRICE"])
     (spark/filter (fn [[cat _ _ _]] (and cat (re-find #"(?i)bourbon" cat))))
     (spark/map
       (fn [[_ _ _ liter price :as row]]
         (conj row (/ (* 1000 price) liter))))
     (spark/map-to-pair
       (fn [[_ _ _ _ _ price-per-liter :as row]]
         (spark/tuple price-per-liter row)))
     (spark/sort-by-key < true)
     (spark/values)
     (spark/take 1))
```

Here we need just one step of enrichment. During map phase add to row additional value `price-per-liter`

Result

```

```

# 12. Aggregation

"Kentucky Gentleman Bourbon" only for $6.74/liter.

# Conclusion

SparkSQL to the rescue.







cache,intersection,randomSplit,sortBy,subtract,aggregate, cartesian, checkpoint,collectPartitions, context, count, countApprox, countApprox, countApproxDistinct, countAsync, countByValue, countByValueApprox, countByValueApprox, first, flatMap, flatMapToDouble, flatMapToPair, fold, foreach, foreachAsync, foreachPartition, foreachPartitionAsync, getCheckpointFile, getStorageLevel, glom, groupBy, groupBy, id, isCheckpointed, isEmpty, iterator, keyBy, map, mapPartitions, mapPartitions, mapPartitionsToDouble, mapPartitionsToDouble, mapPartitionsToPair, mapPartitionsToPair, mapPartitionsWithIndex, mapToDouble, mapToPair, max, min, name, partitions, pipe, pipe, pipe, reduce, saveAsObjectFile, saveAsTextFile, saveAsTextFile, splits,takeOrdered, takeOrdered, takeSample, takeSample, toArray, toDebugString, toLocalIterator, top, top, treeAggregate, treeAggregate, treeReduce, treeReduce, zip, zipPartitions, zipWithIndex, zipWithUniqueId
