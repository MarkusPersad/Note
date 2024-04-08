# 自定义`Starter`

## `Starter`意义

### ``SpringBoot``的`Starter`机制

　`SpringBoot`中的`starter`是一种非常重要的机制，能够抛弃以前繁杂的配置，将其统一集成进`starter`，应用者只需要在maven中引入starter依赖，`SpringBoot`就能自动扫描到要加载的信息并启动相应的默认配置。`starter`让我们摆脱了各种依赖库的处理，需要配置各种信息的困扰。`SpringBoot`会自动通过`classpath`路径下的类发现需要的`Bean`，并注册进`IOC`容器。`SpringBoot`提供了针对日常企业应用研发各种场景的`spring-boot-starter`依赖模块。所有这些依赖模块都遵循着约定成俗的默认配置，并允许我们调整这些配置，即遵循“约定大于配置”的理念。

