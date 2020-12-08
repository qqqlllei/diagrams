### IOC
    Inversion Of Controll,控制反转，是一种设计思想，将原本在程序中手动创建对象的控制权，交给spring框架来管理。实现代码层面的解耦合。
### spring bean 生命周期
    1：BeanDefinitionRegistryPostProcessor.postProcessBeanDefinitionRegistry --> bean加载阶段，可根据自定义规则创建更多的BeanDefiniton。
    2：BeanFactoryPostProcessor.postProcessBeanFactory --> BeanDefinition信息加载完毕后对BeanDefinition属性信息做新增或修改。
    3：InstantiationAwareBeanPostProcessor.postProcessBeforeInstantiation --> bean 实例化之前调用
    4：bean 实例化
    5：InstantiationAwareBeanPostProcessor.postProcessAfterInstantiation --> bean 实例化之后调用
    6：InstantiationAwareBeanPostProcessor.postProcessProperties --> bean 属性注入
    7：invokeAwareMethods --> bean aware类型属性注入
    8: BeanPostProcessor.postProcessBeforeInitialization -->bean 初始化之前调用
    9: InitializingBean.afterPropertiesSet -->如果bean 实现了InitializingBean 会调用该方法
    10：调用bean属性initMethod 设置的方法
    11：BeanPostProcessor.postProcessAfterInitialization -->bean 初始化之后调用
    12: 容器销毁 DisposableBean.destory --> bean配置的destroy-method
    