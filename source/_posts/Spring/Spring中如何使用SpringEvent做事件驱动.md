---
title: SpringEvent���¼�����
date: 2025-07-06 13:33:41
categories: Spring
---

# SpringEvent���¼�����

## ��������

### ��Spring��ʹ��Spring Event���¼�������һ��ʵ�������������Ч��ʽ�����Ѿ�������Spring Event�Ļ��������ʵ�ֲ��裬�������ҽ�ͨ������Ĵ���ʾ������ϸ���������Spring��ʵ���¼��������ơ�

### ����һ�������¼�

���ȣ�������Ҫ����һ���¼��࣬�������Ҫ�̳�`ApplicationEvent`����������У����ǿ��Է�װ���¼���ص����ݡ�

```java
import org.springframework.context.ApplicationEvent;

public class RegisterSuccessEvent extends ApplicationEvent {
    private String userName;

    public RegisterSuccessEvent(Object source, String userName) {
        super(source);
        this.userName = userName;
    }

    public String getUserName() {
        return userName;
    }
}
```

����������У�`RegisterSuccessEvent`���װ��һ���û�ע��ɹ����¼������а������û�����Ϣ��

### ������������¼�������

��������������Ҫ����һ���¼����������¼���������Ҫʵ��`ApplicationListener`�ӿڣ�����Ҳ����ʹ��`@EventListener`ע�������һ��������Ϊ�¼���������

ʹ��`ApplicationListener`�ӿڣ�

```java
import org.springframework.context.ApplicationListener;
import org.springframework.stereotype.Component;

@Component
public class RegisterSuccessListener implements ApplicationListener<RegisterSuccessEvent> {
    @Override
    public void onApplicationEvent(RegisterSuccessEvent event) {
        // �����ﴦ���¼������緢�ͻ�ӭ����
        String userName = event.getUserName();
        System.out.println("User " + userName + " registered successfully. Sending welcome message...");
        // TODO: ���ͻ�ӭ���ŵ��߼�
    }
}
```

����ʹ��`@EventListener`ע�⣺

```java
import org.springframework.context.event.EventListener;
import org.springframework.stereotype.Component;

@Component
public class AnotherRegisterSuccessListener {

    @EventListener
    public void handleRegisterSuccessEvent(RegisterSuccessEvent event) {
        // �����ﴦ���¼��������¼��־
        String userName = event.getUserName();
        System.out.println("Log: User " + userName + " registered successfully.");
        // TODO: ��¼��־���߼�
    }
}
```

ע�⣺ʹ��`@EventListener`ע��ʱ�����ϣ�������ض����͵��¼���������ע����ָ���¼����ͣ������������������ʡ�������ͣ�����ζ�������Լ����������͵��¼�������ͨ�����ǻ�ָ��������¼���������ߴ���������ȺͿɶ��ԣ���

### �������������¼�

���������Ҫ�ں��ʵĵط������¼�����ͨ������ĳ��ҵ���߼��������֮����еġ�

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationEventPublisher;
import org.springframework.stereotype.Service;

@Service
public class UserService {

    @Autowired
    private ApplicationEventPublisher applicationEventPublisher;

    public void registerUser(String userName) {
        // �����û�ע���߼��Ѿ��������
        System.out.println("User " + userName + " is being registered...");
        
        // �����¼�
        RegisterSuccessEvent event = new RegisterSuccessEvent(this, userName);
        applicationEventPublisher.publishEvent(event);
    }
}
```

����������У�`UserService`���е�`registerUser`�������û�ע��ɹ��󷢲���һ��`RegisterSuccessEvent`�¼���

### �ܽ�

ͨ�����ϲ��裬���Ǿ���Spring��ʵ����һ���򵥵��¼��������ơ���`UserService`�е�`registerUser`����������ʱ�����ᷢ��һ��`RegisterSuccessEvent`�¼���Ȼ������ע��������¼����͵ļ��������ᱻ֪ͨ������ִ����Ӧ�Ĵ����߼������ַ�ʽ������ʵ�������Ľ�����������ơ�


## ������


### ��Spring����У����������¼�����������ǿ��Ĺ��ܣ����ǿ��Զ���ʹ�ã���Ҳ���Խ�������������ض���ҵ������`@TransactionalEventListener`ע���������һ����ϵ㣬����������������Ĳ�ͬ�������ڽ׶μ�������Ӧ�¼���

### ��������

- **����Transaction��**�������ݿ�����У�������ָ��Ϊ�����߼�������Ԫִ�е�һϵ�в�������Щ����Ҫôȫ��ִ�У�Ҫôȫ����ִ�С��������ĸ����ԣ�ԭ���ԣ�Atomicity����һ���ԣ�Consistency���������ԣ�Isolation�����־��ԣ�Durability����ͨ�����ΪACID���ԡ�

- **�¼�������Event Listening��**����Spring����У��¼���������������󣨷����ߣ������¼��������������󣨼��������첽��ͬ���ؽ��ղ�������Щ�¼�������һ�ֽ��������ͨ�ŵķ�ʽ��

### `@TransactionalEventListener`��ʹ��

�����ṩ��ʾ���У�`UserService`�������û�ע���߼�������ע��ɹ��󷢲�һ��`UserRegistrationEvent`�¼�������¼���`UserRegistrationEventListener`���������ؼ����ڼ�����ʹ����`@TransactionalEventListener`ע�⣬����ζ���¼��Ĵ�����������ض��׶ν��С�

- **Ĭ����Ϊ**��`@TransactionalEventListener`Ĭ��������ɹ��ύ��`AFTER_COMMIT`�׶Σ������¼�������������Ϊ��ȷ���������еĲ�������Ӱ�쵽����Ļع����ߡ����磬���ͻ�ӭ�ʼ������Ĳ����Ͳ�Ӧ��Ӱ���û�ע������ĳɹ����

- **phase����**��ͨ��`phase`���ԣ��������ü�����������Ĳ�ͬ�׶δ�����
  - `BEFORE_COMMIT`���������ύǰ�����������������񼴽����ʱִ��һЩ������߼�������������׳��쳣�����ܵ�������ع���
  - `AFTER_COMMIT`��������ɹ��ύ�󴥷���������ִ�в���ع��Ĳ������緢��֪ͨ��
  - `AFTER_ROLLBACK`��������ع��󴥷��������ڼ�¼�ع���־��ִ�����������
  - `AFTER_COMPLETION`����������ɺ󴥷��������ύ���ǻع���������ִ�����������޹صĲ�����

### Ӧ�ó���

ʹ��`@TransactionalEventListener`�ĵ��ͳ���������

- ������ɹ���ִ���첽�������緢���ʼ�������֪ͨ�ȣ��Ա��������������ִ�С�
- ������ع���ִ���ض���������¼�������Ա��ڹ����Ų����ơ�
- �����񼴽��ύǰִ��һЩ�����������ݸ��²�����

��֮��`@TransactionalEventListener`�ṩ��һ�����ķ�ʽ��������Ĳ�ͬ�׶���Ӧ�¼����Ӷ�ʵ�ָ����ӵ�ҵ���߼��������Ľ���ͨ�š�