### IOC
    Inversion Of Controll,���Ʒ�ת����һ�����˼�룬��ԭ���ڳ������ֶ���������Ŀ���Ȩ������spring���������ʵ�ִ������Ľ���ϡ�
### spring bean ��������
    1��BeanDefinitionRegistryPostProcessor.postProcessBeanDefinitionRegistry --> bean���ؽ׶Σ��ɸ����Զ�����򴴽������BeanDefiniton��
    2��BeanFactoryPostProcessor.postProcessBeanFactory --> BeanDefinition��Ϣ������Ϻ��BeanDefinition������Ϣ���������޸ġ�
    3��InstantiationAwareBeanPostProcessor.postProcessBeforeInstantiation --> bean ʵ����֮ǰ����
    4��bean ʵ����
    5��InstantiationAwareBeanPostProcessor.postProcessAfterInstantiation --> bean ʵ����֮�����
    6��InstantiationAwareBeanPostProcessor.postProcessProperties --> bean ����ע��
    7��invokeAwareMethods --> bean aware��������ע��
    8: BeanPostProcessor.postProcessBeforeInitialization -->bean ��ʼ��֮ǰ����
    9: InitializingBean.afterPropertiesSet -->���bean ʵ����InitializingBean ����ø÷���
    10������bean����initMethod ���õķ���
    11��BeanPostProcessor.postProcessAfterInitialization -->bean ��ʼ��֮�����
    12: �������� DisposableBean.destory --> bean���õ�destroy-method
    