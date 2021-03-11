# utl-parsing-a-simple-json-file-in-r-and-sas
Parsing a simple json file in r and sas

    Parsing a simple json file in r and sas

    GitHub
    https://tinyurl.com/29p7k5zp
    https://github.com/rogerjdeangelis/utl-parsing-a-simple-json-file-in-r-and-sas

    Inspired by
    for the pure SAS solution see
    https://stackoverflow.com/questions/66443641/parsing-json-in-sas

    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;

    * d:/json/have.json;

    filename ft15f001 "d:/json/have.json";
    parmcards4;
    {
      "totalCount": 2,
      "facets": {},
      "content": [
        [
          {
            "name": "customer_ID",
            "value": "1"
          },
          {
            "name": "customer_name",
            "value": "John"
          }
        ],
        [
          {
            "name": "customer_ID",
            "value": "2"
          },
          {
            "name": "customer_name",
            "value": "Jennifer"
          }
        ]
      ]
    }
    ;;;;
    run;quit;

    *
     _ __  _ __ ___   ___ ___  ___ ___
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    ;

    %utl_submit_r64('
    library(jsonlite);
    library(data.table);
    library(tidyverse);
    library(SASxport);
    have <- fromJSON("d:/json/have.json");
    have;
    want<-as.data.table(rbind(have$content[[1]],have$content[[2]]));
    want$count<-have$totalCount;
    want$row<-c(1,1,2,2);
    str(want);
    want;
    write.xport(want,file="d:/xpt/want.xpt");
    ');

    libname xpt xport "d:/xpt/want.xpt";
    data want;
      set xpt.want;
    run;quit;
    libname xpt clear;

    proc transpose data=want out=want(drop=_name_ row);
      by row count;
      id name;
      var value;
    run;

    *            _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| '_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
     ____    _      |_|
    |  _ \  | | ___   __ _
    | |_) | | |/ _ \ / _` |
    |  _ <  | | (_) | (_| |
    |_| \_\ |_|\___/ \__, |
                     |___/
    ;

    $totalCount
    [1] 2

    $facets
    named list()

    $content
    $content[[1]]
               name value
    1   customer_ID     1
    2 customer_name  John

    $content[[2]]
               name    value
    1   customer_ID        2
    2 customer_name Jennifer


    Classes 'data.table' and 'data.frame':	4 obs. of  4 variables:
     $ name : chr  "customer_ID" "customer_name" "customer_ID" "customer_name"
     $ value: chr  "1" "John" "2" "Jennifer"
     $ count: int  2 2 2 2
     $ row  : num  1 1 2 2
     - attr(*, ".internal.selfref")=<externalptr>

                name    value count row
    1:   customer_ID        1     2   1
    2: customer_name     John     2   1
    3:   customer_ID        2     2   2
    4: customer_name Jennifer     2   2

    *
     ___  __ _ ___
    / __|/ _` / __|
    \__ \ (_| \__ \
    |___/\__,_|___/

    ;

    WORK.WANT total obs=2

                CUSTOMER_    CUSTOMER_
       COUNT       ID          NAME

         2          1        John
         2          2        Jennifer

