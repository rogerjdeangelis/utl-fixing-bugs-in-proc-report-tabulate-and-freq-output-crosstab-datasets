# utl-fixing-bugs-in-proc-report-tabulate-and-freq-output-crosstab-datasets
Fixing bugs in proc report tabulate and freq output crosstab datasets
    Fixing bugs in proc report tabulate and freq output crosstab datasets                                                 
                                                                                                                          
    All the solutions below create ods like output dataset not a static report.                                           
                                                                                                                          
    github                                                                                                                
    https://tinyurl.com/ydbtb8ox                                                                                          
    https://github.com/rogerjdeangelis/utl-fixing-bugs-in-proc-report-tabulate-and-freq-output-crosstab-datasets          
                                                                                                                          
    The ods outout datasets for report, tab and freq do not mimic the static output.                                      
                                                                                                                          
    SAS Forum                                                                                                             
    https://communities.sas.com/t5/SAS-Programming/dynamic-code/m-p/648001                                                
                                                                                                                          
                                                                                                                          
      Solutions                                                                                                           
                                                                                                                          
         a. proc corresp  (this does honor the static report - only one)                                                  
         b. proc freq     (no ods output for crosstabs)                                                                   
         c. proc tabulate (especially tricky because column names are often repeated)                                     
         d. proc report   (ods out column naes are useless ie _c2_ _c3_ ...)                                              
                                                                                                                          
     The key to most ofthese solutios is to make you static output look like a retangular display                         
     that can esaily be mapped into a SAS dataset.                                                                        
                                                                                                                          
    The work is based on original code by                                                                                 
    Bartosz Jablonski                                                                                                     
    yabwon@gmail.com                                                                                                      
                                                                                                                          
    SAS Forum                                                                                                             
    https://communities.sas.com/t5/SAS-Programming/dynamic-code/m-p/648001                                                
                                                                                                                          
    macros                                                                                                                
    https://tinyurl.com/y9nfugth                                                                                          
    https://github.com/rogerjdeangelis/utl-macros-used-in-many-of-rogerjdeangelis-repositories                            
                                                                                                                          
    *_                   _                                                                                                
    (_)_ __  _ __  _   _| |_                                                                                              
    | | '_ \| '_ \| | | | __|                                                                                             
    | | | | | |_) | |_| | |_                                                                                              
    |_|_| |_| .__/ \__,_|\__|                                                                                             
            |_|                                                                                                           
    ;                                                                                                                     
                                                                                                                          
    data have;                                                                                                            
     input DATE anydtdte12. CODE$;                                                                                        
    cards4;                                                                                                               
    10-MAY-2020 G2345                                                                                                     
    10-MAY-2020 AUTO                                                                                                      
    11-MAY-2020 G2345                                                                                                     
    11-MAY-2020 TOP                                                                                                       
    11-MAY-2020 TOP                                                                                                       
    ;;;;                                                                                                                  
    run;quit;                                                                                                             
                                                                                                                          
     WORK.HAVE total obs=5                                                                                                
                                                                                                                          
       DATE    CODE                                                                                                       
                                                                                                                          
      22045    G2345                                                                                                      
      22045    AUTO                                                                                                       
      22046    G2345                                                                                                      
      22046    TOP                                                                                                        
      22046    TOP                                                                                                        
                                                                                                                          
    * create a macro array of the variable names;                                                                         
                                                                                                                          
    %array(vars,values=%utl_varlist(have));                                                                               
                                                                                                                          
    %put &=vars1;                                                                                                         
    %put &=vars2;                                                                                                         
    %put &=varsn;                                                                                                         
                                                                                                                          
    VARS1=DATE                                                                                                            
    VARS2=CODE                                                                                                            
    VARSN=2                                                                                                               
                                                                                                                          
                                                                                                                          
    *            _               _                                                                                        
      ___  _   _| |_ _ __  _   _| |_                                                                                      
     / _ \| | | | __| '_ \| | | | __|                                                                                     
    | (_) | |_| | |_| |_) | |_| | |_                                                                                      
     \___/ \__,_|\__| .__/ \__,_|\__|                                                                                     
                    |_|                                                                                                   
    ;                                                                                                                     
                                                                                                                          
    Up to 40 obs from WANT_COR total obs=3                                                                                
                                                                                                                          
     Label    AUTO    G2345    TOP                                                                                        
                                                                                                                          
     22045      1       1       0                                                                                         
     22046      0       1       2                                                                                         
                                                                                                                          
    *          _       _   _                                                                                              
     ___  ___ | |_   _| |_(_) ___  _ __  ___                                                                              
    / __|/ _ \| | | | | __| |/ _ \| '_ \/ __|                                                                             
    \__ \ (_) | | |_| | |_| | (_) | | | \__ \                                                                             
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|___/                                                                             
      __ _    ___ ___  _ __ _ __ ___  ___ _ __                                                                            
     / _` |  / __/ _ \| '__| '__/ _ \/ __| '_ \                                                                           
    | (_| | | (_| (_) | |  | | |  __/\__ \ |_) |                                                                          
     \__,_|  \___\___/|_|  |_|  \___||___/ .__/                                                                           
                                         |_|                                                                              
    ;                                                                                                                     
                                                                                                                          
    ods select none;                                                                                                      
    ods output observed=want_cor;                                                                                         
    proc corresp data=have dim=1 observed;                                                                                
    tables &vars1,&vars2;                                                                                                 
    run;quit;                                                                                                             
    ods select all;                                                                                                       
                                                                                                                          
    * you will need to use a format to display the date;                                                                  
    WORK.WANT_COR total obs=3                                                                                             
                                                                                                                          
      Label    AUTO    G2345    TOP    Sum                                                                                
                                                                                                                          
      22045      1       1       0      2                                                                                 
      22046      0       1       2      3                                                                                 
      Sum        1       2       2      5                                                                                 
                                                                                                                          
    *_         __                                                                                                         
    | |__     / _|_ __ ___  __ _                                                                                          
    | '_ \   | |_| '__/ _ \/ _` |                                                                                         
    | |_) |  |  _| | |  __/ (_| |                                                                                         
    |_.__(_) |_| |_|  \___|\__, |                                                                                         
                              |_|                                                                                         
    ;                                                                                                                     
                                                                                                                          
    %utl_odsfrq(setup);                                                                                                   
    proc freq data=have;                                                                                                  
     tables &vars1 * &vars2;                                                                                              
    run;quit;                                                                                                             
    %utl_odsfrq(outdsn=want_frq);                                                                                         
                                                                                                                          
    proc print data=want_frq(where=(rownam="COUNT")) width=min;;                                                          
    format data date9.;                                                                                                   
    run;quit;                                                                                                             
                                                                                                                          
      row                                                                                                                 
      Nam     level    AUTO    G2345    TOP    TOTAL                                                                      
                                                                                                                          
     COUNT    22045     1        1       0       2                                                                        
     COUNT    22046     0        1       2       3                                                                        
                                                                                                                          
    *         _        _           _       _                                                                              
      ___    | |_ __ _| |__  _   _| | __ _| |_ ___                                                                        
     / __|   | __/ _` | '_ \| | | | |/ _` | __/ _ \                                                                       
    | (__ _  | || (_| | |_) | |_| | | (_| | ||  __/                                                                       
     \___(_)  \__\__,_|_.__/ \__,_|_|\__,_|\__\___|                                                                       
                                                                                                                          
    ;                                                                                                                     
                                                                                                                          
    %utl_odstab(setup);                                                                                                   
    options formchar='|';                                                                                                 
    proc tabulate data=have;                                                                                              
      class &vars1 &vars2;                                                                                                
     table &vars1='',&vars2=' '*(n=' ')/ misstext='0';                                                                    
    run;quit;                                                                                                             
    %utl_odstab(outdsn=want_tab);                                                                                         
                                                                                                                          
    proc print data=want_tab(keep=j:) width=min;                                                                          
    run;quit;                                                                                                             
                                                                                                                          
    * I use j variables because tabulate often repeats the same column name;                                              
                                                                                                                          
       j1      j2AUTO    j3G2345    j4TOP                                                                                 
                                                                                                                          
      22045      1          1         0                                                                                   
      22046      0          1         2                                                                                   
                                                                                                                          
    *    _                               _                                                                                
      __| |    _ __ ___ _ __   ___  _ __| |_                                                                              
     / _` |   | '__/ _ \ '_ \ / _ \| '__| __|                                                                             
    | (_| |_  | | |  __/ |_) | (_) | |  | |_                                                                              
     \__,_(_) |_|  \___| .__/ \___/|_|   \__|                                                                             
                       |_|                                                                                                
    ;                                                                                                                     
                                                                                                                          
    proc sql;                                                                                                             
      select                                                                                                              
          distinct &vars2                                                                                                 
      into :varpyp                                                                                                        
          separated by '|'                                                                                                
      from have;                                                                                                          
    ;quit;                                                                                                                
                                                                                                                          
    %put &=varpyp;                                                                                                        
    VARPYP=AUTO|G2345|TOP                                                                                                 
                                                                                                                          
    %utl_odsrpt(setup);                                                                                                   
    options missing='0';                                                                                                  
    proc report data=have box formchar='|' noheader nowd missing;                                                         
      title "|&vars1|&varpyp|";                                                                                           
      cols &vars1 &vars2, (n);                                                                                            
        define &vars1 / "" group;                                                                                         
        define &vars2 / "" across;                                                                                        
        define n / " ";                                                                                                   
    run;quit;                                                                                                             
    options missing=.;                                                                                                    
    %utl_odsrpt(outdsn=want_rpt);                                                                                         
                                                                                                                          
    WANT_RPT total obs=2                                                                                                  
                                                                                                                          
      DATE    AUTO    G2345    TOP                                                                                        
                                                                                                                          
     22045      1       1       0                                                                                         
     22046      0       1       2                                                                                         
                                                                                                                          
                                                                                                                          
