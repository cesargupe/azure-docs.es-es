---
title: 'Carga de datos con Azure Machine Learning Data Prep SDK: Python'
description: Aprenda a cargar datos con Azure Machine Learning Data Prep SDK. Puede cargar distintos tipos de datos de entrada, especificar los parámetros y tipos de archivos de datos o utilizar la funcionalidad de lectura inteligente de SDK para detectar automáticamente el tipo de archivo.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 91db32b7056a0cf211e6293a891d58e0239ca499
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237592"
---
# <a name="load-and-read-data-with-azure-machine-learning"></a>Carga y lectura de datos con Azure Machine Learning

Use [Azure Machine Learning Data Prep SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py) para cargar diferentes tipos de datos de entrada. 

Para cargar los datos, especifique el tipo de archivo de datos y sus parámetros.

## <a name="use-text-line-data"></a>Uso de datos de línea de texto 
Una de las formas más sencillas de cargar datos es leerlos como líneas de texto.

Código de ejemplo:
```python
dataflow = dprep.read_lines(path='./data/text_lines.txt')
dataflow.head(5)
```
||Línea|
|----|-----|
|0|Fecha \|\|  Temperatura mínima \|\|  Temperatura máxima|
|1|1-07-2015 \|\|  -4,1 \|\|  10,0|
|2|2-07-2015 \|\|  -0,8 \|\|  10,8|
|3|3-07-2015 \|\|  -7,0 \|\|  10,5|
|4|4-07-2015 \|\|  -5,5 \|\|  9,3|

Después de que se ingieren los datos, puede recuperar un DataFrame de pandas para el conjunto de datos completo.

Código de ejemplo:
```python
df = dataflow.to_pandas_dataframe()
df
```
Salida de ejemplo:
||Línea|
|----|-----|
|0|Fecha \|\| Temperatura mínima \|\| Temperatura máxima|
|1|1-07-2015\|\| 4,1\|\| 10,0|
|2|2-07-2015\|\| 0,8\|\| 10,8|
|3|3-07-2015\|\| 7,0\|\| 10,5|
|4|4-07-2015\|\| 5,5\|\| 9,3|

## <a name="use-csv-data"></a>Uso de datos CSV
Al leer archivos delimitados, puede dejar que el runtime subyacente infiera los parámetros de análisis (como un separador, codificación, si se usan encabezados, etc.) en lugar de proporcionarlos. Para este ejemplo intente leer un archivo especificando solo su ubicación. 

Código de ejemplo:
```python
# SAS expires June 16th, 2019
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dataflow.head(5)
```

Salida de ejemplo:
| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0||stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|ALABAMA|1|101710|Hale County|10171002158| |
|2|ALABAMA|1|101710|Hale County|10171002162| |
|3|ALABAMA|1|101710|Hale County|10171002156| |
|4|ALABAMA|1|101710|Hale County|10171000588|2|

Uno de los parámetros que puede especificar es el número de líneas que se omiten en los archivos que va a leer. Use el siguiente código para filtrar y eliminar la línea duplicada.
```python
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dataflow.head(5)
```

Salida de ejemplo:
| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale County|10171002158|29|
|1|ALABAMA|1|101710|Hale County|10171002162|40 |
|2|ALABAMA|1|101710|Hale County|10171002156| 43|
|3|ALABAMA|1|101710|Hale County|10171000588|2|
|4|ALABAMA|1|101710|Hale County|10171000589|23 |

A continuación, puede mirar los tipos de datos de las columnas.
Código de ejemplo:
```python
dataflow.head(1).dtypes

stnam                     object
fipst                     object
leaid                     object
leanm10                   object
ncessch                   object
schnam10                  object
MAM_MTH00numvalid_1011    object
dtype: object
```

Lamentablemente, todas nuestras columnas regresaron como cadenas. Eso se debe a que, de forma predeterminada, Azure Machine Learning Data Prep SDK no cambia el tipo de datos. El origen de datos del que se lee es un archivo de texto, por lo que el SDK lee todos los valores como cadenas. Sin embargo, en este ejemplo queremos analizar las columnas numéricas como números. Para ello, puede establecer el parámetro inference_arguments como current_culture.

```
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dataflow.head(1).dtypes

stnam                      object
fipst                     float64
leaid                     float64
leanm10                    object
ncessch                   float64
schnam10                   object
ALL_MTH00numvalid_1011    float64
dtype: object
```

Algunas de las columnas se detectaron correctamente como números y su tipo se establece en float64. Tras la ingesta puede recuperar un DataFrame de pandas de todo conjunto de datos.
Código de ejemplo:
```python
df = dataflow.to_pandas_dataframe()
df
```

Salida de ejemplo:
| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1,017100e+10|49,0|
|1|ALABAMA|Hale County|1,017100e+10|40,0|
|2|ALABAMA|Hale County|1,017100e+10|43,0|
|3|ALABAMA|Hale County|1.017100e+10|2.0|
|4|ALABAMA|Hale County|1,017100e+10|23,0|

## <a name="use-excel-data"></a>Uso de datos de Excel
Azure Machine Learning Data Prep SDK incluye una función `read_excel` para cargar archivos de Excel. Código de ejemplo:
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx')
dataflow.head(5)
```

Salida de ejemplo:
||Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|Hoba|Iron, IVB|60000000,0|Encontrado|1920,0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |-19,58333|17,91667|
|1|Cape York|Iron, IIIAB|58200000,0|Encontrado|1818,0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |76,13333|-64,93333|
|2|Campo del Cielo|Iron, IAB-MG|50000000,0|Encontrado|1576,0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |-27,46667|-60,58333|
|3|Canyon Diablo|Iron, IAB-MG|30000000,0|Encontrado|1891,0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |35,05000|-111,03333|
|4|Armanty|Iron, IIIE|28000000,0|Encontrado|1898,0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |47,00000|88,00000|

Ha cargado la primera hoja del archivo de Excel. Se puede lograr el mismo resultado si se especifica explícitamente el nombre de la hoja que se desea cargar. Si desea cargar en su lugar la segunda hoja, puede usar su nombre como argumento. Por ejemplo: 
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dataflow.head(5)
```

Salida de ejemplo:
||Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|None|None|None|None|None|None|None|None|None|
|1|None|None|None|None|None|None|None|None|None|
|2|None|None|None|None|None|None|None|None|None|
|3|RANK|Título|Estudio|Worldwide|Domestic / %|Column1|Overseas / %|Column2|Year^|
|4|1|Avatar|Fox|2788|760,5|0,273|2027,5|0,727|2009^|5|

Como puede ver, la tabla de la segunda hoja tenía encabezados y tres filas vacías. Debe modificar los argumentos de la función en consecuencia. Ejemplo:
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_header=True, skip_rows=3)
df = dataflow.to_pandas_dataframe()
df
```

Salida de ejemplo:
||RANK|Título|Estudio|Worldwide|Domestic / %|Column1|Overseas / %|Column2|Year^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Avatar|Fox|2788|760,5|0,273|2027,5|0,727|2009^|
|1|2|Titanic|Par.|2186,8|658,7|0,301|1528,1|0,699|1997^|
|2|3|Marvel's The Avengers|BV|1518,6|623,4|0,41|895,2|0,59|2012|
|3|4|Harry Potter and the Deathly Hallows Part 2|WB|1341,5|381|0,284|960,5|0,716|2011|
|4|5|Frozen|BV|1274,2|400,7|0,314|873,5|0,686|2013|

## <a name="use-fixed-width-data-files"></a>Uso del tipo de datos de ancho fijo
Para los archivos de ancho fijo, puede especificar una lista de desplazamientos. La primera columna siempre se supone que comienza en el desplazamiento 0. Ejemplo:
```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dataflow.head(5)
```

Salida de ejemplo:
||010000|99999|BOGUS NORWAY|NO|NO_1|ENRS|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|BOGUS NORWAY|NO|NO|ENSO||||
|1|010010|99999|JAN MAYEN|NO|JN|ENJA|+70933|-008667|+00090|
|2|010013|99999|ROST|NO|NO|||||
|3|010014|99999|SOERSTOKKEN|NO|NO|ENSO|+59783|+005350|+00500|
|4|010015|99999|BRINGELAND|NO|NO|ENBL|+61383|+005867|+03270|


Si no hay encabezados en los archivos, deseará que la primera fila se trate como datos. Mediante el uso de `PromoteHeadersMode.NONE` en el argumento de la palabra clave del encabezado puede evitar que se detecte el encabezado y obtener los datos correctos. Por ejemplo: 
```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)

df = dataflow.to_pandas_dataframe()
df
```

Salida de ejemplo:

||Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|BOGUS NORWAY|NO|NO_1|ENRS|Column7|Column8|Column9|
|1|010003|99999|BOGUS NORWAY|NO|NO|ENSO||||
|2|010010|99999|JAN MAYEN|NO|JN|ENJA|+70933|-008667|+00090|
|3|010013|99999|ROST|NO|NO|||||
|4|010014|99999|SOERSTOKKEN|NO|NO|ENSO|+59783|+005350|+00500|
|5|010015|99999|BRINGELAND|NO|NO|ENBL|+61383|+005867|+03270|

## <a name="use-sql-data"></a>Uso de datos de SQL
Azure Machine Learning Data Prep SDK también puede cargar datos de servidores SQL Server. Actualmente solo se admite Microsoft SQL Server.
Para leer datos de un servidor SQL server, cree un objeto de origen de datos que contiene la información de conexión. Por ejemplo: 
```python
secret = dprep.register_secret("[SECRET-USERNAME]", "[SECRET-PASSWORD]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=[DATABASE-PASSWORD])
```
Como puede ver, el parámetro de la contraseña de `MSSQLDataSource` acepta un objeto secreto. Hay dos formas de obtener un objeto de secreto:
-   Registre el secreto y su valor con el motor de ejecución. 
-   Cree el secreto con solo un identificador (lo que es útil si el valor del secreto ya está registrado en el entorno de ejecución).

Después de crear un objeto de origen de datos, puede continuar y leer los datos. Por ejemplo: 
```python
dataflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dataflow.head(5)
```

Salida de ejemplo:
||ProductID|NOMBRE|ProductNumber|Color|StandardCost|ListPrice|Tamaño|Peso|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|ThumbNailPhoto|ThumbnailPhotoFileName|rowguid|ModifiedDate|
|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
|0|680|HL Road Frame - Black, 58|FR-R92B-58|Negro|1059,3100|1431,50|58|1016,04|18|6|2002-06-01 00:00:00+00:00|None|None|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000+00:00|
|1|706|HL Road Frame - Red, 58|FR-R92R-58|Rojo|1059,3100|1431,50|58|1016,04|18|6|2002-06-01 00:00:00+00:00|None|None|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000+00:00|
|2|707|Sport-100 Helmet, Red|HL-U509-R|Rojo|13,0863|34,99|None|None|35|33|2005-07-01 00:00:00+00:00|None|None|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000+00:00|
|3|708|Sport-100 Helmet, Black|HL-U509|Negro|13,0863|34,99|None|None|35|33|2005-07-01 00:00:00+00:00|None|None|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|a25a44fb-c2de-4268-958f-110b8d7621e2|2008-03-11 |10:01:36.827000+00:00|
|4|709|Mountain Bike Socks, M|SO-B909-M|Blanco|3,3963|9,50|M|None|27|18|2005-07-01 00:00:00+00:00|2006-06-30 00:00:00+00:00|None|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|18f95f47-1540-4e02-8f1f-cc1bcb6828d0|2008-03-11 |10:01:36.827000+00:00|

```python
df = dataflow.to_pandas_dataframe()
df.dtypes
```

Salida de ejemplo:
```
ProductID                                     int64
Name                                         object
ProductNumber                                object
Color                                        object
StandardCost                                float64
ListPrice                                   float64
Size                                         object
Weight                                      float64
ProductCategoryID                             int64
ProductModelID                                int64
SellStartDate             datetime64[ns, UTC+00:00]
SellEndDate                                  object
DiscontinuedDate                             object
ThumbNailPhoto                               object
ThumbnailPhotoFileName                       object
rowguid                                      object
ModifiedDate              datetime64[ns, UTC+00:00]
dtype: object
```

## <a name="use-azure-data-lake-storage"></a>Uso de Azure Data Lake Storage
Hay dos maneras de que el SDK pueda adquirir el token de OAuth necesario para acceder a Azure Data Lake Storage:
-   Recuperar el token de acceso de una sesión de inicio de sesión reciente del inicio de sesión de la CLI de Azure del usuario
-   Usar una entidad de servicio (SP) y un certificado como secreto

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>Usar un token de acceso de una sesión de la CLI de Azure reciente
En el equipo local, ejecute el siguiente comando:

> [!NOTE] 
> Si su cuenta de usuario es miembro de más de un inquilino de Azure, deberá especificar el inquilino, con el formato de nombre de host de dirección URL de AAD.


Por ejemplo: 
```azurecli
az login
az account show --query tenantId
dataflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dataflow.head(5) head
```
### <a name="create-a-service-principal-with-the-azure-cli"></a>Creación de una entidad de servicio con la CLI de Azure
La CLI de Azure se puede usar para crear una entidad de servicio y el certificado correspondiente. Esta entidad de servicio concreta está configurada como lector y su ámbito se ha reducido exclusivamente a la cuenta de Azure Data Lake Storage "dpreptestfiles".  Por ejemplo: 
```azurecli
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```
Este comando se emite `appId` y la ruta de acceso al archivo de certificado (que suele estar en la carpeta particular). El archivo .crt contiene el certificado público y la clave privada en formato PEM.

Extraiga la huella digital:
```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

Para configurar la lista de control de acceso para el sistema de archivos de Azure Data Lake Storage, use el valor de objectId del usuario o, en este ejemplo, la entidad de servicio. Por ejemplo: 
```azurecli
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```

Para configurar el acceso de `Read` y `Execute` para el sistema de archivos de Azure Data Lake Storage, debe configurar la lista de control de acceso de los archivos y las carpetas individualmente. Esto es así porque el modelo de la lista de control de acceso de HDFS subyacente no admite herencias. Por ejemplo: 
```azurecli
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r-x" --path /
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r--" --path /farmers-markets.csv
```
```
certThumbprint = 'C2:08:9D:9E:D1:74:FC:EB:E9:7E:63:96:37:1C:13:88:5E:B9:2C:84'
certificate = ''
with open('./data/adls-dpreptestfiles.crt', 'rt', encoding='utf-8') as crtFile:
    certificate = crtFile.read()

servicePrincipalAppId = "8dd38f34-1fcb-4ff9-accd-7cd60b757174"
```
### <a name="acquire-an-oauth-access-token"></a>Adquisición de un token de acceso de OAuth
Use el paquete `adal` (a través de: `pip install adal`) para crear un contexto de autenticación en el inquilino MSFT y adquirir un token de acceso de OAuth. En el caso de ADLS, el recurso de la solicitud del token debe ser para "https://datalake.azure.net", que es diferente de la mayoría de los demás recursos de Azure.

```python
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext('https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate('https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dataflow = dprep.read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dataflow.to_pandas_dataframe().head()
```

||FMID|MarketName|Website|street|city|County|
|----|------|-----|----|----|----|----|
|0|1012063|Caledonia Farmers Market Association - Danville|https://sites.google.com/site/caledoniafarmers... ||Danville|Caledonia|
|1|1011871|Stearns Homestead Farmers' Market|http://Stearnshomestead.com |6975 Ridge Road|Parma|Cuyahoga|
|2|1011878|100 Mile Market|http://www.pfcmarkets.com |507 Harrison St|Kalamazoo|Kalamazoo|
|3|1009364|106 S. Main Street Farmers Market|http://thetownofsixmile.wordpress.com/ |106 S. Main Street|Six Mile|||
|4|1010691|10th Steet Community Farmers Market|http://agrimissouri.com/mo-grown/grodetail.php... |10th Street and Poplar|Lamar|Barton|
