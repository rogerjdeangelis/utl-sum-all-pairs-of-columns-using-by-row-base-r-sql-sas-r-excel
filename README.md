# utl-sum-all-pairs-of-columns-using-by-row-base-r-sql-sas-r-excel
Sum all pairs of columns using base r and sql sas excel r
    %let pgm=utl-sum-all-pairs-of-columns-using-by-row-base-r-sql-sas-r-excel;

    %stop_submission;

    Sum all pairs of columns using base r and sql sas excel r

    Looks like a job for an in memory matrix language

    github
    https://tinyurl.com/593wjnpk
    https://github.com/rogerjdeangelis/utl-sum-all-pairs-of-columns-using-by-row-base-r-sql-sas-r-excel

    CONTENTS

       1 base r
       2 sas sql
       3 r sql
       4 python sql (ditionary tables not supported?)
         SOAPBOX ON
            As far as I can tell python pdsql,
            unlike r, python does not support distionary tables
            You need to create a database and use cursors
            and transaction sql
         SOAPBOX OFF
       5 excel sql

    R PROCESS
    =========

    Key R functionality (abreviated)

      pairs <- combn(names(data), 2)

      Produces a form of the 10 pars
         A B
         A C
         A D
         A E
         B C
         B D
         B E
         C D
         C E
         D E

     Then all you have to do is

     pair_sums[[pair_name]]
      <- rowSums(data[,c(col1, col2)])

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    /*****************************************************************************************************************************/
    /*        INPUT           |            PROCESS                         |                  OUTPUT                             */
    /*         ====           |            =======                         |                  ======                             */
    /*                        |                                            |                                                     */
    /*  A  B  C  D  E         | 1 BASE R                                   |      AB AC AD AE BC BD BE CD CE DE                  */
    /*                        | -==========================================|                                                     */
    /*  1  2  3  4  5         |                                            |       3  4  5  6  5  6  7  7  8  9                  */
    /* 10 20 30 40 50         | %utl_rbeginx;                              |      30 40 50 60 50 60 70 70 80 90                  */
    /*                        | parmcards4;                                |                                                     */
    /* options                | library(haven)                             |                                                     */
    /* validvarname=upcase;   | source("c:/oto/fn_tosas9x.R")              |                                                     */
    /* libname sd1 "d:/sd1";  | data<-read_sas("d:/sd1/have.sas7bdat")     |      A  B  C  D  E    AB=1+2 =3                     */
    /* data sd1.have;         |                                            |                       AC=1+3 =4                     */
    /*  input A B C D E;      | pairs <- combn(names(data), 2)             |      1  2  3  4  5    ...                           */
    /* cards4;                |                                            |      0 20 30 40 50    DE=4+5=9                      */
    /* 01 02 03 04 05         | # Initialize an empty list                 |                                                     */
    /* 10 20 30 40 50         | pair_sums <- list()                        |                                                     */
    /* ;;;;                   |                                            |                                                     */
    /* run;quit;              | # Loop each pair and sum                   |                                                     */
    /*                        | for (i in seq(ncol(pairs))) {              |                                                     */
    /*                        |   col1 <- pairs[1, i]                      |                                                     */
    /*                        |   col2 <- pairs[2, i]                      |                                                     */
    /*                        |   pair_name <- paste0(col1, col2)          |                                                     */
    /*                        |   pair_sums[[pair_name]] <-                |                                                     */
    /*                        |     rowSums(data[, c(col1, col2)])         |                                                     */
    /*                        | }                                          |                                                     */
    /*                        |                                            |                                                     */
    /*                        | result_df <- as.data.frame(pair_sums)      |                                                     */
    /*                        | print(result_df)                           |                                                     */
    /*                        | fn_tosas9x(                                |                                                     */
    /*                        |       inp    = result_df                   |                                                     */
    /*                        |      ,outlib ="d:/sd1/"                    |                                                     */
    /*                        |      ,outdsn ="want"                       |                                                     */
    /*                        |      )                                     |                                                     */
    /*                        | ;;;;                                       |                                                     */
    /*                        | %utl_rendx;                                |                                                     */
    /*                        |                                            |                                                     */
    /*                        |                                            |                                                     */
    /*                        | 2 SAS SQL                                  |                                                     */
    /*                        | ===========================================|                                                     */
    /*                        |                                            |                                                     */
    /*                        | proc sql;                                  |                                                     */
    /*                        |   select                                   |                                                     */
    /*                        |      compbl(                               |                                                     */
    /*                        |        r.name!!"+"!!l.name!!               |                                                     */
    /*                        |        " as "!!cats(r.name,l.name))        |                                                     */
    /*                        |   into                                     |                                                     */
    /*                        |      :_pairs separated by ','              |                                                     */
    /*                        |   from                                     |                                                     */
    /*                        |      sashelp.vcolumn as l,                 |                                                     */
    /*                        |      sashelp.vcolumn as r                  |                                                     */
    /*                        |   where                                    |                                                     */
    /*                        |           l.libname="SD1"                  |                                                     */
    /*                        |      and  l.memname="HAVE"                 |                                                     */
    /*                        |      and  l.libname=r.libname              |                                                     */
    /*                        |      and  l.memname=r.memname              |                                                     */
    /*                        |      and  l.name > r.name                  |                                                     */
    /*                        |   order                                    |                                                     */
    /*                        |      by cats(r.name,l.name)                |                                                     */
    /*                        |  ;                                         |                                                     */
    /*                        |                                            |                                                     */
    /*                        |   *                                        |                                                     */
    /*                        |   A+B as AB                                |                                                     */
    /*                        |   A+C as AC                                |                                                     */
    /*                        |   A+D as AD                                |                                                     */
    /*                        |   A+E as AE                                |                                                     */
    /*                        |   B+C as BC                                |                                                     */
    /*                        |   B+D as BD                                |                                                     */
    /*                        |   B+E as BE                                |                                                     */
    /*                        |   C+D as CD                                |                                                     */
    /*                        |   C+E as CE                                |                                                     */
    /*                        |   D+E as DE ;                              |                                                     */
    /*                        |                                            |                                                     */
    /*                        |  create                                    |                                                     */
    /*                        |     table want as                          |                                                     */
    /*                        |  select                                    |                                                     */
    /*                        |     &_pairs                                |                                                     */
    /*                        |  from                                      |                                                     */
    /*                        |     sd1.have                               |                                                     */
    /*                        |                                            |                                                     */
    /*                        | ;quit;                                     |                                                     */
    /*                        |                                            |                                                     */
    /*                        |                                            |                                                     */
    /*                        | 3 R SQL                                    |                                                     */
    /*                        | ===========================================|                                                     */
    /*                        |                                            |                                                     */
    /*                        |  proc datasets lib=sd1                     |                                                     */
    /*                        |   nolist nodetails;                        |                                                     */
    /*                        |   delete want;                             |                                                     */
    /*                        |  run;quit;                                 |                                                     */
    /*                        |                                            |                                                     */
    /*                        |  %utl_rbeginx;                             |                                                     */
    /*                        |  parmcards4;                               |                                                     */
    /*                        |  library(haven)                            |                                                     */
    /*                        |  library(sqldf)                            |                                                     */
    /*                        |  source("c:/oto/fn_tosas9x.R")             |                                                     */
    /*                        |  have<-read_sas("d:/sd1/have.sas7bdat")    |                                                     */
    /*                        |  print(have)                               |                                                     */
    /*                        |  sqldf("PRAGMA table_info('have')")        |                                                     */
    /*                        |  pars<-sqldf('                             |                                                     */
    /*                        |    select                                  |                                                     */
    /*                        |      group_concat(                         |                                                     */
    /*                        |       r.name||"+"||l.name||                |                                                     */
    /*                        |       " as "||r.name||l.name,",") as sels  |                                                     */
    /*                        |    from                                    |                                                     */
    /*                        |      pragma_table_info("have") as l        |                                                     */
    /*                        |    inner join                              |                                                     */
    /*                        |      pragma_table_info("have") as r        |                                                     */
    /*                        |    on                                      |                                                     */
    /*                        |      l.name > r.name                       |                                                     */
    /*                        |    ')                                      |                                                     */
    /*                        |  pars;                                     |                                                     */
    /*                        |  want<-sqldf(                              |                                                     */
    /*                        |     paste("select"                         |                                                     */
    /*                        |    ,pars$sels                              |                                                     */
    /*                        |    ,"from have"))                          |                                                     */
    /*                        |  want                                      |                                                     */
    /*                        |  fn_tosas9x(                               |                                                     */
    /*                        |        inp    = want                       |                                                     */
    /*                        |       ,outlib ="d:/sd1/"                   |                                                     */
    /*                        |       ,outdsn ="want"                      |                                                     */
    /*                        |       )                                    |                                                     */
    /*                        |  ;;;;                                      |                                                     */
    /*                        |  %utl_rendx;                               |                                                     */
    /*                        |                                            |                                                     */
    /*                        |  proc print data=sd1.want;                 |                                                     */
    /*                        |  run;quit;                                 |                                                     */
    /*                        |                                            |                                                     */
    /*                        |                                            |                                                     */
    /*                        |  4 PYTHON SQL (NO SUPPORT PRAGMA)          |                                                     */
    /*                        |  ==========================================|                                                     */
    /*                        |  FAILS BUT WORKS IN R                      |                                                     */
    /*                        |                                            |                                                     */
    /*                        |  %utl_pybeginx;                            |                                                     */
    /*                        |  parmcards4;                               |                                                     */
    /*                        |  exec(open('c:/oto/fn_python.py').read()); |                                                     */
    /*                        |  have,meta =                               |                                                     */
    /*                        |   ps.read_sas7bdat('d:/sd1/have.sas7bdat');|                                                     */
    /*                        |  pars=pdsql('''                            |                                                     */
    /*                        |   PRAGMA table_info("have")''')            |                                                     */
    /*                        |  print(pars)                               |                                                     */
    /*                        |  ;;;;                                      |                                                     */
    /*                        |  %utl_pyendx;                              |                                                     */
    /*                        |                                            |                                                     */
    /*                        |                                            |                                                     */
    /*                        |  5 EXCEL SQL                               |                                                     */
    /*                        |  ==========================================|=====================================================*/
    /*                        |                                            |   ------------+                                     */
    /*                        |  %utlfkil(d:/xls/wantxl.xlsx);             |   | A1| fx    |                                     */
    /*                        |                                            |   --------------------------------------------+     */
    /*                        |  * CREATE INPUT;                           |   [_] |  A| B | C | E | F | G |  H|  I|  J|  K|     */
    /*                        |  %utl_rbeginx;                             |   --------------------------------------------|     */
    /*                        |  parmcards4;                               |    1  | AB| AC| BC|AD |BD | CD| AE| BE| CE| DE|     */
    /*                        |  library(openxlsx)                         |    -- |---+---+---+---+---+---+---+---+---+---|     */
    /*                        |  library(sqldf)                            |    2  |3  | 4 | 5 | 5 | 6 | 7 | 6 | 7 | 8 | 9 |     */
    /*                        |  library(haven)                            |    -- |---+---+---+---+---+---+---+---+---+---|     */
    /*                        |  have<-read_sas("d:/sd1/have.sas7bdat")    |    3  |30 | 40| 50| 50| 60| 70| 60| 70| 80| 90|     */
    /*                        |  wb <- createWorkbook()                    |    -- |---+---+---+---+---+---+---+---+---+---|     */
    /*                        |  addWorksheet(wb, "have")                  |   [WANT]                                            */
    /*                        |  writeData(wb, sheet = "have", x = have)   |                                                     */
    /*                        |  saveWorkbook(                             |                                                     */
    /*                        |      wb                                    |                                                     */
    /*                        |     ,"d:/xls/wantxl.xlsx"                  |                                                     */
    /*                        |     ,overwrite=TRUE)                       |                                                     */
    /*                        |  ;;;;                                      |                                                     */
    /*                        |  %utl_rendx;                               |                                                     */
    /*                        |                                            |                                                     */
    /*                        |  %utl_rbeginx;                             |                                                     */
    /*                        |  parmcards4;                               |                                                     */
    /*                        |  library(openxlsx)                         |                                                     */
    /*                        |  library(sqldf)                            |                                                     */
    /*                        |  source("c:/oto/fn_tosas9x.R")             |                                                     */
    /*                        |   wb<-loadWorkbook("d:/xls/wantxl.xlsx")   |                                                     */
    /*                        |   have<-read.xlsx(wb,"have")               |                                                     */
    /*                        |   addWorksheet(wb, "want")                 |                                                     */
    /*                        |   pars<-sqldf('                            |                                                     */
    /*                        |    select                                  |                                                     */
    /*                        |      group_concat(                         |                                                     */
    /*                        |       r.name||"+"||l.name||                |                                                     */
    /*                        |       " as "||r.name||l.name,",") as sels  |                                                     */
    /*                        |    from                                    |                                                     */
    /*                        |      pragma_table_info("have") as l        |                                                     */
    /*                        |    inner join                              |                                                     */
    /*                        |      pragma_table_info("have") as r        |                                                     */
    /*                        |    on                                      |                                                     */
    /*                        |      l.name > r.name                       |                                                     */
    /*                        |    ')                                      |                                                     */
    /*                        |   pars                                     |                                                     */
    /*                        |   want<-sqldf(                             |                                                     */
    /*                        |     paste("select"                         |                                                     */
    /*                        |    ,pars$sels                              |                                                     */
    /*                        |    ,"from have"))                          |                                                     */
    /*                        |   print(want)                              |                                                     */
    /*                        |   writeData(wb,sheet="want",x=want)        |                                                     */
    /*                        |   saveWorkbook(                            |                                                     */
    /*                        |       wb                                   |                                                     */
    /*                        |      ,"d:/xls/wantxl.xlsx"                 |                                                     */
    /*                        |      ,overwrite=TRUE)                      |                                                     */
    /*                        |  fn_tosas9x(                               |                                                     */
    /*                        |        inp    = want                       |                                                     */
    /*                        |       ,outlib ="d:/sd1/"                   |                                                     */
    /*                        |       ,outdsn ="want"                      |                                                     */
    /*                        |       )                                    |                                                     */
    /*                        |  ;;;;                                      |                                                     */
    /*                        |  %utl_rendx;                               |                                                     */
    /*                        |                                            |                                                     */
    /*                        |  proc print data=sd1.want;                 |                                                     */
    /*                        |  run;quit;                                 |                                                     */
    /*****************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    options
    validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
     input A B C D E;
    cards4;
    01 02 03 04 05
    10 20 30 40 50
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*    A     B     C     D     E                                                                                           */
    /*                                                                                                                        */
    /*    1     2     3     4     5                                                                                           */
    /*   10    20    30    40    50                                                                                           */
    /**************************************************************************************************************************/

    /*   _
    / | | |__   __ _ ___  ___
    | | | `_ \ / _` / __|/ _ \
    | | | |_) | (_| \__ \  __/
    |_| |_.__/ \__,_|___/\___|

    */

    proc datasets lib=sd1
     nolist nodetails;
     delete want;
    run;quit;

    %utl_rbeginx;
    parmcards4;
    library(haven)
    source("c:/oto/fn_tosas9x.R")
    data<-read_sas("d:/sd1/have.sas7bdat")

    pairs <- combn(names(data), 2)

    # Initialize an empty list
    pair_sums <- list()

    # Loop each pair and sum
    for (i in seq(ncol(pairs))) {
      col1 <- pairs[1, i]
      col2 <- pairs[2, i]
      pair_name <- paste0(col1, col2)
      pair_sums[[pair_name]] <-
        rowSums(data[, c(col1, col2)])
    }

    result_df <- as.data.frame(pair_sums)
    print(result_df)
    fn_tosas9x(
          inp    = result_df
         ,outlib ="d:/sd1/"
         ,outdsn ="want"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.want;
    run;quit;

    /**************************************************************************************************************************/
    /* > print(result_df)               | SAS                                                                                 */
    /*                                  |                                                                                     */
    /*   AB AC AD AE BC BD BE CD CE DE  | ROWNAMES    AB    AC    AD    AE    BC    BD    BE    CD    CE    DE                */
    /*                                  |                                                                                     */
    /* 1  3  4  5  6  5  6  7  7  8  9  |     1        3     4     5     6     5     6     7     7     8     9                */
    /* 2 30 40 50 60 50 60 70 70 80 90  |     2       30    40    50    60    50    60    70    70    80    90                */
    /**************************************************************************************************************************/

    /*___                              _
    |___ \   ___  __ _ ___   ___  __ _| |
      __) | / __|/ _` / __| / __|/ _` | |
     / __/  \__ \ (_| \__ \ \__ \ (_| | |
    |_____| |___/\__,_|___/ |___/\__, |_|
                                    |_|
    */

    proc sql;
      select
         compbl(
           r.name!!"+"!!l.name!!
           " as "!!cats(r.name,l.name))
      into
         :_pairs separated by ','
      from
         sashelp.vcolumn as l,
         sashelp.vcolumn as r
      where
              l.libname="SD1"
         and  l.memname="HAVE"
         and  l.libname=r.libname
         and  l.memname=r.memname
         and  l.name > r.name
      order
         by cats(r.name,l.name)
     ;

      *
      A+B as AB
      A+C as AC
      A+D as AD
      A+E as AE
      B+C as BC
      B+D as BD
      B+E as BE
      C+D as CD
      C+E as CE
      D+E as DE ;

     create
        table want as
     select
        &_pairs
     from
        sd1.have

    ;quit;

     /**************************************************************************************************************************/
     /* META DATA      |    AB    AC    AD    AE    BC    BD    BE    CD    CE    DE                                           */
     /*                |                                                                                                       */
     /* A +B as AB     |     3     4     5     6     5     6     7     7     8     9                                           */
     /* A +C as AC     |    30    40    50    60    50    60    70    70    80    90                                           */
     /* A +D as AD     |                                                                                                       */
     /* A +E as AE     |                                                                                                       */
     /* B +C as BC     |                                                                                                       */
     /* B +D as BD     |                                                                                                       */
     /* B +E as BE     |                                                                                                       */
     /* C +D as CD     |                                                                                                       */
     /* C +E as CE     |                                                                                                       */
     /* D +E as DE     |                                                                                                       */
     /**************************************************************************************************************************/

    /*____                    _
    |___ /   _ __   ___  __ _| |
      |_ \  | `__| / __|/ _` | |
     ___) | | |    \__ \ (_| | |
    |____/  |_|    |___/\__, |_|
                           |_|
    */

    proc datasets lib=sd1
     nolist nodetails;
     delete want;
    run;quit;

    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
    have<-read_sas("d:/sd1/have.sas7bdat")
    print(have)
    sqldf("PRAGMA table_info('have')")
    pars<-sqldf('
      select
        group_concat(
         r.name||"+"||l.name||
         " as "||r.name||l.name,",") as sels
      from
        pragma_table_info("have") as l
      inner join
        pragma_table_info("have") as r
      on
        l.name > r.name
      ')
    pars;
    want<-sqldf(
       paste("select"
      ,pars$sels
      ,"from have"))
    want
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="want"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.want;
    run;quit;

    /**************************************************************************************************************************/
    /* R                                 | SAS                                                                                */
    /*   AB AC BC AD BD CD AE BE CE DE   | ROWNAMES    AB    AC    BC    AD    BD    CD    AE    BE    CE    DE               */
    /*                                   |                                                                                    */
    /* 1  3  4  5  5  6  7  6  7  8  9   |     1        3     4     5     5     6     7     6     7     8     9               */
    /* 2 30 40 50 50 60 70 60 70 80 90   |     2       30    40    50    50    60    70    60    70    80    90               */
    /**************************************************************************************************************************/

    /*  _                 _   _                             _
    | || |    _ __  _   _| |_| |__   ___  _ __    ___  __ _| |
    | || |_  | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | |
    |__   _| | |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | |
       |_|   | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|
             |_|    |___/                                |_|
    */

    FAILS BUT WORKS IN R

    %utl_pybeginx;
    parmcards4;
    exec(open('c:/oto/fn_python.py').read());
    have,meta =
     ps.read_sas7bdat('d:/sd1/have.sas7bdat');
    pars=pdsql('''
     PRAGMA table_info("have")''')
    print(pars)
    ;;;;
    %utl_pyendx;

    /**************************************************************************************************************************/
    /* DOES NOT WORK                                                                                                          */
    /**************************************************************************************************************************/

    /*___                      _             _
    | ___|    _____  _____ ___| |  ___  __ _| |
    |___ \   / _ \ \/ / __/ _ \ | / __|/ _` | |
     ___) | |  __/>  < (_|  __/ | \__ \ (_| | |
    |____/   \___/_/\_\___\___|_| |___/\__, |_|
                                          |_|
    */

    %utlfkil(d:/xls/wantxl.xlsx);

    * CREATE INPUT;
    %utl_rbeginx;
    parmcards4;
    library(openxlsx)
    library(sqldf)
    library(haven)
    have<-read_sas("d:/sd1/have.sas7bdat")
    wb <- createWorkbook()
    addWorksheet(wb, "have")
    writeData(wb, sheet = "have", x = have)
    saveWorkbook(
        wb
       ,"d:/xls/wantxl.xlsx"
       ,overwrite=TRUE)
    ;;;;
    %utl_rendx;

    %utl_rbeginx;
    parmcards4;
    library(openxlsx)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
     wb<-loadWorkbook("d:/xls/wantxl.xlsx")
     have<-read.xlsx(wb,"have")
     addWorksheet(wb, "want")
     pars<-sqldf('
      select
        group_concat(
         r.name||"+"||l.name||
         " as "||r.name||l.name,",") as sels
      from
        pragma_table_info("have") as l
      inner join
        pragma_table_info("have") as r
      on
        l.name > r.name
      ')
     pars
     want<-sqldf(
       paste("select"
      ,pars$sels
      ,"from have"))
     print(want)
     writeData(wb,sheet="want",x=want)
     saveWorkbook(
         wb
        ,"d:/xls/wantxl.xlsx"
        ,overwrite=TRUE)
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="want"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.want;
    run;quit;

    /**************************************************************************************************************************/
    /* --------------+                                                                                                        */
    /* | A1| fx      |                                                                                                        */
    /* ------------------------------------------------------+                                                                */
    /* [_] |  A | B  | C  | E  | F  |  G |  H |  I |  J |  K |                                                                */
    /* ------------------------------------------------------|                                                                */
    /*  1  | AB | AC | BC |AD  |BD  | CD | AE | BE | CE | DE |                                                                */
    /*  -- |----+----+----+----+----+----+----+----+----+----|                                                                */
    /*  2  |3   | 4  | 5  | 5  | 6  | 7  | 6  | 7  | 8  | 9  |                                                                */
    /*  -- |----+----+----+----+----+----+----+----+----+----|                                                                */
    /*  3  |30  | 40 | 50 | 50 | 60 | 70 | 60 | 70 | 80 | 90 |                                                                */
    /*  -- |----+----+----+----+----+----+----+----+----+----|                                                                */
    /* [WANT]                                                                                                                 */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
