---
title: SpringBoot 将结果保存到redis的方式
date: 2025-07-20 13:33:41
categories: redis
---

在Spring Boot项目中，将数据库查询结果保存到Redis是一个常见的需求，用以提高数据访问速度和减轻数据库负载。以下是几种实现这一需求的常见方法；

### 1. 使用Spring Data Redis手动存储

这种方法涉及到直接使用`RedisTemplate`来手动管理缓存。在`DataCacheService`类中，通过`@Autowired`注入`RedisTemplate`和数据库访问的`YourRepository`。在`@PostConstruct`注解的方法中，项目启动后执行数据库查询，并将结果保存到Redis中。

```java
@Service
public class DataCacheService {
    
    @Autowired
    private RedisTemplate<String, Object> redisTemplate;
    
    @Autowired
    private YourRepository yourRepository;
    
    @PostConstruct
    public void init() {
        List<YourEntity> data = yourRepository.findAll();
        redisTemplate.opsForValue().set("cacheKey", data);
        redisTemplate.expire("cacheKey", 1, TimeUnit.HOURS); // 设置过期时间
    }
}
```

**优点**：直接控制缓存的内容和生命周期。
**缺点**：需要手动管理缓存的更新和失效。

### 2. 使用`@Cacheable`注解自动缓存

Spring Cache提供了一种声明式的缓存抽象，`@Cacheable`注解可以自动缓存方法的返回值。首先，需要配置一个`CacheManager`，这里使用Redis作为缓存存储。然后，在需要缓存的方法上使用`@Cacheable`注解。

```java
@Configuration
@EnableCaching
public class CacheConfig {
    @Bean
    public CacheManager cacheManager(RedisConnectionFactory factory) {
        RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig()
            .entryTtl(Duration.ofHours(1))
            .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(new GenericJackson2JsonRedisSerializer()));
        return RedisCacheManager.builder(factory).cacheDefaults(config).build();
    }
}

@Service
public class YourService {
    
    @Autowired
    private YourRepository yourRepository;
    
    @Cacheable(value = "yourCache", key = "'allData'")
    public List<YourEntity> getAllData() {
        return yourRepository.findAll();
    }
}
```

**优点**：简化了缓存管理，只需注解即可。
**缺点**：可能不如手动管理灵活，特别是在复杂的缓存策略上。

### 3. 使用`ApplicationRunner`或`CommandLineRunner`接口

这两个接口用于在Spring Boot应用启动时运行代码。它们非常适合用于初始化数据或加载缓存。

```java
@Component
public class RedisDataLoader implements ApplicationRunner {
    
    @Autowired
    private YourRepository yourRepository;
    
    @Autowired
    private RedisTemplate<String, Object> redisTemplate;
    
    @Override
    public void run(ApplicationArguments args) throws Exception {
        List<YourEntity> data = yourRepository.findAll();
        redisTemplate.opsForValue().set("initialData", data);
    }
}
```

**优点**：适合在应用启动时执行一次性任务。
**缺点**：仅适用于启动时的数据加载，不适合动态缓存更新。

### 4. 使用`@EventListener`监听应用启动事件

这种方法通过监听`ApplicationReadyEvent`事件来在应用完全启动后执行代码。虽然这种方法与`ApplicationRunner`或`CommandLineRunner`类似，但提供了更多的灵活性，特别是在需要更细粒度的控制时。

```java
@Service
public class StartupDataLoader {
    
    @Autowired
    private YourRepository yourRepository;
    
    @Autowired
    private RedisTemplate<String, Object> redisTemplate;
    
    @EventListener(ApplicationReadyEvent.class)
    public void loadDataOnStartup() {
        List<YourEntity> data = yourRepository.findAll();
        redisTemplate.opsForValue().set("startupData", data);
    }
}
```

**优点**：提供了在应用完全启动后执行代码的灵活性。
**缺点**：与`ApplicationRunner`相比，代码可能稍微复杂一些。

### 总结

选择哪种方法取决于具体的需求和应用场景。如果需要简单的缓存管理，`@Cacheable`注解可能是最好的选择。如果需要更灵活的控制或需要在应用启动时执行特定的初始化任务，`ApplicationRunner`、`CommandLineRunner`或`@EventListener`可能更适合。手动使用`RedisTemplate`提供了最大的灵活性，但也需要最多的代码和维护工作。