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
        ......
```

configFile = homeFile.resolve\("config"\)，这段代码即指定了在没有显示配置的情况下使用$ES\_HOME\/config目录。

### systemd 模式运行

现在再来说说以服务形式运行的情况。之所以先介绍了tar包模式运行的的情况，就是为了做一个对比。笔者所使用的操作系统版本是CentOS 7，所以对应下载.rpm包。安装后，根据2.2章节说明，Elasticsearch程序文件存放在\/usr\/share\/elasticsearch目录。其中bin目录下存放的启动脚本，但是并不存在config目录。根据我们之前的分析，此时如果直接运行\/usr\/share\/elasticsearch\/bin目录下的脚本，会找不到配置文件：

```bash
/usr/share/elasticsearch/bin/elasticsearch
Exception in thread "main" ElasticsearchException[Failed to load logging configuration]; nested: NoSuchFileException[/usr/share/elasticsearch/config];
Likely root cause: java.nio.file.NoSuchFileException: /usr/share/elasticsearch/config
    at sun.nio.fs.UnixException.translateToIOException(UnixException.java:86)
    at sun.nio.fs.UnixException.rethrowAsIOException(UnixException.java:102)
    at sun.nio.fs.UnixException.rethrowAsIOException(UnixException.java:107)
    at sun.nio.fs.UnixFileAttributeViews$Basic.readAttributes(UnixFileAttributeViews.java:55)
    at sun.nio.fs.UnixFileSystemProvider.readAttributes(UnixFileSystemProvider.java:144)
```

因此，我们知道，第一，Elasticsearch必须要找到配置文件方可启动，因此配置文件一定存放在其它目录中，第二，采用rpm包安装后，是不能直接使用脚本启动的，必须要进行注册系统服务的配置。对于第一个问题，我们可以先直接给出答案，rpm安装后，配置文件的存放目录为\/etc\/elasticsearch，在该目录下存放了elaticsearch.yml和logging.yml配置文件。对于第二个问题，就回到了2.2节中介绍的内容，采用chkconfig或者systemd来注册和管理服务，二者选其一即可。在CentOS 7上，虽然两个命令都存在，但实际上最终都是由systemctl管理的，也就是采用了systemd模式。即使你使用官方文档中提到的命令service elasticsearch start  启动，系统也会自动转为systemctl模式。因此，虽然如官方文档所述，存在\/etc\/init.d\/elasticsearch 启动脚本，但实际并未使用（笔者尝试过移除该文件，一切正常。）

