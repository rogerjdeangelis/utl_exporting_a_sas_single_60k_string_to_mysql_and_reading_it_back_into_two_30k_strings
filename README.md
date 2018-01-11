# utl_exporting_a_sas_single_60k_string_to_mysql_and_reading_it_back_into_two_30k_strings
Exporting a SAS single 60k string to mySQL and reading it back into two 30k strings Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    Exporting a SAS single 60k string to mySQL and reading it back into two 30k strings

    INPUT
    =====

      %let longString=%sysfunc(repeat(0000000000,2999))%sysfunc(repeat(1111111111,2999));


      %put &=longstring;

           Macro variable LONGSTRING resolves to
           0000000000000000000000000000000000000000000000000000
           0000000000000000000000000000000000000000000000000000
           0000000000000000000000000000000000000000000000000000
           ....

           1111111111111111111111111111111111111111111111111111
           1111111111111111111111111111111111111111111111111111
           1111111111111111111111111111111111111111111111111111

    PROCESS
    =======

        1. Export 60k string to mySQL
        ==============================

            %let longString=%sysfunc(repeat(0000000000,2999))%sysfunc(repeat(1111111111,2999));
            proc sql;
               connect to mysql ( user=root password="sas28rlx" database=sakila);
               execute (drop table longstring)  by mysql;
               execute (
                   create
                     table longstring as
                   select
                      country
                     ,"&longstring" as longstring
                     ,length("&longstring") as len
                     from
                       country
                     where
                       country="Chad")
                  by mysql;
               disconnect from mysql;
            quit;
            run;quit;


         * Prove that string is 60k.
           Note you cannot use proc contents because it truncates snd shows length at 1024k;

         libname mysqllib mysql user=root password="sas28rlx" database=sakila;
         proc print data=mysqllib.longstring;
         var len;
         run;quit;
         libname mysqllib clear;

         Obs       LEN

           1     60000


        2. Import 60k into SAS dataset
        ==============================

          proc sql;
             connect to mysql ( user=root password="sas28rlx" database=sakila);
             create
                table want as
             select
                 *
             from
                 connection to mysql
                 (select
                    country
                   ,SUBSTR(longstring, 1, 30000)      as half1st
                   ,SUBSTR(longstring, 30001, 60000)  as half2nd
                   from
                     longstring);
             disconnect from mysql;
          quit;


    OUTPUT
    ======

         Data Set Name    WORK.WANT   Observations          1
         Member Type      DATA        Variables             3


               Variables in Creation Order

         Variable    Type      Len      Label

         COUNTRY     Char       50      country
         HALF1ST     Char    30000      half1st
         HALF2ND     Char    32767      half2nd


        WORK.WANT total obs=1

         Obs COUNTRY

          1   Chad

         Obs  HALF1ST

          1  0000000000000000000000000000000000000000000000...

         Obs  HALF2ND

          1  1111111111111111111111111111111111111111111111...

          CHECK

          proc sql;
            select
               length(HALF1ST)  as lenHALF1ST
              ,length(HALF2ND)  as lenHALF2ND
            from
               want
          ;quit;

           lenHALF1ST  lenHALF2ND
           ----------------------
                30000       30000

    *           _               _       _
     _ __ ___   __ _| | _____     __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \   / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/  | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|   \__,_|\__,_|\__\__,_|

    ;

      %let longString=%sysfunc(repeat(0000000000,2999))%sysfunc(repeat(1111111111,2999));

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __  ___
    / __|/ _ \| | | | | __| |/ _ \| '_ \/ __|
    \__ \ (_) | | |_| | |_| | (_) | | | \__ \
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|___/

    ;

     All the code is above.


