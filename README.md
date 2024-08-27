# Spring Bean Lifecycle: 

## 0. `IoC Container` инициализация и запуск
<details><summary>details</summary> 

1. Запуск приложения
2. Сканирование конфига приложения 
3. Создание структур данных, необходимых для управления бинами
</details>

## 1. `BeanDefinitionReader` читает конфигурации из метаданных и загружает/регистрирует `BeanDefinition` объекты
<details><summary>details</summary> 

### 1.1 Чтение конфигураций
[Configuration Metadata](https://docs.spring.io/spring-framework/reference/core/beans/basics.html)
- Конфигурационные файлы XML
```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="myService" class="com.example.MyService"/>
</beans>
```
- Конфигурация на основе аннотаций `@Component`, `@Controller`, `@Service`, `@Repository`
```Java
@Component
public class MyService {
    // ...
}
```
- Конфигурация на основе Java классов с аннотациями `@Configuration`, `@Bean`
```Java
@Configuration
public class AppConfig {

    @Bean
    public MyService myService() {
        return new MyService();
    }
}
```
- Конфигурация на основе `AnnotationConfigApplicationContext`
```Java
AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext();
context.register(MyService.class);
context.refresh();
```

## 1.2 Регистрация бинов - создание метаданных бина 
[BeanDefinition](https://docs.spring.io/spring-framework/reference/core/beans/definition.html)
1. Class 
2. Name 
3. Scope 
4. Constructor arguments 
5. Properties 
6. Autowiring mode 
7. Lazy initialization mode 
8. Initialization method 
9. Destruction method

</details>

## 2. `BeanFactoryPostProcessor` настраивает `BeanDefinition` объекты перед фактическим созданием бинов
<details><summary>details</summary>

Реализация интерфейса [BeanFactoryPostProcessor](https://docs.spring.io/spring-framework/reference/core/beans/factory-extension.html#beans-factory-extension-factory-postprocessors) имеет единственный метод _postProcessBeanFactory()_, предоставляет доступ к созданным `BeanDefinition` объектам и позволяет вносить изменения.
</details>

## 3. `BeanFactory` создает бины через их конструкторы (1-ый этап внедрения зависимостей)
<details><summary>details</summary>

`BeanFactory` вызывает конструктор каждого бина и происходит [Dependecy Injection](https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-collaborators.html#beans-constructor-injection).
При необходимости он делегирует это пользовательским экземплярам `FactoryBean`.
</details>

## 4. `IoC Container` или `BeanFactory` (?) выполняет `Dependecy Injection` [через setter's](https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-collaborators.html#beans-setter-injection) (2-ой этап внедрения зависимостей)
<details><summary>details</summary>

Если зависимости внедряются через конструктор, то сначала создаются зависимые бины, а затем те, которые от них зависят.
**Избегайте циклических зависимостей**: используйте Constructor Injection | @Lazy | Refactor Code

</details>

## 5. `BeanPostProcessor` настраивает бины **перед** инициализацией
<details><summary>details</summary>

[BeanPostProcessor](https://docs.spring.io/spring-framework/reference/core/beans/factory-extension.html) вызывает метод _postProcessBeforeInitialization()_ перед вызовом `init-метода` (если он определен) и возвращает бин.

</details>

## 6. [Инициализация бина](https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html) - @PostConstruct, XML init-method, @Bean initMethod, InitializingBean.afterPropertiesSet()

## 7. `BeanPostProcessor` настраивает бины **после** инициализации
<details><summary>details</summary>

[BeanPostProcessor](https://docs.spring.io/spring-framework/reference/core/beans/factory-extension.html) вызывает метод _postProcessBeforeAfterInitialization()_ после вызова `init-метода` (если он определен) и возвращает бин.
Обычно это необходимо, если вам нужно обернуть прокси вокруг бина или в случае циклических зависимостей.

</details>

## 8. Использование бина
## 9. [Уничтожение бина](https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-lifecycle-disposablebean) - @PreDestroy, XML destroy-method, @Bean destroyMethod, DisposableBean.destroy()
## 10. Завершение работы `IoC Container`
