# utl-combine-three-conditional-do-loops-with-different-ranges-into-one-do-loop
    %let pgm=utl-combine-three-conditional-do-loops-with-different-ranges-into-one-do-loop;

    Combine three conditional do loops with different ranges into one do loop

    github
    https://tinyurl.com/mr24f9ea
    https://github.com/rogerjdeangelis/utl-combine-three-conditional-do-loops-with-different-ranges-into-one-do-loop

    I think this is what you are asking?

        SOLUTIONS

           1. wps array do_over
           2, wps datastep arrays
           2, wps marks sentinel

    macro iotan on the end of message

    /*               _     _                                _
     _ __  _ __ ___ | |__ | | ___ _ __ ___     ___ ___   __| | ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \   / __/ _ \ / _` |/ _ \
    | |_) | | | (_) | |_) | |  __/ | | | | | | (_| (_) | (_| |  __/
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|  \___\___/ \__,_|\___|
    |_|
    */

    * NEED TO REDUCE TO SINGLE LOOP ;

    libname sd1 "d:/sd1";

    %let FirstRange = 1 to 5 by 1  ;
    %let SecondRange = 10 to 100 by 5  ;

    data sd1.have ;
         do Type = 'A', 'B' ;
             if Type = 'A' then DO  x = &FirstRange ;  y = 'G1' ;  output;
             if Type = 'A' then END ;
             if Type = 'B' then DO  x = &SecondRange ; y = 'G2' ;  output;
             if Type = 'B' then END ;
        END ;
    run ; quit ;

    /*        _           _     _                       _
    __      _| |__   __ _| |_  (_) __      ____ _ _ __ | |_
    \ \ /\ / / `_ \ / _` | __| | | \ \ /\ / / _` | `_ \| __|
     \ V  V /| | | | (_| | |_  | |  \ V  V / (_| | | | | |_
      \_/\_/ |_| |_|\__,_|\__| |_|   \_/\_/ \__,_|_| |_|\__|

    */

    /**************************************************************************************************************************/
    /*                                  |                                                                                     */
    /* SD1.Have total obs=24            | RULES                                                                               */
    /*                                  |                                                                                     */
    /*  Obs    TYPE      X      Y       |                                                                                     */
    /*                                  |                                                                                     */
    /*    1     A        1    G1        | Range 1 to 5 type=A and Y=GROUP1                                                    */
    /*    2     A        2    G1        |                                                                                     */
    /*    3     A        3    G1        |                                                                                     */
    /*    4     A        4    G1        |                                                                                     */
    /*    5     A        5    G1        |                                                                                     */
    /*                                  |                                                                                     */
    /*    6     B       10    G2        | Range 1 to 5 type=A and Y=GROUP2                                                    */
    /*    7     B       15    G2        |                                                                                     */
    /*    8     B       20    G2        |                                                                                     */
    /*    9     B       25    G2        |                                                                                     */
    /*   10     B       30    G2        |                                                                                     */
    /*   11     B       35    G2        |                                                                                     */
    /*   12     B       40    G2        |                                                                                     */
    /*   13     B       45    G2        |                                                                                     */
    /*   14     B       50    G2        |                                                                                     */
    /*   15     B       55    G2        |                                                                                     */
    /*   16     B       60    G2        |                                                                                     */
    /*   17     B       65    G2        |                                                                                     */
    /*   18     B       70    G2        |                                                                                     */
    /*   19     B       75    G2        |                                                                                     */
    /*   20     B       80    G2        |                                                                                     */
    /*   21     B       85    G2        |                                                                                     */
    /*   22     B       90    G2        |                                                                                     */
    /*   23     B       95    G2        |                                                                                     */
    /*   24     B      100    G2        |                                                                                     */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                                                     _
    __      ___ __  ___    __ _ _ __ _ __ __ _ _   _    __| | ___     _____   _____ _ __
    \ \ /\ / / `_ \/ __|  / _` | `__| `__/ _` | | | |  / _` |/ _ \   / _ \ \ / / _ \ `__|
     \ V  V /| |_) \__ \ | (_| | |  | | | (_| | |_| | | (_| | (_) | | (_) \ V /  __/ |
      \_/\_/ | .__/|___/  \__,_|_|  |_|  \__,_|\__, |  \__,_|\___/___\___/ \_/ \___|_|
             |_|                               |___/            |_____|

    */

    /*---- Separate meta data from data in two macro arrays                  ----*/
    /*---- ALL THE META DATA IS IN THE ARRAYS                                ----*/

    proc datasets lib=sd1 nolist nodetails;delete want; run;quit;

    %utl_wpsbeginx;
    parmcards4;
    options sasautos=("c:/oto" sasautos);
    libname sd1 "d:/sd1";
    %array(_l,values=%iotan(5) %iotan(100,from=10,by=5));
    %array(_g,values=%sysfunc(repeat(%str(A ),4))%sysfunc(repeat(%str(B ),18)));
    data sd1.want;
      %do_over(_l _g,phrase=%str(
        x=?_l;
        type="?_g";
        if type = "A" then y="G1";
        else y="G2";
        output;));
    run;quit;
    proc print data=sd1.want;;
    run;quit;
    %arraydelete(_l);
    %arraydelete(_g);
    ;;;;
    %utl_wpsendx;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  The WPS System                                                                                                        */
    /*                                                                                                                        */
    /*  Obs     X     TYPE    Y                                                                                               */
    /*                                                                                                                        */
    /*    1      1     A      G1                                                                                              */
    /*    2      2     A      G1                                                                                              */
    /*    3      3     A      G1                                                                                              */
    /*    4      4     A      G1                                                                                              */
    /*    5      5     A      G1                                                                                              */
    /*                                                                                                                        */
    /*    6     10     B      G2                                                                                              */
    /*    7     15     B      G2                                                                                              */
    /*    8     20     B      G2                                                                                              */
    /*    9     25     B      G2                                                                                              */
    /*   10     30     B      G2                                                                                              */
    /*   11     35     B      G2                                                                                              */
    /*   12     40     B      G2                                                                                              */
    /*   13     45     B      G2                                                                                              */
    /*   14     50     B      G2                                                                                              */
    /*   15     55     B      G2                                                                                              */
    /*   16     60     B      G2                                                                                              */
    /*   17     65     B      G2                                                                                              */
    /*   18     70     B      G2                                                                                              */
    /*   19     75     B      G2                                                                                              */
    /*   20     80     B      G2                                                                                              */
    /*   21     85     B      G2                                                                                              */
    /*   22     90     B      G2                                                                                              */
    /*   23     95     B      G2                                                                                              */
    /*   24    100     B      G2                                                                                              */
    /*                                                                                                                        */
    /**************************************************************************************************************************/


    %let _la=%iotan(5,delim=%str(,)),%iotan(100,from=10,by=5,delim=%str(,));
    %put &_la;

    %let _lb=%sysfunc(repeat(%str("A",),4))%sysfunc(repeat(%str("B",),17))"B";
    %put &_lb;


    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  META MACRO STRINGS                                                                                                    */
    /*                                                                                                                        */
    /*    _la = 1,2,3,4,5,10,15,20,25,30,35,40,45,50,55,60,65,70,75,80,85,90,95,100                                           */
    /*                                                                                                                        */
    /*    _lb = "A","A","A","A","A","B","B","B","B","B","B","B","B","B","B","B","B","B","B","B","B","B","B","B"               */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                        _       _            _
    __      ___ __  ___    __| | __ _| |_ __ _ ___| |_ ___ _ __     __ _ _ __ _ __ __ _ _   _
    \ \ /\ / / `_ \/ __|  / _` |/ _` | __/ _` / __| __/ _ \ `_ \   / _` | `__| `__/ _` | | | |
     \ V  V /| |_) \__ \ | (_| | (_| | || (_| \__ \ ||  __/ |_) | | (_| | |  | | | (_| | |_| |
      \_/\_/ | .__/|___/  \__,_|\__,_|\__\__,_|___/\__\___| .__/   \__,_|_|  |_|  \__,_|\__, |
             |_|                                          |_|                          |___/

    */
    proc datasets lib=sd1 nolist nodetails;delete want; run;quit;

    %utl_submit_wps64x('

    options sasautos=("c:/oto" sasautos);
    libname sd1 "d:/sd1";

    %let _la=%iotan(5,delim=%str(,)),%iotan(100,from=10,by=5,delim=%str(,));
    %let _lb=%sysfunc(repeat(%str("A",),4))%sysfunc(repeat(%str("B",),17))"B";
    %let _dim=%eval(%length("&_la")/3+1);
    %put &_dim;

    * END NETA DATA ;

    data sd1.want ;
       array la[&_dim]  8  (&_la) ;
       array lb[&_dim] $1  (&_lb) ;
       do idx=1 to dim(la) ;
         x    = la[idx] ;
         type = lb[idx] ;
         if type = "A" then y="G1" ;
         else y="G2" ;
         output ;
       end;
       keep x type y ;
    run;quit;
    proc print data=sd1.want;
    run;quit;
    ');

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  The WPS System                                                                                                        */
    /*                                                                                                                        */
    /*  Obs     X     TYPE    Y                                                                                               */
    /*                                                                                                                        */
    /*    1      1     A      G1                                                                                              */
    /*    2      2     A      G1                                                                                              */
    /*    3      3     A      G1                                                                                              */
    /*    4      4     A      G1                                                                                              */
    /*    5      5     A      G1                                                                                              */
    /*                                                                                                                        */
    /*    6     10     B      G2                                                                                              */
    /*    7     15     B      G2                                                                                              */
    /*    8     20     B      G2                                                                                              */
    /*    9     25     B      G2                                                                                              */
    /*   10     30     B      G2                                                                                              */
    /*   11     35     B      G2                                                                                              */
    /*   12     40     B      G2                                                                                              */
    /*   13     45     B      G2                                                                                              */
    /*   14     50     B      G2                                                                                               */
    /*   15     55     B      G2                                                                                              */
    /*   16     60     B      G2                                                                                              */
    /*   17     65     B      G2                                                                                              */
    /*   18     70     B      G2                                                                                              */
    /*   19     75     B      G2                                                                                              */
    /*   20     80     B      G2                                                                                              */
    /*   21     85     B      G2                                                                                              */
    /*   22     90     B      G2                                                                                              */
    /*   23     95     B      G2                                                                                                */
    /*   24    100     B      G2                                                                                              */
    /*                                                                                                                        */
    /**************************************************************************************************************************/
    /*                                         _                        _   _            _
    __      ___ __  ___   _ __ ___   __ _ _ __| | _____  ___  ___ _ __ | |_(_)_ __   ___| |
    \ \ /\ / / `_ \/ __| | `_ ` _ \ / _` | `__| |/ / __|/ __|/ _ \ `_ \| __| | `_ \ / _ \ |
     \ V  V /| |_) \__ \ | | | | | | (_| | |  |   <\__ \\__ \  __/ | | | |_| | | | |  __/ |
      \_/\_/ | .__/|___/ |_| |_| |_|\__,_|_|  |_|\_\___/|___/\___|_| |_|\__|_|_| |_|\___|_|
             |_|
    */


    /*----  THE -1 INDEX IS A SENTINEL AS FIRST PUBLISHED BY MARK keintz     ----*/

    %let _ranges= 1 to 5,-1,10 to 100 by 5;
    %let _parts='A','B';
    %let _dim=%eval(%length("&_parts")/4);
    %put &=_dim;

    data want;
      array ab(&_dim) $1 _temporary_ (&_parts);
      do idx = &_ranges;
        type   = ab[1];
        if type="A" then y="G1";
        else Y = "G2";
        if idx  = -1 then ab[1]=ab[2];
        else output;
      end;

    run;quit;

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
