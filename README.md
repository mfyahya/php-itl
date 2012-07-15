==========================================
PHP ITL extension

Author: Yahya Mohamamd <mfyahya@gmail.com>
==========================================

Introduction

The itl php extension contains function for Islamic prayer times calculation
and Hijri year date calculation.

Requirements 

The libitl library from the Islamic Tools & Libraries (ITL) is required for
this extension.

Installation

 * libitl must be already be installed along with header files.
 * Download php source code and create directory $PHP_SRC_DIR/ext/itl
   and copy the files config.m4 itl.c php_itl.h and itl.php to it
 * Run the following commands
                cd $PHP_SRC_DIR/ext/itl
                phpize
                ./configure --with-itl=/path/to/libitl
 * The ITL functions can be enabled by compiling the php-itl extension and
 * copying the resulting .so file to PHP's extension directory. 

Functions

The following functions are available:

   1. itl_get_prayer_times -- Get prayer times for a specific location, method,
      and date.
   2. itl_get_north_qibla -- Get Qibla direction measured from North.
   3. itl_h_date -- Convert Gregorian date to Hijri.
   4. itl_g_date -- Convert Hijri date to Gregorian. 

FIXME: Convert documentation below from trac wiki format [http://trac.edgewall.org/wiki/WikiFormatting]

= itl_get_prayer_times = 
itl_get_prayer_times -- Get prayer times for a given location, method, and date.

== Description ==
mixed '''itl_get_prayer_times'''(double longitude, double latitude, double gmtdiff, mixed method, int day, int month, int year [, boolean dst [, double sealevel [, double pressure [, double temperature]]]])

Returns an array containing times for the five prayers and shurooq based on
several parameters. ''method'' may be ''NULL'' in which case the default Umm
Al-Qurra method will be used.

== Parameters ==

 * ''longitude'' -- The longitude of the location in degrees (EPSG 4326)
 * ''latitude'' -- The latitude of the location in degrees (EPSG 4326)
 * ''gmtdiff'' -- GMT offset
 * ''method'' -- The ''method'' parameter can be ''NULL'', an integer or an
   array. If it is ''NULL'', the Umm Al-Qurra method will be used by default. If
   it is an integer, it should have one of the following values:

|| '''method value''' || '''description''' ||
|| 1 || Egyptian General Authority of Survey ||
|| 2 || University of Islamic Sciences, Karachi (Shaf'i) ||
|| 3 || University of Islamic Sciences, Karachi (Hanafi) ||
|| 4 || Islamic Society of North America ||
|| 5 || Muslim World League (MWL) ||
|| 6 || Umm Al-Qurra (Saudi Arabia ||
|| 7 || Fixed Isha Interval (always 90) ||

If ''method'' is an array it should contain the following paramters:

|| '''method array index'''|| '''description'''||
|| 0 || Fajr angle (19 will be used if set to ''NULL'') ||
|| 1 || Isha angle (0 will be used if set to ''NULL'') ||
|| 2 || Imsaak angle (1.5 will be used if set to ''NULL'') ||
|| 3 || Fajr interval (0 will be used if set to ''NULL'') ||
|| 4 || Ishaa interval (0 will be used if set to ''NULL'') ||
|| 5 || Imsaak interval (default 10 minutes before Fajr if Fajr interval is set) ||
|| 6 || Rounding[[br]] 0: No Rounding. "Prayer.seconds" is set to the amount of computed seconds.[[br]] 1: Normal Rounding. If seconds are equal to 30 or above, add 1 minute. Sets "Prayer.seconds" to zero.[[br]] 2: Special Rounding. Similar to normal rounding but we always round down for Shurooq and Imsaak times. (default)[[br]] 3: Aggressive Rounding. Similar to Special Rounding but we add 1 minute if the seconds value are equal to 1 second or more.||
|| 7 || Mathab (1: Shaf'i (default) 2: Hanafi)||
|| 8 || Nearest latitude used for extreme methods. Default is 48.5||
|| 9 || Extreme latitude calculation method [[br]] 0:  none. if unable to calculate, leave as 99:99 [[br]] 1:  Nearest Latitude: All prayers Always [[br]] 2:  Nearest Latitude: Fajr Ishaa Always [[br]] 3:  Nearest Latitude: Fajr Ishaa if invalid [[br]] 4:  Nearest Good Day: All prayers Always [[br]] 5:  Nearest Good Day: Fajr Ishaa if invalid (default) [[br]] 6:  1/7th of Night: Fajr Ishaa Always [[br]] 7:  1/7th of Night: Fajr Ishaa if invalid [[br]] 8:  1/7th of Day: Fajr Ishaa Always [[br]] 9:  1/7th of Day: Fajr Ishaa if invalid [[br]] 10: Half of the Night: Fajr Ishaa Always [[br]] 11: Half of the Night: Fajr Ishaa if invalid [[br]] 12: Minutes from Shorooq/Maghrib: Fajr Ishaa Always (e.g. Maghrib=Ishaa) [[br]] 13: Minutes from Shorooq/Maghrib: Fajr Ishaa If invalid [[br]] ||
|| 10 || Enable offsets. 1: enable 0: disable (see offsets array in next parameter below)||
|| 11 || Offsets array of 6 doubles to add/subtract minutes from prayer times.[[br]]The following array will add 30 seconds to Maghrib, and subtract 2 minutes from Isha[[br]] array(0, 0, 0, 0, 0.5, -2)||

 * ''day'' -- The day of the month.
 * ''month'' -- The month of the year (1-12).
 * ''year'' -- The 4 digit year.
 * ''dst'' -- Whether Daylight Savings Times is being used. default is ''false''.
 * sealevel -- The sea level in meters. Default is 0.
 * pressure -- The pressue in millibars. Default is 1010
 * temperature -- The pressure in Celsius. Default is 10.

== Return values ==

'''itl_get_prayer_times()''' returns an array of 6 elements containing the 5
prayer times and shurooq times.
If an error occurs boolean ''false'' will be returned.
A sample output is show below:
{{{
Array
(
    [0] => Array
        (
            [hour] => 4
            [minute] => 33
            [second] => 0
        )
    [1] => Array
        (
            [hour] => 5
               [minute] => 57
               [second] => 0
           )
       [2] => Array
           (
               [hour] => 12
               [minute] => 21
               [second] => 0
           )
       [3] => Array
           (
               [hour] => 15
               [minute] => 50
               [second] => 0
           )
       [4] => Array
           (
               [hour] => 18
               [minute] => 44
               [second] => 0
           )
       [5] => Array
           (
               [hour] => 20
               [minute] => 14
               [second] => 0
           )
   )
   }}}
   The array indices from 0 to 6 are in the order ''Fajr'', ''Shurooq'', ''Thuhr'', ''Asr'', ''Maghrib'', and ''Isha''.
   
   == Examples ==
    * Calculate prayer times for given coordinates and date using default method (Umm Al-Qurra):
   {{{
   <?php
   itl_get_prayer_times(39.8304, 21.4206, 3, NULL, 21, 8, 2008);
   ?>
   }}} 
   
    * Calculate prayer times specifying method by an integer. (method 6 is Umm Al-Qurra)
   {{{
   <?php
   itl_get_prayer_times(39.8304, 21.4206, 3, 6, 21, 8, 2008);
   ?>
   }}} 
    
    * Calculate prayer times by specifying all method parameters
   {{{
   <?php
   itl_get_prayer_times(39.8304, 21.4206, 3,
           array(
                   5.1,  // fajr angle
                   3.2,  // ishaa angle
                   1.5,  // imsaak angle
                   NULL, // fajr interval
                   NULL, // isha interval
                   NULL, // imsaak interval
                   1,    // round
                   1,    //  mathab (1:shafi or 2:hanafi)
                   48.5, // nearest Lat
                   NULL, // extreme
                   1,    // enable offsets
                   array(3, 0, 0 ,0, 0, -10) // adds 3 minutes to Fajr, and subtracts 10 minutes from Isha
           ),
           21, 8, 2008));
   
   ?>
   }}}
   Wherever ''NULL'' is used as a parameter, default values from Umm Al-Qurra method will be used.
   
   = itl_get_north_qibla = 
   itl_get_north_qibla -- Get Qibla direction measured from North.
   
   == Description == 
   
   double '''itl_get_north_qibla'''(double longitude, double latitude)
   
   == Parameters ==
    * ''longitude'' -- longitude in degrees (EPSG 4326)
    * ''latitude'' -- latitude in degrees (EPSG 4326)
   
   == Return values ==
   Angle to Ka'aba in degrees, measured from North in anti-clockwise direction
   
   == Examples == 
   {{{
   <?php
   print itl_get_north_qibla(36, 21);
   ?>
   }}}
   outputs
   {{{ 
   -82.537307136848 
   }}}
   
   = itl_h_date = 
   itl_h_date -- Convert Gregorian date to Hijri
   
   == Description ==
   array '''itl_h_date'''(int day, int month, int year)
   
   == Parameters ==
    * ''day'' -- day of the month (1-31)
    * ''month'' -- month of year (1-12)
    * ''year'' -- 4 digit year
   
   == Return values == 
   Array containing the day, month, year, weekday, and units (B.H. or A.H.)
   
   == Examples ==
   {{{
   <?php
   print_r(itl_h_date(20, 8, 2008));
   ?>
   }}}
   outputs:
   {{{
   Array
   (
       [day] => 17
       [month] => 8
       [year] => 1429
       [weekday] => 3
       [units] => A.H
   )
   }}}
   
   = itl_g_date = 
   itl_g_date -- Convert Hijri date to Gregorian
   
   == Description ==
   array '''itl_g_date'''(int day, int month, int year)
   
   == Parameters ==
    * ''day'' -- day of the month (1-31)
    * ''month'' -- month of year (1-12)
    * ''year'' -- 4 digit year
   
   == Return values == 
   Array containing the day, month, year, weekday, and units (B.C. or A.D.)
   
   == Examples ==
   {{{
   <?php
   print_r(itl_g_date(17, 8, 1429));
   ?>
   }}}
   outputs:
   {{{
   Array
   (
       [day] => 20
       [month] => 8
       [year] => 2008
       [weekday] => 3
       [units] => A.D
   )
   }}}
   

