django geo应用教程
==========

`参考资料：https://docs.djangoproject.com/en/1.11/ref/contrib/gis/tutorial/`

介绍
-------
GeoDjango是Django的一个包含的contrib模块，是一个世界范围地理Web构件。主要有以下特点：


1. django model 式的[ogc](http://www.opengeospatial.org/)数据
2. 使用django orm 操作和访问原始数据
3. 高级封装的python api操作gis等数据的不同数据形式
4. 从admin编辑地理数据

set up
=======
创建一个空间数据库
--------------
通常没有什么特别的set up，就像普通工程那样搭建数据库

* [安装PostGIS](https://docs.djangoproject.com/en/1.11/ref/contrib/gis/install/postgis/)
* [安装SpatiaLite](https://docs.djangoproject.com/en/1.11/ref/contrib/gis/install/spatialite/)

创建一个新工程文件
-----------
用django-amin创建新工程
`$ django-admin startproject geodjango`

然后创建一个新app

```bash
$ cd geodjango
$ python manage.py startapp world
```

设置settings.py
-------
编辑settings.py里面的数据库设置：

```python
DATABASES = {
    'default': {
         'ENGINE': 'django.contrib.gis.db.backends.spatialite',
         'NAME': 'test.db',
    },
}
```

另外还要修改INSTALLED_APPS,包含`django.contrib.admin`,` django.contrib.gis`和`world`.

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'django.contrib.gis',
    'world',
]
```

Geographic Data
=======

世界边界
----------
世界边界数据保存在一个[zip file](http://thematicmapping.org/downloads/TM_WORLD_BORDERS-0.3.zip).下载并解压

```bash
$ mkdir world/data
$ cd world/data
$ wget http://thematicmapping.org/downloads/TM_WORLD_BORDERS-0.3.zip
$ unzip TM_WORLD_BORDERS-0.3.zip
$ cd ../..
```

这个zip文件包含有一系列[ESRI Shapefile](https://en.wikipedia.org/wiki/Shapefile)的文件，一种最出名的地理数据格式。解包后这个文件存在4个文件：

* .shp:保存世界边界的向量数据
* .shx:空间索引文件
* .dbf:保存非地理信息内容的数据库文件（int 和 char）
* .prj:包含保存在shapefile里面空间指向信息

使用ogrinfo测试空间数据
----------
GDAL 中的ogrinfo可以用来检测shapefile里面的数据

```bash
$ ogrinfo world/data/TM_WORLD_BORDERS-0.3.shp
INFO: Open of `world/data/TM_WORLD_BORDERS-0.3.shp'
      using driver `ESRI Shapefile' successful.
1: TM_WORLD_BORDERS-0.3 (Polygon)
```

ogrinfo 告诉我们这个shapefile只包含一层。你可以通过-so 选项获取更多的数据。

```bash
$ ogrinfo -so world/data/TM_WORLD_BORDERS-0.3.shp TM_WORLD_BORDERS-0.3
INFO: Open of `world/data/TM_WORLD_BORDERS-0.3.shp'
      using driver `ESRI Shapefile' successful.

Layer name: TM_WORLD_BORDERS-0.3
Geometry: Polygon
Feature Count: 246
Extent: (-180.000000, -90.000000) - (180.000000, 83.623596)
Layer SRS WKT:
GEOGCS["GCS_WGS_1984",
    DATUM["WGS_1984",
        SPHEROID["WGS_1984",6378137.0,298.257223563]],
    PRIMEM["Greenwich",0.0],
    UNIT["Degree",0.0174532925199433]]
FIPS: String (2.0)
ISO2: String (2.0)
ISO3: String (3.0)
UN: Integer (3.0)
NAME: String (50.0)
AREA: Integer (7.0)
POP2005: Integer (10.0)
REGION: Integer (3.0)
SUBREGION: Integer (3.0)
LON: Real (8.3)
LAT: Real (7.3)
```

地理模型
=======
定义一个geographic model
----------

```python
from django.contrib.gis.db import models

class WorldBorder(models.Model):
    # Regular Django fields corresponding to the attributes in the
    # world borders shapefile.
    name = models.CharField(max_length=50)
    area = models.IntegerField()
    pop2005 = models.IntegerField('Population 2005')
    fips = models.CharField('FIPS Code', max_length=2)
    iso2 = models.CharField('2 Digit ISO', max_length=2)
    iso3 = models.CharField('3 Digit ISO', max_length=3)
    un = models.IntegerField('United Nations Code')
    region = models.IntegerField('Region Code')
    subregion = models.IntegerField('Sub-Region Code')
    lon = models.FloatField()
    lat = models.FloatField()

    # GeoDjango-specific: a geometry field (MultiPolygonField)
    mpoly = models.MultiPolygonField()

    # Returns the string representation of the model.
    def __str__(self):              # __unicode__ on Python 2
        return self.name
```


这里model注意要用`django.contrib.gis.db`


Run migrate
----
定义好你的model后，需要同步到数据库。

```bash
$ python manage.py makemigrations
Migrations for 'world':
  world/migrations/0001_initial.py:
    - Create model WorldBorder
```

`warning`：其中我遇到了数据库问题，我使用的是sqlite数据库，报一下错误。

```
django.core.exceptions.ImproperlyConfigured: Unable to load the SpatiaLite library extension "libspatialite.so.7" because: /usr/lib/x86_64-linux-gnu/libspatialite.so.7: undefined symbol: sqlite3_spatialite_init
```

搜索一下，需要在settings.py文件加入一下：
`SPATIALITE_LIBRARY_PATH = 'mod_spatialite'`

具体sqlite需要安装一些另外的库，[参见这里](https://docs.djangoproject.com/en/1.10/ref/contrib/gis/install/spatialite/)


--
然后再进行数据库初始化语句生成。(这个sqlmigrate特性我以前没用过，没看过文档，发现好像是生成sql语句创建数据库)

```bash
$ python manage.py sqlmigrate world 0001
```

最后创建数据库

```bash
$ python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions, world
Running migrations:
  ...
  Applying world.0001_initial... OK
```

导入spatial数据
=====
现在来说明如何通过[LayerMapping data import utility](https://docs.djangoproject.com/en/1.11/ref/contrib/gis/layermapping/)导入spatial数据进入geodjango model中。

这里除了使用geodjango的工具，还有许多不一样的方法导入到数据库中

* [ogr2ogr](http://www.gdal.org/ogr2ogr.html):一种命令行式的gdal工具，可以把向量数据格式倒入到PostGIS,Mysql和Oracle中
* [shp2pgsq](https://postgis.net/docs/using_postgis_dbmanagement.html#shp2pgsql_usage):这个工具包含把ESRI shapefile导入到PostGIS中。

GDAL interface
----

先前我们使用过`ogrinfo`来检验世界边界的shapefile。Geodjango也包含了一个Pythonic的接口使用gdal强大的OGR库。

首先进入django shell

```bash
$ python manage.py shell
```

然后使用先前下载的文件

```bash
>>> import os
>>> import world
>>> world_shp = os.path.abspath(os.path.join(os.path.dirname(world.__file__),
...                             'data', 'TM_WORLD_BORDERS-0.3.shp'))
```

打开文件

```bash
>>> from django.contrib.gis.gdal import DataSource
>>> ds = DataSource(world_shp)
>>> print(ds)
/ ... /geodjango/world/data/TM_WORLD_BORDERS-0.3.shp (ESRI Shapefile)
```

datasource 实例可以有多层地理特性。但是，shapefiles只有一层：

```bash
>>> print(len(ds))
1
>>> lyr = ds[0]
>>> print(lyr)
TM_WORLD_BORDERS-0.3
```

```bash
>>> print(lyr.geom_type)
Polygon
>>> print(len(lyr))
246
>>> srs = lyr.srs
>>> print(srs)
GEOGCS["GCS_WGS_1984",
    DATUM["WGS_1984",
        SPHEROID["WGS_1984",6378137.0,298.257223563]],
    PRIMEM["Greenwich",0.0],
    UNIT["Degree",0.0174532925199433]]
>>> srs.proj4 # PROJ.4 representation
'+proj=longlat +ellps=WGS84 +datum=WGS84 +no_defs '
>>> print(lyr.fields)
['FIPS', 'ISO2', 'ISO3', 'UN', 'NAME', 'AREA', 'POP2005', 'REGION', 'SUBREGION', 'LON', 'LAT']
>>> [fld.__name__ for fld in lyr.field_types]
['OFTString', 'OFTString', 'OFTString', 'OFTInteger', 'OFTString', 'OFTInteger', 'OFTInteger', 'OFTInteger', 'OFTInteger', 'OFTReal', 'OFTReal']
>>> for feat in lyr:
...    print(feat.get('NAME'), feat.geom.num_points)
...
Guernsey 18
Jersey 26
South Georgia South Sandwich Islands 338
Taiwan 363
>>> lyr[0:2]
[<django.contrib.gis.gdal.feature.Feature object at 0x2f47690>, <django.contrib.gis.gdal.feature.Feature object at 0x2f47650>]
>>> feat = lyr[234]
>>> print(feat.get('NAME'))
San Marino>>> geom = feat.geom
>>> print(geom.wkt)
POLYGON ((12.415798 43.957954,12.450554 ...
>>> print(geom.json)
{ "type": "Polygon", "coordinates": [ [ [ 12.415798, 43.957954 ], [ 12.450554, 43.979721 ], ...
```

以上是一些gdal操作具体参见https://docs.djangoproject.com/en/1.11/ref/contrib/gis/tutorial/#importing-spatial-data

LayerMapping
===
为了导入数据，在world文件夹建立一个`load.py`文件。

```python
import os
from django.contrib.gis.utils import LayerMapping
from .models import WorldBorder

world_mapping = {
    'fips' : 'FIPS',
    'iso2' : 'ISO2',
    'iso3' : 'ISO3',
    'un' : 'UN',
    'name' : 'NAME',
    'area' : 'AREA',
    'pop2005' : 'POP2005',
    'region' : 'REGION',
    'subregion' : 'SUBREGION',
    'lon' : 'LON',
    'lat' : 'LAT',
    'mpoly' : 'MULTIPOLYGON',
}

world_shp = os.path.abspath(
    os.path.join(os.path.dirname(__file__), 'data', 'TM_WORLD_BORDERS-0.3.shp'),
)

def run(verbose=True):
    lm = LayerMapping(
        WorldBorder, world_shp, world_mapping,
        transform=False, encoding='iso-8859-1',
    )
    lm.save(strict=True, verbose=verbose)
```

* 每个在world_mapping key对应WorldBorder model的每一个field。
* mpoly关键字对应的值是multipolygon。
* encoding的关键字使用shapefile里面的关键字。

```bash
$ python manage.py shell
```

