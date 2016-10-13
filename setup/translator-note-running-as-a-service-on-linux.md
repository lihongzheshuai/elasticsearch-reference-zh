# 译者注

## 关于在CentOS 7 上以服务形式运行Elasticsearch

翻译完2.2节的内容，笔者在回味的时候也自觉是一头雾水。主要还是因为对Linux服务配置和使用方式的不了解。为了更好的理解本节内容，笔者做了一些研究工作，算是初步了解了以服务模式运行的Elasticsearch的配置方式和各种路径下文件的作用。

### tar模式运行

先说一下tar包部署模式，这是笔者目前采用的部署模式，在这种模式下，所有的文件都存放在解压后的目录下，例如在笔者环境中Elasticsearch解压后存放在\/data\/elasticsearch-2.4.1目录下。以此为ES\_HOME，所有的运行脚本存放在$ES\_HOME\/bin下，包括Linux和Windows的运行脚本。所有的配置文件存放在$ES\_HOME\/config 下，包括elaticsearch.yml 和 logging.yml。

此时，我们修改config目录下的elasticsearch.yml和logging.yml配置文件，即可完成对Elasticsearch服务和日志的配置。即，前文提到的配置参数，在此两个配置文件中基本都可找到。

出于“强迫症”的性格，笔者想最后找到Elasticsearch默认从主目录下的config目录加载配置文件的政府。在bin\/elasticsearch启动脚本中，笔者并未发现跟配置文件目录有关的信息，在内部脚本bin\/elasticsearch.in.sh中\(用于应用用户指定的环境变量\)中也没有找到配置，因此只能从源码中找到证据，一路阅读启动代码在Environment.java中找到证据：

```java
public Environment(Settings settings) {
        final Path homeFile;
        if (PATH_HOME_SETTING.exists(settings)) {
            homeFile = PathUtils.get(cleanPath(PATH_HOME_SETTING.get(settings)));
        } else {
            throw new IllegalStateException(PATH_HOME_SETTING.getKey() + " is not configured");
        }

        if (PATH_CONF_SETTING.exists(settings)) {
            configFile = PathUtils.get(cleanPath(PATH_CONF_SETTING.get(settings)));
        } else {
            configFile = homeFile.resolve("config");
        }

        if (PATH_SCRIPTS_SETTING.exists(settings)) {
            scriptsFile = PathUtils.get(cleanPath(PATH_SCRIPTS_SETTING.get(settings)));
        } else {
            scriptsFile = configFile.resolve("scripts");
        }
```

configFile = homeFile.resolve\("config"\)，这段代码即指定了在没有显示配置的情况下使用$ES\_HOME\/config目录。

