---
title: 'Azure Toolkit for IntelliJ: crear aplicaciones Spark para un clúster de HDInsight '
description: Uso del kit de herramientas de Azure para IntelliJ con el fin de desarrollar aplicaciones Spark escritas en Scala y enviarlas a un clúster de HDInsight Spark.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: maxluk
ms.openlocfilehash: ff7cfcd56158bd38d031a29a21247fb9eb6b91f9
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2018
ms.locfileid: "51289077"
---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-an-hdinsight-cluster"></a>Uso del kit de herramientas de Azure para IntelliJ con el fin de crear aplicaciones Spark para un clúster de HDInsight

Uso del complemento de kit de herramientas de Azure para IntelliJ con el fin de desarrollar aplicaciones Spark escritas en Scala y enviarlas a continuación a un clúster de HDInsight Spark directamente desde el entorno de desarrollo integrado (IDE) de IntelliJ. Puede usar el complemento de varias maneras:

* Desarrollar y enviar una aplicación Spark en Scala en un clúster de Spark en HDInsight.
* Tener acceso a los recursos del clúster de Azure HDInsight Spark.
* Desarrollar y ejecutar localmente una aplicación Spark en Scala.

Para crear el proyecto, vea el vídeo [Create Spark Applications with the Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (Creación de aplicaciones Spark con el kit de herramientas de Azure para IntelliJ).

> [!IMPORTANT]
> Puede usar este complemento para crear y enviar aplicaciones solo para un clúster de HDInsight Spark en Linux.
> 

## <a name="prerequisites"></a>Requisitos previos

- Un clúster Apache Spark en HDInsight Linux. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](apache-spark-jupyter-spark-sql.md).
- Kit de desarrollo de Oracle Java. Puede instalarlo desde el [sitio web de Oracle](https://aka.ms/azure-jdks).
- IntelliJ IDEA. En este artículo se usa la versión 2017.1. Puede instalarlo desde el [sitio web de JetBrains](https://www.jetbrains.com/idea/download/).

## <a name="install-azure-toolkit-for-intellij"></a>Instalación del kit de herramientas de Azure para IntelliJ
Para obtener instrucciones de instalación, consulte [Instalación del kit de herramientas de Azure para IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="get-started"></a>Introducción
El usuario puede [iniciar sesión en la suscripción a Azure](#sign-in-to-your-azure-subscription) o [vincular un clúster de HDInsight](#link-a-cluster) con el nombre de usuario y la contraseña de Ambari o credenciales unidas a un dominio para comenzar.


## <a name="sign-in-to-your-azure-subscription"></a>Inicie sesión en la suscripción de Azure

1. Inicie el IDE de IntelliJ y abra Azure Explorer. En el menú **View** (Ver), seleccione **Tools Windows** (Ventanas de herramientas) y luego seleccione **Azure Explorer** (Explorador de Azure).
       
   ![Vínculo de Azure Explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

1. Haga clic con el botón derecho en el nodo **Azure** y después seleccione **Iniciar sesión**.

1. En el cuadro de diálogo **Inicio de sesión en Microsoft Azure**, seleccione **Iniciar sesión** y, a continuación, escriba las credenciales de Azure.

    ![Cuadro de diálogo de inicio de sesión en Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

1. Cuando haya iniciado sesión, en el cuadro de diálogo **Select Subscriptions** (Seleccionar suscripciones) se enumeran todas las suscripciones de Azure asociadas a las credenciales. Seleccione el botón **Seleccionar**.

    ![Cuadro de diálogo Seleccionar suscripciones](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

1. En la pestaña **Azure Explorer**, expanda **HDInsight** para ver los clústeres de HDInsight Spark de su suscripción.
   
    ![Clústeres de HDInsight Spark en Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

1. Para ver los recursos (por ejemplo, las cuentas de almacenamiento) asociados al clúster, puede expandir un nodo de nombre de clúster.
   
    ![Nodo de nombre de clúster expandido](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>Vinculación de un clúster
Puede vincular un clúster de HDInsight normal mediante el nombre de usuario administrado de Ambari. De forma similar, para un clúster de HDInsight unido a un dominio, puede vincular con el dominio y el nombre de usuario, como user1@contoso.com. También puede vincular el clúster del servicio de Livy.

1. Seleccione **Link a cluster** (Vincular un clúster) desde **Azure Explorer**.

   ![menú contextual de vinculación de un clúster](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

2. Tiene dos opciones para vincular clústeres. 

   * Para vincular un clúster de HDInsight, elija **Clúster de HDInsight** en el campo **Cluster Info** (Información del clúster), escriba **Cluster Name/URL** (Nombre/dirección URL del clúster), **Nombre de usuario** y **Contraseña**.

      ![cuadro de diálogo vincular clúster de hdinsight](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

   * Para vincular el clúster del servicio de Livy, elija **Livy Service** (Servicio de Livy) en campo **Cluster Info**, escriba **Livy Endpoint** (Punto de conexión de Livy), **Nombre del clúster**. **Yarn Endpoint** (Punto de conexión de Yarn) es opcional. En el campo **Autenticación**, se proporcionan dos opciones. Son **Autenticación básica** y **Sin autenticación**. Al seleccionar **Autenticación básica**, deben proporcionarse el **Nombre de usuario** y la **Contraseña**. Si obtiene un error de autenticación, debe comprobar el nombre de usuario y la contraseña.
      
      ![cuadro de diálogo de vinculación de clúster de livy](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)
   
3. Si la información introducida es correcta, puede ver un clúster vinculado en el nodo de **HDInsight**. Ahora puede enviar una aplicación a este clúster vinculado.

   ![clúster vinculado](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

4. También puede desvincular un clúster de **Azure Explorer**.
   
   ![clúster no vinculado](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="create-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Creación de una aplicación Spark en Scala en un clúster de HDInsight Spark

1. Inicie IntelliJ IDEA y, después, cree un proyecto. En el cuadro de diálogo **Nuevo proyecto**, siga estos pasos: 

    a. Seleccione **HDInsight** > **Spark en HDInsight (Scala)**.

   b. En la lista de **herramientas de compilación**, seleccione cualquiera de las siguientes, según sus necesidades:

      * **Maven**, para la compatibilidad con el asistente para crear un proyecto de Scala
      * **SBT**, para administrar las dependencias y compilar el proyecto de Scala

    ![Cuadro de diálogo Nuevo proyecto](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

1. Seleccione **Next** (Siguiente).

1. El asistente para crear un proyecto de Scala detecta automáticamente si se ha instalado el complemento de Scala. Seleccione **Instalar**.

   ![Comprobación de complemento de Scala](./media/apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

1. Para descargar el complemento de Scala, seleccione **Aceptar**. Siga las instrucciones para reiniciar IntelliJ. 

   ![Cuadro de diálogo de instalación del complemento de Scala](./media/apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

1. En la ventana **Nuevo proyecto**, haga lo siguiente:  

    ![Selección del SDK de Spark](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

    a. Escriba un nombre y una ubicación de proyecto.

   b. En la lista desplegable **SDK de proyecto**, seleccione **Java 1.8** para el clúster Spark 2.x, o bien **Java 1.7** para el clúster Spark 1.x.

   c. En la lista desplegable **Versión de Spark**, el asistente para crear un proyecto de Scala integra la versión correcta del SDK de Spark y el SDK de Scala. Si la versión del clúster de Spark es inferior a la 2.0, seleccione **Spark 1.x**. De lo contrario, seleccione **Spark 2.x**. Este ejemplo utiliza **Spark 2.0.2 (Scala 2.11.8)**.

1. Seleccione **Finalizar**.

1. El proyecto Spark creará automáticamente un artefacto. Para ver el artefacto, haga lo siguiente:

    a. En el menú **File** (Archivo), seleccione **Project Structure** (Estructura del proyecto).

   b. En el cuadro de diálogo **Project Structure** (Estructura del proyecto), seleccione **Artifacts** (Artefactos) para ver el artefacto predeterminado que se crea. También puede crear su propio artefacto seleccionando el signo más (**+**).

      ![Información de artefacto en el cuadro de diálogo](./media/apache-spark-intellij-tool-plugin/default-artifact.png)
      
1. Agregue el código fuente de aplicación de la siguiente forma:

    a. En el Explorador de proyectos, haga clic con el botón derecho en **src**, elija **New** (Nuevo) y, a continuación, seleccione **Scala Class** (Clase de Scala).
      
      ![Comandos para crear una clase Scala desde el explorador de proyectos](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   b. En el cuadro de diálogo **Create New Scala Class** (Crear nueva clase de Scala), proporcione un nombre, seleccione **Object** (Objeto) en **Kind** (Variante) y seleccione **OK** (Aceptar).
      
      ![Creación de un cuadro de diálogo de nueva clase de Scala](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   c. En el archivo **MyClusterApp.scala** , pegue el siguiente código. El código lee los datos de HVAC.csv (disponible en todos los clústeres de HDInsight Spark), recupera las filas que solo tienen un dígito en la séptima columna del archivo CSV y escribe la salida en **/HVACOut** en el contenedor de almacenamiento predeterminado para el clúster.

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object MyClusterApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACOut")
            }
    
        }

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Ejecución de una aplicación Spark en Scala en un clúster de HDInsight Spark
Después de crear una aplicación de Scala, puede enviarla al clúster.

1. En el Explorador de proyectos, ubique un archivo de Java o Scala y seleccione **Submit Spark Application to HDInsight** (Enviar aplicación Spark a HDInsight) en el menú contextual.
    
      ![Envío de aplicación Spark a comando HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. En la ventana del cuadro de diálogo de configuración, proporcione los valores siguientes, a continuación, haga clic en **SparkJobRun**.

      ![Cuadro de diálogo de envío de Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)
      
    * Para **Spark clusters (Linux only)**(Clústeres de Spark [solo Linux]), seleccione el clúster de Spark de HDInsight en el que desea ejecutar la aplicación.

    * Seleccione un artefacto del proyecto IntelliJ o uno del disco duro.

    * Campo **Main class name** (Nombre de la clase principal): El valor predeterminado es la clase principal del archivo seleccionado. Para cambiar la clase, puede seleccionar el botón de puntos suspensivos (**...** ) y elegir otra clase.   

    * Campo **Job Configurations** (Configuraciones de trabajo): Los valores predeterminados se establecen como la imagen anterior. Puede cambiar el valor o agregar nuevos pares clave/valor para el envío de trabajos. Para más información: [API de REST de Apache Livy](http://livy.incubator.apache.org./docs/latest/rest-api.html)

      ![Significado de la configuración del trabajo del cuadro de diálogo Envío de Spark](./media/apache-spark-intellij-tool-plugin/submit-job-configurations.png)

    * Campo **Argumentos de línea de comandos**: Puede especificar los valores de argumentos divididos por un espacio para la clase principal, si es necesario.

    * Campos **Referenced Jars** (Archivos jar a los que se hace referencia) y **Referenced Files** (Archivos a los que se hace referencia): Puede especificar las rutas de acceso para los archivos jar y los archivos a los que se hace referencia, si existen. Para más información, consulte la [configuración de Spark](https://spark.apache.org/docs/latest/configuration.html#runtime-environment). 

      ![Significado de los archivos JAR del cuadro de diálogo Envío de Spark](./media/apache-spark-intellij-tool-plugin/jar-files-meaning.png)

       > [!NOTE]
       > Para cargar los archivos jar y los archivos a los que hace se referencia, consulte [cómo cargar recursos en un clúster](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).
                         
    * **Ruta de acceso carga**: Puede indicar la ubicación de almacenamiento para el envío de los recursos del proyecto de Jar o Scala. Hay varios tipos de almacenamiento compatibles: **Azure Blob**, **Use Spark interactive session to upload artifacts** (Usar sesión interactiva de Spark para cargar artefactos), **Use cluster default storage account** (Usar cuenta de almacenamiento predeterminada del clúster) y **ADLS Gen1**. La captura de pantalla siguiente es un ejemplo de Azure Blob.

        ![Cuadro de diálogo de envío de Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-upload-storage-types.png)

        ![Cuadro de diálogo de envío de Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-upload-storage-blob.png)

3. Haga clic en **SparkJobRun** para enviar el proyecto para el clúster seleccionado. En la pestaña **Remote Spark Job in Cluster** (Trabajo remoto de Spark en el clúster) se muestra el progreso de ejecución del trabajo en la parte inferior. Puede detener la aplicación haciendo clic en el botón rojo. Para obtener información sobre cómo tener acceso a la salida de trabajo, consulte la sección "Acceso y administración de clústeres de HDInsight Spark mediante el uso del kit de herramientas de Azure para IntelliJ" que encontrará más adelante en este artículo.
      
     ![Ventana de envío de Spark](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)


## <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Depurar aplicaciones Spark de forma local o remota en un clúster de HDInsight 
También se recomienda otra manera de enviar la aplicación Spark al clúster. Puede hacerlo estableciendo los parámetros del IDE de **configuraciones de ejecución o depuración**. Para obtener más información, consulte [Depuración de aplicaciones Spark en un clúster de HDInsight con el Kit de herramientas de Azure para IntelliJ mediante SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).



## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Acceso y administración de clústeres de HDInsight Spark mediante el uso del kit de herramientas de Azure para IntelliJ
Puede realizar varias operaciones mediante el Kit de herramientas de Azure para IntelliJ.

### <a name="access-the-job-view"></a>Acceso a la vista de trabajo
1. En Azure Explorer, expanda **HDInsight** y el nombre del clúster de Spark. Después, seleccione **Trabajos**.  

    ![Nodo Vista de trabajos](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

1. En el panel derecho, la pestaña **Spark Job View** (Vista de trabajos de Spark) muestra todas las aplicaciones que se ejecutaron en el clúster. Seleccione el nombre de la aplicación para la que desea ver más detalles.

    ![Detalles de aplicación](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)
    >Nota:
    >

1. Para mostrar información básica del trabajo de ejecución, mantenga el mouse sobre el gráfico del trabajo. Para ver el gráfico de fases y la información que todo trabajo genera, seleccione un nodo del gráfico del trabajo.

    ![Detalles de fase de trabajo](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

1. Para ver registros usados con frecuencia, como *Driver Stderr*, *Driver Stdout* y *Directory Info*, seleccione la pestaña **Registro**.

    ![Detalles del registro](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

1. También puede ver Spark History UI (IU de historial de Spark) y YARN UI (IU de YARN), en el nivel de aplicación, al seleccionar los vínculos correspondientes de la parte superior de la ventana.

### <a name="access-the-spark-history-server"></a>Acceso al servidor de historial de Spark
1. En Azure Explorer, expanda **HDInsight**, haga clic con el botón derecho en el nombre del clúster de Spark y seleccione **Open Spark History UI** (Abrir IU de historial de Spark). 

1. Cuando se le solicite, escriba las credenciales de administrador del clúster, que especificó al configurar el clúster.

1. En el panel del servidor de historial de Spark, puede usar el nombre de aplicación para buscar la aplicación que acaba de terminar de ejecutar. En el código anterior, se establece el nombre de la aplicación mediante `val conf = new SparkConf().setAppName("MyClusterApp")`. Por lo tanto, el nombre de aplicación Spark es **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Inicio del portal de Ambari
1. En Azure Explorer, expanda **HDInsight**, haga clic con el botón derecho en el nombre del clúster de Spark y seleccione **Open Cluster Management Portal (Ambari)** (Abrir el portal de administración de clústeres [Ambari]). 

1. Cuando se le pida, escriba las credenciales de administrador para el clúster. Estas son las credenciales que especificó durante el proceso de configuración de clúster.

### <a name="manage-azure-subscriptions"></a>Administración de suscripciones de Azure
De forma predeterminada, el kit de herramientas de Azure para IntelliJ enumera los clústeres de Spark de todas las suscripciones de Azure. Si es necesario, puede especificar las suscripciones a las que desea tener acceso. 

1. En Azure Explorer, haga clic con el botón derecho en el nodo raíz de **Azure** y seleccione **Manage Subscriptions** (Administrar suscripciones). 

1. En el cuadro de diálogo, desactive las casillas situadas junto a las suscripciones a las que no desea tener acceso y seleccione **Close** (Cerrar). También puede seleccionar **Sign Out** (Cerrar sesión) si desea cerrar sesión en su suscripción de Azure.

## <a name="spark-console"></a>Consola de Spark
Puede ejecutar Spark Local Console(Scala) o ejecutar Spark Livy Interactive Session Console(Scala).

### <a name="spark-local-consolescala"></a>Spark Local Console(Scala)
1. Establezca la configuración si no tiene ninguna anterior. En la ventana **Run/Debug Configurations** (Ejecutar/depurar configuraciones), haga clic en **+**->**Azure HDInsight Spark**, seleccione la pestaña select tab **Locally Run** (Ejecutar localmente) y **Remotely Run in Cluster** (Ejecutar remotamente en el clúster), elija el nombre de la clase principal y luego haga clic en **Aceptar**.

    ![Establecer configuración en la consola local](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)
 
2. Abra el archivo de clase principal correspondiente y haga clic con el botón derecho en **Spark Console**; a continuación, haga clic en **Run Spark Local Console(Scala)** [Ejecutar Spark Local Console(Scala)]. O vaya al menú **Herramientas**->**Spark Console**->**Run Spark Local Console(Scala)** para iniciar la consola. A continuación, se mostrarán dos cuadros de diálogo para preguntar si desea corregir automáticamente las dependencias. Simplemente haga clic en el botón **Auto Fix** (Corregir automáticamente).

    ![Corrección automática 1 de Spark](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Corrección automática 2 de Spark](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

    ![Punto de entrada local de Spark](./media/apache-spark-intellij-tool-plugin/spark-console-local-entry-script.png)

3. Después de iniciar la consola local correctamente, su aspecto es similar al siguiente. Puede hacer algo que quiera, por ejemplo, escriba **sc.appName**, presione Ctrl+ENTRAR, a continuación, se mostrará el resultado. Puede terminar la consola local; para ello, haga clic en el botón rojo.

    ![Resultado de la consola local](./media/apache-spark-intellij-tool-plugin/local-console-result.png)


### <a name="spark-livy-interactive-session-consolescala"></a>Spark Livy Interactive Session Console(Scala)
Solo se admite en IntelliJ 2018.2 y 2018.3.

1. Establezca la configuración si no tiene ninguna anterior. En la ventana **Run/Debug Configurations** (Ejecutar/depurar configuraciones), haga clic en **+**->**Azure HDInsight Spark**, seleccione la pestaña **Remotely Run in Cluster** (Ejecutar remotamente en el clúster), elija el nombre del clúster y la clase principal y luego haga clic en **Aceptar**.

    ![Agregar entrada de configuración en la consola interactiva](./media/apache-spark-intellij-tool-plugin/interactive-console-add-config-entry.png)

    ![Establecer configuración en la consola interactiva](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

2. Abra el archivo correspondiente a su clase principal y haga clic con el botón derecho en **Spark Console**, a continuación, haga clic en **Run Spark Livy Interactive Session Console(Scala)** [Ejecutar Spark Livy Interactive Session Console(Scala)]. O vaya al menú **Herramientas**, a continuación, haga clic en **Spark Console**, luego, **Run Spark Livy Interactive Session Console(Scala)** para iniciar la consola.

3. Después de iniciar la consola correctamente, puede hacer algo que quiera, por ejemplo, escriba **sc.appName**, presione Ctrl+ENTRAR, a continuación, se mostrará el resultado.

    ![Resultado de la consola interactiva](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Envío de la selección a la consola de Spark
Resulta cómodo predecir el resultado del script mediante el envío de algunos códigos a la consola local o a Livy Interactive Session Console(Scala). Puede resaltar algunos códigos en el archivo de Scala y luego hacer clic con el botón derecho en **Send Selection To Spark Console** (Enviar selección a la consola de Spark). Los códigos seleccionados se enviarán a la consola y se realizarán. El resultado se mostrará después de los códigos en la consola. La consola comprobará los errores, si los hay. 

   ![Envío de la selección a la consola de Spark](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Conversión de las aplicaciones IntelliJ IDEA existentes para usar el kit de herramientas de Azure para IntelliJ
Puede convertir las aplicaciones Spark en Scala existentes creadas en IntelliJ IDEA para que sean compatibles con el kit de herramientas de Azure para IntelliJ. Esto le permitirá utilizar el complemento para enviar las aplicaciones a un clúster de HDInsight Spark.

1. Para una aplicación Spark en Scala existente creada con IntelliJ IDEA, abra el archivo .iml asociado.

1. En el nivel raíz, hay un elemento de **module** similar al siguiente:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Edite el elemento para agregar `UniqueKey="HDInsightTool"` de forma que el elemento de **module** sea similar a lo siguiente:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. Guarde los cambios. La aplicación ahora debe ser compatible con el kit de herramientas de Azure para IntelliJ. Puede comprobarlo si hace clic con el botón derecho en el nombre de proyecto en el explorador de proyectos. El menú emergente ahora debería tener la opción **Submit Spark Application to HDInsight** (Enviar aplicación Spark a HDInsight).

## <a name="troubleshooting"></a>solución de problemas

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>Error en la ejecución local: *Please use a larger heap size* (Utilice un tamaño de montón mayor)
En Spark 1.6, si utiliza un SDK de Java de 32 bits durante la ejecución local, puede encontrar los siguientes errores:

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

Estos errores se producen porque el tamaño del montón no es lo suficientemente grande como para que se ejecute Spark. Spark requiere al menos 471 MB. (para obtener más información, consulte [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081)). Una solución sencilla es utilizar un SDK de Java de 64 bits. También puede cambiar la configuración de JVM en IntelliJ agregando las siguientes opciones:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Incorporación de opciones a la casilla de opciones de máquina virtual en IntelliJ](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>Preguntas más frecuentes
Si el clúster está ocupado, puede aparecer el siguiente error.

![Intellij presenta errores si el clúster está ocupado](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![Intellij presenta errores si el clúster está ocupado](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>Comentarios y problemas conocidos
Actualmente, la visualización de salidas de Spark directamente no se admite.

Si tiene sugerencias o comentarios, o si se le presenta algún problema al usar este complemento, no dude en enviarnos un correo electrónico a la dirección hdivstool@microsoft.com.

## <a name="seealso"></a>Pasos siguientes
* [Introducción a Apache Spark en HDInsight de Azure](apache-spark-overview.md)

### <a name="demo"></a>Demostración
* Creación del proyecto Scala (vídeo): [Creación de aplicaciones Scala de Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (en inglés)
* Depuración remota (vídeo): [Uso del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones Spark en clústeres de HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (en inglés)

### <a name="scenarios"></a>Escenarios
* [Spark con BI: realización del análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: uso de Spark en HDInsight para analizar la temperatura de edificios con los datos del sistema de acondicionamiento de aire](apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Website log analysis using Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Uso del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones Spark mediante VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones Spark mediante SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Uso de las herramientas de HDInsight para IntelliJ con Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Uso de las herramientas de HDInsight del kit de herramientas de Azure para Eclipse con el fin de crear aplicaciones Spark](apache-spark-eclipse-tool-plugin.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](apache-spark-job-debugging.md)
