# 译者注

## 关于在CentOS 7 上以服务形式运行Elasticsearch

翻译完2.2节的内容，笔者在回味的时候也自觉是一头雾水。主要还是因为对Linux服务配置和使用方式的不了解。为了更好的理解本节内容，笔者做了一些研究工作，算是初步了解了以服务模式运行的Elasticsearch的配置方式和各种路径下文件的作用。

### tar模式安装

先说一下tar包部署模式，这是笔者目前采用的部署模式，在这种模式下，所有的文件都存放在解压后的目录下，例如在笔者环境中Elasticsearch解压后存放在\/data\/elasticsearch-2.4.1目录下。以此为ES\_HOME，所有的运行脚本存放在$ES\_HOME\/bin下，包括Linux和Windows的运行脚本。所有的配置文件存放在$ES\_HOME\/conf 下，包括elaticsearch.yml 和 loggin.yml。

