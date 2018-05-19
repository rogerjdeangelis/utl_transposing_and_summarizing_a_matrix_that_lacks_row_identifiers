# utl_transposing_and_summarizing_a_matrix_that_lacks_row_identifiers
Transposing and summarizing a matrix than lacks row identifiers.  Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.

    Transposing and summarizing a matrix than lacks row identifiers

    github
    https://tinyurl.com/y6vpcor6
    https://github.com/rogerjdeangelis/utl_transposing_and_summarizing_a_matrix_that_lacks_row_identifiers

    StackOverflow
    https://tinyurl.com/y8w23pxt
    https://stackoverflow.com/questions/50418265/count-the-number-of-each-value-in-every-variable-and-summarise-it-in-a-new-data

    Wen Profile
    https://stackoverflow.com/users/7964527/wen

    gather macro by Alea Iacta
    https://github.com/clindocu


    INPUT
    =====

     SD1.HAVE total obs=6

       SEX    MUT    HT    WT    GROUP

        1      0      1     0      1
        1      0      1     0      1
        1      0      1     1      0
        1      0      0     1      0
        1      0      1     0      0
        0      0      0     1      0


    EXAMPLE OUTPUT

     WORK.WANT total obs=6

       LABEL    _0    _1    SUM

       GROUP     4     2      6    ** group has 4 0s and 2 1s
       HT        2     4      6
       MUT       6     0      6
       SEX       1     5      6
       WT        3     3      6
       Sum      16    14     30


    PROCESS
    =======

    SAS

      %utl_gather(sd1.have,var,val,,sd1.havXpo,valformat=1.);

      * you probably could use 'proc report' instaead of 'proc corresp';
      ods exclude all;
      ods output observed=want;
      proc corresp data=sd1.havXpo dim=1 observed;
      tables var, val;
      run;quit;
      ods select all;

    WPS PROC R (working code)

      want<-with(stack(have),table(ind,values));
      * some additional code is needed to create SAS compatible output;

      NOTE
      * you cannot use a single proc transpose and stack the results because rows are not unique;
      * transpose creates two fact columns;
      proc transpose data=sd1.have out=xpo;
      by _all_ notsorted;
      var _all_;
      run;quit;


    OUTPUT
    ======

     WORK.WANT total obs=6

       LABEL    _0    _1    SUM

       GROUP     4     2      6    ** group has 4 0s and 2 1s
       HT        2     4      6
       MUT       6     0      6
       SEX       1     5      6
       WT        3     3      6
       Sum      16    14     30


    The WPS System

      CAT      ZEROS    ONES

      SEX        4       2
      MUT        2       4
      HT         6       0
      WT         1       5
      GROUP      3       3

    *                _               _       _
     _ __ ___   __ _| | _____     __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \   / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/  | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|   \__,_|\__,_|\__\__,_|

    ;

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
      input sex mut ht wt group;
    cards4;
    1 0 1 0 1
    1 0 1 0 1
    1 0 1 1 0
    1 0 0 1 0
    1 0 1 0 0
    0 0 0 1 0
    ;;;;
    run;quit;

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __  ___
    / __|/ _ \| | | | | __| |/ _ \| '_ \/ __|
    \__ \ (_) | | |_| | |_| | (_) | | | \__ \
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|___/

    ;

    SAS
    ===

    %utl_gather(sd1.have,var,val,,sd1.havXpo,valformat=1.);

    ods exclude all;
    ods output observed=want;
    proc corresp data=sd1.havXpo dim=1 observed;
    tables var, val;
    run;quit;
    ods select all;


    WPS PROC R
    ==========

    %utl_submit_wps64('
    libname sd1 "d:/sd1";
    options set=R_HOME "C:/Program Files/R/R-3.3.2";
    libname wrk  sas7bdat "%sysfunc(pathname(work))";
    libname hlp  sas7bdat "C:\Progra~1\SASHome\SASFoundation\9.4\core\sashelp";
    proc r;
    submit;
    source("C:/Program Files/R/R-3.3.1/etc/Rprofile.site", echo=T);
    library(haven);
    have<-read_sas("d:/sd1/have.sas7bdat");
    head(have);
    want<-with(stack(have),table(ind,values));
    want<-cbind(colnames(have),want);
    want<-as.data.frame(want);;
    colnames(want)<-c("CAT","ZEROS","ONES");
    endsubmit;
    import r=want   data=wrk.want;
    run;quit;
    proc print data=wrk.want;
    run;quit;
    ');
