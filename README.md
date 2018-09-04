# gdal_contour_comparison

## Performances comparison

* Reference implementation (« gdal ») : gdal git commit b417a2ed57c5f4

* Compared implementations
  * « mine » : oslandia
  * "mine_altls": alternative linestring implementation (as a double std::vector of X and Y rather than a std::list of points)

* Rasters :
  * SRTM_36_03.tif
  * SRTM_36_04.tif
  * SRTM_37_03.tif

Command : 

```
gdal_contour -i 10 raster.tif out.shp
```
      
|            | time(mine) | mem(mine) | count(mine) | time(gdal) | mem(gdal) | count(gdal) |
|------------|------------|-----------|-------------|------------|-----------|-------------|
| SRTM_36_03 | 6.5s       | 91MB      | 129495      | 24s        | 88MB      | 128529      |
| SRTM_36_04 | 38s        | 320MB     | 389866      | 146s       | 274MB     | 384121      |
| SRTM_37_03 | 19.5s      | 443MB     | 341577      | 100s       | 367MB     | 343143      |


The new implementation is way faster than the initial one. About **3 times faster**, and up to **5 times** in certain cases. However, it demands a little bit more RAM (up to about 20% more).
The new implementation generates a similar amount of features (+ or - 1%).

### Valgrind/massif outputs

Mine
```
--------------------------------------------------------------------------------
Command:            /home/hme/src/gdal_gh/gdal/apps/.libs/gdal_contour -i 10 /data/gis/srtm/srtm_36_03.tif /data/gis/srtm/out_mine.shp
Massif arguments:   (none)
ms_print arguments: massif.out.13054
--------------------------------------------------------------------------------


    MB
90.85^                                                                       #
     |                                                                       #
     |                                                                 @     #
     |                                             @@@                 @::  :#
     |                               @             @                :::@:  ::#
     |                               @             @            :::::: @: :::#
     |                    : @@ ::::::@::           @           :::: :: @: :::#
     |                 :::::@ :: :: :@: ::: :: ::::@           :::: :: @: :::#
     |                @: :::@ :: :: :@: :: :: :: : @         :@:::: :: @: :::#
     |               @@: :::@ :: :: :@: :: :: :: : @        ::@:::: :: @: :::#
     |               @@: :::@ :: :: :@: :: :: :: : @  :: :::::@:::: :: @: :::#
     |              @@@: :::@ :: :: :@: :: :: :: : @  ::::: ::@:::: :: @: :::#
     |           :::@@@: :::@ :: :: :@: :: :: :: : @  ::::: ::@:::: :: @: :::#
     |         @@:: @@@: :::@ :: :: :@: :: :: :: : @  ::::: ::@:::: :: @: :::#
     |         @ :: @@@: :::@ :: :: :@: :: :: :: : @  ::::: ::@:::: :: @: :::#
     |       ::@ :: @@@: :::@ :: :: :@: :: :: :: : @  ::::: ::@:::: :: @: :::#
     |      @: @ :: @@@: :::@ :: :: :@: :: :: :: : @  ::::: ::@:::: :: @: :::#
     |     :@: @ :: @@@: :::@ :: :: :@: :: :: :: : @  ::::: ::@:::: :: @: :::#
     |  ::::@: @ :: @@@: :::@ :: :: :@: :: :: :: : @  ::::: ::@:::: :: @: :::#
     | @: ::@: @ :: @@@: :::@ :: :: :@: :: :: :: : @  ::::: ::@:::: :: @: :::#
   0 +----------------------------------------------------------------------->Gi
     0                                                                   18.56
```

GDAL
```
--------------------------------------------------------------------------------
Command:            gdal_contour -i 10 /data/gis/srtm/srtm_36_03.tif /data/gis/srtm/out_gdal.shp
Massif arguments:   (none)
ms_print arguments: massif.out.13275
--------------------------------------------------------------------------------


    MB
88.09^                                                                       #
     |                                                                       #
     |                                                                   @@  #
     |                                                                   @@ :#
     |                                                                @::@@::#
     |                     @                                       ::@@: @@::#
     |                 :   @       ::::                           :: @@: @@::#
     |               ::::@@@::::::::: :@::  :   :      :         ::: @@: @@::#
     |             ::: ::@ @: : :: :: :@: ::::::::::@@:::      @@::: @@: @@::#
     |            @: : ::@ @: : :: :: :@: : :: :::::@ ::::    :@ ::: @@: @@::#
     |          @@@: : ::@ @: : :: :: :@: : :: :::::@ :::::::::@ ::: @@: @@::#
     |        @@@ @: : ::@ @: : :: :: :@: : :: :::::@ ::::: :::@ ::: @@: @@::#
     |      ::@ @ @: : ::@ @: : :: :: :@: : :: :::::@ ::::: :::@ ::: @@: @@::#
     |      ::@ @ @: : ::@ @: : :: :: :@: : :: :::::@ ::::: :::@ ::: @@: @@::#
     |     :::@ @ @: : ::@ @: : :: :: :@: : :: :::::@ ::::: :::@ ::: @@: @@::#
     |   :::::@ @ @: : ::@ @: : :: :: :@: : :: :::::@ ::::: :::@ ::: @@: @@::#
     |  @: :::@ @ @: : ::@ @: : :: :: :@: : :: :::::@ ::::: :::@ ::: @@: @@::#
     | :@: :::@ @ @: : ::@ @: : :: :: :@: : :: :::::@ ::::: :::@ ::: @@: @@::#
     | :@: :::@ @ @: : ::@ @: : :: :: :@: : :: :::::@ ::::: :::@ ::: @@: @@::#
     | :@: :::@ @ @: : ::@ @: : :: :: :@: : :: :::::@ ::::: :::@ ::: @@: @@::#
   0 +----------------------------------------------------------------------->Gi
     0                                                                   103.3
```

## Behaviour comparison

On saddle points, the two implementations may have different behaviours.

(Illustrations made on another raster)

Legend : in green, « mine », in blue « gdal »

![cmp1](images/comparison_1.png)

![cmp2](images/comparison_2.png)

![cmp3](images/comparison_3.png)

![cmp4](images/comparison_4.png)

