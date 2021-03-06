# PHP ITL extension

Author: Yahya Mohamamd <mfyahya@gmail.com>


## Introduction

The itl php extension contains function for Islamic prayer times and Hijri year
and date calculations.

## Requirements 

The [libitl][] library from the Islamic Tools & Libraries (ITL) is required for
this extension.

## Installation

 * `libitl` must be already be installed along with header files.
 * Download php source code and create directory `$PHP_SRC_DIR/ext/itl`
   and copy the files `config.m4`, `itl.c php_itl.h`, and `itl.php` to it.
 * Run the following commands

    ```sh
    cd $PHP_SRC_DIR/ext/itl
    phpize
    ./configure --with-itl=/path/to/libitl
    ```

 * The ITL functions can be enabled by compiling the php-itl extension and
   copying the resulting `.so` file to PHP's extension directory. 

## Functions

The following functions are available:

   1. `itl_get_prayer_times` -- Get prayer times for a specific location,
      method, and date.
   2. `itl_get_north_qibla` -- Get Qibla direction measured from North.
   3. `itl_h_date` -- Convert Gregorian date to Hijri.
   4. `itl_g_date` -- Convert Hijri date to Gregorian. 


----------------------------------------------------------------------------------
### **itl_get_prayer_times**
*itl_get_prayer_times* -- Get prayer times for a given location, method, and date.

##### Description
```php
mixed itl_get_prayer_times(double longitude, double latitude, double gmtdiff, mixed method, int day, int month, int year [, boolean dst [, double sealevel [, double pressure [, double temperature]]]])
```

Returns an array containing timings for the five daily prayers and shurooq
based on several parameters. *method* may be *NULL* in which case the
default Umm Al-Qurra method will be used.

##### Parameters

 * _longitude_ -- The longitude of the location in degrees (EPSG 4326)
 * _latitude_ -- The latitude of the location in degrees (EPSG 4326)
 * _gmtdiff_ -- GMT offset
 * _method_ -- The _method_ parameter can be _NULL_, an integer or an
   array. If it is _NULL_, the Umm Al-Qurra method will be used by default. If
   it is an integer, it should have one of the following values:

<table>
<tr><th> method value </th><th> description </th></tr>
<tr><td> 1 </td><td> Egyptian General Authority of Survey </td></tr>
<tr><td> 2 </td><td> University of Islamic Sciences, Karachi (Shaf'i) </td></tr>
<tr><td> 3 </td><td> University of Islamic Sciences, Karachi (Hanafi) </td></tr>
<tr><td> 4 </td><td> Islamic Society of North America </td></tr>
<tr><td> 5 </td><td> Muslim World League (MWL) </td></tr>
<tr><td> 6 </td><td> Umm Al-Qurra (Saudi Arabia) </td></tr>
<tr><td> 7 </td><td> Fixed Isha Interval (always 90) </td></tr>
</table>

If *method* is an array it should contain the following paramters:

<table>
<tr><th> method array index</th><th> description</th></tr>
<tr><td> 0 </td><td> Fajr angle (19 will be used if set to *NULL*) </td></tr>
<tr><td> 1 </td><td> Isha angle (0 will be used if set to *NULL*) </td></tr>
<tr><td> 2 </td><td> Imsaak angle (1.5 will be used if set to *NULL*) </td></tr>
<tr><td> 3 </td><td> Fajr interval (0 will be used if set to *NULL*) </td></tr>
<tr><td> 4 </td><td> Ishaa interval (0 will be used if set to *NULL*) </td></tr>
<tr><td> 5 </td><td> Imsaak interval (default 10 minutes before Fajr if Fajr interval is set) </td></tr>
<tr><td> 6 </td><td>
                Rounding spec
                <ul>
                <li>0: No Rounding. <code>Prayer.seconds</code> is set to the amount of computed seconds.</li>
                <li>1: Normal Rounding. If seconds are equal to 30 or above, add 1 minute. Sets
                <code>Prayer.seconds</code> to zero.</li>
                <li>2: Special Rounding.  Similar to normal rounding but we always round down for
                Shurooq and Imsaak times. (default)</li>
                <li>3: Aggressive Rounding. Similar to Special Rounding but we add 1 minute if the
                seconds value are equal to 1 second or more.</li>
                </ul>
            </td></tr>

<tr><td> 7 </td><td>
                Mathab 
                <ul>
                <li>1: Shaf'i (default)</li>
                <li>2: Hanafi)</li>
                </ul>
            </td></tr>

<tr><td> 8 </td><td> Nearest latitude used for extreme methods. Default is 48.5</td></tr>

<tr><td> 9 </td><td>
                Extreme latitude calculation method 
                <ul>
                <li>0:  none. if unable to calculate, leave as 99:99 </li>
                <li>1:  Nearest Latitude: All prayers Always </li>
                <li>2:  Nearest Latitude: Fajr Ishaa Always </li>
                <li>3:  Nearest Latitude: Fajr Ishaa if invalid </li>
                <li>4:  Nearest Good Day: All prayers Always </li>
                <li>5:  Nearest Good Day: Fajr Ishaa if invalid (default) </li>
                <li>6:  1/7th of Night: Fajr Ishaa Always </li>
                <li>7:  1/7th of Night: Fajr Ishaa if invalid </li>
                <li>8:  1/7th of Day: Fajr Ishaa Always </li>
                <li>9:  1/7th of Day: Fajr Ishaa if invalid </li>
                <li>10: Half of the Night: Fajr Ishaa Always </li>
                <li>11: Half of the Night: Fajr Ishaa if invalid </li>
                <li>12: Minutes from Shorooq/Maghrib: Fajr Ishaa Always (e.g. Maghrib=Ishaa) </li>
                <li>13: Minutes from Shorooq/Maghrib: Fajr Ishaa If invalid </li>
                </ul>
            </td></tr>
<tr><td> 10 </td><td>
                Enable offsets.
                <ul>
                <li>1: enable</li>
                <li>0: disable (see offsets array in next parameter below)</li>
                </ul>
            </td></tr>
<tr><td> 11 </td><td>
                Offsets array of 6 doubles to add/subtract minutes from prayer times.
                The following array will add 30 seconds to Maghrib, and subtract 2 minutes from Isha
                <code>array(0, 0, 0, 0, 0.5, -2)</code>
            </td></tr>
</table>

 * _day_ -- The day of the month.
 * _month_ -- The month of the year (1-12).
 * _year_ -- The 4 digit year.
 * _dst_ -- Whether Daylight Savings Times is being used. default is _false_.
 * _sealevel_ -- The sea level in meters. Default is 0.
 * _pressure_ -- The pressue in millibars. Default is 1010
 * _temperature_ -- The pressure in Celsius. Default is 10.

##### Return values

**itl_get_prayer_times()** returns an array of 6 elements containing the 5
prayer times and shurooq times.
If an error occurs boolean _false_ will be returned.  A sample output is show
below:

```php
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
```
The array indices from 0 to 6 are in the order _Fajr_, _Shurooq_, _Thuhr_, _Asr_, _Maghrib_, and _Isha_.
   
##### Examples

* Calculate prayer times for given coordinates and date using default method (Umm Al-Qurra):
```php
<?php
itl_get_prayer_times(39.8304, 21.4206, 3, NULL, 21, 8, 2008);
?>
``` 

* Calculate prayer times specifying method by an integer. (method 6 is Umm Al-Qurra)
```php
<?php
itl_get_prayer_times(39.8304, 21.4206, 3, 6, 21, 8, 2008);
?>
``` 

* Calculate prayer times by specifying all method parameters
```php
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
```

Wherever _NULL_ is used as a parameter, default values from Umm Al-Qurra method will be used.
   

----------------------------------------------------------------------------------
### **itl_get_north_qibla**
*itl_get_north_qibla* -- Get Qibla direction measured from North.

##### Description
```php
double itl_get_north_qibla(double longitude, double latitude)
```

##### Parameters
* _longitude_ -- longitude in degrees (EPSG 4326)
* _latitude_ -- latitude in degrees (EPSG 4326)

##### Return values
Angle to Ka'aba in degrees, measured from North in anti-clockwise direction

##### Examples
```php
<?php
print itl_get_north_qibla(36, 21);
?>
```
outputs
```php 
-82.537307136848 
```


----------------------------------------------------------------------------------
### **itl_h_date**
itl_h_date -- Convert Gregorian date to Hijri

##### Description
```php
array itl_h_date(int day, int month, int year)
```

##### Parameters
* _day_ -- day of the month (1-31)
* _month_ -- month of year (1-12)
* _year_ -- 4 digit year

##### Return values
Array containing the day, month, year, weekday, and units (B.H. or A.H.)

##### Examples
```php
<?php
print_r(itl_h_date(20, 8, 2008));
?>
```
outputs:
```php
Array
(
    [day] => 17
    [month] => 8
    [year] => 1429
    [weekday] => 3
    [units] => A.H
)
```


----------------------------------------------------------------------------------
### **itl_g_date**
**itl_g_date** -- Convert Hijri date to Gregorian

##### Description
```php
array itl_g_date(int day, int month, int year)
```

##### Parameters
* _day_ -- day of the month (1-31)
* _month_ -- month of year (1-12)
* _year_ -- 4 digit year

##### Return values
Array containing the day, month, year, weekday, and units (B.C. or A.D.)

##### Examples
```php
<?php
print_r(itl_g_date(17, 8, 1429));
?>
```
outputs:
```php
Array
(
    [day] => 20
    [month] => 8
    [year] => 2008
    [weekday] => 3
    [units] => A.D
)
```


[libitl]: http://projects.arabeyes.org/project.php?proj=IT "Islamic Tools and Libraries"
