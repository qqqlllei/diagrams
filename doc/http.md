### ����һ��http����
    1��DNS��������(�����������ڲ���ϵͳ�����hosts�ļ���NDS������) ��ȡȥip��ַ
    2������TCP��������
        �٣�SYN=1,ACK=0,seq=x(seq = 0 �ʹ������ǵ�0�Ű���,client����syn_sent״̬���ͻ��˵ȴ��������ظ�
        �ڣ�SYN=1,ACK=1,seq=y(��ʾServer �Լ��ĳ�ʼ���,���y=0,���Ƿ���˵ķ��͵ĵ�һ����),ack=x+1(�����յ��ͻ�����һ���������)
        �ۣ�ACK=1,seq=x+1(�ͻ����Լ��ĵ�x+1����),ack=y+1(��������˵ĵؼ�����)
       �ɿ���ԭ�򣺣�1��2���ÿͻ���֪���������ǿ���ͨ�ģ���2��3���÷����֪����ͻ��˿���ͨ�ġ�
    3������http����
    4���������Ӧhttp����
    5��TCP�Ĵλ���
        �٣��ͻ��������˷���FIN����
        �ڣ�����˻ָ���ACK����Ϊ��FIN��Ӧ�𣨱�ʾ�յ��ر����ӵ�����Ӧ��֮��Զ˴�����ֹ�ȴ�״̬�����Խ������ݲ��ܷ������ݣ�
        �ۣ�����˷���FIN���󣬱�ʾ�����Ҳ�ر����ӣ�������յ��Զ˹ر�����֮������ϲ���Ҫ��һЩ�������ڱ���Ҳ�ر�֮ǰ����Ȼ������ͻ��˷������ݣ�
        �ܣ��ͻ����յ�����˹ر���������FIN�� ����Ӧ�������ˣ��Ĵλ��ֽ�������ʵ������ʱ���и�TIME_WAIT�ȴ�״̬��linux�ǵȴ�һ���ӣ������ã���ʱ�Ķ˿��ǿɸ��õģ�
        
        
### ���һ���߿��á��ݵȡ���ȫ�������ܵĽӿ���Ҫ�ֱ���ʲô
#### �ӿڵ����������������һ������֪������ӿ�����ʲô�õ�
#### �ӿڵĲ�������˲����Ĵ�С�Ϳ���չ��
#### �ӿڵİ�ȫ���ڰ��������ӽ���
#### ��֧��ʲôЭ�飬����dubbo �ӿڣ�����ͬʱ֧�ֶ��Э��ĵ���
#### TPS,����������Ӧʱ�����ڿ�����Ϻ���ò����£��������˽�ӿڵĴ�����������Ӧ�Ը߲������󣬱���Ҫ���º�˵Ĵ洢�����Ƿ�����sql���Ƿ���Ҫ���棬��������˷ֲ�ʽ���棬Ҫ���ǻ��洩͸������ѩ�������⡣��ҵ����Ҳ���Զ࿼�ǣ��Ƿ���Ҫ�����̳߳ء�MQ�첽������
#### �ӿ��Ƿ���Ҫ���ݵȣ�����ͻ��������Ե��߼�����ô��Ҫ���Ǹ��ӿ������ݵ�
#### �ӿ����أ�����
#### �Ƿ���Ҫ�����ⲿ�ӿڣ��Ƿ���Ҫ���ⲿ�ӿڻ���
#### �ӿ����Ƿ���Ҫ���ػ��棬�����ӿ����ܡ�
#### ������Ⱥ������Ⱥ�������������⣬�ӿڿ��Զ�д����
