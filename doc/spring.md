### IOC
    Inversion Of Controll,���Ʒ�ת����һ�����˼�룬��ԭ���ڳ������ֶ���������Ŀ���Ȩ������spring�����������ʵ�ִ������Ľ���ϡ�
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
    
    
### spring aop
    ֪ͨ����
        @Aroud @Before  @After @AfterReturning @AfterThrowing
    ִ��˳��
        1:@Aroud(before)-->@Before--> @Aroud(after)--> @After--> @AfterReturning
        2:@Aroud(before)-->@Before--> @Aroud(after)--> @After--> @AfterThrowing
    ���ڶ�̬�����ķ�ʽʵ��aop���ܣ���ʽ������
    1��jdk proxy ����������ʵ���˽ӿ�
    2: cglib ����������û��ʵ�ֽӿ�
    AbstractAutoProxyCreator.postProcessBeforeInstantiation --->����ʵ����֮ǰ�����߼��������û��Զ���TargetSource���󷵻�һ��proxy���󣬿��ԶԷ�spring������������ʹ��aop����
    AbstractAutoProxyCreator.postProcessAfterInitialization --->�����ʼ���󴴽���������
    
    
    
### ѭ������
    ��������
        һ���� singletonObjects
        ������ earlySingletonObjects
        ������ singletonFactories
    ����ʱ����
        ʵ�������������objectFacoty  ���������档
        ����ѭ���������Ӷ��������ȡ����ȡ�����������������ȡ�������ȡ������getObject�������������Ҫaop�Ķ��󣬽���aop�����ش��������ԭʼ���󣬴���������档
        ɾ�����������еĶ��󡣼��������棬��������ʱ����ġ�
        ��ʼ����ɵ�bean����һ�����棬ɾ�������������bean
                